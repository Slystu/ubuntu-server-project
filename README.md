# ubuntu-server-project
Project for Udacity Linux Server Course for FSND
Submission
Please follow these steps to properly submit this project:

Create a new GitHub repository and add a file named README.md.

Your README.md file should include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.

Locate the SSH key you created for the grader user.

During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.

A README file is included in the GitHub repo containing the following information: IP address, URL, summary of software installed, summary of configurations made, and a list of third-party resources used to complete this project.

Summary of steps followed
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
 !!!NB!!! GO BACK TO AWS LIGHTSAIL NETWORKING TABA DN CHECK THAT THE FIREWALL SETTINGS ARE STILL CORRECTLY APPLIED (I.E. PORT 22 SHOULD BE DISABLED)
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
9. Configure the local timezone to UTC: sudo timedatectl set-timezone Etc/UTC. I checked it using the 'date' command
10. Install and configure Apache2
 - sudo apt-get install apache2
 - Tested that the web server up and running by navigating my browser to http://35.177.47.196.xip.io/
 - sudo apt-get install libapache2-mod_wsgi
 - Edit 000-defauly.conf doc: Add 'WSGIScriptAlias / /var/www/html/myapp.wsgi' before </VirtualHost>
 - restart apache: sudo apache2ctl restart
11. Installed adn configured Postgresql:
 - sudo apt-get install postgresql
 - Confirmed that only local connections are allowed by looking in pg_hba.conf: sudo nano /etc/postgresql/9.5/main/pg_hba.conf
 - created a new databse user called 'catalog' with permissions to my catalog application database
   - login to PSQL: sudo -u postgres psql
   - set password for user ALTER USER postgres PASSWORD '[password]';
   - exited psql and logged back in: '\q' then 'psql -U postgres -h localhost' and my password
   - created the new user called catalog: CREATE USER catalog with PASSWORD '[password]';
   - logged out of psql '\q' and logged back in as catalog user 'psql -U postgres -h localhost' and my password
   !!!NB!!! I haven't yet limited permissions so the catalog user access only to my catalog app database
12. Installed git: sudo apt-get install git
13. Cloned project into the home folder of grader: git clone https://github.com/Slystu/stuarts-astro-page.git
 - To get my project to work I've had to isntall sqlalchemy: sudo apt-get install python-flask-sqlalchemy
   


3rd party sources used:
For instructions on how to change the SSH port from 22 to 2200 I used https://www.liquidweb.com/kb/changing-the-ssh-port/
I read a guide on how to allow NTP incoming traffic through the UFW here https://www.digitalocean.com/community/tutorials/how-to-configure-ntp-for-use-in-the-ntp-pool-project-on-ubuntu-16-04
AWS Lightsail steps on downloading and using PuTTY to SSH into my server https://lightsail.aws.amazon.com/ls/docs/en/articles/lightsail-how-to-set-up-putty-to-connect-using-ssh
How to add a sudo user: https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart
To change my timezone to UTC I used https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt
For securing Postgresql I used this resource: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
git installation: https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04
Installing sqlalchemy: https://www.devmanuals.net/install/ubuntu/ubuntu-16-04-LTS-Xenial-Xerus/how-to-install-python-flask-sqlalchemy.html
