# AWS EC2 Lab
This lab is part of the [foundations trainings](https://github.com/octo-technology-downunder/octo-au-foundations) at [OCTO Technology Australia](http://careers.octo.com.au/).

## The Goal
In this lab we'll need to create a load-balanced webserver and host a simple website on it.
That will cover the following topics:
- Working with AWS Elastic Cloud Compute (EC2) service via AWS Console and aws cli
- Creating a simple webserver hosted on EC2 instance
- Creating a load balancers and auto scaling groups

This lab will take approximately 60 minutes.

## Overview of technology
AWS EC2 is usually referenced to a set of services built around virtual machines, some of which are:
- EC2 - instances of virtual machines
- EBS - a block storage, basically virtual disks (and their snapshots) which could be attached to EC2 instances
- AMI - images of EC2 instances
- ELB - load balancers
- ASG - auto scaling groups, abstraction which allows to scale up and down the number of the EC2 instances based on different parameters

Let's spin up some EC2 instances to see how it works
## Launch a standalone EC2 instance
Our HTTP server will consist of a single host and be based on Apache HTTP server<br>
Let's do that!
* Open https://console.aws.amazon.com/ and log in to your AWS account<br>
* Select Services -> EC2 in drop down menu or search for EC2 in service finder
* In opened page you will see a EC2 home page and also a list of services on the left and
* Make sure you're in the Asia Pacific (Sydney) region. It's also referenced as `ap-southeast-2`, e.g. in url box of your browser
* Select `Instances` on the left, then click `Launch Instance`
* Choose your AMI (e.g. free tier Amazon Linux), then choose your instance size (e.g. `t2.micro`)
* In one of the next pages you will be able to edit Tags. Add a tag with key `Name` and value `foundation-labs-ec2-<your_name>`
* Just before you launch an instance, you will be asked to choose a key pair. These are credentials to access the instance in the future. Choose to create a new one and give it a name `foundation-labs-kp-<your_name>`. Download a credentials file in the same popup window

Awesome! You've gone through the steps and created a EC2 instance!<br>
Now you can see it in the instances list:<br>
![Running](images/ec2-running.png "Running")<br>

## Create a simple HTTP server
Having a EC2 instance running, let's now install and launch a Apache HTTP server with a simple page:
* In AWS Console, go to EC2 service and find your EC2 instance. Click on it and on Description tab find and copy a `IPv4 Public IP` field value. We'll be referencing this value as `<your_ec2_ip>`
* Open your terminal and type `ssh ec2-user@<your_ec2_ip> -i <path_to_credentials>` where <br>
  * `ec2-user` is a privileged user name for Amazon Linux
  * `<your_ec2_ip>` is a IP address of your instance copied above,
  * `<path_to_credentials>` is a path to a credentials file you downloaded during EC2 instance creation
* Accept a security prompt to add a host to the list of trusted ones
* If you see a `bad permissions` error, you would need to do `chmod 400 <path_to_credentials>` on your credentials file. Then try to ssh again. If everything is fine, you'll see a Amazon Linux prompt:
```
       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|
```
* To make our life easier for this lab, switch to root:<br> `sudo su -`
* Update your kernel as suggested in the prompt:<br>`yum update`
* Install Apache HTTP server:<br>`yum install httpd`
* Run Appache HTTP server on default port `80`:<br> `service httpd start`
* Create a simple HTML page in server's directory:<br>
`cd /var/www/html`<br><br>
`touch index.html`<br><br>
`echo ' `<br>
`<html> `<br>
`<head></head> `<br>
`<body><h2>Hello OCTO!</h2><h3>This is my AWS EC2 based website!</h3></body> `<br>
`</html>' > index.html`

The last thing you need is to allow external HTTP traffic to your EC2 instance - by default only ssh is allowed:
* In AWS Console, go to EC2 service and find your EC2 instance. Click on it and on Description tab find Security Groups field. Click on the security group associated with instance. It may have some default name like `launch-wizard-NN`
* On Inbound tab click Edit, then Add Rule and add a new HTTP rule on port 80 from 0.0.0.0/0 source:<br>
![Inbound](images/ec2-inbound.png "Inbound")<br>

Now copy `Public DNS (IPv4)` value from your instance and paste into URL of your browser - you should be able to see your HTML page!
