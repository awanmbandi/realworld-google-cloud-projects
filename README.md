# 👨🏼‍💻Deploying a Multi/3 Tier Web Application on GCP👨🏼‍💻
In this runbook, we will discuss/implement the LAMP installation with multi-tier architecture on GCP. We will be using the Compute Engine service of GCP for Linux server, Apache Web server and php. For the Mysql database, we will use the SQL service of GCP as a separate micro service. We will connect both the services to design  a multi-tier architecture for LAMP installation. For Installing LAMP on GCP, you need to have a registered GCP account. At this point, I assume that you already have a registered GCP account.

## Step 1: Creating A MySQL Database Using SQL Service Of GCP
To create a mysql database using SQL service of GCP, go to Navigation menu  > SQL. Now click on “create instance”, choose database engine as MySQL and click on next. Next you need to choose the use case i.e. for development, staging or production. Here we will choose development. Next in the configure step,  choose Instance Id, Root Password, Region and zone. You can also change other configuration parameters as per your requirement but we will keep it default for this demo. Now click on create button to create the mysql instance. Once the Mysql Instance is created, you can click on its instance id to see its details. You can also find the Public IP of the MySQL instance in the Overview section.

![CreateMySQLDB!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/database-instance.png)

#### Create a Database In The Above Database Instance To Host The Application Data
To create a Database in the above DB instance, Click on the Database instance name in Cloud SQL > Click on Databases > Click on `Create Database` provide the Database name e.g `wordpressdb` > and Click `CREATE`.

![CreateADatabase!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/creating-a-database.png)

#### Granting The Database Instance Access to The App Servers
Once you have created a Mysql Database on SQL service on GCP, its time to connect the Database to the Compute Engine VM instance. For this, you need to allow the public IP of the Compute VM, in the firewall rules of the MySQL service. Click on the MySQL database Instance Id that you created and select the Connections Tab, and under the connectivity section, click on “Add network”. Now give a name to the network and add the IP of the Compute VM instance in the CIDR format i.e. <ipv4_address>/32 and finally click on done and save. If you're finding it difficult to save the VM IP, then provide `0.0.0.0/0` and Save. This will allow the Compute VM instance to connect with the MySQL database instance.

![ConnectingToMySQLDB!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/gcp-add_network2.png)

## Step 2: Create An Instance Template
Before creating the Instance Template, you must make sure you have the following pre-requisites taken care of.
#### Prerequisites
- Make sure you've updated the `app-automation.sh` script with your specific parameters/values
- Make sure you've updated the `wp-config.php` script which holds the app database connection details

- Click on either the Hamburger Menu at the top left hand corner or the search box above and Search for the `Compute Engine` 
- Open the `Compute Engine` Service and Click on `Instance Template`
- Click on `Create Instance Template`
- Provide a name `wordpress-webapp-instance-template`
- Machine type: `e2-medium`
- Change Boot Disk to `Ubuntu 18.04`
- Under Firewall, check the `Allow HTTP traffic` option/box
- Expand Advance Options 
    - Click on Management 
    - Navigate to the Automation (Startup-Script) section 
    - Paste the following script in the Box: https://github.com/awanmbandi/google-cloud-projects/blob/main/startup-script/app-automation.sh
- Note: Leave everything else as Default and Create
- Click on `CREATE`

![InstanceTemplate!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/instance-template.png)

## Step 3: Create a Managed Instance Group (MIGs)
Before creating the MIG, you must make sure you have the following pre-requisites taken care of.
#### Prerequisites
- Make sure you've updated the `app-automation.sh` script with your specific parameters/values
- Make sure you've updated the `wp-config.php` script which holds the app database connection details
- Linux OS: Ubuntu 18.04

- Still on the `Compute Engine Console`
- Click on `Instance Groups` on your left 
- Click `Create Instance Group`
    - Name: `wordpress-webapp-instance-group`
    - Instance Template: Select your template
    - Location: Select `Multi Zones` and choose at least two AZs
    - Minimum Instances: 2
    - Maximum: 2
    - Autoscaling Metrics: 60% or 80%
    - Coold Down Period: 60
    - Autohealing: Create a Health Check
        - Click Create Health Check
        - Name: `wordpress-webapp-hc`
        - Protocol: TCP, Port: 80
        - Logs: On
        - Click on `SAVE`
