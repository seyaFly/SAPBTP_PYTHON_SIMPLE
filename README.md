[English](/README.md) | [中文](/README_ZH.md)

## Table Of Content
- [Table Of Content](#table-of-content)
  - [Preparation](#preparation)
    - [BTP Account](#btp-account)
    - [Tools](#tools)
  - [Development](#development)
  - [Test(local)](#testlocal)
  - [Deployment](#deployment)
  - [Test(BTP subaccount)](#testbtp-subaccount)
  - [Refrence](#refrence)

### Preparation 

#### BTP Account

Use this [link](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/e50ab7b423f04a8db301d7678946626e.html) to apply and setup the BTP trial or enterpise enviroment

#### Tools

   **Python** 

   Option1: 
   
   Install and setup from  [python](https://www.python.org/) offical website 

   Option2: 
   
   Install and setup from [python-guide](https://docs.python-guide.org/)

   **IDE** 
    
  we can use the [VSC](https://code.visualstudio.com/) as you favourite development IDE, or other IDE you like. 
   

 * **VSC**
     Install the [python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

### Development

Steps:

1. download the dependency , vitualenv and  flask 
   
   ```
   pip install virtualenv
   pip install Flask
   ```

2. create the project folder and Initializa vitual enviroment
   
   ```
      mkdir demo
      cd demo
      virtualenv flask
   ``` 

3. add file **app.py** with the below simple python code 
   

   ![app.py](/img/app.py.png)

   
   python code :
   
     ``` python
     from flask import Flask
     import os
     app = Flask(__name__)

     cf_port = os.getenv("PORT")

     @app.route('/')
     def hello_world():
        return 'Hello this is the BTP python demo!'

     if __name__ == '__main__':
       if cf_port is None:
        app.run(host='0.0.0.0', port=5000, debug=True)
     else:
        app.run(host='0.0.0.0', port=int(cf_port), debug=True)

     ```

### Test(local)

1. Run python project via command line ```python app.py```
   
2. Test with the link mentioned inthe command line 

 ![python_test](/img/python_test.png)

3. after you get the below response , your python applicaiton is runtime fine
    
  ```
   Hello this is the BTP python demo!
  ```

### Deployment

Deploy for BTP:

1. set cloud foundry endpoint via command :

      ```cf api {EndpointURL} ```

   **EndpointURL** you can find in your subaccount :
   ![APIEndPoint](/img/APIEndPoint.png)

2. login to your BTP endpoint with your btp user and password
   
   command :

      ```cf login ```

3. add manifest.yml for CF BTP development
   
   ![manifest](/img/manifest.png)  

   
4. configure the [route](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/53daaafe8f8345fc9b8497b86d17c9d9.html?q=routes) and [python buildpacks](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/acf8f49356d047fbb1a4d04dcec3fd36.html)


   1. Before config the python buildpack , we need to add 3 files(runtime.text, requirement.txt procfile ) according to [guide](https://docs.cloudfoundry.org/buildpacks/python/index.html)

      runtimetime.text:  used to defined the python runtime version， we can find the right python runtime releaes via the [pythonBuildPackage Relaese](https://github.com/cloudfoundry/python-buildpack/releases)

      example code :

      ```
      3.8.10
      ```

      requirement.txt :used to give the python applicaiton denpendency

      example code :

      ```
      Flask==2.0.1
      ```

      procfile: define the python applicaiton start comand

      example code :
      ```
      web: python app.py
      ``` 

   2. confgure the routes
     here we suggest use bellow format as recomendation :

      ```
         {subdomain}-{appname}.{cfappdoman}
       ```

      **subdomain:** 

      ![subdomain](/img/subdomain.png)

      **appname**: defined by business

      **cfappsdomain:** user the command ```cf domains ``` to get the domain url

      ![cfappdomain](/img/cfappdoman.png)

      Example:

         ```
         ---
         applications:
         - name: pyApp
         memory: 128MB
         buildpacks: 
            - python_buildpack
         routes: 
            - route: 91ccc175trial-pythonapp.cfapps.ap21.hana.ondemand.com 
         ```
   
5. Deploy your nodjs project to your BTP Subaccount
   
   command :
   
    ```cf push ```

### Test(BTP subaccount)

1. Navigte to your space
   
 ![space](/img/space.png)

2. Go you applcation 
   
 ![space_application](/img/space_application.png)

3.  Get applicaiton URL
   
 ![applicationOverview](/img/applicaiton_overview.png)

4. Test it with the URL 
   
   ```
   {applicaitonURL}/#/
   ```

   after you get the below response , your python applicaiton is runtime fine
    
   ```
   Hello this is the BTP python demo!
   ```

### Refrence
Create and deploy python app: [btp-pyton-deploy](https://blogs.sap.com/2021/04/20/deployment-of-python-web-server-to-cloud-foundry-using-mta/)

Flask guide :  [flask Guide](https://flask.palletsprojects.com/en/2.0.x/)





