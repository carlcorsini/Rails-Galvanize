# Rails API and CORS

In this lesson you will learn how to configure Rails to work as an API. It will include completely removing the view layer, learning about the `render` method, and installing CORS.


### Set up a new Rails app

```
rails new notes-api --database=postgresql --skip-test-unit --skip-spring
cd notes-api && rake db:create
```

### Remove View Related Gems

Remove all gems relating to static assets like CSS, JavaScript, or View templates.
* gem 'sass-rails', '~> 4.0.3'
* gem 'uglifier', '>= 1.3.0'
* gem 'coffee-rails', '~> 4.0.0'
* gem 'therubyracer',  platforms: :ruby
* gem 'jquery-rails'
* gem 'turbolinks'

Run `bundle install` to update the GEMFILE.lock.

### Remove Views and Assets

1. remove the entire directory `app/assets`
1. remove the entire directory `app/views`

### Migrate & Seed Database

1. Create a table for Notes. They should have a body & title attribute.
1. Seed the database with a few notes with `rails c`.

### Routes, Controller.... Action!

Set up a route for `notes`. It should have a `notes` controller with an `index` action that returns JSON.

This is an example index action. Notice the use of `render json: @notes`.

```ruby
def index
  @notes = Note.all
  render json: @notes
end
```

### Other controller actions
This API should respond to GET, POST, DELETE, etc. In the `NotesController`, set up actions for `create`, `show`, `update`, & `destroy`.

### Rack CORS

Now we need to install the [Rack CORS gem](https://github.com/cyu/rack-cors) so other apps can read from the API we build. Just include the `rack-cors` gem in the gemfile.

`gem 'rack-cors', '~> 0.3.1'`

Don't forget to run `bundle install` to include this gem!

Add Rack CORS config in `config/application.rb`. It should look something like this:

```ruby
class Application < Rails::Application
  config.middleware.insert_before 0, "Rack::Cors" do
    allow do
      origins '*'
      resource '*', :headers => :any, :methods => [:get, :post, :options, :delete]
    end
  end
end
```

https://students.galvanize.com/cohorts/3/daily_plans/2015-02-09


https://github.com/cyu/rack-cors
