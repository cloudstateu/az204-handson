<img src="../../../img/logo.png" alt="Chmurowisko logo" width="200" align="right">
<br><br>
<br><br>
<br><br>

# Lab: Enhancing a web application by using the Azure Content Delivery Network

## Objectives

After you complete this lab, you will be able to:

-   Register a Microsoft.CDN resource provider.

-   Create Content Delivery Network resources.

-   Create and configure Content Delivery Network endpoints that are bound to various Azure services.

## Instructions

## Create Azure resources

### Task 1: Create a Storage account

1.  Create a new storage account with the following details:
    
    -	New resource group: **YOUR_RESOURCE_GROUP**

    -	Name: **contenthost[yourname]**

    -	Location: **West Europe**

    -	Performance: **Standard**

    -	Account kind: **StorageV2 (general purpose v2)**

    -	Replication: **Read-access geo-redundant storage (RA-GRS)**

    > **Note**: Wait for Azure to finish creating the storage account before you move forward with the lab. You'll receive a notification when the account is created.
   
### Task 2: Create a web app by using Azure App Service

1.  Create a new web app with the following details:

    -   Existing resource group: **YOUR_RESOURCE_GROUP**
    
    -   Name: **landingpage[yourname]**

    -   Publish: **Docker Container**

    -	Operating system: **Linux**

    -	Region: **West Europe**

    -	New App Service plan: **MarketingPlan**
    
    -	SKU and size: **Premium V2 P1v2**

    -	Docker options: **Single Container**

    -	Image source: **Docker Hub**

    -   Access type: **Public**

    -   Image and tag: **microsoftlearning/edx-html-landing-page:latest**
  
    > **Note**: Wait for Azure to finish creating the web app before you move forward with the lab. You'll receive a notification when the app is created.

2.  Access the **landingpage[yourname]** web app that you created earlier in this lab.

3.  In the **Settings** section, go to the **Properties** section, and then record the value of the **URL** text box. You'll use this value later in the lab.

## Configure Content Delivery Network and endpoints

### Task 1: Open Azure Cloud Shell

1.  Open a new Cloud Shell instance in the Azure portal.

1.  If Cloud Shell isn't already configured, configure the shell for Bash by using the default settings.

1.  At the **Cloud Shell** command prompt in the portal, use the **az** command with the **\-\-version** flag to get the version of the Azure Command-Line Interface (Azure CLI) tool.

### Task 2: Register the Microsoft.CDN provider

1.  Use the **az** command with the **\-\-help** flag to find a list of subgroups and commands at the root level of the Azure CLI.

1.  Use the **az provider** command with the **\-\-help** flag to get a list of commands available for resource providers.

1.  Use the **az provider list** command to list all currently registered providers.

1.  Use the **az provider list** command again with the **\-\-query "[].namespace"** flag to list just the namespaces of the currently registered providers.

1.  Observe the list of currently registered providers. The **Microsoft.CDN** provider isn't currently in the list of providers.

1.  Use the **az provider register** command with the **\-\-help** flag to get the required flags to register a new provider.

1.  Use the **az provider register** command to register a namespace with your current subscription by using the following setting:

    -   Namespace: **Microsoft.CDN**

1.  Close the Cloud Shell pane.

### Task 3: Create a Content Delivery Network profile

1.  Create a new Content Delivery Network profile with the following details:
    
    -   Name: **contentdeliverynetwork**

    -   Existing resource group: **YOUR_RESOURCE_GROUP**

    -	Resource group location: **West Europe**

    -	Pricing tier: **Standard Akamai**

    -   Create a new CDN endpoint now: **No**
  
    > **Note**: Wait for Azure to finish creating the CDN profile before you move forward with the lab. You'll receive a notification when the app is created.

### Task 4: Configure Storage containers

1.  Access the **contenthost[yourname]** storage account that you created earlier in this lab.

1.  Select the **Containers** link in the **Blob service** section, and then create a new container with the following settings:
    
    -	Name: **media**

    -	Public access level: **Blob (anonymous read access for blobs only)**
    
1.  Create a new container with the following settings:
    
    -	Name: **video**

    -	Public access level: **Blob (anonymous read access for blobs only)**

