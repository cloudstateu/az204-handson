# Lab: Constructing a polyglot data solution
## Objectives

After you complete this lab, you will be able to:

-   Create instances of various database services by using the Azure portal.

-   Write C# code to connect to SQL Database.

-   Write C# code to connect to Azure Cosmos DB.

## Instructions

### Task 1: Create an Azure SQL Database server resource

1.  Create a new **Azure SQL Database server (logical server)** resource with the following details:
    
    -   Server name: **CHOOSE NAME**
        
    -   Resource group: **YOUR_RESOURCE_GROUP**

    -   Server admin login: **testuser**

    -   Password: **TestPa55w.rd**

    -   Location: **West Europe**

    -   Allow Azure services to access server: **Yes**

    > **Note**: At this point in the lab, we are only creating the Azure SQL logical server. We will create the Azure SQL database instance later in the lab.

### Task 2: Create an Azure Cosmos DB account resource

1.  Create a new **Azure Cosmos DB** instance with the following details:
    -   Account name: **CHOOSE NAME**
    -   Existing resource group: **YOUR_RESOURCE_GROUP**
    -   API: **Core (SQL)**
    -   Notebooks (Preview): **Off**
    -   Apply Free Tier Discount: **Do Not Apply**
    -   Location: **West Europe**
    -   Capacity Mode: **Provisioned throughtput**
    -   Account Type **Non-Production**
    -   Geo-Redundancy: **Disable**
    -   Multi-region writes: **Disable**
    -   Availability Zones: **Disable**
    -   All other fields by default

    > **Note**: Wait for Azure to finish creating the Azure Cosmos DB account before you move forward with the lab. You'll receive a notification when the Azure Cosmos DB account is created.

2.  Go to the blade for your newly created Azure Cosmos DB account resource, and then open the Keys pane.

3.  In the Keys pane, record the value of the **PRIMARY CONNECTION STRING** text box. 

    > **Note**: You'll use these values later in this lab.

### Task 3: Create an Azure Storage account resource

1.  Create a new Azure Storage account with the following details:
    
    -   Storage account name: **CHOOSE_NAME**

    -   Existing resource group: **YOUR_RESOURCE_GROUP**

    -   Account kind: **StorageV2 (general purpose v2)**

    -   Location: **West Europe**

    -   Replication: **Locally-redundant storage (LRS)**

    -   Performance: **Standard**

    -   Access tier (default): **Hot**

    > **Note**: Wait for Azure to finish creating the storage account before you move forward with the lab. You'll receive a notification when the storage account is created.

### Task 3: Upload image blobs

1.  Go to the blade for the Azure Storage account that you created earlier in this lab.

1.  Open the Containers pane, and then create a new container with the following settings:

    -   Name: **images**

    -   Public access level: **Blob (anonymous read access for blobs only)**

1.  Go to the new **images** container, and then open the Properties pane.

1.  In the Properties pane, record the value in the **URL** text box.

    > **Note**: You'll use this value later in this lab.

1.  Return to the blade for the **images** container.

2.  Use the **Upload** button to upload the 42 individual **.jpg** image files from **images** folder on your machine.

### Task 4: Upload an SQL .bacpac file

1.  Go back to the blade for the Azure Storage account, and then open the Containers pane again.

2.  Create a new container with the following settings:

    -   Name: **databases**

    -   Public access level: **Private (no anonymous access)**

3.  Go to the new **databases** container.

4.  Use the **Upload** button to upload the **AdventureWorks.bacpac** file.

### Task 5: Import an SQL database

1.  Go to the blade for the SQL server resource that you created earlier in this lab.

2.  Import the database from your Azure Storage account into the SQL server instance by using the following details:

    -   Storage account: **YOUR_ACCOUNT**
    -   Database backup blob: **databases\AdventureWorks.bacpac**
    -   Pricing tier: **Serverless**
    -   Database name: **AdventureWorks**
    -   Collation: **SQL_Latin1_General_CP1_CI_AS**
    -   Server admin login: **testuser**

    -   Password: **TestPa55w.rd**

    > **Note**: Wait for the database to be created before you continue with this lab. If you receive a firewall-related error on the import step, it means you did not correctly configure the **Allow Azure services to access server** setting on your SQL Server earlier in the lab.  Review your settings, delete the empty **AdventureWorks** database, and then attempt your import again.

### Task 6: Use an imported SQL database

1.  Go back to the blade for the SQL server resource.

2.  Open the Firewalls and virtual networks pane.

3.  Add your current client IP address to the list of allowed IP addresses, and then save the list.

