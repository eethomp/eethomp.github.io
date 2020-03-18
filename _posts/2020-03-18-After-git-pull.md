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

Now there is an important step. db:migrate often hoses up db/schema.rb.
```
git co db/schema.rb
```

After that, resume with making a branch, seeding any new data and launching rails.
