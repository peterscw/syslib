# Koha Installation Documentation
LIS 690: Systems Librarianship

## Installing Koha
### Prerequisites
#### The New VM and Firewall Rule
Since our VM needed to have more resources, a second VM was necessary. The major change other than
storage space was configuring the port/HTTP traffic to allow Koha to function on the web. To allow that,
I had to check **Allow HTTP Traffic** while setting up the VM as well as create a network tag for it,
 `koha-8080` (since it needs access on port 8080).

 The firewall rule is created in conjunction with the network tag:
 1. In VPN Network settings in the GCloud VM, select ** Firewall**.
 2. **Create Firewall Rule**
 3. Name the rule `koha-opac` and add the description "Open port 8080 for the OPAC."
 4. Add target tag `koha-8080`. 
 5. Source IPv4 range should say **0.0.0.0/0**.
 6. "Specified Protocols and Ports" should have TCP port 8080 selected/added.
 7. Click on **Create** to add rule.

 The OPAC can now access the VM through localhost:8080.
 
#### Installing Necessary Software Packages
Koha will need two other packages to run properly. Run this joined command to install both.
```
sudo apt install gnupg2 apt-transport-https
```
After installation is complete, reboot the VM (`sudo reboot now`).

#### Adding the Koha Repo
Koha needs its own remote repository to retrieve updates and other information, and this has to be
connected to the VM. Switch to root (sudo su) and run the following commands:
```
apt install sudo apt-transport-https ca-certificates curl
mkdir -p --mode=0755 /etc/apt/keyrings
curl -fsSL https://debian.koha-community.org/koha/gpg.asc -o /etc/apt/keyrings/koha.asc
apt update
echo 'deb [signed-by=/etc/apt/keyrings/koha.asc] https://debian.koha-community.org/koha stable main' |
 sudo tee /etc/apt/sources.list.d/koha.list
```
(I had to find these in the [Koha documentation] (https://wiki.koha-community.org/wiki/Koha_on_Debian#Installing_the_operating_system)
; the shorter set of commands in the book kept throwing error messages stating it was unable to connect 
to the repo/repo was not valid.)

Next, install the GPG key:
```
wget -qO- https://debian.koha-community.org/koha/gpg.asc | gpg --dearmor | sudo tee /etc/apt/
trusted.gpg.d/koha.gpg > /dev/null
```
### Install Koha
While still as root (you can `exit` from a blank command line at any time to leave root access), install
Koha:
```
apt update
apt show koha-common
apt install koha-common
```

## Configuring Koha, MySQL, and Apache2 (The Fun Part)
Koha requires quite a bit more configuration than the other software packages installed so far. Let's 
start with the usual config file changes, first making a backup of the existing file.
```
cd /etc/koha/
cp koha-sites.conf koha-sites.conf.backup
nano /etc/koha/koha-sites.conf
```
Change `INTRAPORT="80"` to `INTRAPORT="8080"` (the port we configured in the firewall rule).

Next, it's `MySQL` time again! And since this is a new machine, we start at the beginning.
```
apt install mysql-server
mysqladmin -u root password XXXX
```
(Make your own password, not X's.)

Configure `Apache2` now:
```
a2enmod rewrite
a2enmod cgi
systemctl restart apache2
```
Create a database for Koha to use, then tweak `Apache2` a bit more to allow the port passthrough to 
access that database.
```
koha-create --createdb bibliolib
nano /etc/apache2/ports.conf
```
Under `Listen 80` add `Listen 8080`. Check your changes with `apachectl configtest`, and if all is well,
restart as usual with `systemctl restart apache2`.

Now it's time to make `Apache2` use our config instead of its defaults:
```
a2dissite 000-default
a2enmod deflate
a2ensite bibliolib
systemctl reload apache2
systemctl restart apache2
```

And voila! Your CLI run is almost complete!

## Web Configuration of Koha
Before switching to the online GUI, you need to grab a password using the CLI. If you're still root,
open Koha's config file this way:
```
nano /etc/koha/sites/bibliolib/koha-conf.xml
```
Go to line 257 (`^_257` in `nano`). After the `<user>` tag if your username. Copy it down. Also copy
the password on line 258.

In your web browser of choice, access your Koha site at `http://localhost:8080` and log in using the
user/password you copied from the `koha-conf.xml` file.

As it installs, add whatever features you wish. In the end, make an Administrator for the ILS and 
make note of those credentials as well.

Finally, when all is installed, make the OPAC publicly accessible:
1. In Koha as an admin, click **More** at the top.
2. Administration>System Preferences>OPAC
3. Go to **OPACBaseURL** and enter your machine's IP in the box.
4. Save all settings.
5. Test by navigating to your machine IP in a new tab/browser. If all if well, you should see your
Koha OPAC page!

## My Experience
Koha was a beast to install. I made good use of the textbook (and didn't feel the lack of lecture
video), but adding the repo (as noted above) gave me fits until I consulted the documentation. After
that, no issues were encountered, and the web config itself was a breeze. Just make sure you have
the right URL for the right version, too, if you do this again in the future.