4.  Go to the blade for the **AdventureWorks** SQL database resource that you recently imported.

5.  Open the Connection strings pane, and then record the value of the **ADO.NET (SQL Authentication)** connection string. 

6.  Update the connection string that you recorded by replacing the placeholder values for *your_username* and *your_password*

    > **Note**: For example, if your connection string was originally ``Server=tcp:polysqlsrvrinstructor.database.windows.net,1433;Initial Catalog=AdventureWorks;User ID={your_username};Password={your_password};``, your updated connection string will be ``Server=tcp:polysqlsrvrinstructor.database.windows.net,1433;Initial Catalog=AdventureWorks;User ID=testuser;Password=TestPa55w.rd;``

7.  Go back to the blade for the **AdventureWorks** SQL database resource.

8.  Open the Query editor pane, and then sign in by using the following credentials:

    -   Username: **testuser**

    -   Password: **TestPa55w.rd**

9.  Run the following query, and then observe the results:

    ```
    SELECT * FROM AdventureWorks.dbo.Models
    ```

10. Run this additional query, and then observe the results:

    ```
    SELECT * FROM AdventureWorks.dbo.Products
    ```

### Task 7: Open and build the web application

1.  Using Visual Studio Code, open the solution folder **AdventureWorks**.

2.  Using a terminal, build the .NET solution:

    ```
    dotnet build
    ```

    > **Note**: The **dotnet build** command will automatically restore any missing NuGet packages prior to building all projects in the folder.

### Task 8: Update the SQL connection string

1.  Open the **AdventureWorks.Web/appsettings.json** file in Visual Studio Code.

1.  Replace the value of the *ConnectionStrings.AdventureWorksSqlContext* property with the **ADO.NET (SQL Authentication) connection string** of the SQL database that you recorded earlier in this lab.
    
    > **Note**: It's important that you use your updated connection string here. The original connection string copied from the portal won't have the username and password necessary to connect to the SQL database.

1.  Save the **appsettings.json** file.

#### Task 3: Update the blob base URL
    
1.  Replace the value of the *Settings.BlobContainerUrl* property with the *URL* property of the **Azure Storage** blob container named **images** that you recorded earlier in this lab.

1.  Save the **appsettings.json** file.

#### Task 4: Validate the web application

1.  Using a terminal, change your context to the **AdventureWorks.Web** folder:

    ```
    cd .\AdventureWorks.Web\
    ```

1.  Using the same terminal, run the ASP.NET web application project:

    ```
    dotnet run
    ```

    > **Note**: The **dotnet run** command will automatically build any changes to the project and then start the web application without a debugger attached. The command will output the URL of the running application and any assigned ports.

2.  Open web browser.

3.  In the open browser window, browse to the web application that's hosted at **localhost** on port **5000**.

    > **Note**: The URL is <http://localhost:5000>.

4.  In the web application, observe the list of models displayed from the front page.

5.  Find the **Water Bottle** model, and then select **View Details**.

6.  From the **Water Bottle** product detail page, find **Add to Cart**, and then observe that the checkout functionality is currently disabled.

7.  Close the browser window that's displaying your web application.

### Task 5: Create a migration project

1.  Using a terminal, create a new .NET console project named **AdventureWorks.Migrate** in a folder with the same name:

    ```
    dotnet new console --name AdventureWorks.Migrate
    ```

    > **Note**: The **dotnet new** command will create a new **console** project in a folder with the same name as the project.

1.  Using the same terminal, add a reference to the existing **AdventureWorks.Models** project:

    ```
    dotnet add .\AdventureWorks.Migrate\AdventureWorks.Migrate.csproj reference .\AdventureWorks.Models\AdventureWorks.Models.csproj
    ```

    > **Note**: The **dotnet add reference** command will add a reference to the model classes contained in the **AdventureWorks.Models** project.

1.  Using the same terminal, add a reference to the existing **AdventureWorks.Context** project:

    ```
    dotnet add .\AdventureWorks.Migrate\AdventureWorks.Migrate.csproj reference .\AdventureWorks.Context\AdventureWorks.Context.csproj
    ```

    > **Note**: The **dotnet add reference** command will add a reference to the context classes contained in the **AdventureWorks.Context** project.

1.  Using the same terminal, change your context to the **AdventureWorks.Migrate** folder:

    ```
    cd .\AdventureWorks.Migrate\
    ```

1.  Using the same terminal, import version 3.0.1 of **Microsoft.EntityFrameworkCore.SqlServer** from NuGet:

    ```
    dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.1
    ```

    > **Note**: The **dotnet add package** command will add the **Microsoft.EntityFrameworkCore.SqlServer** package from **NuGet**. For more information, go to: [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/3.0.1).

