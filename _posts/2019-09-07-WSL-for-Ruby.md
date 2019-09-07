---
layout: post
title:  "Set up WSL for Ruby"
---

I'm trying to learn Ruby on Rails with a friend, who has build a prototype app and put it in Git. I have a nice, fast Windows 10 machine and rather than set up a Linux VM, I was encouraged by a lot of blog posts from Ruby on Rails developers who have succesfully used the Windows Subystem for Linux (WSL). It is turning out to be er... interesting.

Microsoft provides good, basic instructions for [installing WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10). I am using Ubuntu 18.04 LTS. Following the directions there, enable WSL and install Ubuntu from the Windows Store. Continue following the Microsoft directions to [launch and intialize](https://docs.microsoft.com/en-us/windows/wsl/initialize-distro) your Ubuntu shell. Be sure to make a note of the sudo password.

Next, update your installation.
```
sudo apt update
apt list --upgradable
sudo apt upgrade
```
Once you're finished
`sudo apt install`
should not show any new files to install.

---
## Fix the Windows Filesystem
Next, you need to fix the filesystem on your Ubuntu installation to talk to Windows correctly. Anything in your normal Linux filesystem (e.g. /home, /var, /etc) is not accessable to Windows. If you open or move the files in explorer, you will corrupt them. As of the Fall 2018 Creators Update, Windows 10 filesystems are mounted and you can `cd /mnt/c` to read and write from your C: drive. The problem is all files belong to root and have global read/write/execute permission bits. It's explained in a [Microsoft devblog post](https://devblogs.microsoft.com/commandline/chmod-chown-wsl-improvements/). Ruby sometimes tries to chmod on files and your compilation will fail if your application is located in the Windows filesystem. If you want to be able to access your files from the Windows envionment, you must umount and remount your Windows drives.
```
sudo umount /mnt/c
sudo mount -t drvfs C: /mnt/c -o metadata
```
Tragically, this isn't as useful as it initially seems. Anything you edit in Windows will be changed back to root ownership and have the permissions set back to read/write/execute. If you edit a file on the windows side, `sudo chown username filename` and `chmod 644 filename`.

---
## Install Ruby

These Ruby bits are based on information at [Gorails.com](https://gorails.com/setup/windows/10) but some of the directions are outdated.

You will install a series of dependencies. You need all of these.
```
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev libsqlite3-dev sqlite3
```

Next step is to install Ruby 2.6.3 with rubenv.
```
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL

rbenv install 2.6.3
rbenv global 2.6.3
ruby -v
```
The ruby -v command should output something similar to `ruby 2.6.3p62 (2019-04-16 revision 67580) [x86_64-linux]`

Finally you need to install bundler and rehash your environment.
```
gem install bundler
rbenv rehash
```

---
## Get a Database
Next you're going to want a database. We are using MariaDB. You'll also need to pick up redis and nodejs.
```
sudo apt-get install mariadb-server libmariadb-dev
sudo apt-get install redis-server
sudo apt install nodejs
nodejs --version
```
Again, with a succesful installation, nodejs --version will return a version and not an error.

Now you are ready to configure MariaDB, to be explained in the [next blog post](MariaDB-Ubuntu-WSL.html).










