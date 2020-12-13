**1. Prerequisite**
_____________________
You'll need following to start  
a. Python
b. IBM Cloud Account (if you are new to it create one it's free and only takes a min or so)
c. Cloud Foundry CLI
d. Git
If you have knowledge how flask and python works it is plus point

**2. Clone Repo or Build Locally**
__________________________________

Before you start it's recommended to created virtual environment (see python doc for creating virtual env) and activate it.
You can create Flask app locally as you wish or clone it form the GitHub repo link. After cloning the repo headed into cloned folder.

    https://github.com/rehmaniosama/IBM_Cloud_Foundry_Deployment.git
    cd foldername

Familiarized yourself with different files in clone folder

**3. Install Requirements and Run Locally**
________________________________________
Every application run on it's dependencies which is listed in **requirements.txt** file.
It's important to have virtual environment activated so you can install these dependencies in ideally separate environment
so that these can not clash with your operating system or other python dependencies. 

    pip install -r requirements.txt  

once you install all the requirements, run it locally and see what need to be change according to your need 

    python hello.py

This will start your development server on 

    localhost:8000


Once you will done with modification of code as you needed we wish to continue further to prepare our app for deployment on IBM Cloud Foundry 

**4. Configure Flask app for Deployment on IBM Cloud Foundry**
______________________________________________________________
You have notice that manifest.yml file in clone folder with some basic configurations regarding your app. 
This file hold basic info regarding your app such as 

name of your app 
how much memory you want to allocate for this particular instance
you can see in following code we set random-route : True because once you deploy this Flask app on IBM Cloud Foundry. 
A random domain will be generated for this route so by setting it to True mean we accept that route or if you want host name of your choice you always mention in.

The template is provided in clone file 
you should change application name and allocate the memory
    
    applications:
    - name: YourApplicationName
       random-route: True
       memory: 256M

**5. Deploy Flask app on IBM Cloud Foundry**
___________________________________________
For deploy app onto IBM Cloud Foundry you will need IBM account and Cloud Foundry CLI 
first you will need to login into IBM account with following command and choose account if you have multiple
once you login into your account then 
you need to target the Cloud Foundry or you can choose cloud foundry api to select API-endpoint

    bx login 
    
    bx target --cf   or  cf api (API-endpoint)
    
    bx cf apps 

this will list all the running and stopped apps in IBM Cloud Foundry
now its time to deploy our app onto IBM Cloud Foundry with single command 

    bx cf push

set back and watch this will take a time depending upon your app complexity,
this will take care of all deployment headache and build all the dependencies as listed in requirements.txt file 
once this will complete you will see the success message and URL to your live running app and other info 

if you didn't see the success message or see Failed , you can traceback by following command

       bx cf logs (your_app_name) --recent
    
       bx cf apps

this will list all apps including current deployed app.
After deploy this app is now live but have a problem all the data or
user input didn't able to store in database now it's turn to store data in IBM Cloudant database.

**6. Add IBM Cloudant Database**
________________________________
Cloudant Database is NoSQL database
we'll add this database and configure Flask app accordingly so it can run locally and also run on IBM Cloud.

a. For adding Cloudant database you need to login into your IBM account go to the Dashboard, then select you application.
b. once application dashboard is open go onto connections and create new connection
c. Create Cloudant database service which is in Data and Analytics section
d. When you add or remove service from IBM Cloud you need to select Restage which will restart application and add VCAP_SERVICES environment variable to you application.
This  VCAP_SERVICES environment variable  is only available once application run on IBM Cloud.
Environment variable  is good way to store secrete information i-e your password, secret keys and all. 
Instead of hardcoding you can refer your environment variable in source code.

**7. Cloudant Database in use**
_______________________________
As you see in hello.py file under the VCAP_SERVICES 
which is looking for local json file to load to connect database
local json store the service credential to connect Cloudant database 
    
     with open('vcap-local.json') as f:
         vcap = json.load(f) 
         print('local VCAP_SERVICES found')

By doing this we're point database when application runs locally

a. let's create vcap-local.json file inside application folder to store service credential
b. below template code will help you how to store service credential inside vcap-local.json
you can change according to your need

    
    {
      "services": {
        "cloudantNoSQLDB": [  
          {
            "credentials": {
              "username":"Cloudant-DB-Username",
              "password":" Cloudant-DB-Password",
              "host":" Cloudant-DB-Host"
            },
            "label": "cloudantNoSQLDB"
          }
        ]
      }
    }

c. When application is running on IBM Cloud Foundry it will read service credential from  VCAP_SERVICES  environment variable.
d. This file only used when application runs locally

From where you get this service credential?
well you go to IBM Cloud account , open your App , click Connections , select Cloudant then View Credentials.
Credentials looks like this 



now its time to copy required credential as  Cloudant-DB-Username, Cloudant-DB-Password, Cloudant-DB-Host
past these credential in vcap-local.json file and reload the app

Run your local development server by 

     python hello.py

go to localhost:8000 and add your name, submit it now you will see response coming from data base 
you can confirm by going onto Cloudant database where you can see each entry is present

So you can add Object Storage and uploading the images in Object Storage Bucket as you create Cloudant DB
After making changes what you want its time to redeploy the same Flask app onto IBM Cloud Foundry 

    bx cf push

You can view your deployed app live from any where go to URL 
   
    https://my-new-app-cheerful-cassowary-wk.mybluemix.net/

You also can read step by step guide from start to end 
    
    https://lifeissosimpleman.blogspot.com/2020/12/ibm-cloud-deploy-pythonflask-app-to.html  




