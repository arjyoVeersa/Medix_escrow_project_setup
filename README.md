# Medix Escrow Project Setup

## Step-by-Step Instructions

### 1. Extract the Medix Escrow Project
Extract the project folder to a location on your system. Make note of the file path for future reference.

---

### 2. Update the HOSTS File
1. Open `C:\Windows\System32\drivers\etc\hosts` in a text editor with administrative privileges.
2. Add the following entries, replacing `<your ip>` with your local IP address (found using `ipconfig` in Command Prompt):
   ```plaintext
   <your ip>  proxy-dev.medixinfusion.com
   <your ip>  if-dev.medixinfusion.com
   <your ip>  if-df.medixinfusion.com
   ```
3. Save the file. Note that you may need to update this file if your IP address changes.

---

### 3. Enable IIS Features
1. Open **Turn Windows Features On or Off** from Windows Search.
2. Select **Internet Information Services (IIS)** and all its sub-options.
3. Ensure **IIS Hostable Web Core** is checked.
4. Enable **.NET Framework 3.5** and **.NET Framework 4.8 Advanced Services**.
5. Open the IIS Management Console:
   - If ASP.NET is not enabled in IIS Management Console, ensure that all options containing ASP.NET or .NET Framework are checked in the **Turn Windows Features On or Off** window and restart your computer.
   - This should be visible in IIS Management Console:-
     ![image](https://github.com/user-attachments/assets/a476ff5e-18e8-4488-864c-6c1af83778f8)

   - (Optional) If it still does not work, run the following command in Command Prompt to enable all required ASP.NET features:
     ```cmd
     Start /w pkgmgr /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-ApplicationDevelopment;IIS-ASPNET;IIS-NetFxExtensibility;IIS-ISAPIExtensions;IIS-ISAPIFilter;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI
     ```

---

### 4. Open and Compile DocFlock
1. Navigate to the `df` folder and open the `docflock_medix.sln` solution in Visual Studio.
2. Compile the solution with no errors.

---

### 5. Set Up IIS for DocFlock
1. Open the IIS Management Console.
2. Create a new web server for `if-df.medixinfusion.com`:
   - Right-click on **Sites** in the left-hand dropdown.
     ![image](https://github.com/user-attachments/assets/2d9c0a5e-61d6-44bf-bac0-e9b5bf04ac1d)
   - Fill in the following details:
     - **Site Name**: `if-df.medixinfusion.com`
     - **Physical Path**: `<your_path>\medix_escrow-main\df\DocFlock.Web`
     - **Host Name**: `if-df.medixinfusion.com`
     - **Binding**: Configure both `http` and `https` bindings for this site.
       ![image](https://github.com/user-attachments/assets/acb99fbe-ff29-4b44-acb5-d0597661571a)

   - Add a binding for HTTP as well:
     ![image](https://github.com/user-attachments/assets/6b759106-3274-4c00-bc0e-acc94b50425f)

   - Set permissions:
     1. Go to **Edit Permissions** in the right-hand Actions pane.
     2. Navigate to the **Security** tab and click **Edit**.
     3. Add the following users with Full Control:
        - `IUSR`
        - `IIS_IUSRS`
        - `IIS APPPOOL\if-df.medixinfusion.com`

       ![image](https://github.com/user-attachments/assets/b42f8e2f-381c-423f-8aa2-384d6fad1018)
       ![image](https://github.com/user-attachments/assets/c9547cbd-8789-4242-9513-320e6609475b)

     - Use **Advanced** to find and select the required users:
       ![image](https://github.com/user-attachments/assets/61d4793c-1aa0-4a90-9262-3144ca6706b2)

   - Browse the `if-df.medixinfusion.com` site:
     ![image](https://github.com/user-attachments/assets/31d954b2-497f-43e6-97ed-3799931758f9)

3. (Optional) If the site does not work:
   - Open **Windows Defender Firewall with Advanced Security**.
   - Go to **Inbound Rules** > **New Rule** and add a new rule for ports `80` and `443`:
     ![image](https://github.com/user-attachments/assets/c972f4cd-f7e1-4120-af11-62bdc3452c61)

---

### 6. Install URL Rewrite 
If browsing the site on `if-df.medixinfusion.com` fails:
1. Download and install the **URL Rewrite** module for IIS.
2. Restart IIS to apply changes.

---


### 7. Configure and Compile InfuseFlow
1. Open **Configure Startup Projects** in Visual Studio.
2. Set  `InfuseFlow.Web to start with `InfuseFlow.Web` instead of IIS Express.
 ![image](https://github.com/user-attachments/assets/79ef9c36-a3d2-4c36-8c49-331bb2455386)
3. Click on set multiple startup projects.
4. Set `InfuseFlow.Proxy` to **Start Without Debugging**.
5. Set `InfuseFlow.Web` to **Start**.
6. Build in both `DFIIS` and `Debug` profiles.
7. Download and Install ssl certificate in InfuseFlow.Proxy security folder.
8. Compile the solution. Upon running, two console app pages should open automatically.

---

### 8. Debugging DocFlock
1. Open the **Debug** tab in Visual Studio.
2. Go to **Attach to Process**:
   ![image](https://github.com/user-attachments/assets/5e2da9ac-d907-4015-8952-f5d1a943aeb4)
3. Attach the process `w3wp` to DocFlock (ensure `if-df.medixinfusion.com` is running in the browser):
   ![image](https://github.com/user-attachments/assets/2eff3240-2ae2-462a-b4ba-3df3e98899f4)

---

### 9. Database Setup
Refer to the database README file for details on setting up the database and connection strings.

1. In IIS Management Console, go to **Connection Strings**:
   ![image](https://github.com/user-attachments/assets/1977f9f3-a94c-460a-a155-9fff8f9b96de)
   - Click on Add from the right hand side and add your database configurations.
   ![image](https://github.com/user-attachments/assets/debbc0ed-0489-4618-bcb5-b4227bfa3236)
   ![image](https://github.com/user-attachments/assets/1f861993-39e1-4d48-ac4a-7469ca58c249)

---

### 10. Launch the Project
1. Click on the HTTPS URL for `if-df.medixinfusion.com` from IIS.
2. Verify that the login screen for the website appears.
