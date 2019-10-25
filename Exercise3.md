# Exercise 3: Deploy VM Scale Set (15 minutes)

•	A VM scale set (VMSS) is a way to deploy a number of similar VMs with a single command – you can use a scale set to deploy between 0 to 1000 VMs. Scale sets have built-in high availability and integrate with Azure auto-scale.<br/>
•	We have used the portal to create a VM, so let's use the Azure CLI to deploy a VMSS and an app to it.<br/>

#### 3.1 Launch Cloud Shell

1. Select the **Cloud Shell** icon from the upper right corner of the Azure Portal.<br/>
<img src="images/azureclisign.png"/><br/>
2. Select **BASH** from drop down in cloud shell window.<br/>
3. Use your existing bash storage account which we provisioned in **Exercise 1**.<br/>
#### 3.2 Create a Scale Set

1. Select cloud shell and clone this repo: https://github.com/asinn826/Ignite2019VMSS-HOL usin below command:-<br/>
``
wget https://github.com/asinn826/Ignite2019VMSS-HOL
``
2. To dispaly repo content use below command:-<br/>
``
cd Ignite2019VMSS-HOL
``
3. Create new Resource Group for Virtual machine Sacle Set deployment using below command:-
``
az group create --name <resource group name> --location <location – you can use westus>
``
4. Now, create the deployment by using your new resource group!<br/>
``
az group deployment create -g <resource group name> -n <deployment name> --template-file azuredeploy.json –parameters @azuredeploy.parameters.json 
``
(Now, wait for the deployment to finish.)<br/>

**Familiarizing yourself with your VM scale set (5 minutes)**

1. Go to the Azure Portal, navigate to your resource group, and click on your newly-created VMSS.<br/>
2. Go to Instances. Note that you only have one – this was defined by the template.<br/>
3. Go to individual instance. Here you can view details for your individual VMSS instance - you can restart, deallocate, reimage, or upgrade instance.<br/>

**Note: What is upgrading?**

- VM scale sets work on a model basis - there is a model that the scale set follows, and all VMs within the scale set have the same configuration as defined in the model.<br/>
- If the model changes, you will need to upgrade the instance(s) to the latest model - hence the Upgrade button in the UI (you can also configure scale sets to automatically upgrade when the model changes)<br/>

•	In the instance view, you can also access features like Azure Bastion, Serial console, and Boot diagnostics.<br/>
Note that none of these were configured initially in your VM scale set, so you will need to upgrade the scale set model and then update individual instances to use them.<br/>
There is a bonus section to this lab where you can try this for yourself.
