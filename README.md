# Use-AWS-to-train-machine-learning-model-on-a-tight-budget

In this tutorial, I will walk through how to leverage Amazon EC2 to train the machine learning model in an inexpensive way. I have adopt this approach to work with Kaggle competition and to experiment deep learning techniques. This tutorial is suitable for a person who uses Windows and wants to access to a massive computational resource for training on large data-set. The approach can be summarized as picture below:

<< image >>

## Preriquisite: Preparation in your local system or in Google Drive
In a nutshell, this step is about identifying what is needed and making sure that everything is ready for training process. So, the files can be training data, ipython notebook, etc.

## Step 1: Request EC2 Spot instance
The reason I use Spot instance is because it is much more cheaper than traditional on-demand instance by 60-70%. However, you cannot stop the Spot instance and resume it when needed. Therefore, you will be constantly charged from the time you created the instance to the time you terminated it. That's why, everything needs to be ready before launching the instance to optimize the cost

### 1.1 
Assume that you already have AWS account, the first task is to request the spot instance in your ec2 dashboard
On the dashboard, click "spot requests", and then "Request Spot Instances", when will move you to a new configuration page 
 << image 0.0>>
 << image 0.1>>

### 1.2
There are many fields to fill in this page, but normally, I will leave most of them as it is. Except the field belows:

**AMI**: Normally I choose Ubuntu 14.04 or Linux. But it is up to you to choose software configuration

**EBS volume**: I change the size to 30GB, which is in the free-tier and I think it is enough for me

**Security group**: This is extremely important and will allow you to remote connect from your local system to this instance. If you never created security group before, you need to create a new one as follows:
1. click "create new security group". It will direct to the new page. click "create security group" 
<< image 0.2>>
<< image 0.3>>
2. add name and description. In inbound tap, add rules following the image below, then click "create"
<< image 0.4>>
3. go back to spot configuration page, click refresh in security groups field, and select the one that you just created

**Key pair name**: If you don't have any previous key-pair, you should create a new one. 
1. click "create new key pair". It will direct to the new page. click "Create key pair"
2. fill in the key name and click "create". You will also need to save the key for later use.
<< image 0.5>>

**Maximum price**: select "Set your max price (per instance/hour)" and set the maximum price that you want

### 1.3
After finish configuration, click "launch". You will see your new instance in instance tab in EC2 dashboard and notice that this instance cannot be stopped
<< image 0.6>>

## Step 2: Setup the system environment
After creating new instance, we need to setup the python environment, which I will install Anaconda3 and access to Jupyter Notebook.

### 2.1
For windows user, we need to use the Putty to connect via SSH to the EC2 instance. So download the [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and the Puttygen (used for converting key)


