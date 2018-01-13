# Udacity Full Stack Web Developer Nanodegree - Linux Server Configuration Project

This is a 'for dummies' guide to those (including myself) are struggling with Udacity FSND's Linux Server Configuration project. Udacity provides a very poor instruction and leaves its student hopeless. Fortunately, some Udacity alumni provided some good step-by-step guide on how to set up their Amazon Lightsail website. However, their guides are partly outdated or incomplete. Here I am trying to provide a guide on how I completed this project.

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

1. We need to show the hidden files in Finder. At first, open your Mac OSX terminal and input `killall Finder`

2. In the terminal, input `defaults write com.apple.finder AppleShowAllFiles TRUE`. Now you can see the hidden .ssh folder in Finder now

3. Move your downloaded `.pem` public key file into .ssh folder that is at the root of Finder
![move private key](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic7.png)

4. We need to make our public key usable and secure. Going back to your terminal, input  `chmod 600 ~/.ssh/YourAWSKey.pem`

5. We now use this key to log into our Amazon Lightsail Server: `ssh -i ~/.ssh/YourAWSKey.pem ubuntu@13.58.109.116`

6. Amazon Lightsail does not allow you to log in as a root user, but we can switch to become a root user! type `sudo su -` and boom, we become a root user! Then type  `sudo adduser grader` to create another user 'grader'
![root user](https://github.com/callforsky/udacity-linux-configuration/blob/master/pic/pic9.png)

7. Now create a new file under the sudoers directory: `$ sudo nano /etc/sudoers.d/grader`. Fill that file with `grader ALL=(ALL:ALL) ALL`, then save it (control X, then type `yes`, then hit the return key on your keyboard)

8. In order to prevent the `sudo: unable to resolve host` error, edit the hosts by `$ sudo nano /etc/hosts`, and then add `127.0.1.1 ip-10-20-37-65` under `127.0.1.1:localhost`


