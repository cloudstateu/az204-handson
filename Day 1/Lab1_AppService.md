<img src="../../../img/logo.png" alt="Chmurowisko logo" width="200" align="right">
<br><br>
<br><br>
<br><br>

# Lab: Building a web application on Azure platform as a service offerings

## Objectives

After you complete this lab, you will be able to:

-   Create various apps by using App Service.

-   Configure application settings for an app.

-   Deploy apps by using Kudu, the Azure Command-Line Interface (CLI), and zip file deployment.

## Instructions

### Before you start

#### Sign in to the lab virtual machine

Ensure that you're signed in to your Windows 10 virtual machine by using the following credentials:
    
-   Username: **Admin**

-   Password: **Pa55w.rd**

#### Review the installed applications

Find the taskbar on your Windows 10 desktop. The taskbar contains the icons for the applications that you'll use in this lab:
    
-   Microsoft Edge

-   File Explorer

-   Windows PowerShell

-   Visual Studio Code

### Task 1: Build a back-end API by using Azure Storage and the Web Apps feature of Azure App Service

### Task 1: Create a Storage account

1.  Create a new storage account with the following details:
    
    -   Resource group: **YOUR_RESOURCE_GROUP**

    -   Name: **ENTER NAME**

    -   Location: **West Europe**

    -   Performance: **Standard**

    -   Account kind: **StorageV2 (general purpose v2)**

    -   Replication: **Locally-redundant storage (LRS)**

    -   Access tier: **Hot**

2.  Wait for Azure to finish creating the storage account before you move forward with the lab. You'll receive a notification when the account is created.

3.  Access the **Access Keys** blade of your newly created storage account instance.

4.  Record the value of the **Connection string** text box. You'll use this value later in this lab.

### Task 2: Upload a sample blob

1.  Access your storage account that you created earlier in this lab.

1.  In the **Blob service** section, select the **Containers** link.

1.  Create a new **container** with the following settings:
    
    -   Name: **images**

    -   Public access level: **Blob (anonymous read access for blobs only)**

2.  Go to the new **images** container, and then use the **Upload** button to upload the **grilledcheese.jpg** file.

### Task 3: Create a web app

1.	Create a new web app with the following details:

    -   Existing resource group: **YOUR_RESOURCE_GROUP**
    
    -   Web App name: **ENTER NAME**

    -   Publish: **Code**

    -	Runtime stack: **.NET Core 3.1 (LTS)**

    -	Operating System: **Windows**

    -	Region: **West Europe**

    -	New App Service plan: **ManagedPlan**
    
    -	SKU and size: **Standard (S1)**

    -	Application Insights: **No**

1.  Wait for Azure to finish creating the web app before you move forward with the lab. You'll receive a notification when the app is created.

### Task 4: Configure the web app

1.  Access the web app that you created earlier in this lab.

2.  In the **Settings** section, find the **Configuration** section, and then create a new application setting by using the following details:
    
    -   Name: **StorageConnectionString**

    -   Value: ***Storage Connection String copied earlier in this lab***

    -   Deployment slot setting: **Not selected**

3.  Save your changes to the application settings.

4.  In the **Settings** section, find the **Properties** section.

5.  In the **Properties** section, copy the value of the **URL** text box. You'll use this value later in the lab.

    > **Note**: At this point, the web server at this URL will return a 404 error. You have not deployed any code to the Web App yet. You will deploy code to the Web App later in this lab.

### Task 5: Deploy an ASP.NET web application to Web Apps

1.  Using Visual Studio Code, open the web application in the **API** folder.

2.  Open the **Controllers\\ImagesController.cs** file, and then observe the code in each of the methods.

3.  Open the Windows Terminal application.

4.  Sign in to the Azure CLI by using your Azure credentials:

    ```
    az login
    ```

5.  List all the apps in your **ManagedPlatform** resource group:

    ```
    az webapp list --resource-group YOUR_RESOURCE_GROUP
    ```

6.  Change the current directory to the **API** directory that contains the lab files.

7.  Deploy the **api.zip** file to the web app that you created earlier in this lab:

    ```
    az webapp deployment source config-zip --resource-group YOUR_RESOURCE_GROUP --src api.zip --name <name-of-your-api-app>
    ```

    > **Note**: Replace the *\<name-of-your-api-app\>* placeholder with the name of the web app that you created earlier in this lab. You recently queried this app’s name in the previous steps.

1.	Access the web app that you created earlier in this lab. Open the web app in your browser.

1.	Perform a GET request to the root of the website, and then observe the JavaScript Object Notation (JSON) array that's returned. This array should contain the URL for your single uploaded image in your storage account.

1.  Close the currently running Visual Studio Code and Windows Terminal applications.

