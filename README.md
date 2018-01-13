# Udacity Full Stack Web Developer Nanodegree - Linux Server Configuration Project

This is a 'for dummies' guide to those (including myself) are struggling with Udacity FSND's Linux Server Configuration project. Udacity provides a very poor instruction and leaves its student hopeless. Fortunately, some Udacity alumni provided some good step-by-step guide on how to set up their Amazon Lightsail website. However, their guides are partly outdated or incomplete. Here I am trying to provide a guide on how I completed this project.

## TL:DR

Host Name: ec2-13-58-109-116.us-east-2.compute.amazonaws.com

IP Address: 13.58.109.116

## Amazon Lightsail Server Set Up

1. Somehow you have to use the link provided by Udacity to Amazon Lightsail: https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Flightsail.aws.amazon.com%2Fls%2Fwebapp%3Fstate%3DhashArgs%2523%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fparksidewebapp&forceMobileApp=0

2. Create a new AWS account and then go back to use the link above to log in

3. After you log in, click 'Create Instance'
![create instance](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic1.png)

4. Select Platform and  blueprint
![choose platform and blueprint](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic2.png)

5. Scroll down to name your instance and click 'Create'
![final step](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic3.png)

6. The instance needs 5~10 mins to set up. After it is set up, you will see 'running' in the left corner of the status card. Write down the public IP address on a paper as you will use it a lot in the following steps
![status card](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic4.png)

7. Click the status card and you will get into this page. Click the `Account page` at the bottom
![account page](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic5.png)

8. Click the 'Download' to download your private key, it should go to your Download folder by default. It is a .pem file, not the rsa file in other step-by-step guides, but we can still use it to log into our server
![private key](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic6.png)

9. Click the 'Networking' tab and find the 'Add another' at the bottom. Add port 123 and 2200. Amazon Lightsail allows only port 22 and 80 by default, no matter how you set it up in ubuntu's ufw
![AWS Firewall](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic8.png)

## Server Configuration

1. We need to show the hidden files in Finder. At first, open your Mac OSX terminal and input `$ killall Finder`

2. In the terminal, input `$ defaults write com.apple.finder AppleShowAllFiles TRUE`. Now you can see the hidden .ssh folder in Finder now

3. Move your downloaded `.pem` public key file into .ssh folder that is at the root of Finder
![move private key](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic7.png)

4. We need to make our public key usable and secure. Going back to your terminal, input  `$ chmod 600 ~/.ssh/YourAWSKey.pem`

5. We now use this key to log into our Amazon Lightsail Server: `$ ssh -i ~/.ssh/YourAWSKey.pem ubuntu@13.58.109.116`

6. Amazon Lightsail does not allow you to log in as a root user, but we can switch to become a root user. Type `$ sudo su -` and boom, we become a root user! Then type  `$ sudo adduser grader` to create another user 'grader'
![root user](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic9.png)

7. Now create a new file under the sudoers directory: `$ sudo nano /etc/sudoers.d/grader`. Fill that file with `grader ALL=(ALL:ALL) ALL`, then save it (control X, then type `yes`, then hit the return key on your keyboard)

8. In order to prevent the `$ sudo: unable to resolve host` error, edit the hosts by `$ sudo nano /etc/hosts`, and then add `127.0.1.1 ip-10-20-37-65` under `127.0.1.1:localhost`

9. Run the following commands to update all packages and install finger package:
- `$ sudo apt-get update`
- `$ sudo apt-get upgrade`
- `$ sudo apt-get install finger`

10. Open a new Terminal window (Command+N) and input `$ ssh-keygen -f ~/.ssh/udacity_key.rsa`

11. Stay on the same Terminal window, input `$ cat ~/.ssh/udacity_key.rsa.pub` to read the public key. Copy the public key.

12. Going back to the first terminal window where you are logged into Amazon Lightsail as the root user, move to grader's folder by `$ cd /home/grader`

13. Create a .ssh directory: `$ mkdir .ssh`

14. Create a file to store the public key: `$ touch .ssh/authorized_keys`

15. Edit the authorized_keys file `$ nano .ssh/authorized_keys`

16. Change the permission: `$ sudo chmod 700 /home/grader/.ssh` and `$ sudo chmod 644 /home/grader/.ssh/authorized_keys`

17. Change the owner from root to grader: `$ sudo chown -R grader:grader /home/grader/.ssh`

18. Restart the ssh service: `$ sudo service ssh restart`

19. Type `$ ~.` to disconnect from Amazon Lightsail server

20. Log into the server as grader: `$ ssh -i ~/.ssh/udacity_key.rsa grader@13.58.109.116`

21. We now need to enforce the key-based authentication: `$ sudo nano /etc/ssh/sshd_config`. Find the *PasswordAuthentication* line and change text after to `no`. After this, restart ssh again: `$ sudo service ssh restart`

22. We now need to change the ssh port from 22 to 2200, as required by Udacity: `$ sudo nano /etc/ssh/ssdh_config` Find the *Port* line and change `22` to `2200`. Restart ssh: `$ sudo service ssh restart`

23. Disconnect the server by `$ ~.` and then log back through port 2200: `$ ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@13.58.109.116`

24. Disable ssh login for *root* user, as required by Udacity: `$ sudo nano /etc/ssh/sshd_config`. Find the *PermitRootLogin* line and edit to `no`. Restart ssh `$ sudo service ssh restart`

25. Now we need to configure UFW to fulfill the requirement:
- `$ sudo ufw allow 2200/tcp`
- `$ sudo ufw allow 80/tcp`
- `$ sudo ufw allow 123/udp`
- `$ sudo ufw enable`

------

**Congratulation! You have gone that far! The following deployment part is a headache and is never easy to do. Take a break and come back when you are ready.**

------

## Deploy Catalog Application

We will use a virtual machine, apache2, and postgre to host our application. Before we any thing, ssh to the Amazon Terminal through your Terminal with grader.

1. Install required packages
- `$ sudo apt-get install apache2`
- `$ sudo apt-get install libapache2-mod-wsgi python-dev`
- `$ sudo apt-get install git`

2. Enable mod_wsgi by `$ sudo a2enmod wsgi` and start the web server by `$ sudo service apache2 start` or `$ sudo service apache2 restart`. You should input the public IP address and you should see a page like below. If you do not see the page, you have to check the error message and google a solution:
![it works!](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic10.png)

3. Set up the folder structure (although clumsy, but works)
- `$ cd /var/www`
- `$ sudo mkdir catalog`
- `$ sudo chown -R grader:grader catalog`
- `$ cd catalog`

4. Now we clone the project from Github: `$ git clone [your link]` Copy your link from here is the easiet way:
![it works!](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic11.png)

5. Create a .wsgi file: `$sudo nano catalog.wsgi` and add the following into this file
`import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'supersecretkey'`

6. Rename the application.py to __init__.py
