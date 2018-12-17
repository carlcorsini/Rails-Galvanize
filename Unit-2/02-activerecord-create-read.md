WORK IN PROGRESS

# ActiveRecord Intro (or "Models in Depth")

Rails comes with built-in support for SQLite3, but it could be substituted with PostgreSQL at any time. We will use ActiveRecord, a Ruby library built into Rails, to interact with the database layer.

In this lesson, we're going to explore the CR of CRUD in Rails with ActiveRecord.

## Objectives

* Use ActiveRecord to create objects
* Use ActiveRecord to find objects

## Create
In the last lesson you created an ActiveRecord model called BlogPost with attributes of `title`, `author` and `year`.

The following techniques produce the same result.

#### Technique #1

Using `new`, setting attributes, calling `.save`

```ruby
car = BlogPost.new
car.title = "10 Steps to Better Code"
car.author = "Victoria Swanson"
car.year = 2012
car.save
```

#### Technique #2

Using `new` with a hash, then calling `.save`

```ruby
car = BlogPost.new(title: "10 Steps to Better Code", author: "Victoria Swanson", year: 2012)
car.save
```

NOTE: the following are all different syntax for the same thing:

```ruby
car = BlogPost.new({title: "10 Steps to Better Code", author: "Victoria Swanson", year: 2012})

car = BlogPost.new title: "10 Steps to Better Code", author: "Victoria Swanson", year: 2012

car = BlogPost.new :title => "10 Steps to Better Code", :author => "Victoria Swanson", :year => 2012

car = BlogPost.new(:title => "10 Steps to Better Code", :author => "Victoria Swanson", :year => 2012)
```

Ruby is fun! :P

#### Technique #3

Using `.create`

```ruby
car = BlogPost.create({title: "10 Steps to Better Code", author: "Victoria Swanson", year: 2012})
```

```ruby
# Person is an ActiveRecord class, lives in app/models/person.rb
# Person is a Class, classes always start with a capital letter

# first_name, last_name etc... are column names
# they are like `attr_accessors`, but they are automatically created
# look in schema.rb to find out what's available

# Syntax
#   create is a method call
#   the argument to create is a hash
#     they keys are symbols
#     the values are strings, or integers or booleans or dates

Person.create(
         first_name: 'Joe',
          last_name: 'Example',
          eye_color: 'green',
      date_of_birth: Date.new(2012,2,4),
   height_in_inches: 63,
            awesome: false,
)
```
### ActiveRecord Creation Exercise

Download your app - which should look like this:

```
cd ~/workspace
git clone <repo>
cd <repo>
bundle
rake db:create db:migrate
```

In this case, the repo is located at: https://github.com/gSchool/rails-active-record-practice

- In the `db/seeds.rb` file, write a script that will create 9 people.
- Each person should have a unique name
- Use each of the 3 methods to create people (experiment with different syntaxes)
- Test your file by running `rake db:seed`

To add dates, just use strings:

```ruby
Person.create date_of_birth: '5/10/2013'
# <= british date by default
```

## Read

#### Finding all records

```ruby
BlogPost.all
```

#### Finding a single record

```ruby
BlogPost.find(1)
```

#### Filtering records

```ruby
BlogPost.where(title: "10 Steps to Better Code")
```

#### Ordering records

```ruby
BlogPost.order(:title)
```

#### Chaining methods

```ruby
BlogPost.where(title: "10 Steps to Better Code").order(:year)
```

### ActiveRecord Finding Exercise

In the same repo as above (rails-active-record-practice):

- order by first name
- filter so only awesome people are shown
- filter so only people with green eyes are shown
