# Medix Escrow Project Setup


## Step-by-Step Instructions

### 1. Extract the Medix Escrow Project
Extract the project folder to a location on your system. Make note of the file path for future reference.

### 2. Update the HOSTS File
1. Open `C:\Windows\System32\drivers\etc\hosts` in a text editor with administrative privileges.
2. Add the following entries, replacing `<your ip>` with your local IP address (found using `ipconfig` in Command Prompt):
   ```plaintext
   <your ip>  proxy-dev.infuseflow.com
   <your ip>  if-dev.infuseflow.com
   <your ip>  if-df.infuseflow.com
   ```
3. Save the file. Note that you may need to update this file if your IP address changes.

### 3. Enable IIS Features
1. Open **Turn Windows Features On or Off** from Windows Search.
2. Select **Internet Information Services (IIS)** and all its sub-options. 
3. Ensure **IIS Hostable Web Core** is checked.
4. Check .Net Framework 3.5 and .Net Framework 4.8 Advanced Series also.
5. Open IIS Management Console 
   - If Asp.Net is not enabled in IIS management console then make sure to tick all options containing asp.net or .net framework within the turn windows features on or off window again and restart your computer.
   - (Optional) If it's still not enabled use this command in cmd. This enables all asp.net features required within IIS Management console
     ```cmd
     Start /w pkgmgr /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-ApplicationDevelopment;IIS-ASPNET;IIS-NetFxExtensibility;IIS-ISAPIExtensions;IIS-ISAPIFilter;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI
     ```
### 4. Open and Compile DocFlock
1. Navigate to the `df` folder and open the `dockflock.sln` solution in Visual Studio.
2. Compile the solution with no errors.

### 5. Set Up IIS for DocFlock
1. Open the IIS Management Console.
2. Create a new web server for `if-df.infuseflow.com`:
   - Right click on Sites in the left hand side dropdown
   ![image](https://github.com/user-attachments/assets/2d9c0a5e-61d6-44bf-bac0-e9b5bf04ac1d)

   Make IIS Site by filling these:-
   - **Site Name**: `if-df.infuseflow.com`
   - **Physical Path**: `<your_path>\medix_escrow-main\df\DocFlock.Web`
   - **Host Name**: `if-df.infuseflow.com`
   - **Binding**: Configure both `http` and `https` bindings for this site.
   ![image](https://github.com/user-attachments/assets/acb99fbe-ff29-4b44-acb5-d0597661571a)

   Click on binding in right hand actions pane and add for http also.
   
   ![image](https://github.com/user-attachments/assets/6b759106-3274-4c00-bc0e-acc94b50425f)

   Go to Edit permissions in right hand side, to security, click on edit and add IUSR, IIS_IUSRS and IIS APPPOOL\if-df.infuseflow.com and give them Full Control.

<img width="960" alt="image" src="https://github.com/user-attachments/assets/b42f8e2f-381c-423f-8aa2-384d6fad1018" />
<img width="267" alt="image" src="https://github.com/user-attachments/assets/c9547cbd-8789-4242-9513-320e6609475b" />

Write IIS APPPOOL\if-df.infuseflow.com in Enter the object names to select and for IUSR and IIS_IUSRS go to advanced and select them.
Go to Find Now and select them


<img width="388" alt="image" src="https://github.com/user-attachments/assets/61d4793c-1aa0-4a90-9262-3144ca6706b2" />


Click on browse if-df.infuseflow.com on *.443
<img width="960" alt="image" src="https://github.com/user-attachments/assets/31d954b2-497f-43e6-97ed-3799931758f9" />

(Optional) If it does not work go to `Windows Defender Firewall with Advanced Security`. Go to inbound rules. Click New Rule and add new port
<img width="784" alt="image" src="https://github.com/user-attachments/assets/c972f4cd-f7e1-4120-af11-62bdc3452c61" />
Add 80, 443 in Specific Local Ports 
![image](https://github.com/user-attachments/assets/2e0559f9-c51b-41cd-a637-2e660c130585)

### 8. Install URL Rewrite (Optional)
If on browsing the website on if-df.infuseflow.com then :-
1. Download and install the URL Rewrite module for IIS.
2. Restart IIS to apply the changes.

### 9. Add Database Connection String in IIs Management Console

Go to connection strings

<img width="960" alt="image" src="https://github.com/user-attachments/assets/1977f9f3-a94c-460a-a155-9fff8f9b96de" />

![image](https://github.com/user-attachments/assets/debbc0ed-0489-4618-bcb5-b4227bfa3236)

<img width="960" alt="image" src="https://github.com/user-attachments/assets/1f861993-39e1-4d48-ac4a-7469ca58c249" />

For more details on how to setup database go to < database reamdme link>






### 6. Open the InfuseFlow Solution
1. Navigate to the `gen2` folder and open `infuseflow_medix.sln` in Visual Studio.
2. Make the following changes:
   - **`InfuseFlow.Web`**: Comment out the line `app.UseHttpsRedirection();` (line 61 in `Startup.cs`).
   - **`InfuseFlow.Proxy`**: Comment out lines 74-93 in `Startup.cs`:
     ```csharp
     app.UsePresence(Configuration, options =>
     {
         if (bool.Parse(config["USE_SSL"]))
         {
             var root = Environment.GetEnvironmentVariable("PWD");
             var certPath = string.IsNullOrEmpty(root)
                 ? config["HTTPS_CERTIFICATE"]
                 : System.IO.Path.Combine(root, config["HTTPS_CERTIFICATE"]);
             var certName = config["HTTPS_CERTIFICATE"];
             if (!string.IsNullOrEmpty(certName))
             {
                 options.Certificate = new X509Certificate2(certPath, config["HTTPS_CERTIFICATE_PASSWORD"]);
             }
         }
     });
     app.UseHttpsRedirection();
     ```

### 5. Configure and Compile InfuseFlow
1. Open **Configure Startup Projects** in Visual Studio.
2. Set `InfuseFlow.Proxy` to **Start Without Debugging**.
3. Set `InfuseFlow.Web` to **Start**.
4. Change the build profile to `DFIIS`.
5. Compile the solution. Upon running, two Swagger pages should open automatically.



### 9. Database connection string write that also
how the user is created is written in another document that should be referenced here

### 9. Launch the Project
1. Click on the HTTPS URL for `if-df.infuseflow.com` from IIS.
2. Verify that the login screen for the website appears.

### 10. Debugging DocFlock
1. Open the **Debug** tab in Visual Studio.
2. Go to Attach to Process

    <img width="960" alt="image" src="https://github.com/user-attachments/assets/5e2da9ac-d907-4015-8952-f5d1a943aeb4" />
3. Attach the process  `w3wp` to DockFlock ( make sure that if-df.infuseflow.com is running on browser)
    ![image](https://github.com/user-attachments/assets/2eff3240-2ae2-462a-b4ba-3df3e98899f4)



   




---
