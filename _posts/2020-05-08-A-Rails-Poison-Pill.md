---
layout: post
title:  "A Port 3000 Poison Pill"
---

![Yarn](/assets/yarn.jpg)

Yarn. Get it? OK this really isn't about Yarn but it gives me a good excuse to show off my handspinning.

### Finding the Poison Pill

We had a rails app that would only load assets on port 3000. It was a little mysterious since most of our apps can run on any port. I've seen issues with babel that broke the asset pipeline in the past but that wasn't the issue. One of the things that's hard about modern web development is all the moving parts. Rails, Spring, Node, Yarn, Webpack, Puma.

So why do I even care about running on another port when 3000 is the Puma default? Two reasons:
1. If you don't always use port 3000, you can forget what port you used
2. Another app is already running on port 3000

This app would run on other ports, but there were no assests. No images, no css, nothing but text. Inspecting the source code gave me a clue:

```
<script src="http://localhost:3000/packs/js/application-41ebd2b1de30afa7b3da.js" data-turbolinks-track="reload"></script>
<link rel="stylesheet" media="all" href="http://localhost:3000/packs/css/application-0c245609.css" data-turbolinks-track="reload" />
```

See the absolute references? Those are wrong. Puma was running on localhost:3010 but index.html was requesting assets from port 3000 and naturally they didn't load. But why was the asset pipeline running amok? I looked at another rails app that was happily living on localhost:3076 and all the references were relative. That app could happily start up on any port.

I went deep down a rabbithole of looking at Puma configuration, Yarn packages, Node configuration, hunted for old processes sitting on port 3000, and finally looked in the right place at the Webpacker configuration scripts. They would take a constant path and prefix it in front of application.js. But where was the constant coming from? Grepping for "localhost:3000" finally revealed the poison pill.

```config/environments/development.rb:  config.action_controller.asset_host = "http://localhost:3000"```

The fix was surprisingly simple. Comment that line out and all is well. The app would now run on any port passed in with 

```bundle exec rails s -p <port>```

as expected. Next time the Rails asset declines to work in development, I know at least on easy place to check before I start worrying about Yarn and babel.
