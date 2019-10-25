# Exercise 3: Deploy VM Scale Set (15 minutes)

•	A VM scale set (VMSS) is a way to deploy a number of similar VMs with a single command – you can use a scale set to deploy between 0 to 1000 VMs. Scale sets have built-in high availability and integrate with Azure auto-scale.<br/>
•	We have used the portal to create a VM, so let's use the Azure CLI to deploy a VMSS and an app to it.<br/>

#### 3.1 Launch Cloud Shell

1. Select the **Cloud Shell** icon from the upper right corner of the Azure Portal.<br/>
<img src="images/azureclisign.png"/><br/>
2. Select **BASH** from drop down in cloud shell window.<br/>
3. Use your existing bash storage account which we provisioned in **Exercise 1**.<br/>
#### 3.2 Create a Scale Set

1. Select **Cloud shell** and clone this repo: https://github.com/asinn826/Ignite2019VMSS-HOL usin below command:-<br/>
``
wget https://github.com/asinn826/Ignite2019VMSS-HOL
``
2. To dispaly repo content use below command:-<br/>
``
cd Ignite2019VMSS-HOL
``
3. Create new **Resource Group** for Virtual machine Sacle Set deployment using below command:-
``
az group create --name <resource group name> --location <location – you can use westus>
``
4. Now, create the deployment by using your new **Resource group**.<br/>
``
az group deployment create -g <resource group name> -n <deployment name> --template-file azuredeploy.json –parameters @azuredeploy.parameters.json 
``
(Now, wait for the deployment to finish.)<br/>

**Familiarizing yourself with your VM scale set (5 minutes)**

1. Go to the **Azure Portal**, navigate to your **Resource group**, and click on your newly-created VMSS.<br/>
2. Go to **Instances**. Note that you only have one – this was defined by the template.<br/>
3. Go to individual instance. Here you can view details for your individual VMSS instance - you can restart, deallocate, reimage, or upgrade instance.<br/>

**Note: What is upgrading?**

- VM scale sets work on a model basis - there is a model that the scale set follows, and all VMs within the scale set have the same configuration as defined in the model.<br/>
- If the model changes, you will need to upgrade the instance(s) to the latest model - hence the Upgrade button in the UI (you can also configure scale sets to automatically upgrade when the model changes)<br/>

•	In the instance view, you can also access features like Azure Bastion, Serial console, and Boot diagnostics.<br/>
Note that none of these were configured initially in your VM scale set, so you will need to upgrade the scale set model and then update individual instances to use them.<br/>
There is a bonus section to this lab where you can try this for yourself.

**Use autoscale rules on your VM scale set (25 minutes)**
1. Go to **Scaling** in the VMSS. Currently, the VMSS is set to automatic scaling.<br/>
2. The VMSS will scale automatically based on load as measured by % CPU usage.<br/>

**Autoscale via the deployed web application**

1. Go back to the **Overview** section for your scale set.<br/>
2. Copy the **Public IP address**, and navigate to **<ip-address>:9000** in your browser.<br/>
  •	You will see a landing page that looks like:<br/>
3. To view the autoscale in action, simply click **Start work** on the page.<br/>
4. Then, go back to the VM scale set in the Azure portal and watch its CPU rise Once **CPU > 60%**, a new scale set instance will automatically be created.<br/>
5. Go to **Instances** and watch VMs get created.
6. You can also **SSH** into your individual instance by running below command in Cloud shell:-<br/>
  
 ``
 ssh <adminusername>@<ip-address> -p 50000
 ``
 
**Autoscale manually in the Azure Portal based on date/time**

1. Go back to the **Portal** and go to **Scaling**, and let's try out creating a scheduled autoscale rule (https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview#scheduled-autoscale)
2. Click **Custom Autoscale**, and note all the options you have.<br/>
3. Leave the auto created scale condition with the **Default Settings**.<br/>
4. Click on **Add a scale set condition**.<br/>
- Select **Sale to a specific instance count**.<br/>
- Set Instance count to a **Random number** >1 and <100.<br/>
- Select **Specify start/end dates**.<br/>
- **Timezone**: (UTC-05:00) Eastern Time (US & Canada).<br/>
- **Start date**: today, November 15, 2019, and a time a minute or two in the future<br/>
- **End date**: today, November 15, 2019, and a time several minutes in the future<br/>

**Bonus section (optional): configure your VMSS for serial console (10-15 extra minutes)**

1. For add a password to your VMSS goto the **Reset Password**.<br/>
2. Enter **Username** and **Password**.<br/>
3. Open up **Serial Console** in your VMSS instance and get the boot diagnostics error.<br/>
4. Update the VMSS model to enable **Boot Diagnostics**, then **Save**.<br/>

 


