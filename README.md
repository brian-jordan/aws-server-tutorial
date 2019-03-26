# AWS Tutorial CS308
## AWS Elastic Cloud Compute (EC2)
EC2, which stands for Elastic Compute Cloud, is a service which provides virtual machines for users to run their own applications on the cloud. It forms the bedrock for many other AWS services, such as AWS Elastic Beanstalk, which will be used later in the tutorial. 
### Getting started with EC2
#### Creating an instance
Navigate to the [AWS Console](console.aws.amazon.com) and click on EC2 from the "Compute" menu. Then click Launch Instance to begin. First you will be prompted to choose an OS, for this tutorial keep the default selection of Amazon Linux 2 AMI, click Select. Then you will be prompted to choose an instance type, once again keep the deafult of t2.micro, which will provide enough storage and performance. Then click "Launch". You will then be prompted to create a new key pair to authenticate yourself to the servers. This will allow you to access the servers from your local machine's console, while not allowing anyone without the private key access. Download your key pair, and wait for your server to launch.  
#### Connecting to your EC2 instance
Now that the server is up and running, you will connect to it from your local machine. Ensure you have SSH by typing ssh into the command line of your preferred console (the default being Terminal for Mac users). Change directories to the folder containing the .pem file with the key pair (do this by typing `<cd Documents/Tutorial>` for example). You will need to change permissions of the private key so you can read it, do this by typing `<chmod 400 _______.pem>` Then you are ready to connect to your instance! Then on your command line you must type ssh -i /path/my-key-pair.pem user_name@public_dns_name. Plug in the proper values. For user_name it is probably ec2-user, and you can find the public_dns_name in the EC2 console. The console may respond asking if you wish to continue connecting because authenticity can't be established, type yes. Now you are connected to your EC2 instance, and can use it just as you use your local machine.
## Elastic Beanstalk (EB)
Now that you are familiar with the standard EC2 instance that AWS offers, let's get more robust. Elastic Beanstalk (EBS) is an AWS PaaS that allows you to upload and deploy applications, and run them on top of an EC2 instance. EBS allows for many different types of applications to be run on it, but as we have already created a containerized web app in the last section, why not continue with that example and use Docker as our configuration. Per AWS’s docs: 
>By using Docker with Elastic Beanstalk, you have an infrastructure that automatically handles the details of capacity provisioning, load >balancing, scaling, and application health monitoring.
This is great! By using EBS with Docker we are alleviating much of the stress that is brought with utilizing servers to do our processing. 
### Getting Started
#### Updating the Webapp
Before you can upload your webapp to the cloud, a file must be added Open up the AWS Elastic Beanstalk console. This is the Dockerrun.aws.json file, which tells AWS how to deploy the Docker image as an Elastic Beanstalk application. 
#### Deploying to EBS 
Once you have your Dockerfile and Dockerrun.aws.json files ready for use, uploading the webapp to the cloud is very simple. Open the AWS EBS console, and click Create New Application. Put in an application name like "AWS-Tutorial" and click Create. Then create a new environment in this application. Choose web server environment, and choose Docker from the preconfigured platform. In the Application code section, choose "upload your code", and select the .zip file containing your web app. Then click create. It will take a few minutes to launch, but once complete, you should be able to return to the environment page, and click on the url that now has the live version of your webapp! 
## AWS Elastic Map Reduce (EMR) 
AWS EMR allows users to call a server cluster to process Big Data efficiently. As we learned earlier, MapReduce programs split up a task, compute, and then reduce the computations. EMR let's you do this with multiple servers running the computations. We're going to add to our site a button which calls an EMR cluster through the Javascript SDK, and runs a simple counting task on a set of data. 
### Credentials
Before you can run it, you must generate an accessKeyId and a secretAccessKey in the AWS IAM console. Click on your username in the top right corner of the console, and then click "My Security Credentials." A popup may appear, just x it out. Expand the "Access keys" section, and click "create new access key." Download the key file. Then, navigate to the index.html file, where you will see added lines in the <script> portion of the code. The 
  
  `AWS.config.update({region: 'us-east-2', accessKeyId: 'string', secretAccessKey: 'string', })` 
  
line updates the AWS instance object to have the proper regions and your credentials. Replace the `'string'` values with the actual values you just created.
### Uploading Scripts to S3
As you can see, there are a few things you need to update in the script portion of index.html. First, you need to upload the node-install.sh script somewhere into your S3, so that when your cluster is created, a boostrap action is called to install Node on the servers. Node.js is needed to run the mapper/reducer programs. 

Next you need to upload the sample-mapper.js and sample-reducer.js files to an S3 bucket. 

Next create a blank bucket where output can be stored, and put it's path after the  `-output` argument. 

Finally, create a bucket where the logs can be stored, and place that path in the `LogUri` variable. 
### Calling EMR.runJobFlow()
Peruse the `params` object, and see what exactly is being passed to the EMR instance you are initializing. You are calling a fleet of three servers, 1 master node, and 2 core nodes. The `steps` portion is where jobs are submitted to the cluster. In the `Args` section you will feed the `-input, -output, -mapper, -reducer` scripts, which will be stored on your S3, or a publicly accessible one. 



#### Sources
Mapper/Reducer programs: https://aws.amazon.com/blogs/big-data/node-js-streaming-mapreduce-with-amazon-emr/
