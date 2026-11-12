# Lab 6: Configure Core Endpoint Security Policies in Microsoft Defender for Business

## Objectives

Microsoft Defender for Business brings enterprise-grade endpoint protection to Microsoft 365 Business Premium: next-generation antivirus, firewall, attack surface reduction, endpoint detection and response, and automatic attack disruption. In this lab you will prepare and onboard a Windows 11 device, run the Defender for Business setup wizard, review and harden the default security policies, deploy disk encryption and attack surface reduction rules through Intune, connect device risk to compliance and Conditional Access, and test detection, attack disruption and alert notifications.

- Enroll a Windows 11 device in Intune and onboard it to Defender for Business

- Run the Defender for Business setup wizard and review the default security policies

- Turn on recommended advanced features such as tamper protection

- Deploy a BitLocker disk encryption policy and attack surface reduction (ASR) rules

- Use the Defender device risk score in an Intune compliance policy and a device-based Conditional Access policy

- Run a detection test, review attack disruption and response actions, and configure alert email notifications

**Prerequisites**

A lab administrator account with the **Global Administrator** role.

A **Windows 11 Pro or Enterprise** lab VM on which you can sign in as a local administrator, not joined to Active Directory or Microsoft Entra ID.

Microsoft 365 Business Premium (Defender for Business and Intune). A tenant that also has the Defender Suite still uses the Defender for Business experience.

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

## Exercise 1: Prepare the Lab Environment

**Time - 20 minutes**

In this exercise you create the lab group and test user, allow Windows devices to enroll in Intune automatically, and join the Windows 11 VM to Microsoft Entra ID so that it enrolls in Intune.

### Task 1: Create the lab user and group

1.  Sign in to the Microsoft Entra admin center at https://entra.microsoft.com with your lab administrator account. Go to **Entra ID \> Users \> All users** and click **+ New user \> Create new user**.

![](media/image1.png)

2.  Create a user with **User principal name** **l6user**, **Display name** **Lab6 User**, and the following password – P@55w.rd1234. Click **Review + create**, then **Create**.  
    ![](media/image2.png)

![](media/image3.png)

3.  Go to **Entra ID \> Groups \> All groups**, click **New group**, and create a **Security** group named **SG-L6-Pilot** with **Membership type** **Assigned**. Add **Lab6 User** as a member and click **Create**.

![](media/image4.png)
>
![](media/image5.png)
>
![](media/image6.png)

### Task 2: Confirm Windows automatic enrollment

1.  Sign in to the Microsoft Intune admin center at https://intune.microsoft.com and go to **Devices \> Enrollment**. On the **Windows** tab, click **Automatic Enrollment**.

![](media/image7.png)

2.  Confirm **MDM user scope** is **All**. If it is **None** or **Some**, set it to **All**, set **WIP user scope** to **None**, and click **Save**.

![](media/image8.png)

### Task 3: Join the Windows 11 VM to Microsoft Entra ID

> In this task you create a Windows 11 Enterprise VM in the Azure subscription provided with your lab, joined to Microsoft Entra ID and enrolled in Intune automatically.

1.  Browse to **https://portal.azure.com**, and sign in with the **Azure Credentials** shown in your lab environment details panel.

![](media/image9.png)

2.  Search for **Virtual machines**, open it, click **+ Create** and select **Azure virtual machine** (shown as **Virtual machine** in some tenants).

![](media/image10.png)

3.  On the **Basics** tab, enter the following details:

    - **Subscription**: the subscription provided with your lab

    - **Resource group**: select the existing resource group shown in your environment details (for example **smb-rg**)

    - **Virtual machine name**: **L6-W11-XXXX** (replace XXXX with your lab ID; 15 characters maximum)

    - **Region**: the same region as the resource group

    - **Availability options**: **No infrastructure redundancy required**

    - **Security type**: **Trusted launch virtual machines**

    - **Image**: **Windows 11 Enterprise, version 24H2 - x64 Gen2** (or the latest Windows 11 Enterprise version listed)

    - **Size**: **Standard_D2s_v5** (2 vCPUs, 8 GiB memory), or **Standard_B2ms** if that size is not available

![](media/image11.png)
>
> **Note** - Use Windows 11 version 24H2 or later. Microsoft Entra sign-in for Azure VMs does not support older Windows 11 builds, and Windows Server VMs cannot enroll in Intune.

