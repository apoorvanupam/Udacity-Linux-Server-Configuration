# Udacity Linux Server Configuration
A Project to deploy a flask app on Linux Server. I have used Ubuntu and DigitalOcean droplet.

## IP Address
#### Server IP Address : 139.59.28.100
#### Application URL : 139.59.28.100.xip.io
#### SSH Server Access Port : 2200
#### SSH login username : grader

# Steps to configure server
### 1. Create RSA Key Pair
You have to create the RSA Key Pair on your local machine. This key pair will be used to authenticate yourself while securely logging in to the server via SSH. The private key will be kept with you in your local machine, and the public key will be stored in the server.

To generate the RSA Key Pair, run this command
```console
$ ssh-keygen
```
It will ask for a passphrase, you may either leave it empty or enter some passphrase. A passphrase adds an additional layer of security which prevents unauthorized users from logging in.

The whole process would look like this:

```
thecoder@thelappy:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/thecoder/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/thecoder/.ssh/id_rsa.
Your public key has been saved in /home/thecoder/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:re6fDTCdCdnQ6Y0YnbC04E8bCZTY2QEf+cA0WqHXNXE thecoder@thelappy
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|            .    |
|        . oB .   |
|         +Eo*    |
|    .   So.= o   |
|     o. ..B . .  |
|      B+.*oB.  . |
|     *.=*.Oo.o.  |
|    . o+.++=+o.  |
+----[SHA256]-----+
```
Now, you have a public and private key which you can use to authenticate. The public key is called id_rsa.pub and the respective private key is called id_rsa. The key pair is stored inside the ~/.ssh/ directory.

### 2. Set DigitalOcean Droplet

