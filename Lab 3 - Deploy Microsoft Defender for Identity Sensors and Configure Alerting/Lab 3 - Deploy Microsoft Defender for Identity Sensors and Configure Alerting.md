# Lab 3: Deploy Microsoft Defender for Identity Sensors and Configure Alerting

## Objectives

Microsoft Defender for Identity is a cloud-based security solution that uses on-premises Active Directory signals to detect, identify, and investigate advanced threats, compromised identities, and malicious insider actions. At the core of the solution is a sensor that runs directly on your domain controllers and analyzes their traffic and events in real time. In this lab you will build a domain controller in Azure, onboard it to Defender for Identity with the sensor v3.x standalone onboarding package, configure Windows event collection, resolve health issues, and tune alert thresholds.

- Build a Windows Server 2022 domain controller that meets the sensor v3.x requirements

- Onboard the domain controller to Defender for Identity and validate that the sensor is running

- Configure Windows event collection (automatic auditing) and verify it

- Review and troubleshoot Defender for Identity health issues

- Adjust alert thresholds, generate test activity, and configure alert notifications

**Prerequisites**

A lab administrator account with the **Global Administrator** or **Security Administrator** role.

An Azure subscription in which you can create a resource group and a virtual machine.

A licence that includes **Microsoft Defender for Identity** (included in the Microsoft Defender Suite for Business Premium).

**This lab is self-contained. It does not depend on any other lab in the course.**

**Defender for Identity sensors run only on Windows Server domain controllers (and AD FS, AD CS or Entra Connect servers). A Windows 10/11 client VM will never appear in the Defender portal, which is why this lab builds a real domain controller first.**

## Exercise 1: Build a Domain Controller in Azure

Defender for Identity sensor v3.x requires a domain controller running Windows Server 2019 or later. In this exercise you create a Windows Server 2022 virtual machine and promote it to the first domain controller of a new forest.

### Task 1: Create a Windows Server 2022 virtual machine

This task creates the server that will become your domain controller.

1.  Sign in to the Azure portal at https://portal.azure.com and, under **Azure services**, click **Virtual machines**.

![](media/image1.png)

2.  Click **+ Create** and select **Virtual machine**.

![](media/image2.png)

3.  On the **Basics** tab, enter the following details:

    - **Subscription**: select your lab subscription

    - **Resource group**: click **Create new** and enter **RG-DefenderIdentity**

    - **Virtual machine name**: **DC01-XXXX** (replace XXXX with your unique lab ID)

    - **Region**: select the region provided by your instructor

![](media/image3.png)

- **Availability options**: **No infrastructure redundancy required**

- **Security type**: **Trusted launch virtual machines**

- **Image**: **Windows Server 2022 Datacenter: Azure Edition - x64 Gen2**

![](media/image4.png)

- **Size**: **Standard_D2s_v5** (2 vCPUs, 8 GiB memory) or **Standard_B2ms** if D-series quota is not available

> **Note** - Do not select a Windows 10 or Windows 11 image. The sensor can only be activated on a Windows Server domain controller.

4.  Under **Administrator account**, enter **labadmin** as the **Username** and the following password- **P@55w.rd1234**, and confirm the password. Record the password; you will use it throughout this lab.

![](media/image5.png)

5.  Click **Review + create**. When **Validation passed** appears, click **Create**.

![](media/image6.png)

6.  Wait for **Your deployment is complete**, then click **Go to resource**.

![](media/image7.png)
>
![](media/image8.png)
>
> **Note** - If the deployment fails, check for an unregistered Microsoft.Compute resource provider, a regional vCPU quota limit, or an unavailable VM size, and try another size or region.

### Task 2: Set a static private IP address

A domain controller must keep the same IP address because it is also the DNS server for the domain.

1.  On the **DC01-XXXX** virtual machine page, under **Networking**, click **Network settings**, then click the network interface name.

![](media/image9.png)
>
![](media/image10.png)

2.  Under **Settings**, click **IP configurations** and select **ipconfig1**.

![](media/image11.png)

3.  Change **Allocation** from **Dynamic** to **Static**, note the private IP address, and click **Save**.

![](media/image12.png)

1.  C

2.  +++++++++++++++++++++++

![](media/image13.png)
>
![](media/image14.png)
>
![](media/image15.png)
>
![](media/image16.png)

3.  On the server, right-click **Start** and select **Terminal (Admin)** (or **Windows PowerShell (Admin)**).

![](media/image17.png)

4.  Run the following command to install the Active Directory Domain Services role:

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

![](media/image18.png)

5.  Run the following command to create the new forest. Replace the Directory Services Restore Mode password with your own strong password:

