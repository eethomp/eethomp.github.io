---
layout: post
title:  "COVID and Rails"
---
I had the idea to keep a diary of the COVID pandemic, but truthfully it's not happening. I'm finally starting to really understand Ruby on Rails and coding is becoming enjoyable again. I just spent the weekend adding a new feature to CovidNearMe and it only required a little help from Scott.

Some things I need to remember about Rails. The rails console is lovely... until it's not.

```reload!``` reloads classes but not necessarily constants. Confusingly, if you have made any objects, those still keep the old properties and methods so you have to remake your test classes after a reload.

byebug is helpfully like GDB, including being able to set and clear breakpoints from inside the debugger.

Ruby will coerce numbers into integers behind your back. ```number.to_f``` casts to a float.

Activerecord creates an implicit model with properties of the database fields. This is handy, until you space and name an instance method after a database field. Ruby gives yourself enough rope hang yourself trying to figure out which one you just accessed. I suppose it's not as bad as overloading "=" in C++. (Yes, someone did that. No I was not amused after two days of debugging.)

```rake db:rollback``` is a thing of beauty when you realize you got fields wrong in a migration.

Just when you think you were too paranoid about checking for nil, one sneaks by and crashes your code. ```blank?``` is pretty nice. The negation is ```present?```.

As far as the COVID apocalypse, I've been working at home for four weeks now. Looks like it's likely three weeks more. I was right about insisting on office space for my job, I don't like 100% at home. I'm too much of an extravert to be happy. With the whole state of Maryland closed, grocery shopping at Mom's Organic Market is becoming a major, exciting outing. It's exciting to get things like raspberries and bananas that don't keep well when you're grocery shopping once every 2-3 weeks to minimize time in public. I run at Rock Creek Park. Thankfully they haven't closed the parking lots there. Other than that the end of days is more of a whimper than a bang.
