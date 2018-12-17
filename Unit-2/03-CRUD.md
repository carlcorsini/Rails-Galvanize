# Rails CRUD

In this lesson we'll combine much of what we've learned about Rails to create the full CRUD lifecycle for a resource.

As we do with most features in most applications, we'll start in our routes file.

### Resourceful Routes

A convention in Rails is to follow a similar routing pattern for CRUDing ActiveRecord objects. This pattern is called **resourceful routing**. If I put the following in my routes, `resources :blog_posts` and then run `rake routes`, I should get back 8 **resourceful routes**.  Since **PATCH** and **PUT** are aliases, let's consider a final list of **7 resourceful routes**. That means a fully resourced controller class would have 7 different actions.

By looking at the output of `rake routes`, can you determine what the 7 actions are? Also notice how you see each of the **HTTP methods** mentioned above, as well as the **named routes** for each action. All in all, this is the resourceful convention in Rails.

To let users create a blog post, all we need is the `new`, `create`, and `index` actions. Change our route to this:

`resources :blog_posts, only: [:index, :new, :create]`

Rerun `rake routes` and there should be three routes for our blog_posts.

### Create && Read

The **create** in CRUD will access the **new**, **create**, and **index** resources.

To create new ActiveRecord objects (BlogPosts in our case), users will insert some data into a form and send a **POST** request to our server. The first question we should ask ourselves is "Where should this form go?" We have **conventions** in rails to answer this question.

When making pages to display our forms to create _new_ ActiveRecord objects, we follow a url pattern like this: `<pluralized_active_record_object>/new`, or in our case, `blog_posts/new`. Our resourced route generates this route by default, and assigns it to a **GET** request. When we visit "blog_posts/new", we're really just _getting_ a form to fill out.

Visit "/blog_posts/new" in the browser and follow the trail of errors until you have a working view.

#### The _New_ Resource

Most of the time when we are creating ActiveRecord objects, we'll do so through a form. The **convention** is that a user visits a `/new` page for a **resource**, fills out a form, and sends a **POST** request to our server. Our form is going to look like this on our `blog_posts/new` page:


    <%= form_for @blog_post do |f| %>
      <div>
        <%= f.label :title  %>
        <%= f.text_field :title %>
      </div>

      <div>
        <%= f.label :author %>
        <%= f.text_field :author %>
      </div>

      <div>
        <%= f.label :content %>
        <%= f.text_area :content %>
      </div>

      <%= f.submit "Create Blog Post" %>
    <% end %>

If we visit the page in the browser now, we'll get an error message saying something like this, `First argument in form cannot contain nil or be empty`. Let's break this down:

`form_for` is a rails **helper method** that expects its first argument to be an ActiveRecord object. This is by design, or we could say, another **convention**. The convention for the **new resource** is to set the instance variable (`@blog_post` in our case), to a _new_ ActiveRecord object. To do this, add the following to our BlogPostsController:

    def new
      @blog_post = BlogPost.new
    end

Refresh the page, and you should have a visible form in the view.

#### The _Create_ Resource

The **new** and **create** resources are tied closely together in that the **new** resource generally provides a place for the **create** action to be invoked from. Let's zoom in on the HTML that our `form_for` tag created above. I've removed some of the cruft so we can focus on the important parts.

    <form action="/blog_posts" method="post">
      <div>
        <label for="blog_post_title">Title</label>
        <input type="text" name="blog_post[title]" id="blog_post_title">
      </div>

      <div>
        <label for="blog_post_author">Author</label>
        <input type="text" name="blog_post[author]" id="blog_post_author">
      </div>

      <div>
        <label for="blog_post_content">Content</label>
        <textarea name="blog_post[content]" id="blog_post_content"></textarea>
      </div>

      <input type="submit" name="commit" value="Create Blog Post">
    </form>

