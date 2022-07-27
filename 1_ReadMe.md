














**Deploying Web Application in AWS EC2 using CICD**

**(Code Commit, Code Build, Code Pipeline)**



|**Version**|**Author**|**Created Date**|
| :- | :- | :- |
|1.0|Prashanth Mudaliar|19-July-2022|































Index


|**Steps**|**Page No**|
| :- | :- |
|Step 1: Launching an instance|3|
|Step 2: Modifying the Security Group|8|
|Step 3: Connecting to the EC2 Instances|10|
|Step 4: Installing .Net Runtime Package|11|
|Step 5: Creating Service File For WebAPP|12|
|Step 6: Installing httpd|13|
|Step 7: Installing Code Deploy Agent|14|
|Step 8: Creating WebApp Folder for publishing build files|15|
|Step 9: Creating Repository In Code Commit (AWS Console)|16|
|Step 10: Generating Git Credentials|17|
|Step 11: Creating Project for CodeBuild|19|
|Step 12: Creating Role for CodeDeploy|24|
|Step 13: Creating Application for CodeDeploy|25|
|Step 14: Creating pipeline using CodePipeline|28|
|Step 15: Cloning Repository |32|
|Step 16: Pushing the changes to CodeCommit to Trigger Pipeline|39|
|Step 17: Testing the Web Application|43|























**Step 1**: **Launching an instance**

1. Go to **EC2** service in Amazon Console and Click on **Launch Instance**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.001.png)

1. Name the instance and select the instance type

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.002.png)

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.003.png)





1. Click on **Create New Key Pair**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.004.png)

1. Create a new key pair as follows

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.005.png)

**Note**: **Kindly save the UATKey.pem file in a secure location**



1. Scroll down and Expand **Advanced Details**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.006.png)

1. Click on **Create new IAM profile**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.007.png)

1. A new tab (on browser)will be opened. Click on **Create Role**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.008.png)

1. Select the options as shown in below image and click on **Next**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.009.png)

1. Select the below mentioned policies from the **Add Permissions** Screen and click on **Next** 

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.010.png)






1. Specify the **Name** for the role and click on **Create Role**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.011.png)

1. In the previous tab , click on refresh button and select the role which you created

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.012.png)

1. In the Summary section click on Launch Instance

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.013.png)

1. You will see the below screen

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.014.png)


1. Go To EC2->Instances and you will see the instances running as shown in below image

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.015.png)








































**Step 2**: **Modifying the Security Group**

1. Go To EC2->Instances-> Select the instance which you created->Go To Security Tab-> Click on the Security Group

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.016.png)

1. Click on Edit Inbound Rules

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.017.png)














1. Add the below rules and Click on Save Rules

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.018.png)





































**Step 3**: **Connecting to the EC2 Instances**

1. Select the EC2 Instance and click on **Connect** 

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.019.png)

1. Click on **Connect** 

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.020.png)

1. A new tab(in browser) will be opened

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.021.png)











**Step 4**: **Installing .Net Runtime Package**

1. Run the below commands in the EC2 instance which we connected in Step 3
1. sudo rpm -Uvh <https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm>

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.022.png)

1. sudo yum install dotnet-sdk-3.1

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.023.png)

1. Go to directory **/etc/httpd/conf.d** -> Create a new file **default-site.conf** with contents

<VirtualHost \*:80>

`    `ServerAdmin prashanth.mudaliar@outlook.com

`    `ProxyRequests off

`    `DocumentRoot /app

`    `ProxyPreserveHost On

`    `ServerName www.prashanth-dotnet-aws-softwareengineer.com

`    `# Possible values include: debug, info, notice, warn, error, crit,

`    `# alert, emerg.

`    `LogLevel error

`    `<Location "/">

`        `ProxyPass "http://127.0.0.1:5010/"

`        `ProxyPassReverse  "http://127.0.0.1:5010/"

`    `</Location>

</VirtualHost>





















**Step 5**: **Creating Service File For WebAPP**

1. Go to the below mentioned path

