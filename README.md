# ubuntu-server-project
Project for Udacity Linux Server Course for FSND

Locate the SSH key you created for the grader user.
During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.


URL: http://35.177.47.196.xip.io/
Static IP address: 35.177.47.196
SSH port: 2200

*Summary of steps followed

NB note: After weeks of trying I am unable to get the login journey to work correctly. The issue seems to be caused by this line: login_session['state'] = state. I have therefore commented out this line in var/www/html/stuarts-astro-page/application.py to at least show that the site correctly connects with Google using the correct OAuth information
1. Set up a lightsail ubuntu serer with an IP address of 18.130.129.12
2. I SSH'd into my new server
3. I updated all currently installed packages using 'sudo apt-get update' and then 'sudo apt-get upgrade'
4. Changed the SSH port from 22 to 2200 by editing the sshd-config file at /etc/ssh/sshd_config. I changed the line 'Port 22' to 'Port 2200', exited and saved
5. Configured the Uncomplicated Firewall (UFW) by:
 - Blocking all incoming traffic: sudo ufw default deny incoming
 - Allow incoming traffic from SSH (port 2200), HTTP (port 80), and NTP (port 123): 
    sudo ufw allow 2200/tcp
    sudo ufw allow www
    sudo ufw allow 123/udp
 - Allow all outgoing traffic: sudo ufw default allow outgoing
 - Enabled the firewall sudo ufw enable
 - Followed AWS steps on creating a static IP and installing PuTTy so that I can access my virtual machine from my terminal
 - Rebooted my VM <-- Very important as I was not being permitted access using Putty until I rebooted
6. Create a new user account called Grader: sudo adduser grader
7. Give grader permission to sudo: sudo usermod -aG grader
 - Expired graders password using sudo passwd -e grader
8. Create an SSH keypair for the grader
 - on local machine: ssh-keygen <-- I chose a location to store it in and set a passphrase
 - log in as grader on VM: su - grader
   - mkdir .ssh
   - touch .ssh/authorized_keys
 - Back on local machine I read and copied the public key: type .ssh/linuxCourseProject.pub
 - On grader on the linux machine: 
   - I pasted the public key content into .ssh/authorized_keys
   - Set specific permissions on the authorised key file and .ssh directory: chmod 700 .ssh ; chmod 644 .ssh/authorized_keys
   - Removed the ability to remotely login as root: in /etc/ssh/sshd_config I changed PermitRootLogin from being 'prohibit-password' to 'no'. I then restarted the service: sudo service ssh restart
   - Gave grader the ability to sudo without having to enter a sudo password: 'sudo visudo' and added the following: grader ALL=(ALL) NOPASSWD:ALL
   - Removed the ability for anyone to login with a password by editing the sshd-config file at /etc/ssh/sshd_config. I set PasswordAuthentication to 'no' and then restarted the service: sudo service ssh restart
9. Configure the local timezone to UTC: sudo timedatectl set-timezone Etc/UTC. I checked it using the 'date' command
10. Install and configure Apache2
 - sudo apt-get install apache2
 - Tested that the web server is up and running by navigating my browser to http://35.177.47.196.xip.io/
 - sudo apt-get install libapache2-mod_wsgi
 - Edit 000-default.conf doc: Add 'WSGIScriptAlias / /var/www/html/stuarts-astro-page/astroapp.wsgi' before </VirtualHost>
 - restart apache: sudo apache2ctl restart
11. Installed and configured Postgresql:
 - sudo apt-get install postgresql
 - Confirmed that only local connections are allowed by looking in pg_hba.conf: sudo nano /etc/postgresql/9.5/main/pg_hba.conf
 - created a new databse user called 'catalog' with permissions to my catalog application database
   - login to PSQL: sudo -u postgres psql
   - set password for user ALTER USER postgres PASSWORD '[password]';
   - exited psql and logged back in: '\q' then 'psql -U postgres -h localhost' and my password
   - created the new user called catalog: CREATE USER catalog with PASSWORD '[password]';
   - logged out of psql '\q' and logged back in as catalog user ' psql catalogapp -U catalog -h localhost' and my password
12. Installed git: sudo apt-get install git
13. Cloned project into the home folder of grader: git clone https://github.com/Slystu/stuarts-astro-page.git
 - My project files are located at: var/www/html/stuarts-astro-page
 - To get my project to work I've had to install sqlalchemy: sudo apt-get install python-flask-sqlalchemy
 - install oath2: git clone https://github.com/google/oauth2client then cd oauth2client then python setup.py install
 - Install pip: sudo apt-get install python-pip
 - Install Requests: sudo apt-get install python-requests
 - Install httplib2: sudo apt-get install python-httplib2
 - Edited paths in my application.wsgi file so that import of database_setup will work
 - Altered the create engine url to support postgres (in database_setup and application.py files)
   - used to be: engine = create_engine('sqlite:///catalogapp.db')
   - now engine = create_engine('postgresql://catalog:password@localhost/catalogapp.db')
 - Install psycopg2: sudo apt-get install python-psycopg2
 - imported psycopg2 into my application.py file: import psycopg2
 - created astroapp and imported my app as application: from application import app as application
 - Updated Google oauth Client ID:
 - Added redirect server and new authorized javascript origin
 - Downloaded updated client_secrets.json file and replaced the previous version
14. Make sure that your .git directory is not publicly accessible via a browser: sudo chmod 700 .git

 
 
 

 


3rd party sources used:
For instructions on how to change the SSH port from 22 to 2200 I used https://www.liquidweb.com/kb/changing-the-ssh-port/
I read a guide on how to allow NTP incoming traffic through the UFW here https://www.digitalocean.com/community/tutorials/how-to-configure-ntp-for-use-in-the-ntp-pool-project-on-ubuntu-16-04
AWS Lightsail steps on downloading and using PuTTY to SSH into my server https://lightsail.aws.amazon.com/ls/docs/en/articles/lightsail-how-to-set-up-putty-to-connect-using-ssh
How to add a sudo user: https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart
To change my timezone to UTC I used https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt
For securing Postgresql I used this resource: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
git installation: https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04
Installing sqlalchemy: https://www.devmanuals.net/install/ubuntu/ubuntu-16-04-LTS-Xenial-Xerus/how-to-install-python-flask-sqlalchemy.html
Installing oauth2client https://oauth2client.readthedocs.io/en/latest/
Installing requests: https://www.youtube.com/watch?v=weoYbFaq1-s
installing httplib2: https://stackoverflow.com/questions/1882465/python-httplib2-module-not-found
Editing paths in python: https://stackoverflow.com/questions/4383571/importing-files-from-different-folder
Removing ability to login remotely as root: https://askubuntu.com/questions/27559/how-do-i-disable-remote-ssh-login-as-root-from-a-server
Sudo without a password: https://www.cyberciti.biz/faq/linux-unix-running-sudo-command-without-a-password/
