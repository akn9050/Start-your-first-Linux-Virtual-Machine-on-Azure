# Exercise 2: Deploy VM Scale Set (15 minutes)

A VM scale set (VMSS) is a way to deploy several similar VMs with a single command – you can use a scale set to deploy between 0 to 1000 VMs. Scale sets have built-in high availability and integrate with Azure auto-scale. We have used the portal to create a VM, so let's use the Azure CLI to deploy a VMSS and an app to it.

#### 2.1 Launch Cloud Shell

1. Select the **Cloud Shell** icon from the upper right corner of the Azure Portal.

   ![](images/azureclisign.png)
   
2. Select **BASH** from the drop-down in the cloud shell window.

#### 2.2 Create a Scale Set

1. Select **Cloud shell** and clone this repo: https://github.com/asinn826/Ignite2019VMSS-HOL using below command:-

       git clone https://github.com/asinn826/Ignite2019VMSS-HOL

   ![](images/github.png)
   
2. To display the repo contents use below command:-
  
       cd Ignite2019VMSS-HOL
       
   ![](images/gitcontent.png)
   
3. Edit your **azuredeploy.parameters.json**.

   ![](images/editprameter.png)
   
   > Note vmssNmae: Give unipe name for your VM scale set.
   > adminSshKey: Paste your **Public key**.
   
4. Now, create the deployment by running below command and select your **Resource group** name **linux-empty-unique-id** and wait for the deployment to finish.

   ``az group deployment create -g ODL-linux-XXXX -n <deployment name> --template-file azuredeploy.json --parameters azuredeploy.parameters.json 
   ``

   ![](images/deployed.png)
   
**Deployment notes to read through while you wait (what did I just do?)**

   * The files azuredeploy.json and azuredeploy.parameters.json are most relevant here.

   * The command above just deployed a virtual machine scale set (https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/overview).

   * VM scale sets are great for scaling out applications – for example, if you are running a workload and you anticipate a spike in holiday-related traffic, the VMSS can scale-out automatically to meet your compute needs, and scale back in when traffic subsides.
   * The template deployed the VMSS using a Red Hat Enterprise Linux (RHEL) image, one of the many available images in the Azure Marketplace.
   * The template also used cloud-init to deploy a simple web application onto the VM. The cloud-init script runs once, at the beginning of the deployment, to configure the VM to its desired end state.
   * Note about cloud-init: the RHEL image we chose has cloud-init enabled, which means that cloud-init is the provisioning agent.
      - We are bringing cloud-init to Azure VM images, so this will slowly become the default option.
      - If you are familiar with cloud-init from other environments, this will function exactly the same.
      - Ask the lab proctors for more details if you’re curious.

**2.3 Familiarizing yourself with your VM scale set (5 minutes)**

1. Go to the Azure portal, navigate to your resource group, and click on your newly-created VMSS.

2. Go to the **Instances**. Note that you only have one – this was defined by the template.

   ![](images/scalesetinstances.png)
   
3. Go to the individual instance. Here you can view details for your individual VMSS instance - you can restart, deallocate, reimage, or upgrade the instance.

**Note: What is upgrading?**

   * VM scale sets work on a model basis - there is a model that the scale set follows, and all VMs within the scale set has the same configuration as defined in the model.

   * If the model changes, you will need to upgrade the instance(s) to the latest model - hence the Upgrade button in the UI (you can also configure scale sets to automatically upgrade when the model changes).

   * If you have more questions, ask a proctor.

   * In the instance view, you can also access features like Azure Bastion, Serial console, and Boot diagnostics.

   * Note that none of these were configured initially in your VM scale set, so you will need to upgrade the scale set model and then update individual instances to use them.

   * There is a bonus section to this lab where you can try this for yourself.

**2.4 Use autoscale rules on your VM scale set (25 minutes)**

1. Go to the **Scaling** in the VMSS. Currently, the VMSS is set to automatic scaling.

2. The VMSS will scale automatically based on load as measured by % CPU usage.

   ![](images/2.png)

**2.5 Autoscale via the deployed web application**

1. Go back to the **Overview** section for your scale set.

2. Copy the **Public IP address**, and navigate to **<ip-address>:9000** in your browser. You will see a landing page that looks like:
   
   ![](images/output.png)
   
3. To view the autoscale in action, simply click **Start work** on the page.

4. Then, go back to the VM scale set in the Azure portal and watch its CPU rise once **CPU > 60%**, a new scale set instance will automatically be created.

   ![](images/3.png)
   
5. Go to the **Instances** and watch VMs get created.

6. You can also **SSH** into your individual instance by running below command in the Cloud Shell:-
  
       ssh -i <private key name> <adminusername>@<ip-address> -p 50000
 
   ![](images/ssh.png)

**2.6 Autoscale manually in the Azure Portal based on date/time**
1. Go back to the **Portal** and go to **Scaling**, and let's try out creating a scheduled autoscale rule (https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview#scheduled-autoscale)

2. Click the **Custom Autoscale**, and note all the options you have.

   ![](images/4.png)
   
3. Leave the auto-created scale condition with the **Default Settings**.

4. Click on the **Add a scale set condition**.

   - Select the **Scale to a specific instance count**.
   - Set Instance count to a **Random number** >1 and <100.
   - Select the **Specify start/end dates**.
   - **Timezone**: (UTC-05:00) Eastern Time (US & Canada).
   - **Start date**: today, November 15, 2019, and a time a minute or two in the future.
   - **End date**: today, November 15, 2019, and a time several minutes in the future.

     ![](images/5.png)

**Autoscale documentation and information**

  * See the docs here for more info on autoscale rules:
     - https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview 
     - https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal
  * The following examples are scenarios that may benefit the use of schedule-based autoscale rules:

Automatically scale out the number of VM instances at the start of the workday when customer demand increases. At the end of the workday, automatically scale in the number of VM instances to minimize resource costs overnight when application use is low.
  * If a department uses an application heavily at certain parts of the month or fiscal cycle, automatically scale the number of VM instances to accommodate their additional demands.
  * When there is a marketing event, promotion, or holiday sale, you can automatically scale the number of VM instances ahead of anticipated customer demand.
  * The following scenarios may benefit from load-based autoscale:
     - Elastic loads with no set schedule – DevOps builds for an organization, a web server that can receive traffic from anywhere at any time
   
    > Note: You can combine multiple scale-out and scale-in conditions.

**2.7 Bonus section (optional): configure your VMSS for serial console (10-15 extra minutes)**

1. To add a password to your VMSS go to the **Reset Password**.

2. Enter the **Username** and **Password**.

   ![](images/resetscalinstances.png)
   
3. Open up the **Serial Console** in your VMSS instance and get the boot diagnostics error.

   ![](images/6.png)
   
4. Update the VMSS model to enable **Boot Diagnostics**, then **Save**.

   ![](images/7.png)