All forms must have an **action** and a **method**. The **action** is the URL at which the form will submit to, and the **method** is the **HTTP verb** that the request will be made with. In this case, we could say we are going to send a **POST** request to the `/blog_posts` URL on our server. Remember our resourced routes? Run `rake routes` again and see if we have a route that will handle this request. It looks like we do, and it maps to our _create_ action on our `BlogPostsController`.

If we just click the "Create Blog Post" button right now, we should get a familiar error message: `The action 'create' could not be found for BlogPostsController`. Let's define an empty **create action** in our controller.

##### Params && Putting Active Record to Work

Jump back to the browser, fill out the form and submit the form. Now check the server logs in your terminal and see if you can find the **parameters** (**params**) that were submitted. It should look like this:

`Parameters: {"utf8"=>"✓", "authenticity_token"=>"1O212na6G1FdiDgl5WWJAzZBY3WzqOD1OK96KQEaY66a1gMGoqEu9hntt9ymNF3bDZwc4jvxVF8Ok0YLjyiJAw==", "blog_post"=>{"title"=>"How To Crud", "author"=>"George Bush", "content"=>"LOL JK I don't know how to CRUD"}, "commit"=>"Create Blog Post"}
`

What class does the **params** look like? It's a plain ole ruby **hash**.

Notice how each of the fields from our form came through in the params? When a form is submitted, so do the **inputs** we define on the form. We defined three **inputs** above, each of which had a **name** attribute, ie: `name="blog_post[content]"`. The **name** attributes on inputs help set the **key/value** pairs within our **params** hash.

As it turns out, its pretty valuable to be able to access the **params** inside of our controller actions. To do this, we follow a pattern like this in our controllers:

    private

    def blog_post_params
      params.require(:blog_post).permit(:title, :content, :author)
    end

We call this _sanitizing_ our parameters, and it is basically a way to keep our applications safe. We'll talk more about security later, but for now, get comfortable with the pattern. This method returns a **hash** of data _from_ our params. Specifically, it returns the value of the hash in our params which has the **key** of blog_posts. In other words, it gets us the stuff that we care about.

From the parameters above, our `blog_post_params` will return a hash like this: `{"title"=>"How To Crud", "author"=>"George Bush", "content"=>"LOL JK I don't know how to CRUD"}`. Does this hash look familiar? Remember when we created a blog post for Bob Ross with the `.new` and `.save` methods? The `blog_post_params` method returns _exactly_ what ActiveRecord needs to create our BlogPost object.

We can then access that hash by calling the `blog_post_params` method in our controller. Here's some code that will _initialize_ our object with the **instance** attributes, and then save it to the database. There is some additional logic in there which redirects users to the **index resource**, which we'll build out next.

    def create
      blog_post = BlogPost.new(blog_post_params)
      if blog_post.save
        redirect_to blog_posts_path
      else
        # This is a convention for handling errors, which will be covered more closely at another time
        @blog_post = BlogPost.new(blog_post_params)
        render :new
      end
    end

Fill out the form on the browser and submit it. You'll get a familiar looking error in regards to the **index action**, but before fixing that, let's check out something else. Even though we see an error, our Blog Post actually saved to the database.

-  `rails c`
-  `my_post_from_the_browser = BlogPost.last` Should return the post you submitted from the browser
-  `my_post_from_the_browser.title`

#### The _Index_ Resource

We're almost done with our first story. Let's fix the error message we are seeing by creating the **index** action that is referenced in our `redirect_to` method. Our index view should print all BlogPosts to the page.

See if you can follow these instructions, this should be a familiar part of this story:

-  Define in index action that sets an instance variable called `@blog_posts` to _all_ the BlogPosts in our database. You should use the ActiveRecord `.all` method.
-  Loop through your instance variable in the index view, and print each blog post's title to the page inside of a list.

This should complete our first story, "User can create a blog post. After creating the blog post, user is redirected to a list of all blog posts".

#### The _Show_ Resource

