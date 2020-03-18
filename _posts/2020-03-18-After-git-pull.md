---
layout: post
title:  "After git pull"
---

There is a series of steps that have to be followed after pulling from git to get a rails application updated and running.

First make sure you're in develop and pull. I use co as an alias for checkout.

```
git co develop
git pull
```

Next, you need to see if there are any new gems and update the database.

```
bundle install
bundle exec rake db:migrate
```
If the pull was a major refactor, sometimes it's best to just drop and recreate the development database. With pgsql here's how that works. 

```
dropdb development_db_name
bundle exec rake db:create
bundle exec rake db:migrate
```

Now there is an important step. db:migrate often alters db/schema.rb. If you don't get the old schema.rb back from git, it will merge unwanted changes and screw up your branch.
```
git co db/schema.rb
```

Finally, make a branch, optionally clean out the database, seed, and launch the rails app.
```
git branch foo
git co foo
# sometimes it's a good idea to clean out the data before seeding
bundle exec rake data:clear_all --trace
# seed and launch the server
bundle exec rake data:init --trace
bundle exec rails s -p3000
```
