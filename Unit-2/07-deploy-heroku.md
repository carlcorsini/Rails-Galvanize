### Deploy Rails Apps

Deploying Rails Apps to Heroku is super easy if you follow a few steps.

First off, we want to make sure we are using PostgreSQL.

In your gemfile make sure that you have,

```
  gem "pg"
```
If you do not change `gem "sqlite3"` to `gem "pg"`

run:

```
  $ bundle
```

Make sure your app is saved on git.

```
$ git add .

$ git commit -m"ready to deploy to heroku"
```

Create a heroku app to push to:

```
  $ heroku create
```

Push to that app:

```
  $ git push heroku master
```

Open the app:

```
  $ heroku open
```

Oh no, that didn't work.  We need to run migrations on heroku.

```
  $ heroku run rake db:migrate
```

Now it should work:

```
  $ heroku open
```
Now you should have an app on the web.  Is that it?  Kind of. There are limitations to PostgreSQL.  What if we want static file uploads, such as .png or .jpg files.  

Research
https://github.com/thoughtbot/paperclip
and
https://devcenter.heroku.com/articles/s3
