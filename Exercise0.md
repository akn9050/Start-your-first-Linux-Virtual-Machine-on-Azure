# Exercise 0: Login to your Azure Portal and Verify access to the Lab Resources

In this exercise, you will login to the **Azure portal** using your lab Azure credentials and you will verify access to the lab resources.


### Login to the Azure Portal 

1. Launch the Azure Portal(https://portal.azure.com) in the desktop on left side. You can use the shortcut on the desktop. You would be asked to choose default browser configurations but you can skip those for now by clicking **cancel**. 

   ![](images/azureportal.png)

2. Use **<inject key="AzureAdUserEmail"></inject>** as **Username** and click on **Next**.  Please use right click > **copy** to copy the username and paste inside the browser. Please allow **clipboard access** when asked by the browser. 

3. In the next step, use **<inject key="AzureAdUserPassword"></inject>** as **Password**  and Click on **Sign In**

4. On **Stay signed in** the pop-up window, click **No**.

   ![](images/fpage.png)
   
5. Click on **Resource Groups** to review the pre-created resource groups for you to use in the lab. 

   ![](images/linux1.png)
   
6. You are provided with two pre-created **Resource Groups** in the Lab. Let's review them.

* linux-empty-UNIQUEID: Please use this resource group to deploy all resources as a part of this lab guide. This resource group does not contain any existing resources. 

* linux-completed-UNIQUEID: This resource group is having a pre-created Virtual Machine Scale Set. You can use this to perform lab exercise without provisioning a new VM Scale Set. 

  ![](images/linux2.png)

7. Please note that you do not have rights to create new resource groups in the lab environment. Please use the pre-created resource group named **linux-empty-UNIQUEID** for provisioning all resources in upcoming exercises.  

8. Click **Next** on the bottom right of this page.


