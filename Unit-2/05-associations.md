## Objectives

* Set up a one-to-many association in Rails
* Explain what nested resources are and how we use them with our associations
* Use multiple instance variables in our form_for to create instances of a nested resource
* Ensure we do not have orphaned children when deleting a parent resource
* Build a CRUD app with a 1:M association

## One to Many Associations (has_many and belongs_to)

Examples:

One Owner `has_many` Pets and

Many Pets `belongs_to` one owner

(Foreign Key goes in the pets table)

Always remember - whenever there is a belongs_to in the model, there should be a foreign key in the matching migration!

In our models we add:

```
Owner
- has_many :pets
Pet
- belongs_to :owner
```

In our pets migration file we can add:

`t.references :owner` or `t.integer :owner_id`

Now in Rails console add some information:

```
fido = Pet.create(name: "Fido")
lassie = Pet.create(name: "Lassie")
elie = Owner.create(name: "Elie")
elie.pets
fido.owner
elie.pets << fido
elie.pets << lassie
elie.pets.size
elie.pets.map(&:name)
elie.pets.each {|x| puts "My pet is named #{x.name}!"}
fido.owner
```

## Nested Resources

Ok, we've seen resources.  What's all this nonsense about _nested_ resources? Well, sometimes you'll have resources that "belong to" other resources.  We've already seen how to establish such a relationship at the  model/database layer, but we can also represent this relationship in our routes.  In our example, we may have a resource called Animal that belongs to another resource, called Zoo.

#### Exercise

In our `routes.rb`

```
Rails.application.routes.draw do
  resources :zoos do
    resources :animals
  end
end
```

Running `rake routes` will show that this generates the following URLs:

```
          Prefix Verb   URI Pattern                              Controller#Action
    zoo_animals GET    /zoos/:zoo_id/animals(.:format)          animals#index
                POST   /zoos/:zoo_id/animals(.:format)          animals#create
 new_zoo_animal GET    /zoos/:zoo_id/animals/new(.:format)      animals#new
edit_zoo_animal GET    /zoos/:zoo_id/animals/:id/edit(.:format) animals#edit
     zoo_animal GET    /zoos/:zoo_id/animals/:id(.:format)      animals#show
                PATCH  /zoos/:zoo_id/animals/:id(.:format)      animals#update
                PUT    /zoos/:zoo_id/animals/:id(.:format)      animals#update
                DELETE /zoos/:zoo_id/animals/:id(.:format)      animals#destroy
           zoos GET    /zoos(.:format)                          zoos#index
                POST   /zoos(.:format)                          zoos#create
        new_zoo GET    /zoos/new(.:format)                      zoos#new
       edit_zoo GET    /zoos/:id/edit(.:format)                 zoos#edit
            zoo GET    /zoos/:id(.:format)                      zoos#show
                PATCH  /zoos/:id(.:format)                      zoos#update
                PUT    /zoos/:id(.:format)                      zoos#update
                DELETE /zoos/:id(.:format)                      zoos#destroy
```


- '/zoos' will list all the authors.
- '/zoo/17' will show info about that zoo.
- '/zoo/17/animaks' will show all that zoo's animals.
- '/zoos/17/animals/5' will show info about that zoo's 5th animal.

As we can see, this generates named routes for us as well!

## form_for with multiple resources

Now that we have seen how to set up our association in our migrations, models and routes - how do we implement this in our controllers and views?

The biggest difference here is that we will need to create information for both of our resources. For example, if we are creating a new animal, we need to specify which zoo it will belong to which means our form_for will look something like this.

`form_for [@zoo, @animal]`

If the first instance variable is nil, rails will post to the second one.

## Refactor with Shallow Routing

We've seen this concept before! If we take a look at the routes above, we can see that some of our URLs are getting pretty long. If we think a little more about how our routes are structured, we

`routes.rb`

```
Rails.application.routes.draw do
  resources :zoos do
    resources :animals,shallow: true
  end
end
```

`rake routes`

```
          Prefix Verb   URI Pattern                         Controller#Action
   zoo_animals GET    /zoos/:zoo_id/animals(.:format)     animals#index
               POST   /zoos/:zoo_id/animals(.:format)     animals#create
new_zoo_animal GET    /zoos/:zoo_id/animals/new(.:format) animals#new
   edit_animal GET    /animals/:id/edit(.:format)         animals#edit
        animal GET    /animals/:id(.:format)              animals#show
               PATCH  /animals/:id(.:format)              animals#update
               PUT    /animals/:id(.:format)              animals#update
               DELETE /animals/:id(.:format)              animals#destroy
          zoos GET    /zoos(.:format)                     zoos#index
               POST   /zoos(.:format)                     zoos#create
       new_zoo GET    /zoos/new(.:format)                 zoos#new
      edit_zoo GET    /zoos/:id/edit(.:format)            zoos#edit
           zoo GET    /zoos/:id(.:format)                 zoos#show
               PATCH  /zoos/:id(.:format)                 zoos#update
               PUT    /zoos/:id(.:format)                 zoos#update
               DELETE /zoos/:id(.:format)                 zoos#destroy
```

How does this affect our form_for now? Check out this stack overflow post for your answer http://stackoverflow.com/questions/9772588/when-using-shallow-routes-different-routes-require-different-form-for-arguments.

## Destroying a parent resource

When we delete a parent resource, we most likely will not want to have orphaned children. In mongoose, we wrote a hook that would delete all of the children of a parent resource before deleting the parent itself. In rails this is much easier. All we need to add in our parent model after the `has_many` is  `dependent: :destroy`

`has_many :animals, dependent: :destroy`

# Exercise

## Books and Authors

In this assignment you will build a CRUD app for a Library. Your application should consist of two resources, `books` and `authors`. **Do not use the `scaffold` generator.** All code should be hand written.

Each Author should have a/an:

- first name
- last name
- age

Each Book should have a/an:

- title
- description
- author_id

For this application, assume that **one Author has many books**

### Getting Started

1. Create a new rails application
2. Generate your models and pending migrations (`rails g model`)
3. Write the necessary code to set up your associations between Authors and Books
4. Run `rake db:migrate`
5. Open up `rails console` and make sure you have set up your association correctly
6. Ensure that when an Author is deleted, all of its books are deleted as well (we don't want any orphaned children!)
6. Write your controllers and views (always remember, controllers in the **plural!**)
7. Head to your `routes.rb` and use nested resources to build your routes
8. Write the controller actions and views

### Bonus

1. Refactor your routes.rb and use shallow routing for your nested resource. This will involve you changing some things in your views and controllers as well.
2. See where you are repeating yourself in your controller. Research `before_action` (previously called `before_filter`) and try to refactor your code into a private method that gets called using a `before_action`
