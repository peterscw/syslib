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

## Installing MySQL


### Configuring MySQL


## Verifying the LAMP Stack


## Resolving Issues with the LAMP Stack
