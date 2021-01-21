    
# Lab: Automate business processes with Logic Apps

## Objectives

After you complete this lab, you will be able to:

-   Create a Logic App workflow.

-   Manage products and APIs in a Logic App.

-   Use Azure API Management as a proxy for a Logic App.

## Instructions

### Exercise 1: Create Azure resources

#### Task 1: Create an API Management resource

1.  In the Azure portal, create a new API Management account with the following details:

    -   Eesource group: **YOUR RESOURCE GROUP**

    -   Name: **prodapim*[yourname]***

    -   Location: **West Europe**

    -   Organization name: **stXorg**

    -   Pricing tier: **Consumption (99.9 SLA, %)**

    > **Note**: Wait for Azure to finish creating the API Management resource prior to moving on in the lab. You will receive a notification when the resource is created.

#### Task 3: Create a Logic App resource

1.  In the Azure portal, create a new **logic app** with the following details:
    
    -   Resource group: **YOUR RESOURCE GROUP**

    -   Name: **prodflow*[yourname]***

    -   Location: **West Europe**

    -   Log Analytics: **Off**

    > **Note**: Wait for Azure to finish creating the Logic Apps resource prior to moving on in the lab. You will receive a notification when the resource is created.

#### Task 4: Create a storage account

1.  Create a new storage account with the following details:
    
    -   Resource group: **YOUR RESOURCE GROUP**

    -   Name: **prodstor*[yourname]***

    -   Location: **West Europe**

    -   Performance: **Standard**

    -   Account kind: **StorageV2 (general purpose v2)**

    -   Replication: **Locally-redundant storage (LRS)**

    > **Note**: Wait for Azure to finish creating the storage account before you move on in the lab. You'll receive a notification when the account is created.

#### Task 5: Upload sample content to Azure Files

1.  Access the storage account that you created earlier in this lab.

2.  Select the **File shares** link in the **File service** section, and then create a new share with the following settings:
    
    -	Name: **metadata**

    -	Quota: **1** (GiB) 

3.  Select the recently created **metadata** share.
    
4.  In the **metadata** share, select **Upload** to upload the following files:

    -   **item_00.json**
    
    -   **item_01.json**
    
    -   **item_02.json**
    
    -   **item_03.json**
    
    -   **item_04.json** 

    > **Note**: We recommend that you enable the **Overwrite if files already exist** option.


### Exercise 2: Implement a workflow using Logic Apps

#### Task 1: Create a trigger for the workflow

1.  Access the logic app that you created earlier in this lab.

2.  On the **Logic Apps Designer** blade, select the **Blank Logic App** template.

3.  In the **Designer** area, add a new **When a HTTP request is received (Request)** trigger with the following details:

    -   Method: **GET**

#### Task 2: Create an action to query Azure Storage file shares

1.  In the **Designer** area, add a new **List files (Azure File Storage)** action with the following details:

    -   Connection Name: **filesConnection**

    -   Storage Account: **prodstor*[yourname]***
    
    -   Folder: **/metadata**
    
#### Task 3: Create an action to project list item properties

1.  In the **Designer** area, add a new **Select (Data Operations)** action with the following details:

    -   From: **value (List files)**

    -   Map mode: **Text**

    -   Map: **Name (List files)**

#### Task 4: Build an HTTP response action

1.  In the **Designer** area, add a new **Response (Request)** action with the following details:
    
    -   Status Code: **200**
    
    -   Body: **Output (Select)**

1.  **Save** the workflow.

### Exercise 3: Use Azure API Management as a proxy for Logic Apps

#### Task 1: Create an API integrated with Logic Apps

1.  Access the API Management resource that you created earlier in this lab.

2.  Navigate to the list of **APIs** for the account.

3.  Create a new **Logic App**–integrated API with the following details:

    -   Logic App: **prodflow*[yourname]***
    
    -   Display name: **Metadata Lookup**

    -   Name: **metadata-lookup**

    -   Description: **Look up metadata JSON files**

#### Task 2: Test the API operation

1.  Navigate to the **Test** tab for the new API.

1.  On the **Test** tab, perform the following actions:

    1.  Select the single **GET** operation.

    1.  Observe the value of the **Request URL** field.

    1.  Perform a test request against the operation.

    1.  Observe the JSON results of the test request.

1.	Return to your browser window within the Azure portal.