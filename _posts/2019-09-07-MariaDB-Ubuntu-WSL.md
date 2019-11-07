---
layout: post
title:  "MariaDB on Ubuntu 18.04 LTS in WSL"
---
## Configuring MariaDB

Where I left off on the Rails setup was installing MariaDB.

First start up MariaDB with 
```
sudo service mysql start 
```

Yep, the executable is still called mysql under the hood. Normally, the next step is `mysql -p -u root` to set the root password; however, in the special hell that is WSL, that command is rewarded with:
>ERROR 1698 (28000): Access denied for user 'root'@'localhost'

The *only* way I have found to initially get into the database is from [Ask Ubuntu](https://askubuntu.com/questions/766334/cant-login-as-mysql-user-root-from-normal-user-account-in-ubuntu-16-04).
```
sudo mysql -u root
```
This will start the MariaDB sql prompt without any complaints.

From there, execute the following commands to make a functional root user, replacing *password* with the root password you want to use. If you don't want a password, use empty quotes ('') after IDENTIFIED BY. This will drop the weird passwordless, sudo only root@localhost user and create a normal database root user. 

```
# Make sure there is a root@localhost
SELECT User, Host FROM mysql.user;

# drop and recreate the user with all priveleges
DROP USER 'root'@'localhost';
CREATE USER 'root'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

I was stuck for a while fixing MariaDB. Searching the error leads to a lot of web pages with putative solutions, most of which are centered around versions of UPDATE USER. They didn't throw errors, but they didn't work either. The only fix for my WSL environment seems to be the slightly terrifying drop and recreate the root user.

---
## Building the Rails Application

At this point I moved forward with my Ruby build. MariaDB was up and running, I had pulled down the application from Git, and I was ready to `bundle install`. That went swimmingly, after a worryingly long wait for nokogiri. Just go get yourself some coffee and have a break while you wait. It's not worth even sitting at the computer.

Unfortunately `bundle exec rake db:create` was not so smooth, resulting in:
>Mysql2::Error::ConnectionError: Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)

Again, searching the web provided a lot of bad advice. Various web pages said I needed to turn off Windows MySQL (nope, don't have one), connect over TCP, swith from localhost to 127.0.0.1, do both of the above, or jettison MariaDB entirely and run MySQL. The real solution turned out to be much simpler. As with everyhing else WSL does things differently and I had to find the sock.
```
sudo mysqladmin variables -p | grep sock
```
revealed that my sock is <span style="color:#9933FF">/var/run/mysqld/mysqld.sock</span>. A couple other useful paths I discovered along the way are the thankfully standard <span style="color:#9933FF">/etc/mysql/my.cnf</span> for cofiguration and the slightly idiosyncratic <span style="color:#9933FF">/var/log/mysql/error.log</span> for errors.

As a temporary fix, I edited <span style="color:#9933FF">/config/database.yml</span> to update the location of the sock and was able to compile the rails app. Since I'm developing on a team most of whom have a more standard Linux environment, I will have to figure out how to get MariaDB to put its sock in a standard spot.


