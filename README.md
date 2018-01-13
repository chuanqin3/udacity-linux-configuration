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
