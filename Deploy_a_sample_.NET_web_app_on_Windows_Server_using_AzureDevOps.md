# Deploy a sample .NET web app on Windows Server virtual machine using AzureDevOps

============================================================================================

**Contents**

[1. Create Azure VM](#1)

[2. Set up Azure DevOps](#2)

[3. Create Deployment Group](#3)

[4. Create the Azure DevOps Build Pipeline](#4)

[5. Create the Azure DevOps Release Pipeline](#5)

============================================================================================

## 1. Create Azure VM <a name="1"></a>

Login to Azure portal with your Microsoft Azure account.

### Create a Windows Server Virtual Machine on Azure (OS: Windows Server 2019 Datacenter)

![vm1-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm1-1.png)

### Connect to your Virtual Machine

Navigate to "Networking" tab > "Inbound port rules" section, allow your VM's ports 80, 443, 3389 to be accessible from the Internet.

![vm2-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm2-1.png)

In the left menu, go down to "Connect" tab, select "RDP". In the "Connect with RDP" page, Select "Download RDP File".

![vm3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm3.png)

Open the downloaded RDP file and select **Connect** when prompted. 

In the Windows Security window, enter the **user name** and **password** when you created Azure VM ealier.

![vm4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm4.png)

Select **Yes** to verify the identity of the virtual machine and finish logging on.

![vm5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm5.png)

### Installing IIS Server

When you are inside the remote Windows Server virtual machine:

On Windows Server, go to Server Manager. Click on the "Add roles and features" on the dashboard. Click **Next** to proceed.

Select "Role-based or feature-based installation", and then click **Next**.

Select the server.

![iis1](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/iis1.png)

Choose the "Web Server" option.

![iis2](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/iis2.png)

Choose as below.

![iis19-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/iis19-1.png)

Continue to proceed with below options.

![iis19-2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/iis19-2.png)

In the final screen, click the "Install" button to begin the installation. Once IIS has been installed, click "Close".

In "Server Manager" Dashboard. Go to "Tools". Open "Internet Information Services Manager".

In IIS, you will have an initial site set up called "Default Web Site".

![iis19-3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/iis19-3.png)

### Download and Install ASP.NET Core Runtime 3.1 

Download ASP.NET Core Runtime 3.1 (https://dotnet.microsoft.com/en-us/download/dotnet/3.1).

![dotnet2](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/dotnet2.png)

Double-click the downloaded installation package, tick "I agree to license terms and conditions" then click "Install".

![dotnet4](https://github.com/vottri/CICD-pipeline-with-Jenkins/blob/main/images1/dotnet4.png)

Click "Close" once it has been installed successfully.

![dotnet5](https://github.com/vottri/CICD-pipeline-with-Jenkins/blob/main/images1/dotnet5.png)

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

## 3. Create Deployment Group <a name="3"></a>

A deployment group is a logical set of deployment target machines that have agents installed on each one. Every machine of the deployment group interacts with Azure Pipelines to coordinate the deployment tasks.

You have already created a virtual machine. Now, you have to install an agent on the virtual machine which will make it connect to your particular Azure DevOps Project.

In your Azue Devops Project page, select "Pipelines" > "Deployment groups", then click on "Add a deployment group".

![dg1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg1.png)

Enter a Deployment group name and then select "Create". 

![dg2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg2.png)

A registration script will be generated. Select the "Type of target to register" and then select "Use a personal access token in the script for authentication". Finally, select "Copy script to the clipboard".

![dg3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg3.png)

Log onto your target virtual machine. Run Powershell as administrator and paste the script you copied. Hit "Enter" to run the script. This will register your virtual machine as a target server for deployment. 

![dg4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg4.png)

When prompted for username and password, enter the username and password you used to log onto this particular virtual machine. If the result looks like this, you have successfully installed the agent on this virtual machine.

![dg5-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg5-1.png)

## 4. Create the Azure DevOps Build Pipeline <a name="4"></a>

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

Give your new pipeline a name. Navigate to "Tasks" tab > Pipeline. In the **Agent Specification** section, choose "windows-2019".

![ci7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci7.png)

Check your Github repository.

![ci8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci8.png)

Leave everything else in "Agent job 1" as default. Click "Save and queue".

![ci9](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci9.png)

Click "Save and run".

![ci10](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci10.png)

As soon as the agent is available, your build pipeline will start running. Click "Agent job 1" to view more details.

![ci11](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci11.png)

In here, you see the steps that the build pipeline created. It fetches source code from GitHub, restore the neccesary packages, then builds and publishes the app.

![ci12](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci12.png)

When your pipeline completes, you can go back and view the package. Click on "1 published".

![ci13-1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci13-1.png)

And this is where your package is. You will need it for deployment toward Azure VM.

![ci14](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci14.png)

## 5. Create the Azure DevOps Release Pipeline <a name="5"></a>

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

![cd17](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd17.png)

Open IIS Manager, click on "Default Web Site" and switch to "Content View". 

![cd18](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd18.png)

Open a web browser, access your website with your VM's public IP Address.

![cd20](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd20.png)

Now, we finish deploying a web application onto a virtual machine by using Azure DevOps.
