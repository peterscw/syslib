# Omeka Documentation
LIS 690: Systems Librarianship

## Installing Omeka
### Prerequisites
#### Installing ImageMagick
After successfully configuring your LAMP stack and providing a website for your library, you can add
`Omeka` to easily display digital collections online. Running Omeka first means installing other 
software and checking existing installations.

First, verify your versions of `MySQL` and `PHP` as before with `WordPress`:
```
mysql --version
php --version
```
If your versions are above 8.0 and 7.4, respectively, you can continue! If not, update both programs.

Next, install `ImageMagick` to allow the machine to work with image files. This requires a bit of work
in `Apache` as well.
```
sudo apt install imagemagick
sudo a2enmod rewrite
sudo systemctl restart apache
```
The software should now be installed, and `Apache` is now configured (and restarted) to work with
`ImageMagick`. 

#### Configuration with MySQL
Like with `WordPress`, `Omeka` requires connections to a `MySQL` database. Configure that in the 
usual way, creating a new database/user.
```
sudo su
mysql -u root
create user 'username'@'localhost' identified by 'password';
create database omeka;
grant all privileges on omeka.* to 'username'@'localhost';
\q
```
Remember to record your database credentials for future config changes!

### Installing Omeka
In `/var/www/html`, run `sudo wget https://github.com/omeka/Omeka/releases/download/v3.1.2/omeka-3.1.2.zip`.
Unzip the file in that directory using `sudo unzip omeka-3.1.2.zip`. Then rename the directory to 
simplify things with the `mv omeka-3.1.2 omeka` command. You can now change directories and work in that
one directly to complete the installation. All that remains to complete the installation is modifying the
config file to add our database credentials and changing security permissions. Complete that by doing the
following:
```
cd omeka
sudo nano db.ini
```
Change all XXXX values to match your database credentials, then save/write the file. Now change security
settings and ownership:
```
sudo chown -R www-data:www-data /var/www/html/omeka
```
Finally, restart both `MySQL` and `Apache`.
```
sudo systemctl restart mysql
sudo systemctl restart apache2
```

### Web Configuration
Navigate to http://localhost/omeka and finish setting up the Omeka account. You will create login
credentials here as well; be sure to remember them!

## My Experience
Installing `Omeka` was pretty straightforward, especially with the textbook handy. My only issue came
after returning to the website after a few days to add my content. I forgot how to log in to my own
site, and I had to look up the address to get to the Omeka dashboard (localhost/omeka/admin). Adding
items/collections was easy, and I had done this before for LIS 602. No technical issues were 
encountered during the actual installation or setup.
