# LinuxServerCongif
Amazon Lightsail Server Set Up

1. Navaigate to Amazon Lightsail website https://lightsail.aws.amazon.com
2. Create a new AWS account and log in.
3. After sucsesfull login create new instance, select what instance you want to use. (Ubuntu 16.04 is what I used)
4. Scroll down to name, and create instance.
5. Click the box where your instance is located and scroll down past "connect using ssh" and click "Account page"
6. Navigate to SSH keys then click download.
7. Head back to the main instance page and click on your project box again.
8. Click Networking at the top of the page, and then scroll to find "add another"
9. Add port 123 and 2200.


Server Config

Logged in as root
1. Navigate through local folders to find where the .pem file is you downloaded earlier, and now move it into your .ssh folder on your local machine.
2. We need to make the public key usable, Go back to your terminal and input $ chmod 600 ~/.ssh/yourAWSkey.pem
3. Now log in using this key into our Aamazon Lightsail server: $ ssh -i ~/.ssh/yourAWSkey.pem ubuntu@publicip -p 2200
4. Amazon does not allow us to log in as root user but we can type $ sudo su - and become the root user.
5. Now as root user create user. $ sudo adduser grader
6. After going through and selecting a password and naming your new user hit Y and ENTER to confirm.
7. Now we will create a new file in the sudores.d directory. $ sudo nano /etc/sudoers.d/grader.
8. Fill that file with   grader ALL=(ALL:ALL) ALL, then save with CNTRL X then Y and then ENTER.
9. Next run update and upgrade pacakge commands, $ sudo apt-get update,  $ sudo apt-get upgrade.
10. Install finger $ sudo apt-get install finger (you can check the user we created by typing $ finger grader)
11. Open a new terminal and type $ ssh-keygen -f ~/.ssh/udacity_key.rsa
12. Create a passphrase for this key pair. (to opt out of creating a phrase just hit ENTER twice)
13. Once done in the same window where we created the key pair use cat to view the public key. $ cat ~/.ssh/udacity_key.rsa.pub. copy the information in the .pub file.
14. Head back to the original window where we are logged in as root, and move to the grader's folder. $ cd /home/grader.
15. Create a .ssh directory.
16. Create a file to store the piblic key. $ touch .ssh/authorized_keys
17. Now edit the file we created with $ sudo nano .ssh/authorized_keys and paste the information from udacity_key.rsa.pub.
18. change permissions. sudo chmod 700 /home/grader/.ssh and sudo chmod 644 /home/gader/.ssh
19. chang owner from root to grader $ sudo chown -R grader:grader /home/grader/.ssh
20. Restart the ssh service $ sudo service ssh restart
Logged in as grader:
1.Disconnect from server and re login as user grader. $ ssh -i ~/.ssh/udacity_key.rsa grader@35.171.129.160 -p 2200.
2.Once logged in as grader navigate to $ sudo nano /etc/.ssh/sshd_config . find the PermitRootLogin line and edit to no. Restart ssh $ ssh service ssh restart


Configure the FireWall
- $ sudo ufw allow 2200/tcp
- $ sudo ufw allow 80/tcp
- $ sudo ufw allow 123/udp
- $ sudo ufw enable

Deploy the Catalog application:
1. Install apache2 web server $ sudo apt-get install apache2 and then start the apache service $ sudo service apache2 start
2. Install other required packages $ sudo apt-get install libapache2-mod-wsgi python-dev
3. Install git $ sudo apt-get install git
4. Enable wsgi mod $ sudo a2enmod wsgi and start web server. $ sudo service apache2 start
5. Now $ cd /var/www then $ sudo mkdir catalog. change ownerships $ sudo chown -R grader:grader catalog now $ cd catalog
6. Git clone from your project in github $ git clone [your link] catalog
7. Create a .wsgi file $ sudo nano catalog.wsgi and add the following.
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(o, "/var/www/catalog")

from catalog import app as application
application.secret_key = "yoursecrectkey"

8. Rename your app.py to __init__.py
9. Install Virtual environment $ sudo apt-get install virtualenv
10 Activate Virtual environment and set up path to project. $ source venv/bin/activate $ sudo chmod -R 777 venv
11. With the virtual environment active now we install the required packages for our project.
- $ sudo apt-get install python-pip
- $ pip install flask
- $ pip install httplib2 oauth2client sqlalchemy psycopg2 sqlaclemy_utils requests
12. Now $ sudo nano __init__.py and change the client_secrets.json line.
open('/var/www/catalog/catalog/client_secrets.json', 'r').read
13. Now for the config of virtual host (add xip.io for google signin)
<VirtualHost *:80>
    
