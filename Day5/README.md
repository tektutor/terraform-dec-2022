# Day 5

## Azure App Service Plan SKU
<pre>
Free Tier → F1
Shared Tier → D1
Basic Tier → B1, B2, B3 (Basic Small, Medium, Large)
Standard Tier → S1, S2, S3 (Small, Medium, Large)
PremiumV2 Tier → P1v2, P2v2, P3v2 (Small, Medium, Large)
</pre>

## What is Continuous Integration (CI)?
- the code you wrote is integrated with dev branch several times a day, which triggers a build and automated test execution
- this ensures your code commit hasn't broken any existing functions as it runs your newly added test cases and existing test cases
- this is a fail-fast approach followed in teams that follow any Agile Frameworks( SCRUM, Kanban, XP, etc., )

## What is Continuous Delivery (CD)?
- is an extension of Continuous Integration (CI) since it automatically deploys all code changes to a testing and/or production environment after the build stage. 

- deploying and testing in multiple environments improves quality

## What is Continuous Deployment (CD)?
- goes one step further than continuous delivery
- With this practice, every change that passes all stages of your production pipeline is released to your customers 
- There's no human intervention, and only a failed test will prevent a new change to be deployed to production

## What is DevOps?
- is the combination of cultural philosophies, practices, and tools that increases an organization’s ability to deliver applications and services at high velocity
- enables organizations to better serve their customers and compete more effectively in the market

## Getting familiar with Azure Pipeline Jargons
- Azure Pipeline
- Stage
- Steps
- Trigger
- Job
- Agent
- Task
- Artifact

## What is Azure Pipeline?
- automatically builds and tests code projects to make them available to others
- it supports almost any programming language 
- combines continuous integration (CI) and continuous delivery (CD) to test and build your code and ship it to any target

## Azure Self-hosted agent binaries for all supported Operating Systems can be found here
<pre>
https://github.com/Microsoft/azure-pipelines-agent/releases
</pre>

## Lab - Creating a self-hosted Azure Pipeline agent to run Terraform scripts

#### Create a Personal Access Token
From your Azure portal, search for "Azure DevOps organizations" and click the same
![Azure DevOps Organizations](azure1.png)

You will see below page now, click on "My Azure DevOps Organizations"
![Azure DevOps Organizations](azure2.png)


Click on "Create new organization"

Once you have created your new organization, create a Personal Access Token with Full access and save the token in your system in some text file.  This token will be required to register your self-hosted pipeline agent with your project.
![Azure DevOps Organizations](azure3.png)

1. Provision an Ubuntu 20.04 Virtual Machine in your azure portal.
2. Install azure cli, terraform cli and ansible
```
sudo apt update
sudo apt install -y git ansible tree vim default-jdk maven docker.io
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
sudo snap install terraform --classic
sudo usermod -aG docker $USER
sudo su $USER
docker --version
docker images
mvn --version
javac -version
git -version
az version
terraform version
```
3. From the terminal, type the below command, copy the code and paste the code on the web page that popped up and login to your azure portal to authenticate.
```
az login
```
Copy the credentials details in some file for your future reference.


4. Install the below from terminal to configure the Azure Ubuntu VM as a self-hosted Azure Pipline agent
```
mkdir myagent && cd myagent
wget https://vstsagentpackage.azureedge.net/agent/2.213.2/vsts-agent-linux-x64-2.213.2.tar.gz
tar zxvf vsts-agent-linux-x64-2.213.2.tar.gz
ls
./config.sh
./run.sh
```

Expected output
<pre>
azureuser@my-terraform-agent-vm:~/myagent$ <b>ls</b>
bin  config.sh  env.sh  externals  license.html  run-docker.sh  run.sh  vsts-agent-linux-x64-2.213.2.tar.gz
azureuser@my-terraform-agent-vm:~/myagent$ <b>./config.sh</b>

  ___                      ______ _            _ _
 / _ \                     | ___ (_)          | (_)
/ /_\ \_____   _ _ __ ___  | |_/ /_ _ __   ___| |_ _ __   ___  ___
|  _  |_  / | | | '__/ _ \ |  __/| | '_ \ / _ \ | | '_ \ / _ \/ __|
| | | |/ /| |_| | | |  __/ | |   | | |_) |  __/ | | | | |  __/\__ \
\_| |_/___|\__,_|_|  \___| \_|   |_| .__/ \___|_|_|_| |_|\___||___/
                                   | |
        agent v2.213.2             |_|          (commit 4f90e68)


