### WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

### Intro:

__"LAMP" stands for Linux, Apache, MySQL, and PHP (or Perl, Python). It's a software stack used for building dynamic websites and web applications. Linux serves as the operating system, Apache as the web server, MySQL as the database system, and PHP (or Perl, Python) for server-side scripting.
## Let dive into it__

## Step 1: Create an Ec2 Instance 

__1.__Create EC2 Instance of t2.micro type and Ubuntu OS image was lunched in the us-east-1 region using the AWS console.

![createEc2](https://github.com/OlavicDev/LampStack/assets/124717753/f09ad6ec-24d4-478c-b5c4-313f1a97367f)
![ubuntu](https://github.com/OlavicDev/LampStack/assets/124717753/dd9d14fd-e29b-4fe9-9361-88d7a30e4948)


![create2](https://github.com/OlavicDev/LampStack/assets/124717753/84f8c9cf-89c8-486c-a182-0e2a1d8fd73b)


__2.__ Created SSH key pair named __theghost__ to access the instance on port 22
![keypair](https://github.com/OlavicDev/LampStack/assets/124717753/401e9e47-daca-4fd3-a7a8-19b9bc116a3f)


__3.__ Added a new Inbound rule to allow traffic from HTTP port 80:

![group](https://github.com/OlavicDev/LampStack/assets/124717753/edc2e3e8-e0ce-475b-b510-31ccb3c5a154)


__4.__ The default VPC and Subnet was used for the networking configuration.

![security](https://github.com/OlavicDev/LampStack/assets/124717753/8d2741a9-668a-4cf6-b01a-fa2a92c1abd5)


__5.__ The private ssh key that got downloaded was located, permission was changed for the private key file and then used to connect to the instance by running
```
chmod 400 theghost.pem
```
```
ssh -i theghost.pem ubuntu@54.205.214.77 
```
![connectto](https://github.com/OlavicDev/LampStack/assets/124717753/9e8dcdbe-eacb-41d7-a0ee-04375deb9183)

## Step 2 - Install Apache and Update the Firewall

__1.__ __Update and upgrade the instance__
```
sudo apt update
sudo apt upgrade -y
```
![update1](https://github.com/OlavicDev/LampStack/assets/124717753/719ee76b-a28c-4d5e-83c5-2a51a9fa06f9)

__2.__ __Install apache2 package__
```
sudo apt install apache2 -y
```
![apain](https://github.com/OlavicDev/LampStack/assets/124717753/5cd89a07-6068-4261-a679-32b977871ae8)

__3.__ __Enable and verify that apache is running on as a service on the OS.__
```
sudo systemctl enable apache2
sudo systemctl status apache2
```
## This shows its correctly installed 
![apachestatus](https://github.com/OlavicDev/LampStack/assets/124717753/191e8b9e-c76e-4367-856f-2c1a078b7d85)


__4.__ __The server is running and can be accessed locally in the ubuntu shell by running the command below:__

```
curl http://localhost:80
OR
curl http://127.0.0.1:80
```
![curl](https://github.com/OlavicDev/LampStack/assets/124717753/283d0932-d194-4145-9d8f-3d805d7db8be)


__5.__ __Test with the public IP address if the Apache HTTP server is accessable over the internet (HTTP port :80)__
```
http://54.205.214.77:80
```
![apachepage](https://github.com/OlavicDev/LampStack/assets/124717753/99a37b44-492a-4c0c-9936-dfbf2304cfd9)

This shows that the web server is correctly installed and it is accessible throuhg the firewall.


## Step 2 - Install MySQL
mywsql is well-known relational database 
__1.__ __Install a relational database (RDB)__
```
sudo apt install mysql-server
```
![mysql install](https://github.com/OlavicDev/LampStack/assets/124717753/7f2d9528-5e82-4ffe-92f4-b941fd1cddfa)

__2.__ __Enable and verify that mysql is running with the commands below__
```
sudo systemctl enable --now mysql
sudo systemctl status mysql
```
![enable sql](https://github.com/OlavicDev/LampStack/assets/124717753/a1460166-4e68-492c-8480-e3c1d6d108bc)

__3.__ __Log in to mysql console__
```
sudo mysql
```
This connects to the MySQL server as the administrative database user __root__ infered by the use of __sudo__ when running the command.

__4.__ __Set a password for root user using mysql_native_password as default authentication method.__

Here, the user's password was defined as "Admin123$"
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Admin123$';
```
Exit the MySQL shell
```
exit
```

__5.__ __Run an Interactive script to secure MySQL__

The security script comes pre-installed with mysql. This script removes some insecure settings and lock down access to the database system.
```
sudo mysql_secure_installation
```
![](./images/secure-mysql.png)

Regardless of whether the VALIDATION PASSWORD PLUGIN is set up, the server will ask to select and confirm a password for MySQL root user.

__6.__ __After changing root user password, log in to MySQL console.__

A command prompt for password was noticed after running the command below.
```
sudo mysql -p
```
![](./images/access-mysql-with-password.png)
Exit MySQL shell
```
exit
```

## Step 3 - Install PHP

__1.__ __Install php__
Apache is installed to serve the content and MySQL is installed to store and manage data.
PHP is the component of the set up that processes code to display dynamic content to the end user.

The following were installed:
- php package
- php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases.
- libapache2-mod-php, to enable Apache to handle PHP files.
```
sudo apt install php libapache2-mod-php php-mysql
```
![Install PHP](./images/install-php.png)

Confirm the PHP version
```
php -v
```
![Confirm php version](./images/confirm-php-install.png)
At this ponit, the LAMP stack is completely installed and fully operational.

To tset the set up with a PHP script, it's best to set up a proper Apache Virtual Host to hold the website files and folders. Virtual host allows to have multiple websites located on a single machine and it won't be noticed by the website users.

## Step 4 - Create a virtual host for the website using Apache

__1.__ __The default directory serving the apache default page is /var/www/html. Create your document directory next to the default one.__

Created the directory for projectlamp using "mkdir" command
```
sudo mkdir /var/www/projectlamp
```

__Assign the directory ownership with $USER environment variable which references the current system user.__
```
sudo chown -R $USER:$USER /var/www/projectlamp
```
![Projectlamp Root Directory](./images/create-root-directory.png)

__2.__ __Create and open a new configuration file in apache’s “sites-available” directory using vim.__
```
sudo vim /etc/apache2/sites-available/projectlamp.conf
```

Past in the bare-bones configuration below:
```
<VirtualHost *:80>
  ServerName projectlamp
  ServerAlias www.projectlamp
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/projectlamp
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
![Virtual Host](./images/virtualhost.png)


__3.__ __Show the new file in sites-available__
```
sudo ls /etc/apache2/sites-available
```
```
Output:
000-default.conf default-ssl.conf projectlamp.conf
```
![Projectlamp config file](./images/ls-sites-available.png)

With the VirtualHost configuration, Apache will serve projectlamp using /var/www/projectlamp as its web root directory.

__4.__ __Enable the new virtual host__
```
sudo a2ensite projectlamp
```
![Enable virtual host](./images/enable-root-directory.png)

__5.__ __Disable apache’s default website.__

This is because Apache’s default configuration will overwrite the virtual host if not disabled. This is required if a custom domain is not being used.
```
sudo a2dissite 000-default
```
![Disable Apache default](./images/disable-root-dir.png)

__6.__ __Ensure the configuration does not contain syntax error__

The command below was used:
```
sudo apache2ctl configtest
```
![Check syntax error](./images/check-config-syntac.png)

__7.__ __Reload apache for changes to take effect.__
```
sudo systemctl reload apache2
```
![Reload Apache](./images/reload-apache.png)

__8.__ __The new website is now active but the web root /var/www/projectlamp is still empty. Create an index.html file in this location so to test the virtual host work as expected.__
```
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```
![Root dir content](./images/fill-root-dir.png)


__9.__ __Open the website on a browser using the public IP address.__
```
http://184.72.210.143:80
```
![URL public IP](./images/site-url-ip.png)

__10.__ Open the website with public dns name (port is optional)
```
http://<public-DNS-name>:80
```
![URL public DNS](./images/site-url-pub-dns.png)

This file can be left in place as a temporary landing page for the application until an index.php file is set up to replace it. Once this is done, the index.html file should be renamed or removed from the document root as it will take precedence over index.php file by default.

## Step 5 - Enable PHP on the website

With the default DirectoryIndex setting on Apache, index.html file will always take precedence over index.php file. This is useful for setting up maintenance page in PHP applications, by creating a temporary index.html file containing an informative message for visitors. The index.html then becomes the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root bringing back the regular application page.
If the behaviour needs to be changed, /etc/apache2/mods-enabled/dir.conf file should be edited and the order in which the index.php file is listed within the DirectoryIndex directive should be changed.

__1.__ __Open the dir.conf file with vim to change the behaviour__
```
sudo vim /etc/apache2/mods-enabled/dir.conf
```

```
<IfModule mod_dir.c>
  # Change this:
  # DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
  # To this:
  DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
![Change file list order](./images/index-php-config.png)


__2.__ __Reload Apache__

Apache is reloaded so the changes takes effect.
```
sudo systemctl reload apache2
```
![](./images/reload-apache-2.png)

__3.__ __Create a php test script to confirm that Apache is able to handle and process requests for PHP files.__

A new index.php file was created inside the custom web root folder.

```
vim /var/www/projectlamp/index.php
```

__Add the text below in the index.php file__
```
<?php
phpinfo();
```
![php text](./images/index-php.png)


__4.__ __Now refresh the page__

![PHP page](./images/php-page.png)

This page provides information about the server from the perspective of PHP. It is useful for debugging and to ensure the settings are being applied correctly.

After checking the relevant information about the server through this page, It’s best to remove the file created as it contains sensitive information about the PHP environment and the ubuntu server. It can always be recreated if the information is needed later.
```
sudo rm /var/www/projectlamp/index.php
```


__Conclusion:__

The LAMP stack provides a robust and flexible platform for developing and deploying web applications. By following the guidelines outlined in this documentation, It was possible to set up, configure, and maintain a LAMP environment effectively, enabling the creation of powerful and scalable web solutions.
