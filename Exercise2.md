# Exercise 2: Deploy an Azure Virtual Machine Scale Set
Duration: 45 Minutes

A VM scale set (VMSS) is a way to deploy several similar VMs with a single command – you can use a scale set to deploy between 0 to 1000 VMs. Scale sets have built-in high availability and integrate with Azure auto-scale. In the last exercise, we used the **Azure Portal** to create a VM, so let's use the Azure CLI and ARM Template to deploy a VMSS and an app to it. 


### 2.1 Create a VM Scale Set
In this exercise, You'll be creating a **Virtual Machine Scale Set** using the Azure Cloud Shell and ARM templates. Let's get started.

1.Select the **cloud shell** icon from the upper right corner of the Azure Portal.

   ![](images/azureclisign.png)
   
2.Select **Bash** once prompted to start the **cloud shell**.

3.Clone this repo: https://github.com/asinn826/Ignite2019VMSS-HOL to your **cloud shell** by running the following command:

       git clone https://github.com/asinn826/Ignite2019VMSS-HOL

   ![](images/github.png)
   
4.Run the following command to change the present directory to a newly cloned repository. Run **ls** command to review the contents of the repository. 
  
       cd Ignite2019VMSS-HOL
       ls
       
   ![](images/gitcontent.png)
   
5.This repository contains the ARM template and Parameter file which will provision a Virtual Machine Scale set and deploy an application on it. You can review by template files by browsing https://github.com/asinn826/Ignite2019VMSS-HOL in a separate browser tab. 
   
6.Now, you'll need to edit the **azuredeploy.parameters.json** to provide your deployment specific values. Run the following command to open the parameters file in the **visual studio code**. Please ensure to modify the **vmssName** and **adminSshKey** values in the parameters file. 
      
       code azuredeploy.parameters.json
       
   > vmssName: Give a unique name for your VM scale set.
   
   > adminSshKey: Paste your **Public key** created during the first exercise. 

   ![](images/editprameter.png)
   
7.Save and close the **code editor** once the values are replaced. 

8.Now, let's create the deployment by running the below command. Please ensure to use your existing **resource group** named as **linux-empty-XXXX** and wait for the deployment to finish. 

   ``az group deployment create -g linux-empty-XXXX --template-file azuredeploy.json --parameters azuredeploy.parameters.json 
   ``

   ![](images/deployed.png)
   
**What did you just do ?**

It'll take 5 to 7 minutes for the deployment to complete. Meanwhile, let us review what did we just do. 

   * The files azuredeploy.json and azuredeploy.parameters.json are most relevant here.

   * The command above just deployed a virtual machine scale set (https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/overview).

   * VM scale sets are great for scaling out applications – for example, if you are running a workload and you anticipate a spike in holiday-related traffic, the VMSS can scale-out automatically to meet your compute needs, and scale back in when traffic subsides.
   
   * The template deployed the VMSS using a Red Hat Enterprise Linux (RHEL) image, one of the many available images in the Azure Marketplace.
   
   * The template also used cloud-init to deploy a simple web application onto the VM. The cloud-init script runs once, at the beginning of the deployment, to configure the VM to its desired end state.
   
   * Note about cloud-init: the RHEL image we chose has cloud-init enabled, which means that cloud-init is the provisioning agent.
      - We are bringing cloud-init to Azure VM images, so this will slowly become the default option.
      - If you are familiar with cloud-init from other environments, this will function exactly the same.
      - Ask the lab proctors for more details if you’re curious.
      

### 2.2 Familiarizing yourself with your VM scale set

1.In the Azure portal, navigate to your **resource group**, and click on your newly-created **Virtual Machine Scale Set**.

2.Go to the **Instances**. Note that you only have one instance, as per the instance count defined in ARM Template.

   ![](images/scalesetinstances.png)
   
3.Go to the individual instance. Here you can view details for your individual VMSS instance - you can restart, deallocate, reimage, or upgrade the instance.

**Note: What is upgrading?**

   * VM scale sets work on a model basis - there is a model that the scale set follows, and all VMs within the scale set has the same configuration as defined in the model.

   * If the model changes, you will need to upgrade the instance(s) to the latest model - hence the upgrade button in the UI (you can also configure scale sets to automatically upgrade when the model changes).

   * If you have more questions, ask a proctor.

   * In the instance view, you can also access features like Azure Bastion, Serial console, and Boot diagnostics.

   * Note that none of these were configured initially in your VM scale set, so you will need to upgrade the scale set model and then update individual instances to use them.

   * There is a bonus section to this lab where you can try this for yourself.
 
 In the instance view, you can also access features like Azure Bastion, Serial console, and Boot diagnostics.
 
   * Note that none of these were configured initially in your VM scale set, so you will need to upgrade the scale set model and then update individual instances to use them
   * There is a bonus section to this lab where you can try this for yourself



