---------------------------------------------------------
#Project
1. This project about Udacity linux configuration and python apache deploy
2. IP address: 35.185.86.83
3. Accessible SSH port: 2200
4. Application URL: http://35.185.86.83.xip.io
5. This project using google cloud platform
6. For garder user to login [ssh -i ~/.ssh/udacity_key.rsa grader@35.185.86.83 -p 2200]
7. grader keys [/home/half2t/.ssh]
8. Below is the steps used to accomplish the project
---------------------------------------------------------
# Update all installed packages
apt-get update
apt-get upgrade
---------------------------------------------------------
# Create user grader with sudo permission
1. sudo adduser grader
2. sudo usermod -aG sudo grader
---------------------------------------------------------
# Create SSH keys for user grader
1. sudo mkdir /home/grader/.ssh
2. sudo chown grader:grader /home/grader/.ssh
3. sudo chmod 700 /home/grader/.ssh
4. ssh-keygen -t rsa -f ~/.ssh/graderkey -C grader
5. touch authorized_keys
6. copy the graderkey.pub inside the authorized_keys
7. sudo chown grader:grader /home/grader/.ssh/authorized_keys
8. sudo chmod 644 /home/grader/.ssh/authorized_keys
----------------------------------------------------------
# Change SSH port from 22 to 220
sudo vi /etc/ssh/sshd_config [change the Port 22 to 2200]
sudo service ssh restart
----------------------------------------------------------
# Disable root login
1. sudo vi /etc/ssh/sshd_config [change PermitRootLogin without-password
to PermitRootLogin no and uncomment PasswordAuthentication no]
2. sudo service ssh restart
-----------------------------------------------------------
# Configure the local timezone to UTC
1. sudo dpkg-reconfigure tzdata
-----------------------------------------------------------
# Configure the Uncomplicated Firewall (UFW)
1. sudo ufw default deny incoming
2. sudo ufw default allow outgoing
3. sudo ufw allow 2200/tcp
4. sudo ufw allow www
5. sudo ufw allow ntp
6. sudo ufw show added
7. sudo ufw enable
8. sudo ufw status
-----------------------------------------------------------
# Install apache
1. sudo apt-get install apache2
2. sudo apt-get install libapache2-mod-wsgi python-dev
3. sudo apache2ctl restart
4. apache log /var/log/apache2/error.log
-----------------------------------------------------------
# Install psotgresql and create catalog user and database
1. sudo apt-get install postgresql
2. sudo nano /etc/postgresql/9.6/main/pg_hba.conf [check remote connection]
3. sudo su - postgres
4. psql
5. CREATE USER catalog WITH PASSWORD 'password';
6. ALTER USER catalog CREATEDB;
7. CREATE DATABASE catalog WITH OWNER catalog;
8. \c catalog
9. REVOKE ALL ON SCHEMA public FROM public;
10. GRANT ALL ON SCHEMA public TO catalog;
11. change your app create_engine('postgresql://catalog:password@localhost/catalog')
python /var/www/catalog/catalog/database_setup.py
-----------------------------------------------------------
# Clone catalog project from github
1. sudo apt-get install git
2. cd /var/www
3. sudo mkdir catalog
4. sudo chown -R grader:grader catalog
5. cd catalog
6. git clone https://github.com/half2t/catalog.git catalog
7. Create a catalog.wsgi file
8. rename your python app file to [__init__.py]
-----------------------------------------------------------
#Install virtual environment
1. sudo apt install python-pip
2. sudo pip install virtualenv
3. sudo virtualenv venv
4. source venv/bin/activate
5. sudo chmod -R 777 venv
6. pip install Flask-SQLAlchemy
7. pip install psycopg2
-----------------------------------------------------------
# Install falsk and related libariries
1. pip install Flask
2. sudo pip install httplib2 oauth2client sqlalchemy psycopg2 psycopg2-binary
3. sudo nano /etc/apache2/sites-available/catalog.conf
4. sudo a2ensite catalog
5. sudo service apache2 restart
-----------------------------------------------------------
# References
1. https://askubuntu.com
2. http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/
3. https://cloud.google.com/compute/docs/instances/connecting-advanced#thirdpartytools
4. https://github.com/rrjoson/udacity-linux-server-configuration
5. https://github.com/SteveWooding/fullstack-nanodegree-linux-server-config/blob/master/README.md
------------------------------------------------------------
