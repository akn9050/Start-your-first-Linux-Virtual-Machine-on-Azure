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
- If the model changes, you will need to upgrade the instance(s) to the latest model - hence the Upgrade button in the UI (you can also configure scale sets to automatically upgrade when the model changes)

•	In the instance view, you can also access features like Azure Bastion, Serial console, and Boot diagnostics
Note that none of these were configured initially in your VM scale set, so you will need to upgrade the scale set model and then update individual instances to use them
There is a bonus section to this lab where you can try this for yourself
 




1. Create a virtual machine scale set using **az vmss create** command. This will automatically deploy required resorces such as a Pulic IP, Loadbalancer, Loadbalancing rules, Backend Pools, etc.<br/>
 * resource-group :- Enter your **Resource Group** name.</br>
 * name :- Enter **Scale Set** name.</br>
 * admin-username :- Enter **Admin User** name.</br>
``
az vmss create --resource-group ODL-linux-XXXX --name myScaleSet --image UbuntuLTS --upgrade-policy-mode automatic --custom-data cloud-init.yaml --admin-username azureuser --generate-ssh-keys
``

<img src="images/vmss.png "/><br/>
2. Navigate to **Azure portal** go to **Resourse Group->Load Balancer**. Copy the name of **Load Balancer** and **Backend Pools** for next step.<br/>
<img src="images/LBname.png "/><br/>
3. To allow traffic to reach the web app, create a rule with **az network lb rule create** command. Please provide the following values for running the below command:<br/>
 * resource-group :- Enter your **Resource Group** name.<br/>
 * name  :- Enter name for **Load Balancer Rule**.<br/>
 * lb-name :- Enter your **Load Balancer** name that you copied in previous step.<br/>
 * backend-pool :- Enter your **Backend Pool** name that you copied in previous step.<br/>
``
az network lb rule create --resource-group ODL-linux-XXXX --name myLoadBalancerRuleWeb --lb-name myScaleSetlb --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --protocol tcp
``

<img src="images/loadbalncer.png "/><br/>
4. To view a list of VMs running in your scale set, use **az vmss list-instances** command. Please provide the following values while running the below command:<br/>
 * resource-group :- Enter your **Resource Group** name.<br/>
 * name :- Your **Scale Set** name.<br/>
``
az vmss list-instances --resource-group ODL-linux-XXXX --name myScaleSet --output table
``

<img src="images/instance.png"/><br/>
5. To see your Node.js app on the web, obtain the public IP address of your load balancer with **az network public-ip show** command.  Please provide the following values while running the below command:<br/>
 * resource-group :- Enter your **Resource Group** name.<br/>
 * name :- Your **Scale Set** name.<br/>
``
az network public-ip show --resource-group ODL-linux-XXXX --name myScaleSetLBPublicIP  --query [ipAddress]  --output tsv
``

<img src="images/publicipdisplay.png"/><br/>
6. Enter the public IP address in to a web browser. The app is displayed, including the hostname of the VM that the load balancer distributed traffic to.<br/>
<img src="images/output.png"/><br/>

#### 3.4 Define an autoscale profile

1. To create autosacle in scaleset use **az monitor autoscale create**. Please provide the following values while running the below command:<br/>
 * resource-group :- Enter your **Resource Group** name.<br/>
 * resource :- Enter your **ScaleSet** name.<br/>
 * name :- Enter any **AutoScale** name.<br/>
``
az monitor autoscale create --resource-group ODL-linux-XXXX --resource myScaleSet --resource-type Microsoft.Compute/virtualMachineScaleSets --name autoscale --min-count 2 --max-count 10 --count 2
``
<img src="images/autoscale1.png"/><br/>
2. To create a rule with az monitor autoscale rule create that increases the number of VM instances in a scale set when the average CPU load is greater than 70% over a 5-minute period use the below command. Please provide the following values while running the below command:<br/>
 * resource-group :- Enter your **Resource Group** name.<br/>
 * autoscale-name :- Enter your **autoscale-name** name.<br/>
``az monitor autoscale rule create --resource-group ODL-linux-XXXX --autoscale-name autoscale --condition "Percentage CPU > 70 avg 5m" -scale out 3
``
<img src="images/autoscale2.png"/><br/>
3. To Create another rule with az monitor autoscale rule create that decreases the number of VM instances in a scale set when the average CPU load then drops below 30% over a 5-minute period us below command. Please provide the following values while running the below command:<br/>
 * resource-group :- Enter your **Resource Group** name.<br/>
 * autoscale-name :- Enter your **autoscale-name** name.<br/>
``az monitor autoscale rule create --resource-group ODL-linux-XXXX --autoscale-name autoscale --condition "Percentage CPU < 30 avg 5m" --scale in 1
``

<img src="images/autoscale3.png "/><br/>
#### 3.5 Generate CPU load on scale set

1. To see list the address and ports to connect to VM instances in a scale set use **az vmss list-instance-connection-info**. Please provide the following values while running the below command:<br/>
 * resource-group :- Enter your **Resource Group** name.<br/>
 * name :- Enter your **ScaleSet** name.<br/>
 ``az vmss list-instance-connection-info --resource-group ODL-linux-XXXX --name myScaleSet
``

<img src="images/autoscale4.png"/><br/>
2. SSH to your first VM instance. Specify your own public IP address and port number with the -p parameter, as shown from the below command:<br/>
 * X.X.X.X :- Enter your **Public IP**.<br/>
``ssh azureuser@X.X.X.X -p 50000
``

<img src="images/autoscale5.png"/><br/>
3. Once logged in, install the stress utility. Start 10 stress workers that generate CPU load. These workers run for 420 seconds, which is enough to cause the autoscale rules to implement the desired action.<br/>
``sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
``

<img src="images/autoscale6.png"/><br/>
<img src="images/autoscale7.png"/><br/>
4. To confirm that stress generates CPU load, examine the active system load with the top utility:<br/>
``top  
``

<img src="images/autoscale8.png"/><br/>
5. Exit top, then close your connection to the VM instance. stress continues to run on the VM instance.<br/>
``Ctrl-c
exit
``
6. Connect to second VM instance with the port number listed from the previous **az vmss list-instance-connection-info**:<br/>
 * X.X.X.X :- Enter your **Public IP**<br/>
``ssh azureuser@X.X.X.X -p 50002
``

<img src="images/autoscale9.png"/><br/>
7. Install and run stress, then start ten workers on this second VM instance.<br/>
``sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
``

<img src="images/autoscale6.png"/><br/>
<img src="images/autoscale10.png"/><br/>
8. Close your connection to the second VM instance type **exit**. stress continues to run on the VM instance.<br/>
9. To monitor the number of VM instances in your scale set, use watch. It takes 5 minutes for the autoscale rules to begin the scale-out process in response to the CPU load generated by stress on each of the VM instances:<br/>
``watch az vmss list-instances --resource-group ODL-linux-XXXX --name myScaleSet --output table
``

<img src="images/autoscale11.png"/><br/>