```powershell
Install-ADDSForest -DomainName "contoso.local" -DomainNetbiosName "CONTOSO" -InstallDns `
-SafeModeAdministratorPassword (ConvertTo-SecureString 'P@55w.rd1234’ -AsPlainText -Force) `
-Force
```

![](media/image19.png)
>
> **Note** - The server restarts automatically when promotion completes (about 5-10 minutes). Your RDP session disconnects; this is expected.

6.  Reconnect with RDP and sign in as **CONTOSO\labadmin** with the same password.

7.  Open **Terminal (Admin)** and verify the domain controller is healthy:

```powershell
Get-ADDomain | Select-Object DNSRoot, PDCEmulator
Get-Service NTDS, DNS, Netlogon | Select-Object Name, Status
```

![](media/image20.png)
>
> **Note** - You should see **contoso.local** as the DNSRoot and all three services in the **Running** state.

### Task 4: Prepare the domain controller for the sensor

Complete this task in the RDP session to **DC01-XXXX**, signed in as **CONTOSO\labadmin**. It applies the recommended sensor settings and creates the test accounts you’ll use to generate identity activity.

1.  Set the power plan to **High performance** (Defender for Identity raises a health issue otherwise):

```powershell
powercfg /setactive SCHEME_MIN
powercfg /getactivescheme
```

![](media/image21.png)

2.  Check that the server is fully patched. Sensor v3.0.10 and later requires the September 2026 or later cumulative update (KB5122882 for Windows Server 2022):

```powershell
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 5 HotFixID, InstalledOn
```

![](media/image22.png)
>
> **Note** - If the update is missing, open **Settings \> Windows Update**, click **Check for updates**, install all updates and restart before continuing.

3.  Create an organizational unit and three test users:

```powershell
New-ADOrganizationalUnit -Name "LabUsers" -Path "DC=contoso,DC=local"
$pw = ConvertTo-SecureString 'P@55w.rd1234' -AsPlainText -Force
"jdoe","asmith","bwhite" | ForEach-Object {
New-ADUser -Name $_ -SamAccountName $_ -Path "OU=LabUsers,DC=contoso,DC=local" `
-AccountPassword $pw -Enabled $true
}
Get-ADUser -Filter * -SearchBase "OU=LabUsers,DC=contoso,DC=local" | Select-Object SamAccountName, Enabled
```

![](media/image23.png)

## Exercise 2: Deploy and Validate the Defender for Identity Sensor

The Defender for Identity sensor runs on the domain controller. It reads the controller’s network traffic and security events and sends suspicious identity activity to the Microsoft Defender portal.

There are two ways to activate the latest sensor (v3.x):

- **Through Defender for Endpoint:** if the domain controller is already onboarded to Defender for Endpoint, you can activate the sensor with a single click in the portal. Onboarding a server to Defender for Endpoint needs a separate server licence, which this lab tenant doesn’t have.

- **Standalone onboarding package:** you download a package from the Defender portal and run it on the domain controller. It activates the sensor in identity-only mode, with no Defender for Endpoint needed. **This lab uses this method.**

Complete this exercise in the **RDP session to DC01-XXXX**, signed in as **CONTOSO\labadmin**. Open **Microsoft Edge on the domain controller** to use the Defender portal, so the package downloads straight to the server.

### Task 1: Open the Defender for Identity workspace

Complete this task from the domain controller so that the onboarding package downloads directly to the server.

1.  On **DC01-XXXX**, open **Microsoft Edge**, browse to https://security.microsoft.com and sign in with your lab administrator account. In the left navigation, expand **Setup & configuration** and select **Settings**.

![](media/image24.png)

2.  Click **Identities**.

![](media/image25.png)

3.  If this is the first time Defender for Identity has been opened in the tenant, wait for **Hang on! We're preparing your Microsoft Defender for Identity workspace** to finish.

![](media/image26.png)

**Note** - Workspace creation can take several minutes. Do not refresh the page.

### Task 2: Generate the standalone onboarding package

This task downloads the sensor v3.x onboarding package and its one-time access key.

1.  On the **Identities** settings page, open the **Sensors** page (shown as **On-premises \> Sensor management** in newer tenants). You can also browse directly to <https://security.microsoft.com/securitysettings/identities>.

![](media/image27.png)
>
> **Note** - The Defender portal is being updated. If you see an **Activation** tab rather than **Sensor management**, use it; the options are the same.

2.  Click **Download onboarding package**.

![](media/image28.png)

3.  In the **Download onboarding package** pane, expand **Windows Server 2019 or later**, enter **MDI-Lab-Package** as the **Package name**, and click **Generate package**.

![](media/image29.png)

4.  Click **Download** to save the package to the **Downloads** folder, then click the copy button next to **Access key** and paste the key into **Notepad**.

**\[Screenshot pending: E2T2S4\]**

**The access key is used only during installation. If you regenerate it, the old key stops working and any installation that uses it fails.**

### Task 3: Run the onboarding script on the domain controller

This task installs and activates the sensor.

1.  Open **File Explorer**, go to **Downloads**, right-click the package ZIP and select **Extract All**. Keep the **resources** subfolder with the script.

**\[Screenshot pending: E2T3S1\]**

**Note** - Running the script from inside the ZIP fails. Always extract it first.

2.  In **Terminal (Admin)**, change to the extracted folder and run the onboarding script:

```powershell
Set-Location "$env:USERPROFILE\Downloads\MDI-Lab-Package\DfiOnboarding"
.\DefenderForIdentityV3StandaloneOnboardingScript.cmd
```

**\[Screenshot pending: E2T3S2\]**

**Note** - Adjust the path if your extracted folder has a different name.

3.  When prompted, paste the access key from Notepad and press **Enter**. The input is masked. Wait for the script to report that onboarding completed.

**\[Screenshot pending: E2T3S3\]**

### Task 4: Validate sensor activation

This task confirms that the sensor is registered and running.

1.  In the Defender portal, return to **Settings \> Identities \> Sensors** (**Sensor management**) and click **Refresh**.

**\[Screenshot pending: E2T4S1\]**

2.  Confirm that **DC01-XXXX** is listed with **Type** **Domain controller** and **Sensor status** **Running**.

**\[Screenshot pending: E2T4S2\]**

**Note** - The first sensor activation in a tenant can take up to one hour to show **Running**. No restart is required.

3.  On the server, confirm the sensor can reach the service by checking the latest sensor logs or by waiting for the **Sensor health issues** tab (Exercise 3, Task 4) to show no **Sensor stopped communicating** issue.

## Exercise 3: Configure Event Collection

**Time - 25 minutes**

Several Defender for Identity detections rely on Windows security events from the domain controller, such as 4776 (credential validation), 4662 (object operations) and 5136 (directory changes). In this exercise you let the sensor configure auditing automatically, verify the configuration, and review health issues.

### Task 1: Enable automatic Windows auditing

1.  In the Defender portal, go to **System \> Settings \> Identities**. In the **General** section, click **Advanced features**.

**\[Screenshot pending: E3T1S1\]**

2.  Turn on **Automatic Windows auditing configuration** and click **Save**.

**\[Screenshot pending: E3T1S2\]**

**Note** - The sensor checks the auditing configuration, fixes any gaps in the local system policy, and repeats this every 24 hours. Group Policy settings can override what the sensor applies.

### Task 2: Verify the auditing configuration

This task checks the auditing settings from the domain controller with the DefenderForIdentity PowerShell module.

1.  In **Terminal (Admin)** on **DC01-XXXX**, install the module:

```powershell
Install-Module DefenderForIdentity -Scope AllUsers -Force
```

**\[Screenshot pending: E3T2S1\]**

**Note** - If prompted to install the NuGet provider or trust the PSGallery repository, answer **Y**.

2.  Display the local auditing configuration:

```powershell
Get-MDIConfiguration -Mode LocalMachine -Configuration All | Format-Table -AutoSize
```

**\[Screenshot pending: E3T2S2\]**

3.  Display the effective audit policy:

```powershell
auditpol.exe /get /category:*
```

**\[Screenshot pending: E3T2S3\]**

**Note** - Credential Validation, Directory Service Access, Directory Service Changes, and Security Group Management should show **Success and Failure**.

4.  If any required setting still shows as not configured after 30 minutes, apply all local settings immediately:

```powershell
Set-MDIConfiguration -Mode LocalMachine -Configuration All
gpupdate /force
```

### Task 3: Confirm that events are generated

1.  Sign out of RDP, reconnect and enter a wrong password once for **CONTOSO\jdoe**, then sign in again as **CONTOSO\labadmin**.

2.  Open **Event Viewer**, expand **Windows Logs** and select **Security**. Click **Filter Current Log**, enter **4776** in the event ID box and click **OK**.

**\[Screenshot pending: E3T3S2\]**

**Note** - You should see credential validation events, including a failure for **jdoe**.

### Task 4: Troubleshoot Defender for Identity health issues

Health issues report sensor and configuration problems that reduce detection coverage.

1.  In the Defender portal, go to **System \> Settings \> Identities**. Under **Deployment**, select **Health issues**.

**\[Screenshot pending: E3T4S1\]**

2.  Review the **Global health issues** tab, then the **Sensor health issues** tab. Select any open issue to read its details and recommended fix.

**\[Screenshot pending: E3T4S2\]**

3.  Use the table below to resolve the most common issues on a new domain controller:

    - **NTLM Auditing is not enabled** / **Directory Services Advanced Auditing is not enabled as required**: confirm Task 1 is enabled, or run **Set-MDIConfiguration -Mode LocalMachine -Configuration All**

    - **Directory Services Object Auditing** / **Auditing on the Configuration container is not enabled as required**: run **Set-MDIConfiguration -Mode Domain -Configuration All** on the domain controller

    - **Power mode isn't configured for optimal processor performance**: run **powercfg /setactive SCHEME_MIN**

    - **Sensor stopped communicating**: check that the VM is running and that outbound HTTPS to the Defender for Endpoint service URLs is not blocked

**Note** - Most issues close automatically once Defender for Identity detects the fix. Some auditing health issues can remain visible for several hours after the configuration is corrected; this does not affect detections.

## Exercise 4: Configure and Manage Defender for Identity Alerts

**Time - 25 minutes**

Defender for Identity detections normally use a learning period and High thresholds to reduce false positives. For lab and proof-of-concept environments, Microsoft provides Recommended test mode, which lowers every threshold so alerts appear immediately.

### Task 1: Adjust alert thresholds

1.  In the Defender portal, go to **System \> Settings \> Identities** and click **Adjust alert thresholds**.

**\[Screenshot pending: E4T1S1\]**

2.  Review the detections that support threshold levels, for example **Security principal reconnaissance (LDAP)**, **Suspected Brute Force attack (Kerberos, NTLM)**, and **User and Group membership reconnaissance (SAMR)**. Read the **Information** column to see how Medium and Low change each detection.

**\[Screenshot pending: E4T1S2\]**

3.  Turn on **Recommended test mode**, set the end time to **tomorrow**, and click **Apply changes**.

**\[Screenshot pending: E4T1S3\]**

**While test mode is on, all thresholds are set to Low and are read-only. Turning it off restores the previous values. Never leave test mode on in production.**

### Task 2: Generate test activity and review it

This task runs harmless reconnaissance commands and shows the activity Defender for Identity captured.

1.  On **DC01-XXXX**, open a **Command Prompt** and run the following reconnaissance commands:

```powershell
net user /domain
net group "Domain Admins" /domain
nltest /dclist:contoso.local
```

2.  In the Defender portal, go to **Investigation & response \> Hunting \> Advanced hunting** and run the following query:

```kusto
IdentityQueryEvents
| where Timestamp > ago(2h)
| project Timestamp, ActionType, QueryType, QueryTarget, DeviceName, AccountName
| order by Timestamp desc
```

**\[Screenshot pending: E4T2S2\]**

**Note** - Results confirm that the sensor is capturing SAMR, LDAP and DNS queries. Data can take 10-15 minutes to appear.

3.  Go to **Investigation & response \> Incidents & alerts \> Alerts**, click **Filter**, set **Service source** to **Microsoft Defender for Identity**, and review any alerts.

**\[Screenshot pending: E4T2S3\]**

**Note** - Activity that originates on the domain controller itself may be filtered by some detections. If no alert appears, the Advanced hunting results are sufficient proof that event collection works.

### Task 3: Configure alert email notifications

This task sends an email to the security team when Defender for Identity raises an incident.

1.  In the Defender portal, go to **System \> Settings \> Microsoft Defender XDR** and select **Email notifications**.

**\[Screenshot pending: E4T3S1\]**

2.  On the **Incidents** tab, click **+ Add incident notification rule**.

**\[Screenshot pending: E4T3S2\]**

3.  Enter **MDI-Lab-Incident-Notification** as the name, click **Next**, and on **Notification settings** set **Service sources** to **Microsoft Defender for Identity** and select severities **High** and **Medium**. Click **Next**.

**\[Screenshot pending: E4T3S3\]**

4.  Add your administrator email address as a recipient, click **Next**, and then click **Create rule**.

**\[Screenshot pending: E4T3S4\]**

## Summary

In this lab you built a Windows Server 2022 domain controller in Azure, onboarded it to Microsoft Defender for Identity with the sensor v3.x standalone onboarding package, and validated that the sensor is running. You enabled automatic Windows auditing, verified the audit policy and events on the server, and resolved common health issues. Finally, you enabled Recommended test mode, generated reconnaissance activity, reviewed the captured identity events in Advanced hunting, and configured incident email notifications.

## Clean up resources

1.  In the Defender portal, open **Settings \> Identities \> Adjust alert thresholds**, turn off **Recommended test mode** and click **Apply changes** (or let it expire at the end time you set).

2.  In the Azure portal, open **DC01-XXXX** and click **Stop** to deallocate the VM and stop compute charges. Delete the **RG-DefenderIdentity** resource group at the end of the course.