### 2.3 Use autoscale rules on your VM scale set
An Azure virtual machine scale set can automatically increase or decrease the number of VM instances that run your application. You create rules that define the acceptable performance for positive customer experience. When those defined thresholds are met, autoscale rules take action to adjust the capacity of your scale set. You can also schedule events to automatically increase or decrease the capacity of your scale set at fixed times. Let us review the auto-scale settings for your VMSS.

1.Go to the **Scaling** in the VMSS. Currently, the VMSS is set to automatic scaling.

2.The VMSS will scale automatically based on load measured by CPU Utilization % of the VMSS Instances. Currently, it's set to increase the VMSS by 1 instance if the CPU utilization goes beyond 60% and decrease by 1 instance if CPU utilization goes lower than 30%. Additionally, there're minimum and maximum number of instances defined.  

   ![](images/autoscalerule.png)


### 2.4 Autoscale via the deployed web application
In this exercise, we'll try to generate load on our newly created application hosted on VMSS. Let's get started.

1.Go back to the **Overview** section for your scale set.

2.Copy the **Public IP address**, open a new tab in the browser and paste the public ip address with port number 9000 (**publicIpaddress:9000**). You will see a landing page that looks like:
   
   ![](images/output.png)
   
3.To view the autoscale in action, simply click the **start work** on the page.

4.Then, go back to the VM scale set in the Azure portal and watch its CPU rise. Once CPU Utilization is higher than 60%, a new scale set instance will be created automatically.

   ![](images/cpuutilization.png)
   
5.Go to the **Instances** and watch VM getting created.

6.You can also **SSH** into your individual instance by running below command in the Cloud Shell:-
  
       ssh adminusername@<ip-address -p 50000
 
   ![](images/sshvmss.png)


### 2.5 Autoscale manually in the Azure Portal based on date/time

1.In the **Azure Portal** and go to the **Scaling**, and let's try out creating a scheduled autoscale rule. (https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview#scheduled-autoscale)

2.Click the **Custom Autoscale**, and note all the options you have.

   ![](images/4.png)
   
3.Leave the auto-created scale condition with the **Default Settings**.

4.Click on the **Add a scale set condition**.

   - Select the **Scale to a specific instance count**.
   - Set Instance count to a **2**.
   - Select the **Specify start/end dates**.
   - **Timezone**: (UTC-05:00) Eastern Time (US & Canada).
   - **Start date**: Enter time of 2 minutes from now to test the scheduler.
   - **End date**: Enter time of 10 minutes from now to test the scheduler.

     ![](images/autorule.png)


### 2.6 Additional Autoscale documentation and information

  * See the docs here for more info on autoscale rules:
     - https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview 
     - https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal
  * The following examples are scenarios that may benefit the use of schedule-based autoscale rules:

Automatically scale out the number of VM instances at the start of the workday when customer demand increases. At the end of the workday, automatically scale in the number of VM instances to minimize resource costs overnight when application use is low.
  * If a department uses an application heavily at certain parts of the month or fiscal cycle, automatically scale the number of VM instances to accommodate their additional demands.
  * When there is a marketing event, promotion, or holiday sale, you can automatically scale the number of VM instances ahead of anticipated customer demand.
  * The following scenarios may benefit from load-based autoscale:
     - Elastic loads with no set schedule – DevOps builds for an organization, a web server that can receive traffic from anywhere at any time.
   
    > Note: You can combine multiple scale-out and scale-in conditions.


### 2.7 Bonus section (optional): configure your VMSS for serial console (10-15 extra minutes)

1.To add a password to your VMSS go to the **Reset Password**.

2.Enter the **Username** and **Password**.

   ![](images/resetscalinstances.png)
   
3.Open up the **Serial Console** in your VMSS instance and get the boot diagnostics error.

   ![](images/6.png)
   
4.Update the VMSS model to enable **Boot Diagnostics**, then **Save**.

   ![](images/7.png)

#### Congratulations! 
#### Now we have the VM and VMSS up and running!
#### This concludes the lab. 
