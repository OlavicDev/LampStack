### WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

### Intro:

__LAMP stands for Linux, Apache, MySQL, and PHP (or Perl, Python). It's a software stack used for building dynamic websites and web applications. Linux serves as the operating system, Apache as the web server, MySQL as the database system, and PHP (or Perl, Python) for server-side scripting.
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
![sql login](https://github.com/OlavicDev/LampStack/assets/124717753/1089e827-8039-4f82-aecf-4125065396b4)


__4.__ __Set a password for root user using mysql_native_password as default authentication method.__
use a password you can remember 
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '*******';
```
Exit the MySQL shell
```
exit
```

__5.__ __Run an Interactive script to secure MySQL__

```
sudo mysql_secure_installation
```
The security script comes pre-installed with mysql. This script removes some insecure settings and lock down access to the database system.
![omo1](https://github.com/OlavicDev/LampStack/assets/124717753/6b06c56d-4f72-420e-8b52-1f725ae22975)
![omo2](https://github.com/OlavicDev/LampStack/assets/124717753/6712d57d-ded9-4f88-b5e4-edfa59a7b294)


__6.__ __After changing root user password, log in to MySQL console.__

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
let begin by installing PHP packages 
```
sudo apt install php libapache2-mod-php php-mysql
```
__All ready innstalled__ 
![php install](https://github.com/OlavicDev/LampStack/assets/124717753/98b23c05-a41c-44ab-81f3-d5c08cef7769)


Confirm the PHP version
```
php -v
```
At this ponit, the LAMP stack is completely installed and fully operational.


## Step 4 - Create a virtual host for the website using Apache

__1.__ __The default directory serving the apache default page is /var/www/html. Create your document directory next to the default one.__

Created the directory for projectlamp using "mkdir" command
```
sudo mkdir /var/www/projectlamp
```

__Change the ownnership of the directory.__
```
sudo chown -R $USER:$USER /var/www/projectlamp
```
![change ownership](https://github.com/OlavicDev/LampStack/assets/124717753/a034e2f0-0c59-4e63-b9f2-8ac5bd5c3f24)

__2.__ __Create and open a new configuration file in apache’s “sites-available” directory using vim.__
```
sudo vim /etc/apache2/sites-available/projectlamp.conf
```

Write the configuration below:
by clicking I (for insert)
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
![write in vim1](https://github.com/OlavicDev/LampStack/assets/124717753/8ad380e5-3c15-41fa-8423-f06da7bacee2)

__Save and Exit with "wq!"__


__3.__ __Show the new file in sites-available__
```
sudo ls /etc/apache2/sites-available
```
![0000](https://github.com/OlavicDev/LampStack/assets/124717753/5881756e-325c-4bc9-a8c5-a8b54e49a4f8)


With the VirtualHost configuration, Apache will serve projectlamp using /var/www/projectlamp as its web root directory.

__4.__ __Enable the new virtual host__
```
sudo a2ensite projectlamp
```
![assenite](https://github.com/OlavicDev/LampStack/assets/124717753/09cff30e-391a-4361-94bd-0105369d25c5)

__5.__ __Disable apache’s default website.__

To ensure that Apache’s default configuration will overwrite the virtual host. This is required if a custom domain is not being used.
```
sudo a2dissite 000-default
```

__6.__ __Ensure the configuration does not contain syntax error__

```
sudo apache2ctl configtest
```
Output
```
Syntax Ok
```
__7.__ __Reload apache for changes to take effect.__
```
sudo systemctl reload apache2
```
![reload](https://github.com/OlavicDev/LampStack/assets/124717753/2151b817-7e5c-4eb0-adee-0155411739a4)


__8.__ __The new website is now active but the web root /var/www/projectlamp is still empty. Create an index.html file in this location so to test the virtual host work as expected.__
```
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```

__9.__ __Open the website on a browser using the public IP address.__
```
http://http://54.205.214.77/:80
```
![unauto](https://github.com/OlavicDev/LampStack/assets/124717753/94eca6db-d05b-430d-9776-7fdcd5dff740)


This file can be left in place as a temporary landing page for the application until an index.php file is set up to replace it. Once this is done, the index.html file should be renamed or removed from the document root as it will take precedence over index.php file by default.

## Step 5 - Enable PHP on the website

When Apache's default DirectoryIndex setting is in place, it prioritizes serving an index.html file over an index.php file. This feature proves valuable when configuring maintenance pages for PHP applications. By crafting a temporary index.html file containing pertinent information for visitors, it becomes the landing page during maintenance periods. Once maintenance concludes, simply rename or remove the index.html file from the document root to restore the regular application page.

To modify this behavior, edit the /etc/apache2/mods-enabled/dir.conf file. Adjust the order in which the index.php file appears within the DirectoryIndex directive to reflect the desired precedence.

__1.__ __Open the dir.conf file with vim to change the behaviour__
```
sudo vim /etc/apache2/mods-enabled/dir.conf
```
Input
```
<IfModule mod_dir.c>
  # Change this:
  # DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
  # To this:
  DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

__2.__ __Reload Apache__

Apache is reloaded so the changes takes effect.
```
sudo systemctl reload apache2
```
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
![phpp](https://github.com/OlavicDev/LampStack/assets/124717753/490ea228-0ccb-4529-879d-10b96c5c9099)

__4.__ __Refresh the Page__

This page provides information about the server from the PHP perspective. It serves as a valuable tool for debugging and verifying that settings are applied correctly.

Once you've reviewed the pertinent server information on this page, it's advisable to delete the created file, as it contains sensitive details about the PHP environment and the Ubuntu server.
```
sudo rm /var/www/projectlamp/index.php
```
This file can always be recreated if the information is needed at a later time.









__Conclusion:__

The LAMP stack provides a robust and flexible platform for developing and deploying web applications. By following the guidelines outlined in this documentation, It was possible to set up, configure, and maintain a LAMP environment effectively, enabling the creation of powerful and scalable web solutions.
