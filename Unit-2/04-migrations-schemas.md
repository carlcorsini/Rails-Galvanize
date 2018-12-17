## Schema

The generic concept of a schema is a database structure described in a formal language supported by the database management system (ActiveRecord in Rails) and refers to the organization of data as a blueprint of how a database is constructed.

Schemas are useful for a variety of reasons:

1. They provide a high level overview of the business resources within an application.
2. They may enforce the integrity of the data. That is to say, it may ensure that if the data is expected to be a string, it is stored as a string.
3. Enables developers to generate identical databases platforms (local development, production, etc.)

A schema describes how real world entities are modeled in the database. In other words, a schema is the structure of the database that defines the objects in the database.

> From Wikipedia: "A database schema specifies, based on the database administrator's knowledge of possible applications, the facts that can enter the database, or those of interest to the possible end-users."

The schema in a Rails app is located in `db/scheme.rb`, but it only shows up there after your first migration. Let's generate a quick sample app to explore one.

Create a rails app and cd into it:
`rails new example && cd example`

Create a new model for Users:

`rails g model Users name:string email:string description:text`

Run the migration to create a schema:

`rake db:migrate`

### Example Schema

The previous commands generated this schema for us. This file sets up a SQL database and is executed anytime a developer runs `rake db:create`.

```ruby
# encoding: UTF-8
# This file is auto-generated from the current state of the database. Instead
# of editing this file, please use the migrations feature of Active Record to
# incrementally modify your database, and then regenerate this schema definition.
#
# Note that this schema.rb definition is the authoritative source for your
# database schema. If you need to create the application database on another
# system, you should be using db:schema:load, not running all the migrations
# from scratch. The latter is a flawed and unsustainable approach (the more migrations
# you'll amass, the slower it'll run and the greater likelihood for issues).
#
# It's strongly recommended that you check this file into your version control system.

ActiveRecord::Schema.define(version: 20150910200938) do

  create_table "users", force: :cascade do |t|
    t.string   "name"
    t.string   "email"
    t.text     "description"
    t.datetime "created_at",  null: false
    t.datetime "updated_at",  null: false
  end

end
```

This document is checked into source control, so it can be shared with others on the team. Instead of demanding each developer on your project sets up their own database, this enables the entire team to share a database structure. When executed on different computers or in different environments, it will always set up an identical SQL database.

If you read the code comments above the Ruby code (and you should), you'll see that if you want to change this schema, you shouldn't alter the schema directly. How then, should one do that?

## Migrations

Migrations alter a database schema over time in a consistent and repeatable way. Each is time stamped (YYYYMMDDHHMMSS) and creates a new or revised version of your database.


Read about [Migrations in the docs](http://edgeguides.rubyonrails.org/active_record_migrations.html).

## Create Migration Files

To create a migration, you can use a Rails generator:

`rails generate migration AddDateToPosts`

This will create an empty but appropriately named migration in db/migrations/ts_add_date_to_posts:

```
class AddDateToPosts < ActiveRecord::Migration
  def change
  end
end
```

If the migration name is of the form "AddXXXToYYY" or "RemoveXXXFromYYY" and is followed by a list of column names and types then a migration containing the appropriate add_column and remove_column statements will be created.

`rails generate migration AddDateToPosts date:datetime`

generates:

class AddPartNumberToProducts < ActiveRecord::Migration
  def change
    add_column :posts, :date, :datetime
  end
end


Similarly, you can generate a migration to remove a column from the command line:

`rails generate migration RemoveDateFromPosts date:datetime`

generates:

```
class RemoveDateFromPosts < ActiveRecord::Migration
  def change
    remove_column :posts, :date, :datetime
  end
end
```

### Different migration methods:

  You should spend a few minutes researching what each of these are used for in migrations:

	* create_table
	* add_column
	* add_index
	* add_reference
	* add_timestamps
	* drop_table
	* remove_column
	* remove_index
	* remove_reference
	* remove_timestamps
	* rename_table
	* rename_column

## Up and Down

### `rake db:migrate`

This runs all migration files that have not been run before. Each migration will run in order of their "timestamp" in the filename.

### `rake db:rollback`

Often I make a mistake and use a typo in my `rails g migration` command and end up adding something silly to the database schema. You can quickly undo the latest migration by running `rake db:rollback`.


## Exercise

Use this repo to work through the next steps, but don't worry about the readme in the linked repo. Just follow the instructions here. https://github.com/gSchool/rails-hack-params


#### Clone and setup the Rails app
```
cd ~/workspace
git clone git@github.com:gSchool/rails-hack-params.git
cd rails-hack-params
bundle
rake db:create
rake db:migrate
rails s -p 3005
```

#### Add "Date of Birth" to all users

```
rails g migration add_date_of_birth_to_users
```

Crack open your text editor to add some custom code to the migration the above command generated.

```
class AddDateOfBirthToUsers < ActiveRecord::Migration
  def change
    add_column :users, :date_of_birth, :date
  end
end
```

Now run `rake db:migrate` to update the schema.

#### Add form field to user signup
With that done, start the rails app and visit http://localhost:3000/users/new. You'll notice it asks the user to create a username and password, but doesn't include a Date of Birth. Consider the MVC structure of an application and write code that:

1. lets users add their birthda
2. Stores the birthday with that user record in the database
3. Shows the birthday along with other user information on the `show` page. 
