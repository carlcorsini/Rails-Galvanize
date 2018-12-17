# Understanding the Ruby on Rails Framework

In this lesson, you will be building a job posting board gHire (similar to [Indeed](http://www.indeed.com/)).  Along the way we will demystify the _Rails Magic_ that makes the framework so powerful.

## Goals

* Imlpiment basic CRUD operations in Rails
* Use `rails console`
* Debug with `byebug` or `Pry`
* Rails request [life cycle][lifecycle]

## Getting Started

In this exercise we will not devout time to making our site look good or have a flashy user interface, but instead we will focus on the machinery of Rails (`Models`, `Views`, and `Controllers`).

> To give you guidance as you learn (and master) the framework we have already scaffolded the files, methods, and classes you will need to recreate a basic version of Indeed and have written Rspec tests that you can use to validate that your implementation is correct.

Rails is a ~~big~~ humongous framework with extensive documentation, but in this exercise we are only going to use select parts of the framework. As such, much of [the docs][rails-guides] will be a confusing mess.  One goal of this assignment however is to get used to dealing with a framework that is too large to understand all at once, and learn how to selective use parts of the docs/tutorials.

__The [Rails Guides: Getting Started][getting-started] tutorial contains everything you need to complete this exercise. Follow along with it if you need guidance. Rather than making a blog, you will be making a job board however.__

### Running the Tests

As you complete each of the steps below, you can check the correctness of your application by running the automated tests (and get immediate feedback).  To run the tests simply run `bundle exec rspec` from the top level directory (the one with the `Rakefile` and this `readme.md`).

![test][rspec-console]

To start the application server, run `bundle exec rails server` and navigate to `http://localhost:3000` with your favorite web browser.

### The Application

In this application there are two type of users: `Companies` and `Users`.  Companies post `JobPostings` which `Users` can apply to through a `JobApplication`.

We will start building our application with the most essential component of a job board, the `JobPost`.  And before we can even have users apply to jobs, we need to allow `Companies` to __CREATE__ jobs.

For each of the components (`JobPosts`, `Companies`, `Users`, `JobApplications`) of our application we will start from the outside in, or rather start with the first step of the Rails request life cycle: __the HTTP Request__.

![life cycle](readme_images/rails_mvc.png)

The first step will be to to create a [resource][resource-routes] for our `JobPosts` so that we can properly route a user's HTTP request.

1. Fill in `config/routes.rb` to create resourceful routes for the `JobPosts`. To see which routes are created run: `rake routes`

 > Remember, run `bundle exec rspec` to check whether you have correctly implemented the routes.

 Now that we have a way to properly route requests, we need to create a controller to act on those requests. And the first action we need is way a for a `Company` to __CREATE__ a `JobPosting`.

2. Implement a `new` action in `app/controllers/job_posts_controller.rb` which properly directs the user to view with a form to __CREATE__ a `JobPosting`.  (remember... `bundle exec rspec`)

3. We correctly are directing the user to the right page now, but the page is empty.  Fill in the view template in `app/views/job_posts/new.html.erb` with a form to allow the `Company` to input the required content of a `JobPosting`.

 We are almost done with our `JobPost`!  We have presented a `Company` with a form to allow them to send back to our server information on the `JobPost` they want to create.  Now we need to properly store this information.

4. Generate (and run) a [migration][migrations] only to setup the database for our `JobPost` model (we have provided a model scaffold for you in `app/models/job_post.rb`).  It should have the following fields:
 * title
 * description
 * position (name of job position)
 * location

5. Since we have already setup a basic model for you, the final step to creating a post is to implement a `create` action in our controller that takes the submitted form input and creates a `JobPost` from it.

 Congrats! We have made it through each component of the Rails framework and request life cycle.  To test your application from end to end, spin up the web server (`bundle exec rails server`), visit the route to create a new `JobPost`: `http:localhost:3000/job_posts/new`.  You should be able to create fill in the form and create a new `JobPost` in your database.

6. Since we have not setup and controllers or views to display `JobPosts`, to check that a `JobPost` has correctly been created we need to directly inspect our models/database.  Using the `rails console`, inspect your database through your model to [verify][rails-c] that the correct model was created.

 ![][cheer]

 Inspecting our application from the console is a great way to interactive explore the application, but for real users we will need to display information about `JobPostings` on the site.

7. Create an `index` action in the `JobPosts` controller as well as an `index` view to display all of the job postings.  Also make the application root (`localhost:3000/`) point to the `index` of the `JobPosts`.

 ![][posting-index]

8. Create an `show` action in the `JobPostings` controller as well as a `show` view to display a single job posting.  You should display akk of its fields as well as the date it was posted:

 ![][posting-show]

 The next step of our application is to repeat the above process but for our `Company` entity to allow us to associate a given job posting with a given company.

 Let us start with our [data model][data-model] this time since this will allow us to experiment with a small component of the `Company` component in isolation (without the complexity of the routes, views, or controllers).

4. Generate (and run) only a [migration][migrations] to setup the database for our `Company` model (we have provided a model scaffold for you in `app/models`).  It should have the following fields:
         * name
         * size
         * email
         * location

 In our application, a single `Company` can have many different `JobPostings`.  To model this we will create a `has_many` relation.

5. To properly associate our `Company` model with our `JobPost` model we first need to alter our database.  Using a migration, add a `company_id` field to our `JobPost` table.

6. Next, update the 'Company' and 'JobPosting' models to create a `has_many` and a corresponding `belongs_to` relation.

7. Create a `Company` using the `rails console` and save it to the database.  It should have the corresponding data:
    * name: 'Galvanize'
    * size: 50
    * email: 'jobs@galvanize.com'
    * location: Colorado

 We will not allow any `Company` to sign-up however through the public website (an admin will create them through the `rails console`).  Because of this we will not need need a `create` or `new` action on our `Company` controller.

7. Create a routes in the `config/routes.rb` for the `index` and `show` routes of our `Companies`.

8. Also fill in the corresponding controller in `app/controllers/companies_controller.rb` with a `index` and `show` action.

 And the last step is to create templates in the `app/views` folder for the `index` and `show` actions.  The `index` will be our company directory and the `show` will display information on the specific company.

9. Create a view template for the `index` that displays all of our `Companies`.

 ![][company-index]

10. Create a view template for the `show` that displays the details of one `Company`.

 ![][company-show]

 Rather than deal with Users and accounts, we will simply let anyone apply for a job posting (as long as they provide their name, email, and phone number).  We will attach this information to the `JobApplication`.  Also, we will not show the applications on the site (assume they get messaged/emailed to the corresponding company) so we will not need a `index` or `show` action or route.

11. Just as with the `Companies`, first create a migration for the `JobApplication`.  It should have the following fields:
         * job_post_id
         * name
         * email
         * phone_number
         * cover_letter

12. This time we have not scaffolded a model for you.  Create a model for the `JobApplication` with a `belongs_to` relation to a `JobPost`.  Also setup the corresponding `has_many` relation in the `JobPost` model and a [`has_many :through`][has_many] association on the `Company` model (A `Company` has many `JobApplications` through the `JobPost`).

 To apply to a job, a user will apply on the `JobPost` show page.  To implement this we will need to create a form on the `show` view of the `JobPost`.

13. Create a form on the `JobPost` page to allow a user to sumbit a `JobApplication`.  It should properly route to the `JobAppplcation` create action as well as associate the new `JobApplication` with the `JobPost` it came from.

 ### Adding Complexity

 We have implemented the basic functionality of a Job board site: `Companies` can create `JobPosts` and users can submit `JobApplications`.

 Since we do not require users to create an account or login to submit an application, we should use validations to cut down on spam applications.

14. When a user submits a `JobApplication` [validate][validation] that the name, email, and phone number are present.  Do not allow a `JobApplication` without these fields.

 Validating the presence of these fields will cut down on user induced errors from submitting to early, but we still want to make sure that the emails and phone numbers are valid.  You may need to use basic regular expressions, [Rubular][rubular] is a great interactive tool to test Regex.

15. Perform basic validation to verify that the email submitted is a properly formed: some characters separated by an `@` and then either `.com`, `.edu`, or `.org` ([some name] @ { .com | .edu | .org})

16. Do the same for the phone number field, validate that it is a 3 digits, `-`, 3 digits, `-`, then 4 digits (xxx-xxx-xxxx)
 that the email field is a valid email (name seperated by a @ with a .com .net .edu .org)


## Resources

* [Rails API](http://api.rubyonrails.org/)
* [Rails command-line](http://guides.rubyonrails.org/command_line.html)
* [Debugging](http://guides.rubyonrails.org/debugging_rails_applications.html)
* [Pry with Rails](http://railscasts.com/episodes/280-pry-with-rails)
* [Testing Rails Applications](https://robots.thoughtbot.com/how-we-test-rails-applications)

Extra credit:

* Allow Users to upload a resume file
* use polymorphic relations to have a single model for both Company and users
* Active Mailer to email a company a JobApplication
* allow a company to login and view all submitted applications
* add authentication

* Allow users to sign up with a username and password.
        * Use `secure_password` to roll your own authentication system. See this [Railscast](http://railscasts.com/episodes/270-authentication-in-rails-3-1) for an example of this.
        * Only a posting's creator should be able to edit or delete the posting.
        * Each user should have a "My Postings" page that lists only their postings.
* Allow users to upload photos in their postings.  See the [Paperclip](https://github.com/thoughtbot/paperclip) library.
* Add sorting/filtering/searching to the postings.
* Include absolutely __no__ styling or markup to your views to make your site look like it was designed in 1992 ;)

<!-- Links -->

[lifecycle]: http://tutorials.jumpstartlab.com/images/rails_mvc.png
[getting-started]: http://guides.rubyonrails.org/getting_started.html
[fun-gif]: http://media.giphy.com/media/e7eZRpz4JDkqc/giphy.gif
[rails-guides]: http://guides.rubyonrails.org/
[resource-routes]: http://guides.rubyonrails.org/getting_started.html#getting-up-and-running
[migrations]: http://guides.rubyonrails.org/active_record_migrations.html#creating-a-standalone-migration
[rails-c]: https://www.railstutorial.org/book/modeling_users#sec-creating_user_objects
[cheer]: http://media.giphy.com/media/jJCyOEQXmva3m/giphy.gif
[data-model]: http://en.wikipedia.org/wiki/Data_model
[sqlite]: http://www.sqlite.org/
[company-index]: readme_images/company-index
[company-show]: readme_images/company-show
[has_many]: http://guides.rubyonrails.org/association_basics.html#the-has-many-through-association
[rubular]: http://rubular.com/
[validation]: https://www.railstutorial.org/book/modeling_users#sec-format_validation
[rspec-console]: readme_images/console_rspec.png

https://github.com/gSchool/rails-review/tree/exercise
