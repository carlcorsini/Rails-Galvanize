# Rails Routes

Like routes in nearly all web frameworks, they are responsible for directing primitive HTTP methods and mapping the request to useful, application specific behavior. Rails convention demands our routes use a RESTful design pattern that you're familiar with.

Here's what the route for a `projects` resource might look like.

|  HTTP Verb |      Path     |     Used For                                      |
|------------|---------------|------------------------------------------------|
| GET        | /projects     |     display a list of all projects
| GET        | /projects/new |     return an HTML form for creating a new project |
| POST       | /projects     |     create a new project                           |
| GET        | /projects/:id |     display a specific project                     |
| GET        | /projects/:id |     return an HTML form for editing a project      |
| PATCH/PUT  | /projects/:id |     update a specific project                      |
| DELETE     | /projects/:id |     delete a specific projec                       |


One could write Ruby code to define each of these routes, but because this pattern is so prevalent, Rails makes it super easy to generate all of these routes.

In `config/route.rb`, write:

```ruby
resources :projects
```

That's it! To confirm these routes were generated, `cd` into your Rails app and run `rake routes`. This command should list all routes available in your application.

### HTTP Methods

The above route definition will work for 90% of the applications you build in Rails, but sometimes you may need a more granular routing mechanism.

Rails lets you define a route for any incoming **HTTP methods**, or **HTTP verbs**. So far, we've only dealt with **GET** requests. Imagine I have a route like this:

`get '/superheroes', to: "superheroes#index"`

When I type in my browser "localhost:3000/superheroes", my browser sends a **GET** request to my rails server and my route handles it. This is the default behavior of "visiting" a website. My route would then map that request to the controller, and the **MVC** pattern would commence.

The **convention** in rails, is largely around the following HTTP methods: **GET**, **POST**, **PUT**,  and **DELETE**.

As a general rule:

-  **GET** requests primarily _fetch_ or _get_ data out of a database.
-  **POST** requests primarily _create_ or _post_ data to our databases.
-  **PUT** requests primarily _update_ data in our databases.
-  **DELETE** requests primarily _destroy_ data in our databases.

With these **HTTP verbs**, I can now expand my toolbelt around routing. If I wanted to make a **POST** request on my app, it would look like this.

`post '/superheroes', to: "superheroes#create"`

Now, anytime a **POST** request is directed towards "/superheroes", the **create action** in my SuperheroesController will execute. Notice how we can have two routes which share a URI pattern ("/superheroes")?

## Conventions

You may start to notice a pattern. Rails makes a ton of assumptions about your code organization based on route definitions.

Here are the basic assumtions Rails makes about your app based on routes:

* the corresponding controller name matches the part before the '#' in a route definition.
* The view names / action names match the part after the '#' in a route definition.
* The controller file name is plural, lowercase
* The controller class name inside the controller is plural, uppercase
* The folder for the views is plural, lowercase
* The action name matches the name of the view file

Check out the [Rails naming convention cheat-sheet](../img/rails-naming-guide.pdf) I made to get a better sense of these conventions.
