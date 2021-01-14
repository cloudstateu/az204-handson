<img src="../../../img/logo.png" alt="Chmurowisko logo" width="200" align="right">
<br><br>
<br><br>
<br><br>

# Deploying compute workloads by using images and virtual machines.

## LAB Overview

### Objectives

After you complete this lab, you will be able to:

-   Create a VM by using the Azure Command-Line Interface (CLI).

-   Install simple webserver and expose it to the Internet

## Instructions

### Task 1: Run CloudShell environment


1.  Sign in to the Azure portal (<https://portal.azure.com>).

2.  Open a new Cloud Shell instance in the Azure portal. (Click proper icon on a right side of a top bar)

3.  If the Cloud Shell is not already configured, configure the shell for **Bash** by using the default settings.

### Task 2: Create new vm using azure cli

1.  Use the **az** command with the **\-\-help** flag to find a list of subgroups and commands at the root level of the CLI.

2.  Use the **az vm** command with the **\-\-help** flag to find a list of subgroups and commands for Azure Virtual Machines:

3.  Use the **az vm create** command with the **\-\-help** flag to find a list of arguments and examples for the **Create Virtual Machine** command:

4.  Use the **az vm create** command to create a new VM with the following settings:
    
    -	Resource group: **YOUR_RESOURCE_GROUP**
    -	Name: **quickvm**
    -	Image: **Debian**
    -	Username: **student**
    -	Password: **StudentPa55w.rd** 
    -   Size: **Standard_DS1_v2**

    > **Note**: Wait for the VM creation process to complete. After the process completes, the command will return a JavaScript Object Notation (JSON) file with details about the machine.

5.  Use the **az vm show** command to find a more detailed JSON file that contains various metadata about the newly created VM.

6.  Use the **az vm list-ip-addresses** command to list all the IP addresses associated with the VM:

    ```
    az vm list-ip-addresses --resource-group YOUR_RESOURCE_GROUP --name quickvm
    ```

7.  Use the **az vm list-ip-addresses** command and the **\-\-query** argument to filter the output to only return the first IP address value:

    ```
    az vm list-ip-addresses --resource-group YOUR_RESOURCE_GROUP --name quickvm --query '[].{ip:virtualMachine.network.publicIpAddresses[0].ipAddress}' --output tsv
    ```

8.  Use the following script to store the results of the previous command in a new Bash shell variable named *ipAddress*:

    ```
    ipAddress=$(az vm list-ip-addresses --resource-group YOUR_RESOURCE_GROUP --name quickvm --query '[].{ip:virtualMachine.network.publicIpAddresses[0].ipAddress}' --output tsv)
    ```

9.  Use the following script to render the value of the Bash shell variable *ipAddress*:

    ```
    echo $ipAddress
    ```

10.  Use the following script to connect to the VM that you created earlier in this lab by using the Secure Shell (SSH) tool and the IP address stored in the Bash shell variable *ipAddress*:

    ```
    ssh student@$ipAddress
    ```

11.  During the connection process, you'll receive a warning that the authenticity of the host can't be verified. Continue connecting to the host. Finally, use the password **StudentPa55w.rd** when prompted for credentials

12.  After connecting to the VM, use the following command to get information about the machine to ensure that you're connected to the correct VM:

    ```
    uname -a
    ```

### Task 3: Setup simple http server on your vm

1. Update OS: `sudo apt update -y`
2. Install Apache Server: `sudo apt install apache2 -y`
3. Check that server is running: `curl -I localhost`. You should get a 200 HTTP response status.
4. Now we want to open our webserver running on port 80 for the whole internet. Go to Azure portal, look for a **quickvm** in your resource group and click it. 
5. On the left menu, look for a **Networking** button (in **settings** section) and click it.
6. Click **Add inbound port rule**
7. Fill in the form:
- Source: **Any**
- Source port ranges: *
- Destination: **Any**
- Destination port ranges: **80**
- Protocol: **Any**
- Action: **Allow**
- Priority: **leave default**
- Name: **Port_80**

7. Click **Add**
8. Go to web browser and enter your vm's public ip. You should get an apache welcome page. (You may need to wait a few minutes for the firewall rules to apply)

9.   Use the **exit** command to end your SSH session:

    ```
    exit
    ```

10. Close the Cloud Shell pane.