1.  Using the same terminal, import version 3.4.1 of **Microsoft.Azure.Cosmos** from NuGet:

    ```
    dotnet add package Microsoft.Azure.Cosmos --version 3.4.1
    ```

    > **Note**: The **dotnet add package** command will add the **Microsoft.Azure.Cosmos** package from **NuGet**. For more information, go to: [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.4.1).

1.  Using the same terminal, build the .NET console application:

    ```
    dotnet build
    ```

### Task 6: Create a .NET class 

1.  Open the **AdventureWorks.Migrate/Program.cs** file in Visual Studio Code.

1.  Delete all existing code in the **Program.cs** file.

1.  Add the following **using** directives for libraries that will be referenced by the application:

    ```
    using AdventureWorks.Context;
    using AdventureWorks.Models;
    using Microsoft.Azure.Cosmos;
    using Microsoft.EntityFrameworkCore;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    ```

1.  Create a new **Program** class with two constant string properties and an asynchronous **Main** entry point method:

    ```
    public class Program
    {
        private const string sqlDBConnectionString = "";
        private const string cosmosDBConnectionString = "";
        
        public static async Task Main(string[] args)
        {
        }
    }
    ```

1.  Update the **sqlDBConnectionString** string constant by setting its value to the **ADO.NET (SQL Authentication) connection string** of the SQL database that you recorded earlier in this lab.

    > **Note**: It's important that you use your updated connection string here. The original connection string copied from the portal won't have the username and password necessary to connect to the SQL database.

1.  Update the **cosmosDBConnectionString** string constant by setting its value to the **PRIMARY CONNECTION STRING** of the Azure Cosmos DB account that you recorded earlier in this lab.

### Task 7: Get SQL database records by using Entity Framework

1.  Within the **Main** method, add the following blocks of code to export all model and product records from SQL Database to local memory:

    ```
    await Console.Out.WriteLineAsync("Start Migration");

    using AdventureWorksSqlContext context = new AdventureWorksSqlContext(sqlDBConnectionString);

    List<Model> items = await context.Models
        .Include(m => m.Products)
        .ToListAsync<Model>();

    await Console.Out.WriteLineAsync($"Total Azure SQL DB Records: {items.Count}");
    ```

1.  Save the **Program.cs** file.

1.  Using a terminal, change your context to the **AdventureWorks.Migrate** folder:

    ```
    cd .\AdventureWorks.Migrate\
    ```

1.  Using the same terminal, build the .NET console application project:

    ```
    dotnet build
    ```

### Task 8: Insert items into Azure Cosmos DB

1.  Still within the **Main** method, add the following blocks of code to import the in-memory model and product data as documents to Azure Cosmos DB:

    ```
    using CosmosClient client = new CosmosClient(cosmosDBConnectionString);

    Database database = await client.CreateDatabaseIfNotExistsAsync("Retail");

    Container container = await database.CreateContainerIfNotExistsAsync("Online",
        partitionKeyPath: $"/{nameof(Model.Category)}",
        throughput: 1000
    );

    int count = 0;
    foreach (var item in items)
    {
        ItemResponse<Model> document = await container.UpsertItemAsync<Model>(item);
        await Console.Out.WriteLineAsync($"Upserted document #{++count:000} [Activity Id: {document.ActivityId}]");
    }

    await Console.Out.WriteLineAsync($"Total Azure Cosmos DB Documents: {count}");
    ```

1.  Save the **Program.cs** file.

1.  Using a terminal, change your context to the **AdventureWorks.Migrate** folder:

    ```
    cd .\AdventureWorks.Migrate\
    ```

1.  Using the same terminal, build the .NET console application project:

    ```
    dotnet build
    ```
### Task 8: Perform a migration

1.  Using the same terminal, run the .NET console application project:

    ```
    dotnet run
    ```

    > **Note**: The **dotnet run** command will start the console application.

1.  Observe the various data that prints to the screen, including initial SQL record count, individual upsert activity identifiers, and final Azure Cosmos DB document count.

1.  Close the current terminal.

### Task 9: Validate the migration

1.  Return to the web browser window with the Azure portal.

2.  Go to the blade for the Azure Cosmos DB account that you created earlier in this lab.

3.  Open the Data Explorer pane.

4.  Create a new **SQL query** tab within the context of the **Retail** database and **Online** container.

5.  Run the following query, and then observe the results:

    ```
    SELECT * FROM models
    ```

6.  Run the following query, and then observe the results:

    ```
    SELECT VALUE COUNT(1) FROM models
    ```