>> End User License Agreements:

Building sources from a TFVC repository requires accepting the Team Explorer Everywhere End User License Agreement. This step is not required for building sources from Git repositories.

A copy of the Team Explorer Everywhere license agreement can be found at:
  /home/azureuser/myagent/license.html

Enter (Y/N) Accept the Team Explorer Everywhere license agreement now? (press enter for N) > <b>N</b>

>> Connect:

Enter server URL > https://dev.azure.com/azurejegan
Enter authentication type (press enter for PAT) > 
Enter personal access token > ****************************************************
Connecting to server ...

>> Register Agent:

Enter agent pool (press enter for default) > 
Enter agent name (press enter for my-terraform-agent-vm) > 
Scanning for tool capabilities.
Connecting to the server.
Successfully added the agent
Testing agent connection.
Enter work folder (press enter for _work) > 
2022-11-24 23:37:14Z: Settings Saved.

azureuser@my-terraform-agent-vm:~/myagent$ <b>./run.sh</b>
Scanning for tool capabilities.
Connecting to the server.
2022-11-25 00:15:55Z: Listening for Jobs
</pre>

## ⛹️‍♂️ Lab -Creating an Linux Azure Pipeline self-hosted agent using Terraform
```
cd ~/terraform-dec-2022
git pull
cd Day5/self-hosted-azure-pipeline-linux-agent

export AZDO_ORG_SERVICE_URL=https://dev.azure.com/jegantektutor
export AZDO_PERSONAL_ACCESS_TOKEN=your-azuredevops-organization-personal-access-token

terraform init
terraform apply --auto-approve  -var your_devops_organization_name=jegantektutor -var your_devops_organization_personal_access_token=your-pat
```

## ⛹️‍♂️ Lab -Creating a Windows Azure Pipeline self-hosted agent using Terraform
```
cd ~/terraform-dec-2022
git pull
cd Day5/self-hosted-azure-pipeline-windows-agent

export AZDO_ORG_SERVICE_URL=https://dev.azure.com/jegantektutor
export AZDO_PERSONAL_ACCESS_TOKEN=your-azuredevops-organization-personal-access-token

terraform init
terraform apply --auto-approve -var your_devops_organization_name=jegantektutor -var your_devops_organization_personal_access_token=your-pat
```

## ⛹️‍♂️ Lab - Creating an azure pipeline that invokes Terraform scripts

Create a Azure DevOps Pipeline using the below GitHub. Please fork the below GitHub repository to your GitHub account, before creating the Azure pipeline.
```
https://github.com/tektutor/terraform-azure-pipelines.git
```

##  ⛹️‍♂️ Lab - Storing Terraform state in Azure storage account to share with other team members
```
cd ~/terraform-dec-2022
git pull
cd Day5/centrally-stored-terraform-state

terraform init
terraform apply --auto-approve
```

Now you need to uncomment the backend block in the providers.tf file so that when you do 'terraform init' it would be able to migrate the local terraform state to the centralized azure storage account.
```
cd ~/terraform-dec-2022
git pull
cd Day5/centrally-stored-terraform-state

terraform init
```
When it prompts to migrate, you say 'yes'

You may go to your azure portal, locate the storage account, container and then download and verify the terraform state stored in the azure storage account.


## Terraform import
- Let's day your IT Team created a Virtual Machine manually 
- In case you wish to use the existing Virtual Machine in your Terraform script, you need to import the details of existing Azure VM by creating resource block with dummy mandatory parameters and import as shown below
```
terraform import azurerm_virtual_machine.existing /subscriptions/9eb2e122-36af-4b34-9186-2cc8053669e3/resourcegroups/centralized-state-rg/providers/Microsoft.Compute/virtualMachines/vm1
```

#### Points to note
If you perform terraform destroy, the imported resources will also be deleted just like other resources


## Terraform datasource (data block)
- Let's day your IT Team created a Virtual Machine manually 
- In case you wish to use the existing Virtual Machine in your Terraform script but you don't want Terraform to manage the existing resource ( i.e create/update/delete ) then you can follow the below instructions
- create a main.tf with data blocks with the mandatory argruments as per Terraform documentation
- Perform a terraform init
- Perform a terraform apply


## Please complete the Terraform post-test
<pre>
https://app.mymapit.in/code4/tiny/s7t2aj
</pre>

 
## Kindly provide your Feedback link 
<pre>
https://tcheck.co/nts888
</pre>
