
# Deploy a sample .NET web app on Linux Server virtual machine using AzureDevOps

============================================================================================

**Contents**

[1. Create Azure VM](#1)

[2. Set up Azure DevOps](#2)

[3. Create the Azure DevOps Build Pipeline](#3)

[4. Create the Azure DevOps Release Pipeline](#4)

============================================================================================

## 1. Create Azure VM <a name="1"></a>

### Create a Linux Server Virtual Machine on Azure (OS: Ubuntu 20.04)



### Connect to your Virtual Machine




### Install ASP.NET Core Runtime 3.1 

**Register the Microsoft Product key as trusted**

```sh
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

**Install .NET Core Runtime (allows you to run apps that were made with .NET Core)**

```sh
sudo apt-get update
sudo apt-get install -y aspnetcore-runtime-3.1 
```
dotnet --list-runtimes

### Create a systemd file for your web application (run your webapp as a service).

Create your web app service file.

```sh
sudo nano /etc/systemd/system/devopsweb.service
```

```sh
[Unit]
Description=Example .NET Web App running on Ubuntu 

[Service]
WorkingDirectory=/www/devopsweb/
ExecStart=/www/devopsweb/DevOpsWeb
Restart=always
RestartSec=10
KillSignal=SIGINT
User=jenkins
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=ASPNETCORE_URLS=http://*:5000

[Install]
WantedBy=multi-user.target
```

Enable your web app service.

```sh
sudo systemctl enable devopsweb.service
```

### Installing Nginx webserver

Install Nginx.

```sh
sudo apt-get -y install nginx
```

Start Nginx Server.

```sh
sudo systemctl start nginx
```

Configure Nginx as a reverse proxy to forward requests to ASP.NET Core app:

Create a new file /etc/nginx/sites-available/devopsweb for your webapp.

```sh
sudo nano /etc/nginx/sites-available/devopsweb
```

```sh
server {
    listen 80;
    server_name 127.0.0.1;
    location / {
        proxy_pass         https://127.0.0.1:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Create a symlink for your webapp in the sites-enabled directory.

```sh
sudo ln -s /etc/nginx/sites-available/devopsweb /etc/nginx/sites-enabled/devopsweb
```

Remove the default website in the sites-enabled directory.

```sh
sudo rm /etc/nginx/sites-enabled/default
```

Restart nginx server.

```sh
sudo systemctl restart nginx
```

## 2. Set up Azure DevOps <a name="2"></a>

Open the Azure Portal, log on with your account. Type "azure devops" in the Azure Portal search bar. Click "Azure Devops organizations".

![dev1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dev1.png)

After page "Azure Devops" page finishes loading, click "My Azure DevOps Organizations".

![dev2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dev2.png)

You will arrive at a page that asks you to "Create new organization". The organization is the root object for your work in Azure DevOps and contains projects.

![dev3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dev3.png)

After that, you will get to "Create a project to get started" page. Enter the project name, then click "Create project". 

![dev4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dev4.png)

You are inside and ready to start a full development project, including: backlogs, kanban boards, code repositories, building and deploying software, ...

![dev5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dev5.png)

## 3. Create the Azure DevOps Build Pipeline <a name="3"></a>

In Azure DevOps, go to your project page:

From the left pane, select "Pipelines", then select "Create Pipeline".

![ci1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci1.png)

Click on "Use the classis editor to create a pipeline without YAML".

![ci2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci2.png)

On the "Select a source" page, choose "Github", leave everything as default and click "Authorize with a Github personal access token".

![ci3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci3.png)

Visit GitHub and sign into your account. Click on your user icon in the upper-right hand corner and select Settings from the drop down menu.

On the page that follows, locate the **Developer settings** section on the left-hand menu and click Personal access tokens. Choose Tokens (classic).

![gh1](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/gh1.png)

Click on Generate new token button on the next page. Also choose Generate new token (classic).

![gh2](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/gh2.png)

Give your token a name refer to your intergration with Azure DevOps. 

![ci4-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci4-1.png)

In the **Select Scopes** section, choose the same as below:

![gh3](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/gh3.png)
 
When you are finished, click Generate token at the bottom.

You get a token.

![gh4](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/gh4.png)
 
Copy the token to clipboard. Go back to Azure DevOps, paste it inside the "Token" box then click "Authorize".

![ci4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci4.png)

Provide the Github repository and its branch (where your .NET sample code is), then click "Continue".

![ci5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci5.png)

On the "Select a template", select "ASP.NET Core" and click "Apply".

![ci6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci6.png)

Give your new pipeline a name. Navigate to "Tasks" tab > Pipeline. In the **Agent Specification** section, choose "ubuntu-20.04".

![ci7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/ci7.png)

Check your Github repository.

![ci8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci8.png)

Head over to the **Triggers** tab. Choose **Enable continous intergration** so that this build pipeline will get started whenever changes to your source code are committed.

![lci1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci1.png)

Click on "Agent job 1". Rename this to "Build sample .NET app"

![lci2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci2.png)

Click on "Publish". Uncheck **Zip published projects**.

![lci3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci3.png)

Click "Save & queue". Chose "Save".

![lci4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci4.png)

Clik "Save".

![lci5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci5.png)

On the left pane menu, choose "Pipelines". You can see that your build pipeline is saved but it has not started running.

![lci6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci6.png)

You can run it manually by choosing the build pipeline, then click "Run pipeline" > "Run".

![lci7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci7.png)

Here, I want to trigger the build pipeline by making some changes to the source code of the web app on my Github repository. 

Visit GitHub and edit the contents of some random file in the web app repository.

![lci8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci8.png)

Commit changes after you edited it.

![lci9](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci9.png)

Click "Save and run".


As soon as the agent is available, your build pipeline will start running. Click "Agent job 1" to view more details.



In here, you see the steps that the build pipeline created. It fetches source code from GitHub, restore the neccesary packages, then builds and publishes the app.



When your pipeline completes, you can go back and view the package. Click on "1 published".



And this is where your package is. You will need it for deployment toward Azure VM.

![lci14](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci14.png)

## 4. Create the Azure DevOps Release Pipeline <a name="4"></a>

Inside Azure DevOps project page, go to Pipelines > Releases. Click "New pipeline" button to start the creation process.

![cd1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd1.png)

On the "Select a template" page, type "IIS" in the Search bar. Scroll down, choose "IIS website deployment" and click Apply.

![cd2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd2.png)

Give your stage a name.

![cd3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd3.png)

Give your release pipeline a name, then in the **Artifacts** section, click "Add" to add the artifacts from your build Pipeline to this Release.

![cd4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd4.png)

Select your build pipeline from "Source (build pipeline)". This point to the package you received from that build pipeline. When you are ready, click "Add".

![cd5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd5.png)

Next move over to the **Stages** section, click on "1 job, 2 tasks".

![cd6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd6.png)

In the "Deploy to AzureVM" stage, leave everything as default.

![cd7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd7.png)

Go down to the "IIS deployment" job. In the **Deployment targets** section, point to the deployment group you created earlier.

![cd8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd8.png)

Leave everything else as default. 

![cd9](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd9.png)

![cd10](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd10.png)

Click "Save" then click OK.

![cd11](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd11.png)

Click "Create release".

![cd12](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd12.png)

There is nothing to change in "Create a new release" page. Just click "Create". Azure release pipeline will pick up an artifact and deploy toward your virtual machine.

![cd13](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd13.png)

A release has been created. Click on "Release-1" for more details.

![cd14](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd14.png)

In the **Stages** section, it shows that there is one target which are in the deployment process. Click on "Logs" 

![cd15-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd15-1.png)

This page will show the status of the deployment. Click on the name off your virtual machine.

![cd16](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd16.png)

The release pipeline has successfully completed. Head over to your virtual machine to check the results.

![lcd1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd1.png)


![lcd2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd2.png)


![lcd3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd3.png)


![lcd4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd4.png)


![lcd5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd5.png)


![lcd6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd6.png)


![lcd7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd7.png)


![lcd8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd8.png)

![lcd9](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd9.png)


![lcd10](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd10.png)


![lcd11](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd11.png)


![lcd12](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd12.png)


![lcd13](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd13.png)


![lcd15](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd15.png)


![lcd16](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd16.png)


![lcd17](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd17.png)


![lcd18](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd18.png)

![lcd19](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd19.png)

![lcd20](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd20.png)

![lcd21](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd21.png)

![lcd22](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd22.png)

![lcd23](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd23.png)

![lcd24](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd24.png)

![lcd26](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd26.png)