1. Log in on [DigtalOcean](https://cloud.digitalocean.com/login). If you don't have an account, then create one.
2. Navigate to Dashboard, click on **Create Droplet**
3. Choose Ubuntu 18.04 x64 image from the list of given images.
4. Choose the size, I have chosen the **1GB/1 vCPU/25GB** configuration for this project.
5. In the section **Add Your SSH Keys**, paste the content of your public key, 'id_rsa.pub':
   This step will automatically create the file ~/.ssh/authorized_keys with appropriate permissions and add your public key      to it. It would also add the following rule in the /etc/ssh/sshd_config file automatically:
```
PasswordAuthentication no
```
This rule essentially disables password authentication on the `root` user, and rather enforces SSH logins only.
6. Click **Create** to create the droplet. This action will take some time for completion.
After the droplet has been created successfully, a public IP address will be assigned.
The public IPv4 address assigned to me for this project is is 139.59.28.100.
 
### 3. Logging In as `root` via SSH and Updating the System

As the droplet has been successfully created, you can now log into the server as `root` user by running the following command in your host machine:
```
  $ ssh root@139.59.28.100
```
(Note: Your Address can be different. '139.59.28.100' is allocated to me, so it's showing 'root@139.59.28.100'
This will look for the private key in your local machine and log you in automatically if the corresponding public key is found on your server. After you are logged in, you might see something similar to this:
```
$ ssh root@139.59.28.100
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-29-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Nov 22 19:08:35 UTC 2018

  System load:  0.0               Processes:           99
  Usage of /:   8.0% of 24.06GB   Users logged in:     0
  Memory usage: 25%               IP address for eth0: 139.59.28.100
  Swap usage:   0%

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

Last login: Thu Nov 22 18:52:23 2018 from 116.75.105.166
```
Now, update the virtual server
Run the following command
```
 # apt update && apt upgrade
```
The above command will update all the packages.
If the available update is a kernel update, you might need to reboot the server by running the following command:
```
# reboot
```

### 4. Change the SSH Port from 22 to 2200

1. Open `/etc/ssh/sshd_config` file with `nano`
```
# nano /etc/ssh/sshd_config
```
2. Find the line `#Port 22` in the file and change it to `Port 2200`, and save the file.
3. Restart the SSH server to reflect these changes:
```
# service ssh restart
```
4. To confirm whether the changes have come into effect or not, run:
```
# exit
```
This will take you back to your host machine. After you are back to your local machine, run:
```
$ ssh root@139.59.28.100 -p 2200
```   
Now, try to log in to the server as `root` on port 2200. The `-p` option explicitly tells at what port the SSH server operates on. It now no more operates on port number 22. 

### 5. Configure Timezone to Use UTC

To configure the timezone to use UTC, run the following command:

```
# dpkg-reconfigure tzdata
```

It then shows you a list. Choose ``None of the Above`` and press enter. In the next step, choose ``UTC`` and press enter.
The timezone will use UTC after this.

### 6. Setting Up the Firewall

Now we would configure the firewall to allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123) in grader.
Run the following commands to configure the firrewall.
```
$ ssh grader@139.59.28.100 -p 2200
# sudo ufw allow 2200/tcp
# sudo ufw allow 80/tcp
# sudo ufw allow 123/udp
```
Run the following command to enable the above firewall:
```
# sudo ufw enable
```
Run the following command to confirm whether the above firewall has been successfully enabled or not:
```
# sudo ufw status
```
You should see something like this:
```
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)
```

### 7. Create the User `grader` and add it to the `sudo` Group

As per Udacity requirements, we need to create a a user called   `grader`
While being logged into the virtual server, run the following command and proceed:
```
# adduser grader
```
The output would look like this:
(Note : The command will ask for other informations like Full Name, Room Name etc. You can enter values or you can leave it blank.)
```
  Adding user `grader' ...
  Adding new group `grader' (1000) ...
  Adding new user `grader' (1000) with group `grader' ...
  Creating home directory `/home/grader' ...
  Copying files from `/etc/skel' ...
  Enter new UNIX password:
  Retype new UNIX password:
  passwd: password updated successfully
  Changing the user information for grader
  Enter the new value, or press ENTER for the default
	  Full Name []:
	  Room Number []:
	  Work Phone []:
	  Home Phone []:
	  Other []:
  Is the information correct? [Y/n]
```
(Note: For the above command, I have entered the password of user `grader` as `grader`.)
Run the following command to add the user `grader` to the `sudo` group to grant it administrative access:
```
 # usermod -aG sudo grader
```
Run the following command also to eliminate errors.
```
$ sudo nano /etc/sudoers.d/grader
```
then add 
```grader ALL=(ALL:ALL) ALL```
to the file then save (press Ctrl-X on your keyboard, then type 'Y' to save, then return to save the filename).
You will be returned to command promt.

#### 7.1. Adding SSH Access to the user `grader`

To allow SSH access to the user `grader`, first log into the account of the user `grader` from your virtual server:
```
# su - grader
```
Now enter the following commands to allow SSH access to the user `grader`:
```
$ mkdir .ssh
$ chmod 700 .ssh
$ touch .ssh/authorized_keys
$ nano .ssh/authorized_keys
```
After running above commands, go back to your local machine and copy the content of the public key file `~/.ssh/udacity.pub`. Paste the public key to the server's `authorized_keys` file using `nano` or any other text editor, and save (press Ctrl-X on your keyboard, then type 'Y' to save, then return to save the filename).

Run the following command to restrict the access for other users.
```
$ chmod 644 authorized_keys
```
After that, run `exit`. You will now return to your local machine. To confirm that it worked, run the following command in your local machine:
```
$ ssh grader@139.59.28.100 -p 2200
```
You should now be able to log in as `grader` and would get a prompt to enter commands.
Next, run `exit` to go back to the host machine and proceed to the following step to disable `root` login.

### 8. Disabling Root Login

1. Run the following command on your local machine to log in as `root` in the server:
```
$ ssh root@139.59.28.100 -p 2200
```
2. After you are logged in, open the file `/etc/ssh/sshd_config` with `nano`:
```
# nano /etc/ssh/sshd_config
```
3. Find the line `PermitRootLogin yes` and change it to `PermitRootLogin no`.
4. Restart the SSH server:
```
# service ssh restart
```
5. Terminate the connection:
```
# exit
```
6. After you are back to the host machine, when you try to log in as `root`, you should get an error:
```
thecoder@thelappy:~$ ssh root@139.59.28.100 -p 2200
Permission denied (publickey).
```

### 9. Install apache2,mod_wsgi and git

Log in as the `grader` user via SSH:
Run the following command:
```
$ sudo apt-get update
$ sudo apt-get install apache2
$ sudo apt-get install libapache2-mod-wsgi python-dev
$ sudo a2enmod wsgi
$ sudo service apache2 start
$ sudo apt-get install git
```
Enter the URL `http://139.59.28.100` in your Web browser to confirm whether apache2 has been successfully installed
After entering `http://139.59.28.100` you will see `Apache2 Ubuntu Default Page`

### 10. Configure Apache to serve a Python mod_wsgi application

Clone the Udacity-item-catalog app from Github
```
$ cd /var/www
$ sudo mkdir catalog
$ sudo chown -R grader:grader catalog
$ cd catalog
$ git clone https://github.com/apoorvanupam/Udacity-item-catalog.git catalog
```
To make .git directory is not publicly accessible via a browser, create a .htaccess file in the .git folder and put the following in this file:
```
RedirectMatch 404 /\.git
```
### 11. Install pip , setup virtual environment and also install Flask and its dependencies:

Install pip , virtualenv (in /var/www/catalog)
```
$ sudo apt-get install python-pip
$ sudo pip install virtualenv
$ sudo virtualenv venv
$ source venv/bin/activate
$ sudo chmod -R 777 venv
```
(Note: Don't deactivate the virtualenv after the above commands. The following commands should be run in venv after activating it.)
Install Flask and other dependencies:
```
$ sudo pip install -r catalog/requirements.txt
```
Install Python's PostgreSQL adapter psycopg2:
```
$ sudo apt-get install python-psycopg2
```
Configure and Enable a New Virtual Host
```
$ sudo nano /etc/apache2/sites-available/catalog.conf
```
Add the following content:
```
<VirtualHost *:80>
   ServerName 139.59.28.100
   ServerAlias 139.59.28.100.xip.io
   ServerAdmin grader@139.59.28.100
   WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
   WSGIProcessGroup catalog
   WSGIScriptAlias / /var/www/catalog/catalog.wsgi
   <Directory /var/www/catalog/catalog/>
       Order allow,deny
       Allow from all
   </Directory>
   Alias /static /var/www/catalog/catalog/static
   <Directory /var/www/catalog/catalog/static/>
       Order allow,deny
       Allow from all
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/error.log
   LogLevel warn
   CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enable the new virtual host:
```
$ sudo a2ensite catalog
```
Create and configure the .wsgi File
```
$ cd /var/www/catalog/
$ sudo nano catalog.wsgi
```
Add the following content:
```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'secret'
```
Modify the `app.py` file to `__init__.py` by running the following command:
```
$ mv project.py __init__.py
```
Update the absolute path of client_secrets.json in __init__.py to its complete path:
```
/var/www/catalog/catalog/client_secrets.json
```

### 12. Installing and Configuring PostgreSQL

Install some necessary Python packages for working with PostgreSQL:
```
$ sudo apt-get install libpq-dev python-dev.
```
Install PostgreSQL:
```
$ sudo apt-get install postgresql postgresql-contrib
```
PostgreSQL automatically creates a new user 'postgres' during its installation. Connect to the database by using postgres username with:
```
$ sudo -u postgres psql
```
1. Create a new user called 'catalog' with his password:
```
# CREATE USER catalog WITH PASSWORD 'catalog';
```
2. Give catalog user the CREATEDB permission:
```
# ALTER USER catalog CREATEDB;
```
3. Create the 'catalog' database owned by catalog user:
```
# CREATE DATABASE catalog WITH OWNER catalog;
```
4. Connect to the database:
```
# \c catalog
```
5. Revoke all the rights:
```
# REVOKE ALL ON SCHEMA public FROM public;
```
6. Lock down the permissions to only let catalog role create tables:
```
# GRANT ALL ON SCHEMA public TO catalog;
```
7. Log out from PostgreSQL:
```
# \q
```
Then return to the grader user:
```
$ exit.
```
8. Use nano again to edit python files to change the database engine from 
```
engine = create_engine('sqlite:///datamenu.db')
```
to
```
create_engine('postgresql://catalog:catalog@localhost/catalog')
```
9. Remote connections to PostgreSQL should already be blocked. Double check by opening the config file:
```
$ sudo nano /etc/postgresql/10/main/pg_hba.conf
```
It shoudld look some what like this:
```
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
```

### 13. Restart Apache server:

Restart the Apache server by using the following command:
```
$ sudo service apache2 restart
```
Now enter <http://139.59.28.100.xip.io/> in your browser to run the application.

## References

[1] <https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04>
[2] <http://terokarvinen.com/2016/deploy-flask-python3-on-apache2-ubuntu>
[3] <https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps>