4.  Under **Administrator account**, enter **l6admin** as the **Username** and a the following password—P@55w.rd1234, and confirm it. Record the password.

![](media/image12.png)

5.  Under **Inbound port rules**, select **Allow selected ports** and choose **RDP (3389)**. Under **Licensing**, select **I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights**.

![](media/image13.png)

6.  On the **Management** tab, under **Microsoft Entra ID**, select **Login with Microsoft Entra ID**.

![](media/image14.png)
>
> **Note** - Selecting Microsoft Entra login turns on the system-assigned managed identity automatically and installs the **AADLoginForWindows** extension, which joins the VM to Microsoft Entra ID during deployment.

7.  On **Review + create** page. When **Validation passed** appears, click **Create**, and wait for **Your deployment is complete** (about 5 minutes). Click **Go to resource**.

![](media/image15.png)
>
![](media/image16.png)
>
![](media/image17.png)

8.  On the VM page, select **Settings \> Extensions + applications** and confirm **AADLoginForWindows** shows **Provisioning succeeded**.

![](media/image18.png)

9.  In the Entra admin center, go to **Entra ID \> Devices \> All devices** and confirm **L6-W11-XXXX** is listed with **Join type** **Microsoft Entra joined**.

![](media/image19.png)

10. In the Intune admin center, go to **Devices \> Windows** and confirm the VM appears with **Managed by** **Intune**.

> **Note** - Intune enrollment can take up to 15 minutes. If the VM is still missing, complete Task 4.

11. Back on the Azure portal, go to VM **Overview** page, click **Connect \> Connect**, select **Native RDP**, click **Download RDP file**.

![](media/image20.png)
>
![](media/image21.png)
>
![](media/image22.png)

12. Open the downloaded RDP file, and sign in as **.\l6admin** with the following password --- P@55w.rd1234. You will use this RDP session later in the lab.

![](media/image21.png)
>
![](media/image23.png)
>
![](media/image24.png)
>
![](media/image25.png)
>
> **Note** - If RDP from the jump box is blocked, use **Connect via Bastion** on the VM page instead.

### Task 4: Enroll the VM in Intune manually

Use this task only if the VM did not appear in **Intune \> Devices \> Windows** after 15 minutes. Complete these steps inside the RDP session to **L6-W11-XXXX**.

1.  On the test VM click Settings, select **Accounts**\> **Access work or school**

![](media/image26.png)

2.  Select Connect and enter the tenant credentials.

3.  Once the device is connected expand the user details pane, and click **Info**

![](media/image27.png)
>
![](media/image28.png)

4.  Select **Sync** to sync the user

![](media/image29.png)

5.  Open **Terminal** and run the following command. Confirm **AzureAdJoined : YES**:

```powershell
dsregcmd /status
```

![](media/image30.png)
>
![](media/image31.png)
>
> **Note** - If there is no **Info** button, the device joined Entra ID but did not enroll in Intune. Go back to **Access work or school**, click **Enroll only in device management**, and sign in with your lab administrator account.

6.  Return to the Intune admin center, go to **Devices \> Windows**, and click **Refresh** until the VM appears.

![](media/image32.png)

## Exercise 2: Set Up the Default Settings in Defender for Business

**Connect Defender to Intune and Onboard the Device**

**Task 1: Turn on the Intune connection in the Defender portal**

1.  Open the Microsoft Defender portal at <https://security.microsoft.com>. In the left navigation, go to **System \> Settings** and select **Endpoints**.

![](media/image33.png)
>
![](media/image34.png)
>
> **Note:** If the **Home** page shows **Welcome to Microsoft Defender for Business**, ignore it and use the left navigation. Click **Show navigation** if the menu is hidden. The first time the portal is opened, Defender can take a few minutes to set itself up.

2.  Under **General**, select **Optional features**.

![](media/image35.png)

3.  Turn **Microsoft Intune connection** **On** and click **Save preferences**.

![](media/image36.png)

**Task 2: Turn on the Defender connector in Intune**

