**LINUX SERVER CONFIGURATION**

**Project**

This project aims to configure a virtual private server on Linux 18.04, to host
a web application.

**Server information**

-   Public IP Address : 139.162.160.241

-   SSH port : 2200

-   Application url : http:// 139.162.160.241.xip.io

**Server configuration steps**

>   **Update currently installed packages**

-   apt-get update && apt-get upgrade

>   **Change hostname**

-   hostnamectl set-hostname movie-catalog

-   nano /etc/hosts -\> add 139.162.160.241movie-catalog

-   save and close

>   **Create « grader » user and give it permissions**

-   adduser grader

-   give it a password : grader

-   nano /etc/sudoers.d/grader

-   add : grader ALL=(ALL:ALL) ALL

-   exit and log as ssh grader\@139.162.160.241

-   create a .ssh directory with mkdir .ssh

>   **SSH key authentication**

-   On another console create the ssh-kew with :

-   ssh-keygen -b 4096

-   Copy the key to the server with :

-   scp \~/.ssh/grader_key.pub grader\@139.162.160.241:\~/.ssh/authorized_keys

-   Log in with ssh grader\@139.162.160.241 -i \~/.ssh/grader_key

-   Change permissions on .ssh directory and subfolders : sudo chmod 700 \~/.ssh
    and sudo chmod 600 \~/.ssh/\*

>   **Change ssh port and password authentication**

-   Run sudo nano /etc/ssh/sshd_config

-   Set Port 22 to 2200

-   Set PermitRootLogin to no

-   Set PasswordAuthentication to no

-   Sudo systemctl restart sshd

>   **Firewall configuration**

-   sudo apt-get install ufw

-   sudo ufw default allow outgoing

-   sudo ufw default deny incoming

-   sudo ufw allow ssh

-   sudo ufw allow 80/tcp

-   sudo ufw allow 123/udp

-   sudo ufw allow 2200/tcp

-   sudo ufw enable

-   Check parametres with sudo ufw status

**Deployment steps**

>   **Update installed packages**

-   sudo apt-get update

>   **Installation of apache2**

-   sudo apt-get install apache2

-   sudo apt-get install apache2-dev

-   to test if installation worked make point your browser to 139.162.160.241,
    and it should display the Apache page.

>   **Installation of WSGI and python3-dev**

-   sudo apt-get install libapache2-mod-wsgi-py3

-   sudo apt-get install python3-dev

>   **Enable mod_wsgi**

-   sudo a2enmod wsgi

>   **Construction of the file structure**

-   cd /var/www

-   sudo mkdir MovieCatalog

-   cd MovieCatalog

-   sudo mkdir MovieCatalog

-   cd MovieCatalog

>   **Import project Movie-catalog**

-   cd /var/www/MovieCatalog/MovieCatalog

-   git clone https://github.com/ManuGE-rep/Movie-Catalog.git

-   rename the application.py to \__init__.py so that wsgi recognise it : sudo
    mv application.py \__init__.py

>   **Update and upgrade installed packages**

-   sudo apt-get update && sudo apt-get upgrade

>   **Make python 3.6.7 the default python**

-   sudo ln -sf /usr/bin/python3 /usr/bin/python

>   **Install pip3**

-   sudo apt-get install python3-pip

>   **Install, create and activate a virtual environment**

-   sudo pip3 install virtualenv

-   sudo virtualenv venv

-   source venv/bin/activate

>   **Install requirements**

-   sudo pip3 install -r requirements.txt

-   test the app with sudo python \__init__.py

>   **WSGI configuration**

-   sudo nano /etc/apache2/sites-available/MovieCatalog.conf

-   add this to the file :

**\<VirtualHost \*:80\>**

>   **ServerName movie-catalog**

>   **ServerAdmin admin\@email.com**

>   **WSGIDaemonProcess catalog python-path=/var/www/MovieCatalog:/var/www/MovieCatalog/MovieCatalog/venv3/lib/python3.6/site-packages**

>   **WSGIProcessGroup MovieCatalog**

>   **WSGIScriptAlias / /var/www/MovieCatalog/moviecatalog.wsgi**

>   **\<Directory /var/www/MovieCatalog/MovieCatalog/\>**

>   **Order allow,deny**

>   **Allow from all**

>   **\</Directory\>**

>   **Alias /static /var/www/MovieCatalog/MovieCatalog/static**

>   **\<Directory /var/www/MovieCatalog/MovieCatalog/static/\>**

>   **Order allow,deny**

>   **Allow from all**

>   **\</Directory\>**

>   **ErrorLog \${APACHE_LOG_DIR}/error.log**

>   **LogLevel warn**

>   **CustomLog \${APACHE_LOG_DIR}/access.log combined**

**\</VirtualHost\>**

-   enable the site with sudo a2ensite MovieCatalog

-   disable the default site conf : sudo a2dissite 000-default.conf

-   reload apache : sudo systemctl reload apache2

>   **Configure wsgi file**

-   cd /var/www/MovieCatalog/

-   sudo nano moviecatalog.wsgi

-   add this configuration to the file :

>   \#!/usr/bin/python3

>   import sys

>   import logging

>   logging.basicConfig(stream=sys.stderr)

>   sys.path.insert(0,"/var/www/MovieCatalog/")

>   from MovieCatalog import app as application

>   application.secret_key = 'e38ce1f92e4e5f3a9c0cd7ceb7cd26ca'

-   restart apache2 : sudo systemctl restart apache2

>   **Set the server to respond to port 80**

-   Stop Apache : sudo systemctl stop apache2

>   **Install Sqlite3 database**

-   sudo apt-get install sqlite3 libsqlite3-dev

>   **Set the database and run the app on the server**

-   Set de database up : sudo python dbsetup.py

-   Populate the database : sudo python fakedata.py

-   Run the application : sudo python \__init__.py

-   Point your browser to : http:// 139.162.160.241.xip.io

**Ressources**

<http://leonwang.me/post/deploy-flask>

<https://www.digitalocean.com>
