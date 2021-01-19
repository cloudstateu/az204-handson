# Lab: Implement task processing logic by using Azure Functions


## Objectives
After you complete this lab, you'll be able to:
- Create an Azure Functions app in the Azure Portal.
- Create various functions by using built-in triggers and input integrations.


## Instructions

### Exercise 1: Create a schedule-triggered function

#### Task 1: Create a function app
1. Press "Create a resource" and select "Function App"
2. Provide following details in **Basis** tab: 
    - Existing subscription: **ChmurowiskoLab**
    - Existing resource group: **YOUR_RESOURCE_GROUP**
    - Function App name: **[yourname]functionapp**
    - Publish: **Code**
    - Runtime stack: **Node.js**
    - Version: **12 LTs**
    - Region: **West Europe**
3. In the "Hosting" tab: 
    - Storage account: **funcstor[yourname]**
    - Operating system: **Windows**
    - Plan: **Consumption (Serverless)**
4. In the "Monitoring" make sure **Yes** is selected.
5. Press "Review + create" in the bottom and then "Create"
    > **Note**: Wait for Azure to finish creating the function app before you move forward with the lab. You'll receive a notification when the app is created.

#### Task 2: Create a function app
1. Wait for the resources to be created and press "Go to resource" then
2. Select "Functions" from the left side menu
3. Press "Add" at the top of "Functions" blade
4. Select following parameters in the "Add function" blade:
    - Development environmemt: **Develop in portal" 
    - Template: **timer trigger** 
    - New function: **TimerTrigger1** 
    - Authorization level: **Function**
    - Schedule: ``0 */5 * * * * ``
5. Click "Add" in the bottom

#### Task 3: Test and review function
1. Select "Code + Test" from the left side menu
2. Code should look like this
   ```js
   module.exports = async function (context, myTimer) {
        var timeStamp = new Date().toISOString();
    
        if (myTimer.IsPastDue)
        {
            context.log('JavaScript is running late!');
        }

        context.log('JavaScript timer trigger function ran!', timeStamp);   
    };
   ```
3. Press "Test/Run" in the top
4. Make sure **master (Host key)** is selected in the "Key" dropdown
5. Press "Run" in the bottom
6. Log will be opened and you should be able to locate log entry with current timestamp
   ```[Information] JavaScript timer trigger function ran! <current time>```


### Exercise 2: Create a function that's triggered by an HTTP request

#### Task 1: Create an HTTP-triggered function
1. Open the Funtion App created in the previous excercise 
2. Create a new function with the following details:
    - Development environment: **Develop in portal**
    - template: **HTTP trigger**
    - name: **HttpTrigger1**
    - Authorization level: **Function**
3. Click "Add" in the bottom

#### Task 2: Test and review function
1. Select "Code + Test" from the left side menu
2. Code should look like this
   ```js
   module.exports = async function (context, req) {
        context.log('JavaScript HTTP trigger function processed a request.');

        const name = (req.query.name || (req.body && req.body.name));
        const responseMessage = name
            ? "Hello, " + name + ". This HTTP triggered function executed successfully."
            : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

        context.res = {
            body: responseMessage
        };
    }
   ```
3. Press "Get function ULR" in the top
4. Make sure **default** is selected in the "Key" dropdown
5. Copy the function URL
6. Open a new tab in the browser and paste the URL and add **&name=[yourname]** and the end
7. You should see a content similar to following:
    ```Hello, [yourname]. This HTTP triggered function executed successfully.```


### Exercise 3: Create a function that integrates with other services

#### Task 1: Create an Azure Storage account
1. Create a new storage account with the following details:
    - New resource group: **YOUR_RESOURCE_GROUP**
    - Name: **samplestor[yourname]**
    - Location: **West Europe**
    - Performance: **Standard**
    - Account kind: **StorageV2 (general purpose v2)**
    - Replication: **Locally-redundant storage (LRS)**
    - Access tier: **Hot**
    >Note: Wait for Azure to finish creating the storage account before you move forward with the lab. You'll receive a notification when the account is created.
2. Open the Access Keys section of your newly created storage account instance.
3. Record the value of the Connection string text box.
    > Note: You'll use this value later in the lab. It doesn't matter which connection string you choose. They are interchangeable.
4. Select the Containers link in the Blob service section, and then create a new container with the following settings:
    - Name: **sample-container**
    - Public access level: **Private (no anonymous access)**

#### Task 2: Create an Blob-triggered function
1. Open the Funtion App created in the previous excercise 
2. Create a new function with the following details:
    - Development environment: **Develop in portal**
    - template: **Azure Blob Storage trigger**
    - name: **BlobTrigger1**
    - Path: **sample-container/{name}** (created in previous task)
3. Click "Add" in the bottom and wait for the resource to be created.
4. Wait for the function to be created, then review the code:
    ```js
    module.exports = async function (context, myBlob) {
        context.log("JavaScript blob trigger function processed blob \n Blob:", context.bindingData.blobTrigger, "\n Blob Size:", myBlob.length, "Bytes");
    };
    ```
5. Close the newly created function blade and open Settings > Configuration from the left side menu.
6. Locate AzureWebJobsStorage and paste connection string to storage account from the previous task.
7. Go to Monitoring > Log stream from the left side menu.

#### Task 3: Upload sample blob to the storage
1. Open Azure Portal in new browser tab
2. Access the samplestor[yourname] storage account that you created earlier in this lab.
3. Select the recently created content container.
In the content container, select Upload to upload the ```/Day 1/files/grilledcheese.jpg```
    >Note: You should enable the Overwrite if files already exist option.
4. Go to the other browser tab to review funtion log. You should be able to see something similar to:
    ```[Information] JavaScript blob trigger function processed blobBlob: container/grilledcheese.jpg(...)```