1.  In the Intune admin center (<https://intune.microsoft.com>), go to **Endpoint security \> Microsoft Defender for Endpoint**.

![](media/image37.png)

2.  Confirm **Connection status** shows **Available**.  
    **Note:** The status can take up to 15 minutes to change. Click **Refresh** if needed.

3.  Under **Compliance policy evaluation**, set **Connect Windows devices version 10.0.15063 and above to Microsoft Defender for Endpoint** to **On**, and click **Save**.

![](media/image38.png)

**Task 3: Download the onboarding package**

1.  In the Defender portal, go to **System \> Settings \> Endpoints**. Under **Device management**, select **Onboarding**

![](media/image39.png)
>
> **Note:** You can also go to **Assets \> Devices** and click **Onboard**, which opens the same page.

2.  Under **Select operating system to start onboarding process**, select **Windows 10 and 11**.

3.  Set **Connectivity type** to **Streamlined** and **Deployment method** to **Local Script (for up to 10 devices)**.

![](media/image40.png)

4.  Click **Download onboarding package** and save the ZIP file to the **Downloads** folder on the jump box.

![](media/image41.png)

**Task 4: Run the onboarding script on the Windows 11 VM**

1.  Copy the ZIP file from the lab vm and paste it onto the **test VM** of the RDP session to **L6-W11-XXXX**. Copy and paste works over RDP.

![](media/image42.png)

2.  In the RDP session, right-click the ZIP file, select **Extract All**, and extract it to the **Desktop**.

3.  Right-click **Start**, select **Terminal (Admin)**, and run:

> & "\$env:USERPROFILE\Desktop\WindowsDefenderATPOnboardingPackage\WindowsDefenderATPLocalOnboardingScript.cmd"
>
> **Note:** Adjust the path if your extracted folder has a different name.  
> 4. When prompted **Press (Y) to confirm and continue**, type **Y** and press **Enter**. Wait for the message that the device was onboarded successfully, then press any key to close the script.
>
![](media/image43.png)

**Task 5: Verify the device is onboarded**

1.  In the RDP session, run this in **Terminal (Admin)** and confirm **Status** is **Running**:

Get-Service -Name Sense \| Select-Object Name, Status

![](media/image44.png)

2.  In the Defender portal, go to **Assets \> Devices** and search for **L6-W11-XXXX**. Confirm it shows **Onboarding status: Onboarded** and **Sensor health state: Active**.

![](media/image45.png)  
> **Note:** A newly onboarded device usually appears within 5–15 minutes. Click **Refresh** if it isn’t listed yet. You can start Exercise 3 while you wait.

## Exercise 3: Review and Edit the Default Security Policies

**Time - 20 minutes**

Defender for Business groups its policies into next-generation protection, firewall, web content filtering and attack surface reduction. In this exercise you review the defaults the wizard created and turn on the recommended advanced features.

### Task 1: Review the next-generation protection and firewall policies

1.  In the Defender portal, go to **Endpoints \> Configuration management \> Device configuration**.

![](media/image46.png)
>
> **Note** - The navigation label can differ slightly between tenants. You can also search for **Device configuration** in the portal search box.

2.  Expand **Windows (Next-generation protection)** and open the default policy. Click Edit to review the settings, including **Real-time protection**, **Cloud-delivered protection**, **Network protection** and **Potentially unwanted application (PUA) protection**.

![](media/image47.png)
>
![](media/image48.png)

3.  Click **Edit**, set **Network protection** to **Block mode** (if it is not already), and save the policy.

![](media/image49.png)
>
> **Note** - Block mode is recommended for production and is required if you later use Defender for Cloud Apps to block unsanctioned apps.

4.  Expand **Windows (Firewall)** and open the default policy. Confirm the firewall is **On** for **Domain**, **Private** and **Public** networks. Do not change any settings.

![](media/image50.png)
>
![](media/image51.png)

5.  Open **Intune admin center \> Endpoint security \> Antivirus** and **Endpoint security \> Firewall** and note that the policies created by Defender for Business are listed there too.

![](media/image52.png)
>
![](media/image53.png)
>
> **Note** - Policies managed in the Defender portal are shown in Intune as Antivirus and Firewall policies. Always edit them in the portal where they were created.

### Task 2: Turn on recommended advanced features

These tenant-wide settings harden all onboarded devices.

1.  In the Defender portal, go to **Settings \> Endpoints \> General \> Optional features**.

![](media/image54.png)

2.  Turn **On** the following features, then click **Save preferences**:

    - **Tamper protection**: prevents malware or local admins from turning off Defender protection

    - **Automated investigation** (and **Automatically resolve alerts** if shown)

    - **Allow or block file**

    - **Custom network indicators**

    - **Intune connection** (normally already on after the wizard)

    - **Preview features**

![](media/image55.png)
>
![](media/image56.png)
>
> **Note** - Feature names vary slightly between Defender for Business and Defender for Endpoint. Turn on whichever of these appear in your tenant.

## Exercise 4: Configure a Disk Encryption Policy

**Time - 10 minutes**

BitLocker protects data on lost or stolen devices. In Business Premium, disk encryption is deployed from the Intune admin center.

### Task 1: Create a BitLocker policy

1.  In the Intune admin center, go to **Endpoint security \> Disk encryption** and click **+ Create policy**.

![](media/image57.png)

2.  Set **Platform** to **Windows** and **Profile** to **BitLocker**, then click **Create**.

![](media/image58.png)

3.  On **Basics**, enter **L6-BitLocker-Windows** as the name and click **Next**.

![](media/image59.png)

4.  On **Configuration settings**, under **BitLocker**, set **Require Device Encryption** to **Enabled**, **Allow Warning For Other Disk Encryption** to **Disabled**, and **Allow Standard User Encryption** to **Enabled**. Under **Operating System Drives**, enable **Enforce drive encryption type on operating system drives** with **Used Space Only encryption**. Click **Next**.

![](media/image60.png)

![](media/image61.png)

5.  Skip **Scope tags**. On **Assignments**, click **Add all devices**, then **Next** and **Create**.

![](media/image62.png)
>
![](media/image63.png)
>
![](media/image64.png)
>
> **Note** - Many lab VMs have no virtual TPM, so encryption may report an error on the VM. This is expected in the lab; the policy itself is correct for physical devices.

## Exercise 5: Enable Attack Surface Reduction Rules

**Time - 10 minutes**

ASR rules block common malware behaviours such as Office macros launching processes or credential theft from LSASS. Microsoft recommends deploying new rules in **Audit** mode first.

### Task 1: Create an ASR rules policy

1.  In the Intune admin center, go to **Endpoint security \> Attack surface reduction** and click **+ Create policy**.

![](media/image65.png)

2.  Set **Platform** to **Windows** and **Profile** to **Attack Surface Reduction Rules**, then click **Create**.

![](media/image66.png)

3.  On **Basics**, enter **L6-ASR-Rules** as the name and click **Next**.

![](media/image67.png)

4.  On **Configuration settings**, configure the following rules and click **Next**:

    - **Block abuse of exploited vulnerable signed drivers**: **Block**

    - **Block credential stealing from the Windows local security authority subsystem**: **Block**

    - **Block executable content from email client and webmail**: **Audit**

    - **Block all Office applications from creating child processes**: **Audit**

    - **Block Office applications from creating executable content**: **Audit**

    - **Use advanced protection against ransomware**: **Audit**

![](media/image68.png)
>
![](media/image69.png)
>
![](media/image70.png)

5.  Skip **Scope tags**. On **Assignments**, click **Add all devices**, then **Next** and **Create**.

![](media/image71.png)
>
![](media/image72.png)
>
![](media/image73.png)
>
> **Note** - Review audit events for a few weeks in **Reports \> Attack surface reduction rules** in the Defender portal before switching rules to **Block**.

## Exercise 6: Enforce Compliance and Conditional Access with Intune

**Time - 20 minutes**

Connecting Defender to Intune lets the device risk score decide whether a device is compliant, and Conditional Access can then block access from compromised devices.

### Task 1: Connect Defender for Business to Intune compliance

1.  In the Intune admin center, go to **Endpoint security \> Microsoft Defender for Endpoint**. Confirm **Connection status** is **Enabled**.

![](media/image74.png)

2.  Under **Compliance policy evaluation**, set **Connect Windows devices version 10.0.15063 and above to Microsoft Defender for Endpoint** (shown as **Connect Windows devices to Microsoft Defender for Endpoint** in some tenants) to **On** and click **Save**.

![](media/image75.png)
>
![](media/image76.png)

### Task 2: Create a Windows compliance policy that uses device risk

1.  Go to **Devices \> Compliance**, select the **Policies** tab, and click **+ Create policy**. Set **Platform** to **Windows 10 and later** and click **Create**.

![](media/image77.png)
>
![](media/image78.png)

2.  On **Basics**, enter **L6-Windows-Compliance-Risk** and click **Next**.

![](media/image79.png)

3.  On **Compliance settings**, configure the following and click **Next**:

    - **Microsoft Defender for Endpoint \> Require the device to be at or under the machine risk score**: **Low**

![](media/image80.png)

- **System Security \> Defender \> Microsoft Defender Antimalware**: **Require**

- **System Security \> Defender \> Real-time protection**: **Require**

![](media/image81.png)

4.  On **Actions for noncompliance**, keep **Mark device noncompliant** **Immediately**. Click **Next**.

![](media/image82.png)

5.  On **Assignments**, click **Add all devices**, then **Next** and **Create**.

![](media/image83.png)
>
![](media/image84.png)

### Task 3: Create a device-based Conditional Access policy in Report-only mode

1.  In the Intune admin center, go to **Endpoint security \> Conditional Access** and click **+ Create new policy**.

![](media/image85.png)
>
![](media/image86.png)

2.  Enter **L6-Require-Compliant-Device** as the **Name**. Under **Users**, include the group **SG-L6-Pilot**. On the **Exclude** tab, select your lab administrator account.

![](media/image87.png)

3.  Under **Target resources**, select **Select resources** \> Select specific resources and add **Office 365**.

![](media/image88.png)

4.  Under **Grant**, select **Grant access**, check **Require device to be marked as compliant**, and click **Select**.

![](media/image89.png)
>
![](media/image90.png)

5.  Set **Enable policy** to **Report-only** and click **Create**.

![](media/image91.png)
>
> **Report-only lets you see the impact in the sign-in logs before enforcing. Switch to On only after confirming no one will be locked out.**
>
![](media/image92.png)

## Exercise 7: Test Detection, Attack Disruption, and Alert Notifications

**Time - 20 minutes**

Automatic attack disruption is built into Defender for Business: when a human-operated attack such as ransomware is detected, Defender can contain the device and user account automatically. It needs no configuration, but your devices must be onboarded and administrators need to know where to review and undo the actions.

### Task 1: Run the detection test

1.  On the test VM, create the folder **C:\test-MDATP-test**, then open **Command Prompt** as administrator and run the following command:

```powershell
powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden $ErrorActionPreference = 'silentlycontinue';(New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe');Start-Process 'C:\\test-MDATP-test\\invoice.exe'
```

![](media/image93.png)
>
![](media/image94.png)
>
> **Note** - The command window closes automatically. This is Microsoft's harmless detection test.

2.  In the Defender portal, go to **Incidents & alerts \> Incidents**. Within about 10 minutes an incident appears for the device. Open it and review the **Attack story**, **Alerts** and **Assets** tabs.

![](media/image95.png)
>
![](media/image96.png)

### Task 2: Review attack disruption and response actions

1.  In the incident list, use **Filter** and look for the **Attack Disruption** tag. Incidents with this tag show which devices and users were contained automatically.

> **Note** - The detection test does not trigger attack disruption; real human-operated attacks do. You are learning where to find and reverse these actions.

2.  Go to **Actions & submissions \> Action center** and select the **History** tab. Review any automated actions. Note that a **Contain device** or **Contain user** action can be reversed with **Undo**.

3.  Go to **Assets \> Devices**, open the VM, and click the **...** menu to see the manual response actions: **Isolate device**, **Run antivirus scan**, **Collect investigation package** and **Restrict app execution**. Click **Run antivirus scan**, select **Quick scan** and click **Confirm**.

![](media/image97.png)
>
![](media/image98.png)
>
![](media/image99.png)

### Task 3: Review and edit email notification rules

1.  Go to **Settings \> Endpoints**. Under **General**, select **Email notifications**.

![](media/image100.png)

2.  On the **Vulnerabilities** tab, review the vulnerability notification rule and confirm your email address is a recipient.

![](media/image101.png)
>
![](media/image102.png)

## Summary

In this lab you enrolled and onboarded a Windows 11 device, ran the Defender for Business setup wizard, reviewed the default next-generation protection and firewall policies, set network protection to block mode, and turned on tamper protection and other advanced features. You deployed BitLocker and ASR rules from Intune, connected Defender device risk to an Intune compliance policy, created a device-based Conditional Access policy in report-only mode, and tested detection, response actions and alert notifications.

## Clean up resources

1.  Leave the device onboarded and the policies in place; they do not affect other labs.

2.  If you switched **L6-Require-Compliant-Device** to **On**, set it back to **Report-only** or delete it.
