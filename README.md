# Linux Server Configuration

## Server informations
IP: 52.41.20.50  
SSH-Port: 2200  
Grader Password: Loots44dueling41Bedrock  
Catalog-Application-Url: [http://52.41.107.135/](http://52.41.107.135/)

## Installed Software
- Apache 2
- libapache2-mod-wsgi
- postgresql
- git
- python-pip
- python-dev
- libpq-dev
- flask
- flask-sqlalchemy
- flask-login
- flask-script
- flask-wtf
- flask-bootstrap
- github-flask
- psycopg2

## Configurations

### Add  `grader` user
##### On The Local Machine
```
ssh-keygen -t rsa -b 4096 -c "Udacity Grader"
clip < ~/.ssh/udacity_grader.pub
```
##### On The Server
```
adduser grader
echo "grader ALL=(ALL) ALL" > /etc/sudoers.d/grader
mkdir /home/grader/.ssh
nano /home/grader/.ssh/authorized_keys
# Paste public key form clipboard and save
chown grader:grader -R /home/grader/.ssh
chmod 700 /home/grader/.ssh
chmod 600 /home/grader./ssh/authorized_keys
```

### Set local timezone to UTC
```
dpkg-reconfigure tzdata
# Select "None of the above" > "UTC"
```

### Update installed packages
```
apt-get update && apt-get upgrade -y
```

### Configure OpenSSH
```
nano /etc/ssh/sshd_config
# Change Port 22 to 2200
# Set PermitRootLogin to no
```

### Configure Firewall
```
ufw default deny incoming
ufw default allow outgoing
ufw allow www
ufw allow ntp
ufw allow 2200/tcp
ufw enable
```

### Install Apache and mod_wsgi
```
apt-get install apache2 libapache2-mod-wsgi -y
```

### Install PostgreSQL
```
apt-get install postgresql libpq-dev -y
sudo -i -u postgres
createuser --interactive 
createdb catalog
psql
ALTER USER "catalog" WITH PASSWORD 'the password';
\q
exit
```

### Install Git
```
apt-get install git -y
```

### Setup Catalog-Application
```
apt-get install python-pip
```

#### Clone Application
```
git clone https://github.com/herrkrebs/udacity-fsnd-project-3-movie-catalog.git /var/www/Catalog/Catalog
cd /var/www/Catalog/Catalog
git checkout porject-5-version-with-postgresql
```

#### Create WSGI-File
```
nano /var/www/Catalog/catalog.wsgi
```
##### Enter the following lines and save
```Python
import sys
sys.path.inser(0, '/var/www/Catalog')
from Catalog import app as application
```

#### Install Python Requirements
```
pip install -r /var/www/Catalog/Catalog/requirements.txt
```

#### Modify Catalog-Application
```
nano /var/www/Catalog/Catalog/app/__init__.py
# Set the PostgreSQL database uri
# Set the GitHub client id
# Set the GitHub client secret
```

#### Initialize Database
```
cd /var/ww/Catalog/Catalog
python
```
#### Run the following code in Python
```Python
from app import create_app
from app import db
from app.database_initialization import initialize_database

app = create_app()

with app.app_context():
    db.create_all()
    initialize_database()
```

#### Configure Apache
```
nano /etc/apache2/sites-enabled/000-default.conf
# Add the following line inside the VirtualHost block
# WSGIScriptAlias / /var/www/Catalog/catalog.wsgi
service apache2 restart
```

## Third Party Resources
[DigitalOcean - How To Install and Use PostgreSQL on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04)  
[DigitalOcean - How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
