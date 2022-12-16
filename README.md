# Deploying a Multi/3 Tier Application on GCP
In this runbook, we will discuss/implement the LAMP installation with multi-tier architecture on GCP. We will be using the Compute Engine service of GCP for Linux server, Apache Web server and php. For the Mysql database, we will use the SQL service of GCP as a separate micro service. We will connect both the services to design  a multi-tier architecture for LAMP installation. For Installing LAMP on GCP, you need to have a registered GCP account. At this point, I assume that you already have a registered GCP account.

## Step 1: Creating A Compute Engine VM
For creating a Compute engine VM, you may refer to my previous blog on running a compute engine VM on GCP. In this blog, we will use the following:

- Linux OS: Ubuntu 16.04
- Apache Version: v2.4
- Php Version: v7.0
- Mysql Version : v5.7

First create a Compute Engine VM on your GCP account/project and once you have created a VM instance, ssh/login to the VM server to install apache and php. You can login/ssh either via GCP provided web console, CloudShell or third-party ssh tools like terminal or putty. Here we will use our local to login, however like I mentioned you can also use the GCP web console for ssh login as shown below.

To SSH login to the VM instance via “ssh web interface”, click on the SSH button of the instance from the console by going to Navigation menu > Compute Engine > VM Instances. On clicking on the ssh button of the VM instance, an ssh web interface will be prompt where  you will be logged in to that particular instance. Here you can perform any operations on the server via ssh.

![CreateVMInstance!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/gcp-ssh2.png)

## Step 2: Installing Apache And PHP
After login into the VM instance, you can install apache web server and PHP. You can define your preferred php version for installation.

#### To install apache web server, Use the following commands.
```
sudo apt-get -y install apache2
```
#### To install php and its extensions, use the bellow commands. We will use php7.0 for the demo.
```
sudo locale-gen en_US.UTF-8
export LANG=en_US.UTF-8
sudo apt-get update
sudo apt-get install -y software-properties-common python-software-properties
sudo LC_ALL=en_US.UTF-8 add-apt-repository -y ppa:ondrej/php
sudo apt-get update
apt-get -y install php7.0 php7.0-curl php7.0-bcmath php7.0-intl php7.0-gd php7.0-dom php7.0-mcrypt php7.0-iconv php7.0-xsl php7.0-mbstring php7.0-ctype php7.0-zip php7.0-pdo php7.0-xml php7.0-bz2 php7.0-calendar php7.0-exif php7.0-fileinfo php7.0-json php7.0-mysqli php7.0-mysql php7.0-posix php7.0-tokenizer php7.0-xmlwriter php7.0-xmlreader php7.0-phar php7.0-soap php7.0-mysql php7.0-fpm libapache2-mod-php7.0
sed -i -e"s/^memory_limit\s*=\s*128M/memory_limit = 512M/" /etc/php/7.0/apache2/php.ini

```










