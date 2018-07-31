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
  - touch .ssh/authorized keys
 - Back on local machine: 



3rd party sources used:
For instructions on how to change the SSH port from 22 to 2200 I used https://www.liquidweb.com/kb/changing-the-ssh-port/
I read a guide on how to allow NTP incoming traffic through the UFW here https://www.digitalocean.com/community/tutorials/how-to-configure-ntp-for-use-in-the-ntp-pool-project-on-ubuntu-16-04
AWS Lightsail steps on downloading and using PuTTY to SSH into my server https://lightsail.aws.amazon.com/ls/docs/en/articles/lightsail-how-to-set-up-putty-to-connect-using-ssh
How to add a sudo user: https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart
