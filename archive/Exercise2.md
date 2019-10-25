# Exercise 2: Work with CloudShell

### 2.1 Launch Cloud Shell

1. Select the **Cloud Shell** from the upper right corner of the Azure Portal.<br/>
<img src="images/azureclisign.png"/><br/>
2. Select **BASH** in cloud shell window.<br/>
3. Select **Show Advance Setting**.<br/>
4. Create a storage accounr for Bash, provide unique name for **Storage Account**, **File share** and then click on **Create Storage**.<br/>
<img src="images/bashst.png"/><br/>

### 2.2 Get Public IP of Virtual Machine Using Azure CLI

1. Run following command to get **Public IP** of your virtual machine.<br/>
  * g :- Enter you **Resource Group** name.<br/>
  * n :- Enter  your **Virtual Machine** name.<br/>
``
az vm show -d -g ODL-linux-XXXX -n VMname --query publicIps -o tsv
``

### 2.3 SSH to VM using Public IP

1. Run below command to take **SSH** of your virtual machine.<br/>
  * x.x.x.x :- Replace this with your virtual machine **Public IP**.<br/>
``
ssh azureuser@x.x.x.x
``<br/>
<img src="images/ssh.png"/><br/>

### 2.4 Perform Basic Operation Inside Virtual Machine

1. Run the following commands for the basic operations:<br/>
<img src="images/task.png"/><br/>

### 2.5 Access Serial Console of Virtual Machine

1. For using serial console of Ubuntu virtual machine navigate to the **Resource Group->Virtual Machine->Overview->Support + Troubleshooting->Serial Console**.<br/>
<img src="images/serialconsole.png "/><br/>
