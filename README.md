# Use-AWS-to-train-machine-learning-model-on-a-tight-budget

In this tutorial, I will walk through how to leverage Amazon EC2 to train the machine learning model in an inexpensive way. I have adopt this approach to work with Kaggle competition and to experiment deep learning techniques. This tutorial is suitable for a person who uses Windows and wants to access to a massive computational resource for training on large data-set. The approach can be summarized as picture below:

<< image >>

## Preriquisite: Preparation in your local system or in Google Drive
In a nutshell, this step is about identifying what is needed and making sure that everything is ready for training process. So, the files can be training data, ipython notebook, etc.

## Step 1: Request EC2 Spot instance and connect to the instance 
The reason I use Spot instance is because it is much more cheaper than traditional on-demand instance by 60-70%. However, you cannot stop the Spot instance and resume it when needed. Therefore, you will be constantly charged from the time you created the instance to the time you terminated it. That's why, everything needs to be ready before launching the instance to optimize the cost

### 1.1: Request EC2 Spot instance
Assume that you already have AWS account, the first task is to request the spot instance in your ec2 dashboard
On the dashboard, click "spot requests", and then "Request Spot Instances", when will move you to a new configuration page 
 << image 0.0>>
 << image 0.1>>

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

After finish configuration, click "launch". You will see your new instance in instance tab in EC2 dashboard and notice that this instance cannot be stopped
<< image 0.6>>

### 1.2 Connect to the instance
After creating new instance, we need to setup the python environment, which I will install Anaconda3 and access to Jupyter Notebook.

For windows user, we need to use the PuTTY to connect via SSH to the EC2 instance. So download the [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and the Puttygen (used for converting key)

Firstly, we need to convert key (created in step 1) from ".pem" to ".ppk". To do that, launch PuTTYgen application and click "load"
<< image 0.7>> 
Then select the key and click "Save private key". We will get the new key file with ".ppk" file extension.
<< image 0.7>>

After key conversion, launch PuTTY
<< image 0.7>>

To fill in *Host Name*, open your EC2 dashboard and obtain information as picture below
<< image 0.8>>

Then go to SSH >> Auth and Browse the key that you converted. Click "Open" and the terminal that connects to the EC2 instance will pop up.
<< image 0.9>>
<< image 1>>

## Step 2: Setup the python environment and Jupyter Notebook

Firstly, we need to get the link to download Anaconda3. You can look at this [link](https://repo.continuum.io/archive/) and save link address of the file. In my case, I will download and install Anaconda3-5.2.0-Linux-x86_64.sh 

In PuTTY, run the following commands to download the file from the link

```
wget https://repo.continuum.io/archive/Anaconda3-5.2.0-Linux-x86_64.sh
```

and then run the command to install the package. Follows the instruction and type "yes" to accept the license and to append the path

```
bash Anaconda3-5.2.0-Linux-x86_64.sh
```
<< image 3>>
<< image 4>>

After finish installation, close and reopen the terminal to switch to anaconda environment. You can command *which python* to check the python version

I will also install needed libraries such as Lightgbm and HDBscan

```
conda install -c conda-forge lightgbm
pip install hdbscan
```

Next, we will create password to access jupyter notebook
```
ipython
from IPython.lib import passwd
passwd()
```
You will be asked to type in the password and it will provide a SHA hash. Please sure that you save this somewhere as you need to refer to this hash in the future

exit the ipython
```
exit
```

Then, we will configure Jupyter notebook and generate the key

```
jupyter notebook --generate-config
mkdir certs
cd certs
sudo openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
cd
```

```
vim .jupyter/jupyter_notebook_config.py
```
