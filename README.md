# Deploying a Multi/3 Tier Application on GCP
In this runbook, we will discuss/implement the LAMP installation with multi-tier architecture on GCP. We will be using the Compute Engine service of GCP for Linux server, Apache Web server and php. For the Mysql database, we will use the SQL service of GCP as a separate micro service. We will connect both the services to design  a multi-tier architecture for LAMP installation. For Installing LAMP on GCP, you need to have a registered GCP account. At this point, I assume that you already have a registered GCP account.

## Step 1: Creating A Compute Engine VM
For creating a Compute engine VM, you may refer to my previous blog on running a compute engine VM on GCP. In this blog, we will use the following:

- Linux OS: Ubuntu 16.04
- Apache Version: v2.4
- Php Version: v7.0
- Mysql Version : v5.7

First create a Compute Engine VM on your GCP account/project and once you have created a VM instance, ssh/login to the VM server to install apache and php. You can login/ssh either via GCP provided web console, CloudShell or third-party ssh tools like terminal or putty. Here we will use our local to login, however like I mentioned you can also use the GCP web console for ssh login as shown below.

![CreateVMInstance!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/gcp-ssh2.png)