Think of the _show_ resource as the place we read our blog post. It is the **individual** blog post's home. We associate this with the following pattern in our URI's, `/blog_posts/:id`, or `blog_posts/1`.

Edit the resources in our routes file to look like this: `resources :blog_posts, only: [:index, :new, :create]`

If we rake routes, we should see a route that matches the pattern above.

In our controller, we'll need to define a show **action**. This action will do one thing, and that is assign an `@blog_post` variable to a single blog post. We can get the appropriate blog post like this:

    def show
        @blog_post = BlogPost.find(params[:id])
    end

In our **GET** request to the **show action**, we have access to the params hash, and specifically the ID value. That is, the value that comes after `blog_post` in our URI. So in the example above, `params[:id]` is equal to 1.

Create our show view in `app/views/bog_posts/show.html.erb` and we'll have access to our BlogPost **instance** in the view.

#### _Edit_ and _Update_ Resource

Now that we are able to show off an individual `BlogPost` object, let's take the next step and make it so that we can edit the object's state in the database.

Add this line to the bottom of `app/views/blog_posts/show.html.erb`:

```html
Edit me here: <%= link_to 'Edit', edit_blog_post_path(@blog_post) %>
```

We encounter a (now more familiar) error message: "The action 'edit' could not be found for BlogPostsController" (so we fix it!)

After fixing this we encounter another (now more familiar) error message:  "Missing template blog_posts/edit" (so we fix it!)

Copy this into the fix for the last issue:

```html
<%= form_for @blog_post, url: blog_post_path(@blog_post), method: :patch do |f| %>
  <div>
    <%= f.label :title  %>
    <%= f.text_field :title %>
  </div>

  <div>
    <%= f.label :author %>
    <%= f.text_field :author %>
  </div>

  <div>
    <%= f.label :content %>
    <%= f.text_area :content %>
  </div>

  <%= f.submit "Update Blog Post" %>
<% end %>
```

We now get the familiar: "The action 'update' could not be found for BlogPostsController" (and fix it!)

Time to fill in the update action in the `BlogPosts` controller. First, we start by finding the relevant `BlogPost` record that we want to update:

```ruby
@blog_post = BlogPost.find(params[:id])
```

`.update` is a method provided to us by `ActiveRecord::Base` that returns `true` if the object is updated or `false` if (for some reason) it could not be updated.

By convention, when an update fails, the user is shown the edit form again, otherwise they are taken to the show action.

```ruby
if @blog_post.update(blog_post_params)
  redirect_to blog_post_path(@blog_post)
else
  # This is a convention for handling errors, which will be covered more closely at another time
  render :edit
end
```

After this the only remaining item in completing CRUD is Delete/Destroy.


#### Burning Down the House (aka _Delete_ resource

Earlier we discussed Create and Read, and then Update. Let's use that demo app to add Delete.

Links for delete can often be found in several places...

  * On the index page listed next to each item
  * On the show page
  * On the edit page

Now we will add the delete link to the show page, right next to the edit link.

```
<%= link_to "Delete this post", blog_post_path(@blog_post), method: :delete %>
```

Notice this looks just like a regular link to a show page, with the exception of that last hash on the end.

Refresh that show page. What does the browser indicate we do next?

```
def destroy

end
```

If we delete an object, where do you want to send the user? There is no delete view. Usually the answer is the index page. Let's add that to our action.

```
def destroy
  redirect_to blog_posts_path
end
```

That's great, but we haven't really deleted anything. Let's add that now. Find the object to delete, and then destroy it. Note that we will not pass this into a view, therefore no need for instance variables.

```
def destroy
  blog_post = BlogPost.find(params[:id])
  blog_post.destroy
  redirect_to blog_posts_path
end
```

And that concludes the entire CRUD cycle!

## Exercise

Complete the [Rails CRUD Practice repo](https://github.com/gSchool/rails-practice-events-create).

-  git clone
-  create database
-  Read the README.md
-  Use the patterns defined here to create Events, instead of BlogPosts
