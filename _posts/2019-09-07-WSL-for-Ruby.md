---
layout: post
title:  "Set up WSL for Ruby"
---

A lot of this is from [Gorails.com](https://gorails.com/setup/windows/10) but my experiences weren't exactly smooth.

For starters, I wouldn't recommend joining Windows Insider. If you have the Fall Creators Update you have the ability to run WSL. Joining the "Fast" circle is only a good idea if you don't use your machine for personal finances because it could expose you to insecure patches. This means you will not have WSL2 yet.

Enable WSL and install Ubuntu from the Windows Store. You will now have a naked bash shell. You will be promped to make a username and password. The password is for sudo so make a note of it.

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
Next, you need to fix the filesystem on your Ubuntu installation to talk to Windows correctly. Anything in /home/username is not accessable to Windows. If you touch the files in explorer, you will damage them. WSL doesn't have smb but your Windows 10 filesystems are mounted and you can `cd /mnt/c` to read and write from your C: drive. The problem is all files belong to root and have global read/write/execute permission bits. It's explained at [Microsoft](https://devblogs.microsoft.com/commandline/chmod-chown-wsl-improvements/). Ruby sometimes tries to chmod on files and your commands will fail if you are working on files that are on the Windows 10 side of the computer. To fix this, you have to unmount and remount your Windows drives.
```
sudo umount /mnt/c
sudo mount -t drvfs C: /mnt/c -o metadata
```
Tragically, anything you edit in Windows will be changed back to root ownership and have the permissions set back to read/write/execute. If you edit a file on the windows side, `sudo chown username filename` and `chmod 644 filename`.

---
## Install Ruby

Now you will install a series of dependencies. You need all of these.
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

Finally you need to install bundler and rehash.
```
gem install bundler
rbenv rehash
```

---
## Get a Database
Next you're going to want a database. I use MariaDB. You'll also need to pick up redis and nodejs.
```
sudo apt-get install mariadb-server libmariadb-dev
sudo apt-get install redis-server
sudo apt install nodejs
nodejs --version
```
Again, with a succesful installation, nodejs --version will return a version and not an error.

Now you are ready to configure MariaDB, to be explained in the next blog post.










