### Rails Views

The final html output of your rails apps is comprised of Templates, Layouts, and Partials. Let's go over each of these.

In your workspace folder create a new rails project.

```
$ rails new kittens
```

cd into your project and create something with attributes

```
$ rails g scaffold kittens name:string favorite_toy:string cute:boolean age:integer

$ rake db:migrate
```

Start your server and visit [http://localhost:3000/kittens](http://localhost:3000/kittens). Consider adding a root path to your routes `root 'kittens#index'`to default to that view. Click around and see what has been made for you with the scaffolding. Open your project in atom:

```
$ atom .
```

### Templates - ERB
Inside `kittens/app/views/kittens` you see that rails created a bunch of files.  The files either have a `html.erb` extension or a `json.jbuilder` extension.  Let's look at the `html.erb` files.  These are HTML files that use the ERB (Embedded RuBy) template system.

With ERB we can inject RUBY using both `<% %>` and `<%= %>` tags. The `<% %>` tags are used to execute Ruby code that does not print anything, such as conditions, loops or blocks, and the `<%= %>` tags are used when you want output to be displayed. Let's look at our `kittens/index.html.erb` file.

```
<% @kittens.each do |kitten| %>
  <tr>
    <td><%= kitten.name %></td>
    <td><%= kitten.favorite_toy %></td>
    <td><%= kitten.cute %></td>
    <td><%= kitten.age %></td>
    <td><%= link_to 'Show', kitten %></td>
    <td><%= link_to 'Edit', edit_kitten_path(kitten) %></td>
    <td><%= link_to 'Destroy', kitten, method: :delete, data: { confirm: 'Are you sure?' } %></td>
  </tr>
<% end %>
```

The loop is set up using regular embedding tags (`<% %>`) and the name is inserted using the output embedding tags (`<%= %>`).

### Layouts

Let's look at our `layouts/application.html.erb`.  In this file we see html that is rendered when we render any of our templates, along with our script and link tags.  We also see `<%= yield %>` in our `<body>` tags.  Yield is where are templates will be rendered.
A layout may be a good place to render a navbar that is present throughout your site.  What else might be good to put in your layout?  How and why would we add multiple layouts?  Research [layouts in the Rails Guides](http://guides.rubyonrails.org/layouts_and_rendering.html) and add another layout to your project.

### Partials

Partials are kinda what they sound like.  It gives us a way to extract some code that we think we might write again and encapsulate that code to use later.  Let's make our kitten list a partial.

In your `views/kittens` folder create a file called `_kitten_list.html.erb`.  Cut the following code out of `kittens/index.html.erb`

```
<% @kittens.each do |kitten| %>
  <tr>
    <td><%= kitten.name %></td>
    <td><%= kitten.favorite_toy %></td>
    <td><%= kitten.cute %></td>
    <td><%= kitten.age %></td>
    <td><%= link_to 'Show', kitten %></td>
    <td><%= link_to 'Edit', edit_kitten_path(kitten) %></td>
    <td><%= link_to 'Destroy', kitten, method: :delete, data: { confirm: 'Are you sure?' } %></td>
  </tr>
<% end %>

```

and paste it into `_kitten_list.html.erb`.

In your `kittens/index.html.erb` add:

```
<%= render kitten_list%>
```

where you want to render your kitten list. Now that we know what a partial is we see that when we ran `$ rails g scaffold` rails created a partial for us.  What partial did it create?  Why did it make that partial?
Furthermore, what else can we do with or put in partials?  Research partials here http://guides.rubyonrails.org/action_view_overview.html.

### Exercises:

Choose a new model and recreate what we did without `rails g scaffold`

Then practice what we've learned:  https://github.com/gSchool/arrays-in-erb
