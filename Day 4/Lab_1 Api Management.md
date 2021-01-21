# Creating an api using Azure Api Management


## Objectives

After you complete this lab, you will be able to:
    - Configure an API as a proxy for another Azure service with header and payload manipulation.
    - Use subscriptions to secure the API
    - ?

## Instructions

### Exercise 1: Build an API proxy tier to the Azure services by using Azure API Management

#### Task 1: Create a function app
1. Press "Create a resource" and select "Function App"
2. Provide following details in **Basis** tab: 
    - Existing subscription: **ChmurowiskoLab**
    - Existing resource group: **YOUR_RESOURCE_GROUP**
    - Function App name: **[yourname]apifunctionapp**
    - Publish: **Code**
    - Runtime stack: **Node.js**
    - Version: **12 LTs**
    - Region: **West Europe**
3. In the "Hosting" tab: 
    - Storage account: **funcstor[yourname]**
    - Operating system: **Windows**
    - Plan: **Consumption (Serverless)**
4. In the "Monitoring" make sure **No** is selected.
5. Press "Review + create" in the bottom and then "Create"
    > **Note**: Wait for Azure to finish creating the function app before you move forward with the lab. You'll receive a notification when the app is created.

#### Task 2: Create an HTTP-triggered function
1. Open the Funtion App created in the previous excercise 
2. Create a new function with the following details:
    - Development environment: **Develop in portal**
    - template: **HTTP trigger**
    - name: **HttpTrigger1**
    - Authorization level: **Function**
3. Click "Add" in the bottom
4. After the function is created, go to the "Integration" and select **HTTP (req)** trigger.
5. In the "HTTP methods" combo deselect **GET**

#### Task 3: Create an API Management resource

1.  In the Azure portal, create a new API Management service instance with the following details:
    -   Existing resource group: **YOUR_RESOURCE_GROUP**
    -   Name: **prodapi[yourname]**
    -   Location: **West Europe**
    -   Organization name: **Contoso**
    -   Pricing tier: **Consumption (99.9 SLA, %)**

#### Task 4: Define a new API

1.  Access the **prodapi*[yourname]*** API Management service instance that you created earlier in this lab and go to "APIs" from the left side menu.
2.  Select **Function App** from the "Add new api" blade and browse for the Function App created in previous steps. Select the function and continue. 
3.  Provide following details for the new API:
    -   Display name: **FunctionApp Proxy**
    -   Name: **functionapp-proxy**
    -   API URL suffix: **functionapp-proxy**
4. Press "Create"

#### Task 5: Manipulate the API operation

1. Select **HttpTrigger1** operation and in the **Frontend** section press **Edit**.
   1. Change POST to GET
   2. Open "Query" tab and add parameter
      - Name: **Name**
      - Type: **string**
   3. Press "Save"
2. In the **Inbound processing** section press **+ Add polict** button and select **Other policies**
3. To the *inbound* section add 
    ```xml
        <set-method>POST</set-method>
        <set-body>{ "name": @(context.Request.MatchedParameters["name"])}</set-body>
    ``` 
    and press "Save" in the bottom

#### Task 6: Test the operation

1. Select operation created in previous steps and open "Test" tab
2. Provide **name** query parameter and press "Send" in the bottom
3. Verify whether response is OK (HTTP/1.1 200 OK) and response body contains provided name parameter.

> In this section you learned how to create Azure Api Management, call other Azure resources and how to manipulate with the provided values to properly pass them to existing backend resources.



### Exercise 2: Call external backed and manipulate the response

#### Task 1: Add HttpbinApi

1. Open the Api Management resource created in the previous task
2. Open "APIs" from the left side menu and add **Bland API** with the following properties:
    -   Display name: **HttpBin Proxy**
    -   Name: **httpbin-proxy**
    -   Web service URL: **https://httpbin.org**
3. Press "Create" and open the new API
4. Add operation
   - Display name: **Xml To Json**
   - Name: **xml-to-json**
   - Url: **GET /xml**
5. Test the **Xml To Json** operation in the **HttpBin Proxy***, observing the results of the API request.
    > **Note**: At this point, the results should be in XML format.

#### Task 2: Add outband policy

1.  Add a new custom outbound policy scoped to the **HttpBin Proxy** operation by first locating the following block of XML content:
    ```xml
    <outbound>
        <base />
    </outbound>
    ```
2.  Replace that block of XML with the following XML, and then save the policy: 
    ```xml
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
    ```
3.  Test the **Xml To Json** operation, observing the results of the API request.
    > **Note**: The new results are in JSON format.
4.  Use the **Trace** feature of the test tool to observe the request sent to the back-end service.