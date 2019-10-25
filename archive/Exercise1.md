# Exercise 1: Create your first Linux VM

**1.1 Create Ubuntu VM from Azure Portal**

1. Click on **Create a resource** in the upper left corner of the Azure portal and select **Ubuntu Server 18.04 LTS**.<br/>
<img src="images/ubuntunew.png"/><br/>
2. In the basics tab under **Project details**, make sure the correct **Subscription** is selected and then choose your **Resource       group**.<br/>
<img src="images/suscription.png"/><br/>
3. Under **Instance details**, type **Virtual Machine Name** , choose your **Region**, select **Ubuntu Server 18.04 LTS or Ubuntu Server   16.04 LTS** image and select the virtual machine **size** from the following:<br/>
``
"Standard_DS2_v2", "Standard_DS1_v2", "Standard_B1ls1", "Standard_B1s", "Standard_B1ms", "Standard_B2s", "Standard_B2ms", "Standard_B4ms", "Standard_B8ms", "Standard_E16-4s_v3", "Standard_E2s_v3", "Standard_E4-2s_v3", "Standard_E4s_v3", "Standard_E8-2s_v3", "Standard_E8-4s_v3", "E16-4s_v3", "E16-8s_v3".
``
<img src="images/vmname.png"/><br/>
4. Under **Administrator account** select **Password** for authentication type. Provide **User Name** and **Password**.<br/>
<img src="images/adminp.png"/><br/>
5. Under **Inbound port** rules > Public inbound ports, choose **Allow selected ports** and then select **SSH (22)** from the drop-down.<br/>
<img src="images/portssh.png"/><br/>
6. Leave the remaining options defaults and then select the **Review + create** button at the bottom of the page.<br/>
7. On the Create a **Virtual Machine Page**, you can see the details about the VM you are about to create. When you are ready, select **Create**.<br/>
<img src="images/validation.png"/> <br/>     
8. After sometime you can see that your virtual machine successfully deployed.<br/>
<img src="images/overview.png"/><br/>
