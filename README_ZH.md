[English](/README.md) | [中文](/README_ZH.md)

## 目录
- [目录](#目录)
  - [准备阶段](#准备阶段)
    - [BTP 账户](#btp-账户)
    - [工具](#工具)
  - [开发](#开发)
  - [测试（本地）](#测试本地)
  - [Deployment](#deployment)
  - [Test(BTP subaccount)](#testbtp-subaccount)
  - [Refrence](#refrence)

### 准备阶段 

#### BTP 账户

使用该 [link](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/e50ab7b423f04a8db301d7678946626e.html) 申请BTP的试用账户或者配置企业账户

#### 工具

   **Python** 

   选择1: 
   
   从 [官网](https://www.python.org/) 安装并配置 python

   选项2: 
   
   从 [python-guide](https://docs.python-guide.org/) 安装并配置 python

   **IDE** 
    
   使用 [VSC](https://code.visualstudio.com/) 作为开发集成环境, 或者其他个人合适的集成开发环境 
   

 * **VSC**
    安装 [python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

### 开发

步骤:

1. 下载依赖包 vitualenv 和  flask 
   
   ```
   pip install virtualenv
   pip install Flask
   ```

2. 创建项目工程文件夹并初始化虚拟环境
   
   ```
      mkdir demo
      cd demo
      virtualenv flask
   ``` 

3.  添加 **app.py** 文件， 并写入以下代码
   

   ![app.py](/img/app.py.png)

   
   python 代码 :
   
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

### 测试（本地）

1. 通过该命令行 ```python app.py```运行python工程
   
2. 通过命令行输出文件，寻找到对应的测试链接，并打开

 ![python_test](/img/python_test.png)

3. 一旦你获得如下的响应, 我们的python工程运行正常
    
  ```
   Hello this is the BTP python demo!
  ```

### Deployment

部署至BTP，步骤如下:
1. 设置 cloud foundry endpoint
   
   命令行：

      ```cf api {EndpointURL} ```

   **EndpointURL** 你可以在子账户中看到对应的API endpint :
   ![APIEndPoint](/img/APIEndPoint.png)

2. 使用你的BTP账户登录对应的BTP CF环境
   
   命令行 :

      ```cf login ```

3. 添加 manifest.yml 到pyton工程目录
   
   ![manifest](/img/manifest.png)  

   
4.  配置 [route](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/53daaafe8f8345fc9b8497b86d17c9d9.html?q=routes) 和 [python buildpacks](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/acf8f49356d047fbb1a4d04dcec3fd36.html)
   
    1.  在我们配置 python buildpack之前 , 我们仍然需要添加 3个(runtime.text, requirement.txt procfile )文件来保证 python buildpack运作正常

        runtimetime.text:  定义python运行版本, 我们可以通过[pythonBuildPackage Relaese](https://github.com/cloudfoundry/python-buildpack/releases)查阅对应的可运行python运行版本

        example code :

        ```
        3.8.10
        ```

        requirement.txt : 放置python依赖以及相应版本

        example code :

        ```
        Flask==2.0.1
        ```

        procfile: 定义python工程运行起始命令

        example code :
        ```
        web: python app.py
        ``` 
    2. 配置路由
   
       推荐使用以下方式形成对应的route :

        ```
        {subdomain}-{appname}.{cfappdoman}
        ```
        
       **subdomain:** 

       ![subdomain](/img/subdomain.png)

       **appname**: 由业务定义

       **cfappsdomain:**：  可使用命令行 ```cf domains ``` 获取对应的domains

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
   
5. 部署 pytho 工程到TP环境中
   
   命令行 :
   
    ```cf push ```

### Test(BTP subaccount)

1. 导航到到 sapce
   
 ![space](/img/space.png)

2. 进入到 applcation 
   
 ![space_application](/img/space_application.png)

3.  查看 applicaiton URL
   
 ![applicationOverview](/img/applicaiton_overview.png)

4.  用以下链接来测试
   
   ```
   {applicaitonURL}
   ```

   一旦获得如下的响应， 我们的python工作在BTP的运行环境下已经运行正常
    
   ```
   Hello this is the BTP python demo!
   ```

### Refrence
创建并部署python工程: [btp-pyton-deploy](https://blogs.sap.com/2021/04/20/deployment-of-python-web-server-to-cloud-foundry-using-mta/)

Flask 向导 :  [flask Guide](https://flask.palletsprojects.com/en/2.0.x/)