/etc/systemd/system

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.024.png)

1. Create a service file MyFirstWebAPP.service with the following content

[Unit]

Description=My First Web APP

[Service]

ExecStart=/usr/bin/dotnet /app/webapp/MyFirstWebAPP\_AWS.dll

User=root

Restart=always

RestartSec=10

WorkingDirectory=/app/webapp

[Install]

WantedBy=multi-user.target




























**Step 6**: **Installing httpd** 

1. Run the below commands
1. sudo yum install httpd
1. sudo systemctl start httpd && sudo systemctl enable httpd















































**Step 7**: **Installing Code Deploy Agent** 

1. Run the below commands
1. sudo yum update
1. sudo yum install ruby
1. wget https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install
1. chmod +x ./install
1. sudo ./install auto












































**Step 8**: **Creating WebApp Folder for publishing build files**

1. Run the below commands
1. sudo mkdir /app
1. cd /app
1. sudo mkdir /webapp














































**\*\* CICD Section \*\***

**Step 9**: **Creating Repository In Code Commit (AWS Console)**

1. Go To CodeCommit -> Create Repository

1. Enter the details and Click on **Create**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.025.png)


1. The Repository will be created and displayed

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.026.png)














**Step 10**: **Generating Git Credentials**

1. Go To IAM -> Users 

Click on the **user** for whom you need to create **Git Credentials**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.027.png)

1. Go to **Security Credentials** tab

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.028.png)

1. Scroll down and go to **HTTPS Git Credentials for AWS CodeCommit.** Click on **Generate Credentials**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.028.png)





1. Credentials will be generated. Download the .CSV file by clicking on **Download credentials**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.029.png)






























**Step 11**: **Creating Project for CodeBuild**

1. Go To CodeBuild -> Click on Create Project 

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.030.png)

1. Enter the **Project Configuration** Details as shown below

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.031.png)















1. Enter/ Select the **Source** details as shown below

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.032.png)

























1. Enter/ Select the **Environment** Details as shown below

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.033.png)

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.034.png)










1. Enter the **Buildspec** details as shown below

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.035.png)

1. Enter/ Select the **Artifacts** details as shown below

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.036.png)

1. Enter/ Select the **Logs** details as shown below and click on Create build project

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.037.png)


1. The CodeBuild Project will be created

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.038.png)










































**Step 12**: **Creating Role for CodeDeploy**

1. Go To IAM-> Roles ->Create Role as shown below

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.039.png)

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.040.png)

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.041.png)


**Step 13**: **Creating Application for CodeDeploy**

1. Go To CodeDeploy -> Click on Create Application

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.042.png)

1. Enter the **Application Name** , Select the **Compute Platform** in **Application Configuration** window and Click on **Create application**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.043.png) 

1. The Application Will be Created

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.044.png)






1. Click on Application Name

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.045.png)

1. Go to **Deployment Groups** Tab and Click on **Create deployment group**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.046.png)

1. Enter the **Deployment Group Name** and Select the **Service Role** which we created in **Step 11**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.047.png)



1. Select **In-place** as **Deployment type**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.048.png)

1. Select **CodeDeployDefault.AllAtOnce** in **Deployment settings** and click on **Create deployment group**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.049.png)

















**Step 14**: **Creating pipeline using CodePipeline**

1. Go To **CodePipeline** -> Click on **Create Pipeline**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.050.png)

1. Enter the **Pipeline Name** in **Pipeline settings**, fill the remaining details as shown below and Click on **Next**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.051.png)









1. Enter/ Select the **Source** details as shown below and Click on **Next**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.052.png)






















1. Enter/ Select **Build** details as shown below and Click on Next

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.053.png)

1. Enter the **Deploy** details as shown below and click on **Next**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.054.png)


1. Review the changes and click on **Create Pipeline**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.055.png)

















































**Step 15**: **Cloning Repository** 

1. Go To **CodeCommit** -> Click on **Repositories** -> Select the **Repository** -> Click on Clone URL -> Click on **Clone HTTPS**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.056.png)

