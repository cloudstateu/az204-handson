<img src="../../../img/logo.png" alt="Chmurowisko logo" width="200" align="right">
<br><br>
<br><br>
<br><br>

# Deploying compute workloads by using containers.

## LAB Overview

### Objectives

After you complete this lab, you will be able to:

-   Deploy a container from a container image in Container Registry by using Container Instances.

## Instructions

### Task 1: Open the Cloud Shell and editor

1.  Open a new Cloud Shell instance in the Azure portal.

2.  At the **Cloud Shell** command prompt, change the active directory to **\~/clouddrive**.

    > **Note**: The command to change directory in Bash is **cd *path***.

3.  At the **Cloud Shell** command prompt, create a new directory named **ipcheck** in the **\~/clouddrive** directory.

    > **Note**: The command to create a new directory in Linux is **mkdir *directory name***.

4.  Change the active directory to **\~/clouddrive/ipcheck**.

5.  Use the **dotnet new console --output . --name ipcheck** command to create a new .NET console application in the current directory.

6.  Create a new file in the **\~/clouddrive/ipcheck** directory named **Dockerfile**.

    > **Note**: The command to create a new file in Bash is **touch *file name***. The file name **Dockerfile** is case sensitive.

7.  Open the embedded graphical editor in the context of the current directory.

    > **Note**: You can open the editor by using the **code .** command or by selecting the editor button.

### Task 2: Create and test a .NET application

1.  In the graphical editor, open the **Program.cs** file and replace its contents with the following code, and then save the file:

    ```
    public class Program
    {
        public static void Main(string[] args)
        {        
            // Check if network is available
            if (System.Net.NetworkInformation.NetworkInterface.GetIsNetworkAvailable())
            {
                System.Console.WriteLine("Current IP Addresses:");

                // Get host entry for current hostname
                string hostname = System.Net.Dns.GetHostName();
                System.Net.IPHostEntry host = System.Net.Dns.GetHostEntry(hostname);
                
                // Iterate over each IP address and render their values
                foreach(System.Net.IPAddress address in host.AddressList)
                {
                    System.Console.WriteLine($"\t{address}");
                }
            }
            else
            {
                System.Console.WriteLine("No Network Connection");
            }
        }
    }
    ```

2.  Use the **dotnet run** command at the command prompt to run the application and validate that it finds one or more IP addresses.

3.  Open the **Dockerfile** file in the graphical editor, replace its contents with the following code, and then save the file:

    ```
    # Start using the .NET Core 3.1 SDK container image
    FROM mcr.microsoft.com/dotnet/core/sdk:3.1-alpine AS build

    # Change current working directory
    WORKDIR /app

    # Copy existing files from host machine
    COPY . ./

    # Publish application to the "out" folder
    RUN dotnet publish --configuration Release --output out

    # Start container by running application DLL
    ENTRYPOINT ["dotnet", "out/ipcheck.dll"]
    ```

4.  Close the Cloud Shell pane.

### Task 3: Create a Container Registry resource

1. Go to Azure Portal and Create new "Azure Container Registry" resource.
2. Enter following details:
    -	Name: ***Any globally unique name***
    -	Resource group: **YOUR_RESOURCE_GROUP**
    -	Location: **West Europe**
    -	SKU: **Basic** 

    > **Note**: Wait for the creation task to complete before moving on with this lab.
    
### Task 4: Open Azure Cloud Shell and store Container Registry metadata

1.  Open a new Cloud Shell instance.

2.  At the **Cloud Shell** command prompt, use the **az acr list -o table** command to get a list of all container registries in your subscription.

3.  Use the following command to output the name of your container registry:

    ```
    az acr list -g YOUR_RESOURCE_GROUP --query "[0].name" --output tsv
    ```

4.  Use the following command to save the name of your container registry in a Bash shell variable named *acrName*:

    ```
    acrName=$(az acr list -g YOUR_RESOURCE_GROUP --query "[0].name" --output tsv)
    ```

5.  Use the following script to render the value of the Bash shell variable *acrName*:

    ```
    echo $acrName
    ```

### Task 5: Deploy a Docker container image to Container Registry

1.  Change the active directory to **\~/clouddrive/ipcheck**.

2.  Use the **ls** command to get the contents of the current directory.

    > **Note**: You'll know that you're in the correct directory if both the **Program.cs** and **Dockerfile** files that you edited earlier in this lab are there.

3.  Use the following command to upload the source code to your container registry and build the container image as a  Container Registry task (don't miss the last dot):

    ```
    az acr build --registry $acrName --image ipcheck:latest .
    ``` 

    > **Note**: Wait for the build task to complete before moving forward with this lab.

4.  Close the Cloud Shell pane.

### Task 6: Validate your container image in Container Registry

1.  Access the container registry that you created earlier in this lab.

2.  Select the **Repositories** link to find your images in the registry.

3.  Proceed through the **Images** and **Tags** blades to find the metadata associated with the **ipcheck** image with the **latest** tag.

    > **Note**: You can also select the **Run ID** link to find the build task metadata.

### Task 7: Enable the admin user in Container Registry

1.  Access the container registry that you created earlier in this lab.

2.  Select **Update** to find the settings for the container registry.

3.  **Enable** the **Admin User**, **Save** your changes, and then close the **Update container registry** blade.

### Task 8: Manually deploy a container image to Container Instances
1. Go to Azure Portal

2.  Create a new Azure container instance resource with the following information :

    -	Resource group: **YOUR_RESOURCE_GROUP**
    -	Container name: **manualcompute**
    -	Region: **West Europe**
    -	Image source: **Azure Container Registry**
    -	Registry: ***Azure Container Registery resource created earlier in the lab**
    -   Image: **ipcheck**
    -   Image tag: **latest**
    -   Size: **2 vcpu, 4 GiB memory**

    > **Note**: Wait for the creation task to complete before moving forward with this lab.

### Task 9: Validate that the container instance ran successfully

1.  Access the **manualcompute** container instance that you created earlier in this lab.

1.  Select the **Containers** link to get a list of the current containers that are running.

1.  Find the contents of the **Events** list for the container instance that ran your **ipcheck** application.

1.  Select the **Logs** tab, and then find the text logs from the container instance.

    > **Note**: You can also optionally find the **Events** and **Logs** from the **managedcompute** container instance.