1.  Observe the updated list of containers.

### Task 5: Create Content Delivery Network endpoints

1.  Access the **contentdeliverynetwork** CDN profile that you created earlier in this lab, and then select **+ Endpoint**.

1.  Add a new endpoint with the following properties:

    -   Name: **cdnmedia[yourname]**

    -   Origin type: **Storage**

    -   Origin hostname: **contenthost[yourname].blob.core.windows.net** (the Storage account that you created earlier in this lab)

    -   Origin path: **/media**

    -   Optimized for: **General web delivery**

    > **Note**: Wait for Azure to finish creating the CDN endpoint before you move forward with the lab. You'll receive a notification when the account is created.

1.  Add a new endpoint with the following properties:

    -   Name: **cdnvideo[yourname]**

    -   Origin type: **Storage**

    -   Origin hostname: **contenthost[yourname].blob.core.windows.net** (the Storage account that you created earlier in this lab)

    -   Origin path: **/video**

    -   Optimized for: **Video on demand media streaming**

    > **Note**: Wait for Azure to finish creating the CDN endpoint before you move forward with the lab. You'll receive a notification when the account is created.

1.  Add a new endpoint with the following properties:

    -   Name: **cdnweb[yourname]**

    -   Origin type: **Web App**

    -   Origin hostname: **landingpage[yourname].azurewebsites.net** (the Web App that you created earlier in this lab)

    -   Optimized for: **General web delivery**

    > **Note**: Wait for Azure to finish creating the CDN endpoint before you move forward with the lab. You'll receive a notification when the account is created.

## Upload and configure static web content

### Task 1: Observe the landing page

1.  Access the **landingpage[yourname]** web app that you created earlier in this lab.

1.  Go to the URL for the **landingpage[yourname]** web app.

1.  Observe the error message displayed on the screen. The website won't work until you configure the specified settings to reference multimedia content.

1.  Return to the Azure portal.

### Task 2: Upload Storage blobs

1.  Access the **contenthost[yourname]** storage account that you created earlier in this lab.

1.  Select the **Containers** link in the **Blob service** section, and then select the **media** container.

2.  Upload the following files:

    -   **campus.jpg**
    
    -   **conference.jpg**
    
    -   **poster.jpg**

    > **Note**: We recommend that you enable the **Overwrite if files already exist** option.

3.  Record the value in the **URL** text box. You will use this value later in the lab.

4.  Back in the **Containers** section, select the **video** container.

5.  Upload the **welcome.mp4** file

    > **Note**: We recommend that you enable the **Overwrite if files already exist** option.

6.  Record the value in the **URL** text box. You will use this value later in the lab.

### Task 3: Configure Web App settings

1.  Access the **landingpage[yourname]** web app that you created earlier in this lab.

1.  In the **Settings** section, go to the **Configuration** section.

1.  Find and access the **Application Settings** tab in the **Configuration** section.

1.  Create a new application setting by using the following details:
    
    -	Name: **CDNMediaEndpoint**

    -	Value: The **URI** value of the **media** container in the **contenthost[yourname]** storage account that you recorded earlier in this lab.
    
    -	Deployment slot setting: **Not selected**

1.  Create a new application setting by using the following details:
    
    -	Name: **CDNVideoEndpoint**

    -	Value: The **URI** value of the **video** container in the **contenthost[yourname]** storage account that you recorded earlier in this lab.
    
    -	Deployment slot setting: **Not selected**

1.  Save your changes to the application settings.

### Task 4: Validate the corrected landing page

1.  Access the **landingpage[yourname]** web app that you created earlier in this lab.

1.  **Restart** the currently running Web App.

    > **Note**: Wait for the restart operation to complete before you move forward with the lab. You'll receive a notification when the operation is done.

1.  Go to the URL for the **landingpage[yourname]** web app.

1.  Observe the updated website rendering multimedia content of various types.

## Use Content Delivery Network endpoints

### Task 1: Retrieve endpoint Uniform Resource Identifiers (URIs)

1.  Access the **contentdeliverynetwork** CDN profile that you created earlier in this lab.

