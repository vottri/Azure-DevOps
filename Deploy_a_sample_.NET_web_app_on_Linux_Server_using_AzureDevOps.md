
# Deploy a sample .NET web app on Linux Server virtual machine using AzureDevOps

============================================================================================

**Contents**

[1. Create Azure VM](#1)

[2. Set up Azure DevOps](#2)

[3. Create the Azure DevOps Build Pipeline](#3)

[4. Create the Azure DevOps Release Pipeline](#4)

[5. Continuous Integration and Continuous Deployment with Azure Pipelines](#5)

============================================================================================

## 1. Create Azure VM <a name="1"></a>

### Create a Linux Server Virtual Machine on Azure (OS: Ubuntu 20.04)

![ub1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/ub1.png)

### Connect to your Virtual Machine

Navigate to "Networking" tab > "Inbound port rules" section, allow your VM's ports 80, 443, 22 to be accessible from the Internet.

![ub2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/ub2.png)

Use **PuTTY** for connecting to the Linux Virtual Machine. Enter your Linux machine's public IP address in Host Name and Port will be 22. Click on **Open** button to connect.

![ub3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/ub3.png)

When you are inside the Linux VM:

![ub4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/ub4.png)

Check for working Internet connection.

Update your system

```sh
sudo apt-get update
```

Install some required packages:

```sh
sudo apt-get -y install wget apt-transport-https
```

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
![dotnet](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/dotnet.png)

### Create a systemd file for your web application (run your webapp as a service).

Create your web app service file.

```sh
sudo nano /etc/systemd/system/devopsweb.service
```

```sh
[Unit]
Description=Example .NET Web App running on Ubuntu

[Service]
WorkingDirectory=/home/cloud_user/devopsweb/
ExecStart=/home/cloud_user/devopsweb/DevOpsWeb
Restart=always
RestartSec=10
KillSignal=SIGINT
User=cloud_user
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
        proxy_pass         http://127.0.0.1:5000;
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

![web3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web3.png)

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

Go back to the **Tasks** tab, click on "Agent job 1". Rename this to "Build sample .NET app"

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

The build should be on it way shortly after the commit. Select **Pipelines** on the left pane menu to see if it’s in progress.

![lci10](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci10.png)

As soon as the agent is available, your build pipeline will start running. Click the build to track it.

![lci10-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci10-1.png)

The build has just finished successfully. You can review your build tasks by clicking "Build sample .NET app".

![lci10-2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci10-2.png)

You can review console output per task, just choose the task you want to check and scroll the right pane to view its logs.

![lci10-3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci10-3.png)

You can return to the summary view by clicking the back button. Click on "1 published" and you can view the package your pipeline just built. 

![lci10-4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci10-4.png)

And this is where your package is. You will need it for deployment toward Azure VM.

![lci14](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lci14.png)

## 4. Create the Azure DevOps Release Pipeline <a name="4"></a>

Inside Azure DevOps project page, go to Pipelines > Releases. Click "New pipeline" button to start the creation process.

![cd1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd1.png)

On the "Select a template" page, click "Empty job".

![lcd0](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd0.png)

Give your stage a name.

![lcd1-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd1-1.png)

In the **Artifacts** section, click "Add" to add the artifacts from your build Pipeline to this Release.

![lcd2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd2.png)

Select your build pipeline from "Source (build pipeline)". This point to the package you received from that build pipeline. When you are ready, click "Add".

![lcd3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd3.png)

Next click the **Continous Deployment Trigger** button.

![lcd4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd4.png)

Choose **Enabled** and include your repository. This will help trigger the "release pipeline" everytime a new build is available from the "build pipeline".

![lcd5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd5.png)

Next move over to the **Stages** section, click on "1 job, 0 tasks".

![lcd6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd6.png)

Give your release pipeline a name.

![lcd7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd7.png)

Give your **Agent job** a name. In **Agent pool** section, select "Azure pipelines" then change **Agent specification** to "ubuntu-20.04".

![lcd8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd8.png)

Start adding tasks to your release pipeline.

![lcd9](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd9.png)

In the **Add tasks** page, type "ssh" in the search bar, select **SSH** and click **Add**.

![lcd10](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd10.png)

Click "Manage" to set up a "SSH service connection".

![lcd11](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd11.png)

In the **Service connections** page, click the **New service connection** button.

![lcd12](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd12.png)

Type "ssh" in the search bar, choose **SSH** and then click **Next**.

![lcd13](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd13.png)

Enter you Linux machine's Public IP Address.

![lcd14](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd14.png)

Scroll down. Fill in your Linux machine's login username and password. Give your service connection a name. After you are ready, click **Save**.

![lcd15](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd15.png)

The SSH Connection to your Linux machine has been created.

![lcd16](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd16.png)

Go back to your release pipeline. Give your task a name.

In the **SSH service connection** section, choose "SSH to ub01" (the one you just created).

In the **Commands** section. Add the command for creating a directory on the Linux machine where you deploy your web application.

![lcd17](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd17.png)

Add another task. In the **Add tasks** page, type "ssh" in the search bar, select **Copy files over SSH** and click **Add**.

![lcd18](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd18.png)

Give your task a name. In the **Source folder** section, click the **Browse** button.

![lcd18-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd18-1.png)

Point to the package you received from the build pipeline.

![lcd19](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd19.png)

In the **Target folder** section, point to the directory you just created in the above task so the pipeline can transfer the package over your Linnux machine.

![lcd20](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd20.png)

Add another task. In the **Add tasks** page, type "ssh" in the search bar, select **SSH** and click **Add**.

![lcd21-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd21-1.png)

Give your task a name. In the **Commands** section. Add the command for making the DevOpsWeb file executable.

![lcd21-2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd21-2.png)

After you are ready, click **Save**.

![lcd22-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd22-1.png)

Click OK.

![lcd23](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd23.png)

Click **Create release**.

![lcd24-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd24-1.png)

Leave everything as default and click **Create**.

![lcd25](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd25.png)

A release has been created. Click on "Release-1" for more details.

![lcd26-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd26-1.png)

Depending on load, the build may need to wait in the queue for a moment.

In the **Stages** section, it shows that there are 3 tasks in the deployment process. 

![lcd27](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd27.png)

The deployment process should eventually succeed. Click on "Logs" to view more details.

![lcd28](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd28.png)

Just like with the build pipeline, this page show the status of the deployment process's tasks, you can review the logs of each of your tasks here.

![lcd29](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd29.png)

The release pipeline has successfully completed. Head over to your virtual machine to check the results.

![lcd30](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd30.png)

When you are inside your Linux machine:

You can check your web app directory.

![web1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web1.png)

Check your web app service.

![web2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web2.png)

Or check the status of your nginx server.

![web3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web3.png)

After you are ready, open up a web browser and access your website by using your Linux machine's Public Ip Address.

![web4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web4.png)

## 5. Continuous Integration and Continuous Deployment with Azure Pipelines  <a name="5"></a>

Visit your Github repository. Make some minor changes to your source code, by tweaking the contents of the document. 

![web5-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web5-1.png)

![web5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web5.png)

Make sure to commit after you edit it. This will trigger the build pipeline in Azure Devops. Select **Pipelines** > **Pipelines** to see if it’s already started.

![lcd31](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd31.png)

You should now see that a new build (note the **.2**) is in progress and that it was triggered by your change. Click the build to track it. 

![lcd32](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd32.png)

This build should run and succeed just like the previous build. It also gives a new package for deployment.

![lcd33](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd33.png)

On the left pane menu, click **Pipelines** > **Release**. You can see your release pipeline has also been triggered because of the new artifact it received from the build pipeline.

![lcd34](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd34.png)

Click on **Release-2** to access your running release pipeline. The deployment tasks are in process.

![lcd35](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd35.png)

The new release pipeline has successfully completed. If you want to review a specific task, you can just click on it in the right pane and view its logs.

![lcd36](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd36.png)

As you can see, your pipeline was configured to support continuous integration and continuous deployment. After you make some changes in the source code for this project, Azure Devops can help you automate the build-release process, allow new build to be released with just one button push.

![lcd37](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd37.png)

![lcd38](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd38.png)

![lcd39](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/lcd39.png)

Just refresh your web page again to check the result.

![web6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images2/web6.png)
