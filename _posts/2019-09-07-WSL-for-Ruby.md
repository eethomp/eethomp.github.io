---
layout: post
title:  "Set up WSL for Ruby"
---

I'm trying to learn Ruby on Rails with a friend, who has build a prototype app and put it in Git. I have a nice, fast Windows 10 machine and rather than set up a Linux VM, I was encouraged by a lot of blog posts from Ruby on Rails developers who have succesfully used the Windows Subystem for Linux (WSL). It is turning out to be er... interesting.

Microsoft provides good, basic instructions for [installing WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10). I am using Ubuntu 18.04 LTS. Following the directions there, it was straighforward to enable WSL and install Ubuntu from the Windows Store. Microsoft also has good directions to [launch and intialize](https://docs.microsoft.com/en-us/windows/wsl/initialize-distro) the Ubuntu shell. Be sure to make a note of the sudo password.

Next, update the installation.
```
sudo apt update
apt list --upgradable
sudo apt upgrade
```
Once that's all finished
`sudo apt install`
should not show any new files to install.

---
## Fix the Windows Filesystem
Next, fix the filesystem on Ubuntu to talk to Windows correctly. Anything in the normal Linux filesystem (e.g. /home, /var, /etc) is not accessable to Windows. Opening or moving the files with explorer will corrupt them. As of the Fall 2018 Creators Update, there is a workaround where Windows 10 filesystems are writable from WSL. Typing `cd /mnt/c` accesses the C: drive. Unfortunately it's a bit of a hack. All files created in the Windows 10 side belong to root and have global read/write/execute permission bits. It's explained in a [Microsoft devblog post](https://devblogs.microsoft.com/commandline/chmod-chown-wsl-improvements/). Ruby sometimes tries to chmod on the root owned files, so compilation will fail if the application is placed in the Windows filesystem. There is a fix that allows WSL to set normal Linux permissions. Unmount and remount the Windows drives with metadata enabled.
```
sudo umount /mnt/c
sudo mount -t drvfs C: /mnt/c -o metadata
```
Tragically, this isn't as useful as it initially seems. Editing a file in Windows will change it back to root ownership with permissions set back read/write/execute. Run `sudo chown username filename` and `chmod 644 filename` to give it normal Linux permisisons again. The changes are not sticky; you have to chown and chmod every... single... time... you move or modify a file in Windows.

---
## Install Ruby

These Ruby bits are based on information at [Gorails.com](https://gorails.com/setup/windows/10) but some of the directions are outdated.

First install a series of dependencies. All of these are necessary.
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

Finally install bundler and rehash the environment.
```
gem install bundler
rbenv rehash
```

---
## Get a Database
Next is the database. My team is using MariaDB. Also pick up redis and nodejs.
```
sudo apt-get install mariadb-server libmariadb-dev
sudo apt-get install redis-server
sudo apt install nodejs
nodejs --version
```
Again, with a succesful installation, nodejs --version will return a version and not an error.

Next I had to figure out how to configure MariaDB, to be explained in the [next blog post](MariaDB-Ubuntu-WSL.html).










