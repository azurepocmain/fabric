# Azure Synapse VNet Connectivity for PBI Datasets

**Overview:**
A client would like to implement VNet connectivity for their Power BI Datasets when it calls their Azure Synapse service. This will ensure that traffic going to and from the Power BI data set, will only traverse the private network. This document will outline the steps on how to implement their Power BI gateway to their Azure VNet.  

**Steps:**

**Step 1:**

Go to the Azure subscription	that the Azure Synapse resides in, go to Subscription -> Resource providers, and search for “Microsoft.PowerPlatform” and register the provider: 
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/6f34029b-1392-4ea9-857b-4ecc245cc734)

Confirm that you see the provider “Microsoft.PowerPlatform” registered before proceeding. 
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/d2fc2c10-91c6-4240-9485-e0ef3b1cc25a)


**Step 2:**

Create a VNet or leverage a VNet that Azure Synapse already is in use with. Proceed to create a subnet in that VNet, one for Power BI gateway connection and another for Azure Synapse. Please note that “This new subnet cannot be shared with other services but will be used entirely by the Power Platform VNet service. Also “Do not use the subnet name, “gateway subnet”, as this is a reserved word for the Azure Gateway Subnet feature. You won't be able to use it to create a VNet data gateway. Make sure this subnet doesn't have an IPV6 address space added. Therefore, Synapse and Power BI gateway will both need separate subnets with a total of at least two subnets. Regarding the IP addresses “reserved Ips for the service, our recommendation is to have approximately 5-7 IPs so you can add more VNet gateways to the same VNet and Subnet.” References: [Use virtual network (VNet) data gateway in Power Platform dataflows | Microsoft Docs  Use virtual network (VNet) data gateway in Power Platform dataflows | Microsoft Docs](https://docs.microsoft.com/en-us/data-integration/vnet/data-gateway-power-platform-dataflows)
Regarding Azure VNet pricing cost, please visit the following link for additional info: Azure Virtual Network FAQ | Microsoft Docs Virtual Network Pricing | [Microsoft Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-faq)
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/ab982e9a-1663-47ef-9f49-b19b43f8723a)

![image](https://github.com/azurepocmain/fabric/assets/91505344/62d1bdd7-4bfe-4764-9e72-294f6033198c)

 
 

**Step 3:**

Next, go to the Power Platform admin center, at: https://admin.powerplatform.microsoft.com/
Select, “Data (preview)” -> “Create a virtual network data gateway”. 
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/a04892f7-dcbe-452f-abce-ac269bc24453)

 
**Step 4:**

Enter the information for the virtual network that you created in “Step 2” that was designated to Power BI. Then confirm that the virtual data gateway is created: 

![image](https://github.com/azurepocmain/fabric/assets/91505344/f53141f7-8512-44f8-83a6-e6b6d6f6726e)
![image](https://github.com/azurepocmain/fabric/assets/91505344/aed241ad-e972-4edc-a8fc-c644d302d2a0)

  




**Step 5:**

Next, check the status of the gateway by selecting the “Status” button in the below image and confirm that it says “Online”. 
 
![image](https://github.com/azurepocmain/fabric/assets/91505344/416cd1f7-62b3-4bfb-9ff4-e8512a5e8823)

 ![image](https://github.com/azurepocmain/fabric/assets/91505344/12379833-77c0-489d-ae19-2737a0fcc4d7)




**Step 6:**

Next go to Azure Synapse workspace, in the security section in the left pane, select “Private endpoint connections” and proceed to create a private endpoint. As we can see below, for the resource type, you will select the synapse workspace and resource name. As for the sub-resource type, there are three types: Sql is for SQL query execution in SQL pool. SqlOnDemand is for SQL built-in query execution. Dev is for accessing everything else inside Azure Synapse Analytics Studio workspaces. In the below image, we are selecting the SQL Serverless pool. Therefore, if you require access to multiple pools, you will process this step more than once. 
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/dbb7d9b5-2dfb-473e-8079-b2cb31119ca4)


Step three, we will select the same VNET and subnet that was created above, specifically for Synapse. Remember, Synapse and PowerBI should have their independent subnet.
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/ba760cda-6d47-419a-abf9-610edd96fd70)
![image](https://github.com/azurepocmain/fabric/assets/91505344/a232a182-8f80-4218-a6bd-9d3d6e165ce3)

 

**Step 7:**

Next, go to your Power BI workspace and select dataset settings on the data set that you want to use the Azure VNet data gateway. 
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/6d72594e-5f72-4074-a4d5-4ab6abbd04d5)


It may be necessary to turn on “Use an On-premises or VNet data gateway” before the gateway that was created is selectable. You will see that it may state that it is not configured correctly. Proceed to select the drop-down arrow and add the dataset data source to the Azure VNet that was used above. Remember, a Power BI Premium subscription is required for VNet Gateways. 

 ![image](https://github.com/azurepocmain/fabric/assets/91505344/4debc182-78da-4b33-8633-27bc9b4fc9cd)










Then in the “Maps to” drop down, proceed to select the VNet aka Data Source setting for the data set as the below image:
 ![image](https://github.com/azurepocmain/fabric/assets/91505344/c59e075b-7077-4adf-90b4-08c2ee9c0028)


Please note that the above configuration may take some time. Once completed you will see an alert confirmation as the below and access to that service via VNet will be enabled. Proceed to disable public access and test the connection. ![image](https://github.com/azurepocmain/fabric/assets/91505344/da2c9f29-3503-47f9-ad7d-51d4363dc928)
 


DISCLAIMER: Sample Code is provided for the purpose of illustration only and is not intended to be used in a production environment unless thorough testing has been conducted by the app and database teams. THIS SAMPLE CODE AND ANY RELATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY AND/OR FITNESS FOR A PARTICULAR PURPOSE. We grant You a nonexclusive, royalty-free right to use and modify the Sample Code and to reproduce and distribute the object code form of the Sample Code, provided that. You agree: (i) to not use Our name, logo, or trademarks to market Your software product in which the Sample Code is embedded; (ii) to include a valid copyright notice on Your software product in which the Sample Code is embedded; and (iii) to indemnify, hold harmless, and defend Us and Our suppliers from and against any claims or lawsuits, including attorneys’ fees, that arise or result from the use or distribution or use of the Sample Code.

