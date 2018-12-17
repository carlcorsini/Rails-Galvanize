# Rails Controllers

Controllers in Rails are the Express equivalent of the callback function you pass to route methods. It's the code that defines the behavior associated with the route.

#### In Express

```javascript
router.get('/foo', function(req, res) {
  // controller code
})
```

#### In Rails
In `config/routes.rb`:

```
get 'foo' => 'foo#bar'
```

In 'app/controllers/foos_controller.rb':

```ruby
class FoosController < ApplicationController

  def bar
    # controller / bar action code
  end

end
```

## Controller Actions

Controller actions are the methods defined inside of a controller class (see `bar` in the example above). They are first specified in the routes. (run `rake routes` to determine what controller actions are expected for a given controller...Typically there are 7 of them for the full CRUD interface).

It's here that you may define instance variables, access the database, or render a template for incoming HTTP requests.

## Instance Variables

Instance variables are the mechanism Rails uses to bind data from the model to data in the template. They are easy to recognize because they all start with `@` signs. These variables are  assigned a value (written to) in a controller and then used (read from) in a view.

Below is an example of a controller and the corresponding view where instance variables are assigned and used respectively.

```ruby
# in some controller, like app/controllers/pages_controller.rb

class PagesController < ApplicationController

  def index
    @personA = Person.new
    @personA.first_name = "Michael"
    @personA.last_name = "Scott"

    @personB = Person.new
    @personB.first_name = "Dwight"
    @personB.last_name = "Schrute"
  end

end
```

```
# in the corresponding view, like app/views/pages/index.html.erb

<p>Hi <%= @personA.first_name ' ' +  @personA.last_name %>!</p>

<p>Hi <%= @personB.first_name + ' ' +  @personB.last_name %>!</p>
```

#### Instance Variable Scope
As a rule, `instance variables` in rails are scoped only to the `action` in which they are defined. Let's add another `action` and talk about it.

```ruby
# in some controller, like app/controllers/pages_controller.rb

class PagesController < ApplicationController

  def index
    @personA = Person.new
    @personA.first_name = "Michael"
    @personA.last_name = "Scott"

    @personB = Person.new
    @personB.first_name = "Dwight"
    @personB.last_name = "Schrute"
  end

  def extra
    @personC = Person.new
    @personC.first_name = " Pam"
    @personC.last_name = " Pam"
  end

end
```

In this example, the `index` action does not have access to the `@personC` `instance variable`. The only other place that has access, or we may say has _scope_ to the `@personC` `instance variable`, would be the `view` which is associated with our extra `action`. Said view would be named `extra.html.erb` in the `app/views` directory.

## Exercise
[Practice with Instance Variables](https://github.com/gSchool/rails-practice-instance-variables/tree/6e94273de92a4fb8ee3778d879c9fd02c4a63388)
