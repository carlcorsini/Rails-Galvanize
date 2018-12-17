# Rails Models

### Setting up a demo app

When working in rails, we will primarily do four different types of actions to `ActiveRecord` objects. We will **create** them, **read** them, **update** them, and **destroy** them (CRUD). Each of these actions will follow a similar pattern, and we'll tackle them each individually. Let's create an app and generate a BlogPost model:

-  `rails new crud_demo`
-  `cd crud_demo`
-  `rake db:create`
-  `rails g model BlogPost title:string author:string content:text`

File generated from `rails g model` and lives in the `db/` directory:

```ruby
    class CreateBlogPosts < ActiveRecord::Migration
      def change
        create_table :blog_posts do |t|
          t.string :title
          t.string :author
          t.text :content

          t.timestamps null: false
        end
      end
    end
```

Another file that is generated from this lives in the models directory. It is named `app/models/blog_post.rb` and will inherit from `ActiveRecord::Base` by default. It should look like this:

```ruby
    class BlogPost < ActiveRecord::Base
    end
```

- `rake db:migrate`
  -  This will migrate your table.

At this point, our database is now ready to handle **BlogPost** objects. Let's prove that. Follow these commands:

-  `rails c` the 'c' stands for **console**
-  `BlogPost.all.count` should return 0
-  `BlogPost.create!(title: "How to Paint", author: "Bob Ross", content: "Just smile and move your paint brush")` This creates and saves an object in our database. **Keep this method in the back of your head**
-  `BlogPost.all.count` should return 1
-  `BlogPost.first` should return Bob Ross's blog post on "How to Paint"
-  `BlogPost.first.title`
-  `BlogPost.first.author`
-  `BlogPost.first.content`

We've successfully added a `BlogPost` to our database. We're getting a lot for free from ActiveRecord, but this still isn't super useful to our users. This is where CRUD comes into play, as its the process in which _users_ interact with our database.

In the next lesson, we'll take a break from developing this BlogPost app to review Active Record in more detail. We'll come back to this app in lesson 03-CRUD, so keep it handy.
