Project 9 - Linux Server Configuration


SSH Links:
ssh -i ~/.ssh/udacity_key.rsa root@35.167.47.191
ssh -i ~/.ssh/udacity_key.rsa grader@35.167.47.191

ssh -i ~/.ssh/udacity_key.rsa grader@35.167.47.191 -p 2200

FQDN Link:
http://ec2-35-167-47-191.us-west-2.compute.amazonaws.com/

STEPS:
You will need to create an AWS Development Environment
https://www.udacity.com/account#!/development_environment
Follow the instructions on the above page and SSH into your linux server


Create a new user named grader:
sudo adduser grader
password: UDACITYgr@der001

Give the grader user the permission to sudo:
nano /etc/sudoers.d/grader
grader ALL=(ALL) NOPASSWD:ALL

Chnage to user to grader:
su grader

Make .ssh directory in home:
mkdir .ssh
chmod 700 .ssh

Create authorized_keys in /.ssh folder:
touch authorized_keys
chmod 600 authorized_keys

Retrieve public key from private key download from Udacity:
(http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#retrieving-the-public-key)
1. On local Mac, run ssh-keygen -y
2. Specify which file
3. It will output the public key
4. Copy the public key to the authorized_key file for user grader
5. Restart ssh - service ssh restart
6. Now you can ssh into the server as grader

Update all the current packages:
apt-get update

Change ssh port from 22 to 2200:
(https://help.ubuntu.com/community/SSH/OpenSSH/Configuring)
1. Make backup of original file
    a. cp /etc/ssh/sshd_config /etc/ssh/sshd_config.factory-defaults
    b. chmod a-w /etc/ssh/sshd_config.factory-defaults
2. Edit sshd_config
    a. Change port to 2200
3. Restart ssh
    a. service ssh restart
4. Now you have to ssh with -p 2200 option
    a. ssh -i ~/.ssh/udacity_key.rsa grader@35.163.236.163 -p 2200

Configure the UFW (firewall) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):
1. Check status of ufw
    a. ufw status
2. Deny all incoming ports
    a. ufw default deny incoming
3. Allow all outgoing ports
    a. ufw default allow outgoing
4. Allow ssh
    a. ufw allow ssh
5. Allow ssh on port 2200
    a. ufw allow 2200/tcp
6. Allow http
    a. ufw allow www
7. Allow ntp
    a. ufw allow ntp
8. Enable ufw
    a. ufw enable
    b. ufw status
9. Log out of ssh with grader and log back in to check that ufw is enabling log in

Conffigure the local timezone to UTC:
1. dpkg-reconfigure tzdata

Install and configure Apache to serve a Python mod_wsgi application:
1. apt-get install apache2
2. apt-get install libapache2-mod-wsgi
    a. Edit the .conf file for Apache
        1. sudo nano /etc/apache2/sites-enabled/000-default.conf
        2. Add the following line to the end of the <VirtualHost *:80>
            a. WSGIScriptAlias / /var/www/html/myapp.wsgi
        3. Restart Apache
            a. sudo apache2ctl restart
        4. Test .wsgi is working by going to the URL


Configuring Flask
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

Install sqlalchemy
1. Activate the virtaul environment from the above steps
2. sudo pip install sqlalchemy
3. sudo pip install python-psycopg2

Install httplib2
1. sudo pip install httplib2

Install oauth2client
1. sudo pip install --upgrade oauth2client

** The above 4 things the app needed in the project before, so you WILL need this again. **


Install and configure PostgreSQL:
(https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)
1. sudo apt-get update
2. sudo apt-get install postgresql postgresql-contrib

3. sudo su - postgres
4. psql
5. \conninfo
    a. You are connected to database "postgres" as user "postgres" via socket in "/var/run/postgresql" at port "5432"
6. Change password for the postgres user
    a. \password postgres
    b. Ud@c1ty986532
7. \q
8. Set PostgreSQL to not allow any remote connections
    a. sudo vim /etc/postgresql/9.3/main/pg_hba.conf

Install and configure phpPgAdmin:
1. sudo apt-get update
2. sudo apt-get install phppgadmin
3. Configure Apache for phpPgAdmin
    a. sudo nano /etc/apache2/conf.d/phppgadmin
        1. Comment out "deny from all"
        2. Comment out "allow from 127.0.0.0/255.0.0.0 ::1/128"
        3. Add "allow from all"
    b. sudo nano /etc/apache2/apache2.conf
        1. Add "Include /etc/apache2/conf.d/phppgadmin"
    c. Restart Apache
        1. sudo service apache2 restart
4. Configure phpPgAdming
(http://www.codevoila.com/post/7/installing-and-using-phppgadmin-on-ubuntu)
    a. sudo nano /etc/phppgadmin/config.inc.php
        1. $conf['servers'][0]['host'] = 'localhost';
        2. $conf['extra_login_security'] = false;
5. You can now run phpPgAdmin by going to the URL:
    a. http://<yoururl>/phppgadmin/


Install git, then clone and setup your Catalog App project:
1. sudo apt-get install git