- Click on `CREATE` to create MIG

![InstanceTemplate!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/manage-instance-group.png)

#### (OPTIONAL) SSH and Verify Setup Was Completed As Expected
To SSH login to the VM instance via “ssh web interface”, click on the SSH button of the instance from the console by going to Navigation menu > Compute Engine > VM Instances. On clicking on the ssh button of the VM instance, an ssh web interface will be prompt where  you will be logged in to that particular instance. Here you can perform any operations on the server via ssh.

![SSHintoVMInstance!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/ssh-into-vm.png)

## Step 4: (OPTIONA) Verify that the VMs are properly authenticated to the MySQL database
SSH login to the Compute VM instance and check if you are connected and able to login to the database instance. For this you need the Public IP of the database instance which you can easily get in the overview section of the MySQL Instance.
```
ping  <public_IP_of_MySQL_instance>
```
```
mysql -h <public_IP_of_MySQL_instance> -u root -p
```
If you are able to login to the MySQL instance through the public IP, then your Compute instance VM is properly authenticated the MySQL database instance.

Congrats, you have now successfully set up LAMP on GCP with multi-tier architecture with separate Instance for Compute and Database.

## Step 5: Create a Global Load Balancer Resource
- Navigate either using the Hamburger menu at the top left or the Search box on your console
- Search for `Load Balancing` and Click on it to open the Service
    - Click on `Create Load Balancer`
    - Click on `Start Configuration` on the `HTTP(S) Load Balancing` Option
    - Internet facing or internal only: Selctect `From Internet to my VMs or serverless services`
    - Global or Regional: Select `Global HTTP(S) Load Balancer` 
    - Click on `Continue`
    - Name: `wordpress-global-lb`
    - Click on: `Backend configuration`
        - Select: `Create a Backend Service`
        - Name: `wordpress-webapp-bs`
        - Backend type: `Instance group`
        - Instance group: `select your instance group`
        - Port numbers: `80`
        - Balancing Mode: `Utilization`
        - Maximum backend Utilization: `80%`
        - Cloud CDN: Check the Box `Enable Cloud CDN`
        - Cache mode: Cache static content `(recommended)`
        - Health Check: `Select the Health Check you created earlier for MIG`
        - Logging: `Enable`
            - Sample rate: `1`
        - NOTE: Leave every other Configuration as Default
        - Click `CREATE` and Select the Backend

        - Click on `CREATE` again to create the Global Load Balancer

#### TEST: Copy the Load Balancer Front End IP Address and Query on The Browser
![CopyLoadBalancerIP!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/loadbalancer-frontend-ip.png)

#### APP: You Should See This
![CopyLoadBalancerIP!](https://github.com/awanmbandi/google-cloud-projects/blob/project-resources/images/wordpress-app-display.png)
        
## Step 6: Create a Cloud DNS Hosted Zone and Record
#### Prerequisites
- You need a Domain to be able to engage this aspect
- You can either register a Domain in Cloud DNS or Export your Domain Name Service to Cloud DNS from where you registered it

1. Register a Domain: https://cloud.google.com/dns/docs/tutorials/create-domain-tutorial#register-domain
2. Set Up Your Domain to Point At The Global Load Balancer IP
- In the `Google Cloud console`, go to the `Cloud DNS` service.
- Click on `Create a DNS zone`
- For the `Zone type`, select `Public`
- For the `Zone name`, enter `wordpress-web-app-zone`
- For the `DNS name`, enter a DNS name suffix for the zone by using a domain name that you registered `(for example, example.com)`.
- For `DNSSEC`, ensure that the `Off` setting is selected
- Click `Create` to create a zone populated with the NS and SOA records.
3. To point your registered domain name to the IP address of the hosting server, you must add an A record to your zone:
- On the `Zone details` page, click `Add record set`.
- Select `A` from the `Resource Record Type` menu.
- For `IPv4 Address`, enter the Global External IP address of your `Load Balancer`.
- Click `Create` to create the A record for your zone.

#### Validate: Copy Your Domain and Past on The Browser to Access The Application
# 🍀 Congratulations On Deploying a 3 Tier App on GCP 🍀











