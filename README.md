# Use Amazon EC2 to train a machine learning model on a budget (by using Spot Instance)

In this tutorial, I will walk through how to leverage Amazon EC2 to train the machine learning model in an inexpensive way. With a tight budget, I use this approach to work with Kaggle competition and to experiment deep learning techniques. Note: this tutorial is suitable for a person who uses Windows and wants to access to a massive computational resource for training on large data-set. 

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

In this part, I found that there is a great walkthrough in the internet already. Please follow [this](https://medium.com/@alexjsanchez/python-3-notebooks-on-aws-ec2-in-15-mostly-easy-steps-2ec5e662c6c6)

Apart from Anaconda and jupyter, you can install additional libraries such as Lightgbm and HDBscan via the terminal. 
ex.
```
conda install -c conda-forge lightgbm 
pip install hdbscan
```

## Step 3: Transfer file to the EC2 instance 
I normally use *FileZilla* application to transfer file between a local machine and EC2 instance. Since I mainly use Google Drive to store a very large file, I also use *gdown* library to download the file from google drive to the instance.  

### Transfer file using FileZilla
1. Download the [FileZilla Client](https://filezilla-project.org/), and install the program 
2. Open the program and click setting according to the picture below
<< image 12.1>>
3. Select SFTP and add key file (You can use key file generated in step 1.1) and hit 'ok'
<< image 12.2>>
4. Click top-left icon (Open the site manager) and "New Site"
<< image 12.3>>
5. in *Host* field, fill in the public DNS of the instance, and fill in the *user* field. Click 'connect'
<< image 12.4>>
6. Then, you will see the directory of the instance, which allows you to transfer file to it
<< image 12.5>>

### Download file from Google Drive
1. install *gdown* library
```
pip install gdown
```
2. open jupyter notebook and write this following code
```
import gdown

url = 'https://drive.google.com/uc?id=[ID]'
output = '[filename]'
gdown.download(url, output, quiet=False)
```

3. you need to replace [ID] and [filename]. To obtain the ID, open the google drive, right click on the file, and select *get shareable link* 
<<image 21>>
Then you will get the link, which contain the ID of the document. Make sure that the file is set as *Anyone with the link...*
<< image 22>>

4. run the code

So, after setting up the environment and transferring all files to the instance, it is time for you to run the code to train the model

## Step 4: Save the result and terminate the instance
After training the model, I normally use *pickle* to store the trained model
```
import pickle
with open("myModel.pkl", 'wb') as pickle_file:
  pickle.dump(model, pickle_file)
```
Then I transfer the model back to my local machine through *FileZilla*

Next, I terminate the instance to stop charging
<<image 23>> 

And that's it. It appears that there are many tasks to setup the environment. However, if you are familiar with the process, it can be very fast to setup and you can save your money up to 60-70%!!!

