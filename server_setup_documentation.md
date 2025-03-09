# Server Setup Documentation
## Introduction to LAMP Stacks
A LAMP (**L**inux, **A**pache, **M**ySQL, **P**HP) Stack is a functional web server based upon the four
open source products identified in its title. Linux provides the OS; Apache acts as the web server 
itself; MySQL creates web-accessible databases; and PHP is the server-side programming language 
used in conjunction with HTML to bind it all together into a functioning whole.

A LAMP stack is a quick, free way to create and run a web server with extra content management
capabilities. The addition of MySQL and PHP to the Apache platform enables programmers to build websites
which serve as gateways to databases and which can run a number of other web-based processes.

## Installing Apache
Before installing any additional software on the Linux machine, the OS must be updated. From the CLI,
run the following commands:

```
sudo apt update
sudo apt -y upgrade
```

Once updates are complete, locate and install the Apache package by running the following commands:

```
apt search apache2
apt show apache 2
sudo apt install apache2
```

The `show` command will allow you to to see additional information about the package and verify it is 
the correct software. Using `sudo` is necessary since installing software requires full root access.

### Configuring Apache
Once Apache is installed, verify that it is active and enabled by using `systemctl status apache2`.
(It should show both active \[currently running\] and enabled \[will restart automatically if the
machine is rebooted\].)

To make sure your Apache server is accessible on the web, install the `w3m` browser and navigate to
the loopback address, `localhost`:

```
sudo apt install w3m
w3m localhost
```

If the default page appears, congratulations! Apache is up and running successfully!

## Installing PHP
Like installing Apache, installing PHP requires root access:

```
sudo apt install php libapache2-mod-php
sudo systemctl restart apache2
```

Note that you will have to restart Apache after installing PHP. (After the reboot, you may want
to verify that Apache is back up and going successfully by checking its status as you did previously.
The command once again is `systemctl status apache2`.)

### Configuring PHP
Before changing any configurations, you should verify that PHP is working on your machine. Using
a text editor, create `info.php` in the `/var/www/html/` directory:

```
cd /var/www/html/
sudo nano info.php
```

Add the following to the file:

```
<?php
phpinfo();
?>
```

Check that the file displays by navigating to your external IP with `/info.php` added. Alternatively,
use `w3m` again:

```
w3m http://localhost/info.php
```

If the page displays, PHP is successfully running! However, additional configuration is necessary
to make PHP useful on your LAMP Stack.

Navigate to the `/etc/apache2/mods-enabled/` directory and make a backup of the original config file,
then open the original in your text editor:

```
cd /etc/apache2/mods-enabled/
sudo cp dir.conf dir.conf.bak
sudo nano dir.conf
```
Move `index.php` to the front of the various index options; that way, Apache will default to your
PHP page instead of the HTML page. Exit the config file and check its syntax using
`apachectl configtest`. If you receive "Syntax OK," then all is well. At that point, reload Apache
and verify its status:

```
sudo systemctl reload apache2
sudo systemctl restart apache2
systemctl status apache2
```

You are now free to create an `index.php` page using PHP scripts. Navigate to `/var/www/html` and 
create a page in your text editor:

```
cd /var/www/html/
sudo nano index.php
```

This will become your default page for you LAMP stack/Apache server, the first thing viewed at its 
IP. You can thus verify the page is working by navigating to `externalIP/index.php` on a browser
or by checking it in `w3m` with `w3m http://localhost/index.php`. 

## Installing MySQL
To install MySQL requires the usual steps:
1. Update your machine
2. Install the software
3. Configure the package to work with the rest of the LAMP stack

The command to install MySQL once again requires root access:

```
sudo apt install mysql-server
```

Like Apache, it's necessary to verify that MySQL is active and running. This requires using the same
command as before, except for the `mysql` program: `systemctl status mysql`. If everything is active
and enabled, then the package is online successfully!

### Configuring MySQL
Should you need to verify you have installed the correct version of MySQL in order to run it alongside
specific software packages, you can confirm your version number by running `mysql --version`.

Unfortunately, the basic package is a bit lax on security, and a secondary post-installation script is 
necessary to enable necessary security features. For that, run the following:

```
sudo mysql_secure_installation
```

For the prompts which follow, answer with **Y**. The exception is the `Password validation policy`,
select the option best suited for your configuration.

Following this, to access the app and make any additional changes (creating users/passwords, etc.),
log in using `sudo mysql -u root`. Users can then be added with the following:

```
mysql> create user 'username'@'localhost' identified by 'password';
```

Note that the single quotations are necessary; enter the desired credentials in place of 
**username** and **password**. Also note that all `mysql` commands must end with a semicolon.

From here, you're ready to log in as the root user and create databases! You can then grant
permissions to access databases to the user(s) you created earlier.

One additional step is necessary. MySQL needs to connect to PHP. First, install two small
packages that will enable the connection, and then restart both Apache and MySQL:

```
sudo apt install php-mysql php-mysqli
sudo systemctl restart apache2
sudo systemctl restart mysql
```

To craft the connection and enable authentication, you will need to make a login file and grant 
ownership and permissions only to your machine's root user. The commands for that are as follows:

```
cd /var/www
sudo touch login.php
sudo chmod 640 login.php
sudo chown :www-data login.php
ls -l login.php
sudo nano login.php
```

Now that the file is created, permissions are set, and the file is open in your text editor,
add the following PHP scripts, with your own credentials in the place of the database to be used, 
username, and password:

```
<?php
$db_hostname = "localhost";
$db_database = "databasename";
$db_username = "username";
$db_password = "password";
?>
```

Your database is now configured and ready to be put online on your Apache server!

## Verifying the LAMP Stack
The easiest way to verify that all components of the LAMP stack are working together as they should
is to create a test database in MySQL (which was necessary to finish configuration with the PHP above)
and then craft a PHP page for it on the Apache server. By creating PHP scripts to pull information
from the database, you will place the database on your server's website. If the data appear there
as they should, then everything is working as it should!

To add your test database to the web server, first create a PHP page in the proper directory:

```
cd /var/www/html
sudo nano databasename.php
```

Use HTML and PHP to write the page, then test its syntax:

```
sudo php -f /var/www/html/databasename.php
```

If all is well, you should be able to see your page on your website at http://localhost/databasename.php
(either in a browser, with external IP substituted for `localhost`, or using that address in `w3m`.

---

## Resolving Issues with the LAMP Stack
While running through the above, I encountered very few problems. My only issues came in writing the
PHP for the test database (`opac.php`) and the other test page for PHP (our `index.php`). The stress
on attention to detail was very real for me there, and I had to take time to uncover my typos in 
both pages. Fortunately validating the syntax for `opac.php` revealed the exact line numbers containing
errors, so things were easier to fix on that page. A couple of quick edits later, and everything was
online and running as it should be.

There were no errors or issues in installing any of the actual LAMP software or making configuration
changes on my end. On the whole, I've had a lot of fun with making this, and I'm enjoying learning
more PHP as we go.

*I feel like my Configuration sections replicate the book too much, but I wanted to have specific
changes here as well so I can refer to them later.*
