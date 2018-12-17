## Projects:
* https://github.com/gSchool/todo_orm/blob/master/README.md
* https://github.com/zipfian/fullstack-scraper/blob/master/part2.md


Repos:

https://github.com/gSchool/sql-query-exercise
https://github.com/gSchool/sql-with-ruby

## Misc
http://blog.codinghorror.com/a-visual-explanation-of-sql-joins/

HTTP basics in Rails
https://robots.thoughtbot.com/back-to-basics-http-requests

##### Named Routes

Notice how both the `root route` and faq `route` follow a similar pattern? On the far left we see the `named route` for each of our individual routes. By default, a `named route` of `root` will be given to our `root route`.

We can use `named routes` as easy ways to reference our pages. For example, if I was in a `view` and wanted to link to my homepage, I could use my root's `named route` like this: `<%= link_to "Home", root_path %>`. This is a rail's _helper method_ that generates the following HTML when our page compiles: `<a href="/">Home</a>`. The `convention` for using named routes, is to append `_path` to the `named route`. For our other route, we could write something like `<%= link_to "FAQ", team_faq_path %>`, and this would generate this HTML: `<a href="/team/faq">FAQ</a>`


### Objectives
* Get comfortable using Rails Console
* Familiarize with Active Record queries
* Learn rails command line tools: rails g model and rails g migration

Let's create a Galvanize contact list!

Go to http://guides.rubyonrails.org/active_record_basics.html and read about the CRUD actions.

- Add 5 classmates to your contact list
- Each person should have a unique name
- Use each of the 2 methods(new/save and create) to create students (experiement with different syntaxes)
- Use each of the 2 methods(find/save and update) to update information
- Delete one student from your contact list
- Use Rails Console

### Create a new Rails app
```ruby
- rails new galvanize_contact_list -TBd postgresql
- cd galvanize_contact_list
- rake db:create
- rails generate model Person first_name:string last_name:string awesome:boolean
```
- Add an email column to students table
  * `rails generate migration addEamilToPeople` email:string
  * add_column(table, column, type, options)
- Set a validation in model
  * validates :first_name, :last_name, presence: true

### BONUS
- See your database in postgres
```ruby
  * psql
  * \list
  * \connect your database_name
  * SELECT * FROM your_model;
```

CORS:
https://github.com/gSchool/fullstack-curriculum/blob/63b2a3db237065c8b17c1d8d357d38c548d365bd/weekly-g4/week-17/unscheduled/rack-cors.md

Testing:
https://github.com/gSchool/fullstack-curriculum/blob/63b2a3db237065c8b17c1d8d357d38c548d365bd/weekly-g4/week-06/unscheduled/capybara-setup-and-introduction.md


Associations:






https://github.com/gSchool/activerecord_associations

https://github.com/gSchool/activerecord_associations_from_scratch


https://students.galvanize.com/cohorts/13/daily_plans/2015-07-14
https://students.galvanize.com/cohorts/13/daily_plans/2015-07-15
