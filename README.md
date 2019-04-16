# AWS Tutorial CS308
AWS provides a comprehensive suite of development tools to take your code completely onto the cloud. It offers developers the ability to upload static webapps on services like **Elastic Cloud Compute (EC2)**, automatically scale servers with services like **Elastic Beanstalk** (think: Jack's magical beans which *automatically* grow up), keep data in a simple, easily read/write-able storage with **Amazon Simple Storage Service (S3)** and have server clusters on standby to run backend computations efficiently with **Amazon Elastic MapReduce (EMR)**. AWS provides all the infrastructure for this, so that developers do not have to worry about any of the difficulties that come with maintaining their own servers. Have you ever wanted to get a website up and running quickly, but do not have the access or know-how to operate servers? Follow this tutorial and you will be able to do that. Note: the AWS Free Tier *should* have you covered for all the uses in this tutorial, just don't do anything crazy, and terminate our instances after use. 
## Make an AWS Account
[Create a new AWS Account](https://portal.aws.amazon.com/billing/signup#/start). Choose the free tier, enter card information, etc. You will not be charged unless you refrain from terminating instances after use. Once you have successfully created and confirmed a new account, you should be redirected to the home console page. Next to your username in the top right you should see either "Ohio" or "N. Virginia", if your region is Ohio, change it to N. Virginia. This just ensures that all the instances started in this tutorial will be in the same region, so that they can work together when the time comes!
## AWS Elastic Cloud Compute (EC2)
EC2 is a service which provides virtual machines for users to run their own applications on the cloud. It forms the bedrock for many other AWS services, such as AWS Elastic Beanstalk, which will be used later in the tutorial. Simply put, EC2 essentially is hosting a computer for you to use on the cloud. 
### Getting started with EC2
#### Creating an instance
* Navigate to the [AWS Console](console.aws.amazon.com), and click on EC2 from the "Compute" menu. 
* Click Launch Instance to begin. First you will be prompted to choose an OS, for this tutorial keep the default selection of Amazon Linux 2 AMI, click Select. 
* Then you will be prompted to choose an instance type, once again keep the default of t2.micro, which will provide enough storage and performance. 
* Click "Launch". 
* You will then be prompted to create a new key pair to authenticate yourself to the servers. This will allow you to access the servers from your local machine's console, while not allowing anyone without the private key access. Download your key pair, and wait for your server to launch.  
#### Connecting to your EC2 instance
Now that the server is up and running, you will connect to it from your local machine. If you have any problems with the below directions, see the AWS documentation for [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html)
**Mac Directions:** 
* Open Terminal, or your preferred command line application. 
* Ensure you have SSH by typing `ssh` into the command line. 
* Change directories (`cd Path_To_File` or something of the like) to the folder containing the .pem file with the key pair. 
* You will need to change permissions of the private key so you can read it, do this by typing `<chmod 400 file_name.pem>` Now you are ready to connect to your instance! 
* On the command line you must type: 
`ssh -i /path/my-key-pair.pem user_name@public_dns_name`. 
  * Plug in the proper values. For `user_name` it is generally `ec2-user`, and you can find the `public_dns_name` in the **EC2 console column titled 'Public DNS (IPv4)**. The console may respond asking if you wish to continue connecting because authenticity can't be established, **type yes**. Now you are connected to your EC2 instance, and can use it just as you use your local machine.
  * Plug in the proper values. For user_name it is probably ec2-user, and you can find the public_dns_name in the EC2 console. The console may respond asking if you wish to continue connecting because authenticity can't be established, **type yes**. Now you are connected to your EC2 instance, and can use it just as you use your local machine.
**Windows Directions:**
* See the link at the top of this section for detailed instructions on a few ways to connect on a Windows device
* The recommended way is through [PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html), which allows you both to convert the private key's permissions, and also provides an ssh client to access the server. Follow the directions linked here.
You can play around in the command line (`echo "Hello world!"`), but this section was mainly to show you what exactly the other services are running on top of. 
#### Terminating EC2 instance (VERY IMPORTANT!)
* Navigate to the EC2 console 
* In the navigation pane, choose **Instances**
* Select the instance, and choose **Actions, Instance State, Terminate**
* Choose **Yes, Terminate**
If you do not do this, you will eventually use up the free hours/month you are alotted. 
#
#
> ```diff
> + Checkpoint Number 1: At this point, you should have spun up and terminated an EC2 instance. 
> ```
#
#
## AWS Elastic Beanstalk (EB)
Now that you are familiar with the standard EC2 instance that AWS offers, let's get more robust. Elastic Beanstalk (EB) is an AWS Platform-as-a-Service that allows you to upload and deploy applications, and run them on top of an EC2 instance. EB allows for many different types of applications to be run on it, but as we have already created a containerized web app in the last section, why not continue with that example and use Docker as our configuration. Per AWS’s docs: 
>By using Docker with Elastic Beanstalk, you have an infrastructure that automatically handles the details of capacity provisioning, load >balancing, scaling, and application health monitoring.
This is great! By using EB with Docker we are alleviating much of the stress that is brought with utilizing servers to do our processing. 
### Getting Started
#### Updating the Webapp
Navigate to the Dockerfile in this tutorial's directory. You should see that the line `EXPOSE 80`has been added. This line is added so that the Docker image is built with a port that the server can access to use the image as the webpage. 
#### Deploying to EB 
Once you have your Dockerfile files ready for use, uploading the webapp to the cloud is very simple. 
* First, if you have not already, download the files from this tutorial to your local machine.
* Open the AWS EB console, and click **Get started** (or if you have already used EB, **Create New Application**). 
* Put in an **Application name** like "AWS-Tutorial"
* For **Platform** select **Docker**
* For **Application code** select **Upload your code** and upload the **Package.zip** in the directory. This .zip contains **html** and **Dockerfile** compressed.
** Click **Create application**
It will take a few minutes to launch, but once complete, you should be able to return to the environment page, and click on the url that now has the live version of your webapp! 
At this point, you have a live website, whose link you could send to friends, and they could access it too. You could buy a domain name, and host that domain through this EB instance. 
#### Troubleshooting
* If your deployment was aborted, and you need to redeploy, navigate to your Applications page, and click **Actions, Create environment**, and follow the original directions, renaming your code-source if need be.
    * This is because redeploying to the original environment with new source code will cause an error because it is not a newer version, but rather a completely new set of files (to the console, at least) 
See [Troubleshooting] (https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/troubleshooting.html) for further information. 
#### Terminate your EB instance
* On your **Environment** page, Click **Actions, Terminate Environment**
* On the **Application** page  Click **Actions, Delete Application**
#
#
> ```diff
> + Checkpoint Number 2: At this point, you should have created a live web-app. 
> ```
#
#
## AWS Elastic Map Reduce (EMR) 
AWS EMR allows users to call a server cluster to process Big Data efficiently. As we learned earlier, MapReduce programs split up a task, compute, and then reduce the computations. EMR let's you do this with multiple servers running the computations. We're going to add to our site a button which calls an EMR cluster through the Javascript SDK, and runs a simple counting task on a set of data. In this tutorial, we will be running a [Hive script](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hive.html), which is a higher-level package that runs on top of Hadoop. It makes the calls for a MapReduce program more simple than in a language such as Java. If you'd like to view the script that we will be calling, you can find it in the [AWS Hive Tutorial](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-gs-process-sample-data.html). Read the section titled **Understanding the Data and Script** 
### Launching a Cluster from the Console
First, let's launch a cluster from the EMR console.
* Click **Create cluster** in the top left corner.
* Choose the latest version for **Release**, and **Core Hadoop** for Applications 
* For **EC2 key pair** select 'proceed without an EC2 key pair'
* Keep the rest of the default values, and click **Create cluster**
* Once the cluster has spun up, you can terminate it. 
We did this part to show you the options available for launching a cluster, so that the code version in JS is more familiar, but also to create the default roles (`EMR_EC2_DefaultRole` and `EMR_DefaultRole`)that are needed to launch a cluster. These roles are not attached to your account automatically, but are once you launch your first cluster through the console. Now we can proceed with our end goal: Launching a cluster to run a compuation through a JS call in a web-app. 
#
#
> ```diff
> + Checkpoint Number 3: At this point, you should have launched your first cluster.
> ```
#
#
### Credentials
Before you can run it, you must generate an accessKeyId and a secretAccessKey in the AWS IAM console. 
* Click on your username in the top right corner of the console, and then click "My Security Credentials." 
* A popup may appear,  click 'Continue'. 
* Expand the "Access keys" section, and click "Create new access key." 
* Download the key file.
### Updating Index.html
Navigate to the index.html file, and open it in your favorite text editor.
There are multiple values that must be updated, first:  
*  `AWS.config.update({region: 'us-east-1', accessKeyId: 'string', secretAccessKey: 'string', })` 
  * This code updates the AWS instance object to have the proper regions and your credentials. Replace the `'string'` values with the actual values you just created and downloaded. Now your AWS instance will be able to call on other AWS services with your credentials.
* Update the `LogUri` field in params to have your account-number, which can be found by clicking on your username in the top right of the console, and then 'My Account' 
### Uploading your scripts to S3
S3 is a simple storage service that can be accessed by AWS customers and instances alike. We will use it to house our files that will be written to when we call our MapReduce program, as well as store a script we will call when instantiating our cluster. 
Before we update the rest of index.html file, let's go to S3 in our console and create a few buckets where we will be storing data for our MapReduce program. An S3 'bucket' is essentially a folder. 
* Output folder:
   * Click **Create bucket**, enter a unique **Bucket name** something like 'output-for-my-program-lastname'
   * Click **Create folder** when inside the bucket, and title it 'output'
   * Inside the bucket, Click on the **Permissions** tab, and then **Bucket Policy** below it.
   * Copy and paste the following into the editor, and change the value of `Resource` from `ARN` to the full ARN above the text field (should look something like this: `arn:aws:s3:::output-for-my-program`): 
```
{
    "Id": "Policy1553621793148",
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1553621746537",
            "Action": "s3:*",
            "Effect": "Allow",
            "Resource": "ARN",
            "Principal": {
                "Service": [
                    "elasticmapreduce.amazonaws.com"
                ]
            }
        }
    ]
 }
```
* This allows for EMR calls made by you to have read/write permissions for the S3 bucket. This step will be reproduced for a few buckets. * In index.html, on line 70, update `OUTPUT=s3://PATH/output'` with the proper values. The path value is just the name of the bucket. It should look like: `'OUTPUT=s3://output-for-my-program/output'`
* Next, create a new bucket to house the node-install.sh script. Follow the same directions as above, creating a new bucket with a unique name like 'node-install-script-bucket', then click **Upload** and upload nodeinstall.sh to the bucket. Update the **Permissions** of the bucket as you did above, plugging in the proper `ARN` 
* Copy the path of the script (by clicking on the script while inside the bucket, and selecting **Copy path**, and replace on line 78 `Path: 's3://path/nodeinstall.sh'`in `BootstrapActions`section with the proper path.Just highlight the entire default s3 value, and paste in the proper new path you just copied from the console. 
### Looking at EMR.runJobFlow()
Peruse the `params` object in index.html, and see what exactly is being passed to the EMR instance you are initializing. You should recognize some of the values from when you started up an EMR cluster from the console. You are calling a fleet of three servers, 1 master node, and 2 core nodes. The `Steps` portion is where jobs are submitted to the cluster. The `Jar` value is `command-runner.jar`, which is a simple file AWS provides that allows us to run some command-line scripts. In `Args` we have `'hive-script'`passed first, which tells the command-runner that the next arguments will be for a hive script. The first s3 file is the Hive script alluded to at the start of the EMR section. The `INPUT`object is where the data is coming from that the Hive script is mapping and reducing. 
#
#
> ```diff
> + Checkpoint Number 4: At this point, index.html should have new values for all the lines with the //update 
> ```
#
#
### Click Run numbers
This will run a step, and after it is done, check your S3 output bucket for the results! 
#### Sources
Hive tutorial & code: https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-gs.html
AWS Explanations: https://www.expeditedssl.com/aws-in-plain-english 
