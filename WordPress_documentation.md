# WordPress Documentation
## Creating a Library Presence Online
LIS 690: Systems Librarianship

## Installing WordPress
### Prerequisites
Before installing WordPress on your library LAMP stack, first ensure your machine has the latest versions
of `MySQL` and `PHP` installed. Run the following commands:
```
mysql --version
php --version
```
If `MySQL` is above v. 8.0 and `PHP` is newer that v. 7.4, you're ready to go!

The `WordPress` package requires unzipping a download from their website, so first make sure `unzip` is
installed. If not, simply run `sudo install apt-get unzip` before continuing. (This was an issue with my
own installation, but simply installing the `unzip` package took care of things.)

### Installation
In the `/var/www/html` directory, run the following commands:
```
sudo wget https://wordpress.org/latest.zip
sudo unzip latest.zip
```

### Configuring with `MySQL`
Now that `WordPress` is up and running, you need to link it to your `MySQL` database so it can store and
access data on your machine. Run the following commands to create a new user and a new database:
```
sudo su
mysql -u root
create user 'username'@'localhost' identified by 'password';
create database wordpress;
grant all privileges on wordpress.* to 'username'@'localhost';
\q
```
Create a unique username and password to use in those commands.

Now you can change the pertinent config files in `WordPress` to use that database. First, use `cd /var/www/html/wordpress`
to change to the `WordPress` directory. Make a copy of your default config file:
```
sudo cp wp-config-sample.php wp-config.php
```
Open your `wp-config.php` in `nano` or your text editor of choice. Edit the file to include the database
name, username, and password in the appropriate fields, then add this line to end to disable FTP uploads:
```
define('FS_METHOD','direct');
```

### Security Setup
Finally, change ownership of the `WordPress` files to allow the software to write to its directory:
```
sudo chown -R www-data:www-data /var/www/html/wordpress
```

## Creating Your Site
Congratulations! `WordPress` is now available online at http://localhost/wordpress! Navigate there and
complete setting up your library website using the `WordPress` dashboard. Note that you will have to
create login credentials online in order to finish setup and edit your page.

## My Experience
All went well by following the written instructions in the textbook. As noted above, however, I did have
to figure out why my VM kept throwing a `unzip not found` error. I had assumed it would have been part
of the regular `Ubuntu` install, but it wasn't. One quick `apt search unzip` later revealed the 
package was available, and `sudo apt install unzip` got it on my VM. 

From there, I had to learn how to use `WordPress`, which was less intuitive than I had imagined it would
be. I had problems figuring out how to remove some of the theme elements and how to edit the top menu, 
but reading the `WordPress` forums and other documentation resolved all issues quickly.