1.  Select the **cdnmedia[yourname]** endpoint.

1.  Copy the value of the **Endpoint hostname** text box. You will use this value later in the lab. 

1.  Select the **cdnvideo[yourname]** endpoint.

1.  Copy the value of the **Endpoint hostname** text box. You will use this value later in the lab. 

### Task 2: Test multimedia content by using the CDN

1.  Construct a URL for the **campus.jpg** resource by combining the **Endpoint hostname** URL from the **cdnmedia[yourname]** endpoint that you copied earlier in the lab with a relative path of **/campus.jpg**.

    > **Note**: For example, if your **Endpoint hostname** URL is **https://cdnmediastudent.azureedge.net/**, your newly constructed URL would be **https://cdnmediastudent.azureedge.net/campus.jpg**.

1.  Construct a URL for the **conference.jpg** resource by combining the **Endpoint hostname** URL from the **cdnmedia[yourname]** endpoint that you copied earlier in the lab with a relative path of **/conference.jpg**.

    > **Note**: For example, if your **Endpoint hostname** URL is **https://cdnmediastudent.azureedge.net/**, your newly constructed URL would be **https://cdnmediastudent.azureedge.net/conference.jpg**.

1.  Construct a URL for the **poster.jpg** resource by combining the **Endpoint hostname** URL from the **cdnmedia[yourname]** endpoint that you copied earlier in the lab with a relative path of **/poster.jpg**.

    > **Note**: For example, if your **Endpoint hostname** URL is **https://cdnmediastudent.azureedge.net/**, your newly constructed URL would be **https://cdnmediastudent.azureedge.net/poster.jpg**.

1.  Construct a URL for the **welcome.mp4** resource by combining the **Endpoint hostname** URL from the **cdnvideo[yourname]** endpoint that you copied earlier in the lab with a relative path of **/welcome.mp4**.

    > **Note**: For example, if your **Endpoint hostname** URL is **https://cdnvideostudent.azureedge.net/**, your newly constructed URL would be **https://cdnvideostudent.azureedge.net/welcome.mp4**.

1.  Open a new **Microsoft Edge** browser window.

1.  In the new browser window, go to the URL that you constructed for the **campus.jpg** media resource, and then verify that the resource was successfully found.

    > **Note**: If the content isn't available yet, the CDN endpoint is still initializing. This initialization process can take anywhere from 5 to 15 minutes.

1.  Go to the URL that you constructed for the **conference.jpg** media resource, and then verify that the resource was successfully found.

1.  Go to the URL that you constructed for the **poster.jpg** media resource, and then verify that the resource was successfully found.

1.  Go to the URL that you constructed for the **welcome.mp4** video resource, and then verify that the resource was successfully found.

1.  Close the browser window that you created in this task.

### Task 3: Update the Web App settings

1.  Access the **landingpage[yourname]** web app that you created earlier in this lab.

1.  In the **Settings** section, go to the **Configuration** section.

1.  Find and access the **Application Settings** tab in the **Configuration** section.

1.  Update the **CDNMediaEndpoint** application setting by changing its value to the **Endpoint hostname** URL from the **cdnmedia[yourname]** endpoint that you copied earlier in the lab.

1.  Update the **CDNVideoEndpoint** application setting by changing its value to the **Endpoint hostname** URL from the **cdnvideo[yourname]** endpoint that you copied earlier in the lab.

1.  Save your changes to the application settings.

1.  Restart the currently running Web App.

    > **Note**: Wait for the restart operation to complete before you move forward with the lab. You'll receive a notification when the operation is done.

### Task 4: Test the web content

1.  Access the **contentdeliverynetwork** CDN profile that you created earlier in this lab.

1.  Select the **cdnweb[yourname]** endpoint.

1.  Copy the value of the **Endpoint hostname** text box. You will use this value later in the lab.

1.  Open a new **Microsoft Edge** browser window.

1.  In the new browser window, go to the **Endpoint hostname** URL for the **cdnweb[yourname]** endpoint.

1.  Observe the website and multimedia content that are all served by using Content Delivery Network.

#### Review

You updated your Web App to use Content Delivery Network to serve multimedia content and to serve the web application itself.
