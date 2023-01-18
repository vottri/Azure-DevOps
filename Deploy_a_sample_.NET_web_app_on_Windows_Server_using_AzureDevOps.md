============================================================================================

Contents

[1. Create Azure VM](#1)

[2. Create Deployment Group](#2)

[3. Create the Azure DevOps Build Pipeline](#3)

[4. Create the Azure DevOps Release Pipeline](#4)

[5. web](#5)

============================================================================================

## 1. Create Azure VM <a name="1"></a>

### Create a Windows Server Virtual Machine on Azure (OS: Windows Server 2019 Datacenter)

![vm1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm1.png)

### Connect to your Virtual Machine

Navigate to "Networking" tab, allow Remote Desktop to the Azure VM in "Inbound port rules" section.

![vm2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm2.png)

Go back to "Overview" tab, click "Connect", select "RDP". In the new page, Select "Download RDP File".

![vm3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm3.png)

Open the downloaded RDP file and select **Connect** when prompted. 

In the Windows Security window, enter the **user name and password** when you created Azure VM ealier.

![vm4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm4.png)

Select **Yes** to verify the identity of the virtual machine and finish logging on.

![vm5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/vm5.png)

### Installing IIS Server

Inside the virtual machine:

On Windows Server, go to Server Manager. Click on the "Add roles and features" on the dashboard. Click ***Next*** to proceed.

Select "Role-based or feature-based installation", and then click ***Next***.

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

Set up Azure Devops

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

## 2. Create Deployment Group <a name="2"></a>

![dg1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg1.png)

![dg2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg2.png)

![dg3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg3.png)

![dg4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg4.png)

![dg5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/dg5.png)


## 3. Create the Azure DevOps Build Pipeline <a name="3"></a>

![ci1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci1.png)


![ci2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci2.png)


![ci3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci3.png)


![ci4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci4.png)

![ci5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci5.png)


![ci6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci6.png)


![ci7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci7.png)


![ci8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci8.png)


![ci9](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci9.png)


![ci10](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci10.png)


![ci11](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci11.png)


![ci12](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci12.png)


![ci13](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci13.png)



![ci14](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/ci14.png)

## 4. Create the Azure DevOps Release Pipeline <a name="4"></a>

![cd1](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd1.png)

![cd2](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd2.png)

![cd3](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd3.png)

![cd4](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd4.png)

![cd5](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd5.png)


![cd6](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd6.png)

![cd7](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd7.png)

![cd8](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd8.png)

![cd9](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd9.png)

![cd10](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd10.png)

![cd11](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd11.png)

![cd12](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd12.png)

![cd13](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd13.png)

![cd14](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd14.png)

![cd15](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd15.png)

![cd16](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd16.png)


![cd17](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd17.png)


![cd18](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd18.png)


![cd19](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd19.png)


![cd20](https://raw.githubusercontent.com/vottri/Azure-DevOps/main/images1/cd20.png)