1. Go To **Visual Studio**-> **AWS Explorer** -> Click on **Add AWS Credentials Profile** **Icon** 

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.057.png)




























1. Enter the **Profile Details** as shown below

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.058.png)

**Note:** You can generate **Access Key** and **Secret Access Key** from AWS Console 





















1. Go to **Visual Studio** -> **Team Explorer** -> In the **AWS** section click on **Connect**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.059.png)

1. Once connected Click on **Clone**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.060.png)













1. In the **Clone AWS CodeCommit Repository** pop-up window select the **Repository** name you want to clone and the **Path** where you want to clone. Click on **Ok**  button

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.061.png)

1. Once cloned add the **appsec.yml** in the root directory

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.062.png)


**appspec.yml** file contents

version: 0.0

os: linux

files:

`  `- source: /build\_output/MyFirstWebAPPPublish/

`    `destination:  /app/webapp/

permissions:

`  `- object: /app/webapp/

`    `pattern: "\*\*"

`    `owner: root

`    `group: root

`    `mode: 774

hooks:

`  `BeforeInstall:

`    `- location: codedeploy-scripts/stop-MyFirstWebAPP.sh

`      `timeout: 300

`      `runas: root

`  `AfterInstall:

`    `- location: codedeploy-scripts/start-MyFirstWebAPP.sh

`      `timeout: 300

`      `runas: root

1. Add the **buildspecdu.yml** in the root directory (**Note**: this file name should be same as which we mentioned in **Step 11(e)**)

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.062.png)

**buildspecdu.yml** file contents

version: 0.2

phases: 

`  `install: 

`    `runtime-versions: 

`      `dotnet: 3.1 

`  `pre\_build: 

`    `commands: 

`      `- echo Restore started on `date`

`      `- echo $CODEBUILD\_BUILD\_ID > build-id.txt      

`      `- dotnet restore -r rhel.7-x64 

`  `build: 

`    `commands: 

`      `- echo Build started on `date`. 

`      `- dotnet publish -c Release -r rhel.7-x64 --self-contained false /p:MicrosoftNETPlatformLibrary=Microsoft.NETCore.App -o ./build\_output/MyFirstWebAPPPublish MyFirstWebAPP\_AWS/MyFirstWebAPP\_AWS.csproj

cache:

`    `paths:

`        `- '~/.nuget/packages'

artifacts: 

`  `files: 

`    `- build\_output/\*\*/\* 

`    `- codedeploy-scripts/\*\*/\* 

`    `- appspec.yml 

`    `- build-id.txt

1. Create a folder **codedeploy-scripts** in the root directory

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.062.png)

1. Create two files **start-MyFirstWebAPP.sh** and **stop-MyFirstWebAPP.sh**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.062.png)


**start-MyFirstWebAPP.sh** file contents

#!/bin/bash

systemctl start MyFirstWebAPP.service

**stop-MyFirstWebAPP.sh** file contents

#!/bin/bash

systemctl stop MyFirstWebAPP.service

1. In the **Program.cs** add the below line

webBuilder.UseStartup<Startup>().UseUrls("http://127.0.0.1:5010");









































**Step 16**: **Pushing the changes to CodeCommit to Trigger Pipeline**

1. Go To **Visual Studio** -> **Team Explorer** -> Click on **Changes**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.063.png)

1. Enter the **Commit** Message and click on **Commit All**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.064.png)






1. **Team Explorer** -> Click on **Sync**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.065.png)

1. In the **Outgoing Commits** section click on **Push**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.066.png)







1. Once Pushed the Code, you can see in the AWS Console that the pipeline is triggered

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.067.png)




































**Step 17**: **Testing the Web Application**

1. Go To **Route53** -> **Hosted Zone**-> Click on the **Domain Name**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.068.png)

1. Click on **Create Record**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.069.png)

1. Enter the details as shown below and click on **Create Records**

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.070.png)






1. Open any browser and hit the url which you created above

![](Aspose.Words.d57fd952-0f64-432a-9794-4f43074846fb.071.png)


2

