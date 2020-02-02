layout: post
title:  "Sudden babel problems in Rails"
---
## What, no CSS?

Rails is complicated. It uses Webpack to manage all the Javascript and even people who know Rails well don't necessarily grok Webpack. All of a sudden today my Rails enviornment declined to apply any CSS and only show HTML. I hadn't done anything; I just pushed some view changes to git and pulled down a controller and routes change. Everything worked fine on Scott's machine and utterly failed on mine. Scott, my Ruby on Rails guru and teacher, just shrugged his shoulders ‾\\_(ツ)_/‾ and went back to the [purgatory of Rails deployment](https://fuzzyblog.io/blog/rails/2020/02/01/the-power-of-production-in-development.html) which is way above my pay grade.

I ran a brew upate and brew upgrade to no avail. I did a fresh git clone. Nope. I tried rerunining yarn install. Nope. Rehashed the rbenv. Nope. Things were looking grim.

I popped open the Chrome console and found a compilation error in the Webpack. I had a clue, "Module build failed (from ./node_modules/babel-loader/lib/index.js". Like all developers, I pasted it into Google and landed at [Stack Overflow](https://stackoverflow.com/questions/52087421/module-build-failed-from-node-modules-babel-loader-lib-index-js-typeerror). A little reading and I decided I had nothing to lose by trying one of the suggestions.
```
npm install @babel/core @babel/preset-env. 
npm install -g npm
```
Now, how to get the webpack to update? I tried "yarn install --check files" but that didn't help. It threw an error complaining about package-lock.json. Removing that caused yarn to fail with errors about config/webpacker.yml so I removed that too. That was bad. In the hopes of regenerating a working webpacker.yml I tried running rails webpacker:install but it just threw more errors. I tried a git pull but package.json and yarn.lock got merged which was not the plan. Yarn still wouldn't run. At that point, since I had no new code and nothing to lose, I deleted the whole development directory and checked out a fresh copy. FINALLY I could webpack, start the server, and with fresh babel and npm I had CSS.

So... the final solution for babel badness was
```
npm install @babel/core @babel/preset-env. 
npm install -g npm
rm -rf <local copy>
git clone <repo>
yarn install --check files
bundle exec rails s -p3000
```
Not elegant but I was able to contimue working.
