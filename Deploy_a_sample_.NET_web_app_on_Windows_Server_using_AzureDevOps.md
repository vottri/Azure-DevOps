

Create Azure VM

### Create Windows Server Virtual Machine on Azure 

Allow Remote Desktop to the Azure VM in "Inbound port rules" section

Go back to "Overview" tab, click "Connect", select "RDP". Select "Download RDP File".

Open the downloaded RDP file and select **Connect** when prompted. 

In the Windows Security window, enter the **user name and password** when you created Azure VM ealier.

Select **Yes** to verify the identity of the virtual machine and finish logging on.

### Installing IIS Server

Inside the virtual machine:

On Windows Server, go to Server Manager. Click on the "Add roles and features" on the dashboard. Click ***Next*** to proceed.

Select "Role-based or feature-based installation", and then click ***Next***.

Select the server.

![iis1](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/iis1.png)

Choose the "Web Server" option.

![iis2](https://raw.githubusercontent.com/vottri/CICD-pipeline-with-Jenkins/main/images1/iis2.png)

Choose as below.



Continue to proceed with below options.



In the final screen, click the "Install" button to begin the installation. Once IIS has been installed, click "Close".

In "Server Manager" Dashboard. Go to "Tools". Open "Internet Information Services Manager".

In IIS, you will have an initial site set up called "Default Web Site".

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

Create Deployment Group




