# Scaffolds

Scaffolds are used by professional engineers for one purpose only: rapid prototyping. Otherwise, they are training wheels and are meant to come off.

## Objectives
* Use a scaffold to create 7 controller actions
* know that scaffolds create a resource route (which maps to the 7 actions)

## Activity

Scaffolds give us very basic CRUD for any resource.

The steps are:

* run the `rails generate scaffold` command (see below)
* run migrations

**Steps to running a scaffold**

If you were generating a model for a Car with a make, model and year:

```
rails g scaffold car make:string model:string year:integer
```

Then run:

```
rake db:migrate
```

To see it in action, go to `http://localhost:3000/cars`

Check out [the docs](http://guides.rubyonrails.org/command_line.html) for more details on the Rails command line.

## Exercise

Finish and submit [scaffold-playground](https://github.com/gSchool/scaffold-playground) exercise.
