# Lab 5: Deploy Compliance, Apps, and Conditional Access via Microsoft Intune

## Exercise 1: Deploy App protection policies (MAM)

Mobile Application Management (MAM) app protection policies let you protect corporate data within managed apps without requiring full device enrollment. In this exercise you will create protection policies for both iOS/iPadOS and Android, assign them to a pilot user group, and verify that the policies are applied.

### Task 1: Create an iOS/iPadOS App Protection Policy

Create separate app protection policies for iOS/iPadOS and Android that enforce data-loss-prevention controls on Microsoft 365 mobile apps.

1.  Sign in to the Microsoft Intune admin center at https://intune.microsoft.com using an account with the Intune Administrator or Global Administrator role.

![](media/image1.png)

2.  In the left navigation pane, click Apps → Under **Manage Apps** select **Protection**.

![](media/image2.png)

3.  Click + Create policy and select iOS/iPadOS from the drop-down.

![](media/image3.png)

> **4.** On the Basics tab, fill in the following fields:
>
> **a.** Name: MAM-iOS-M365-Policy
>
> **b.** Description: App protection policy for iOS Microsoft 365 apps
>
> **c.** Platform: iOS/iPadOS (pre-selected)
>
> **d.** Click Next.
>
![](media/image4.png)
>
> **5.** On the Apps tab:
>
> **a.** Set Target policy to: Selected apps.
>
> **b.** Click + Select public apps. In the search box type Microsoft Outlook and select it. Repeat for Microsoft Teams, Microsoft Word, Microsoft Excel, Microsoft PowerPoint, and Microsoft Edge.
>
![](media/image5.png)
>
> **c.** Click Next.
>
![](media/image6.png)
>
> **6.** On the Data protection tab, configure the following settings:
>
> **1.** Backup org data to iTunes and iCloud backups: Block
>
> **2.** Send org data to other apps: Policy managed apps

![](media/image7.png)

> **3.** Receive data from other apps: Policy managed apps
>
> **4.** Restrict cut, copy and paste between other apps: Policy managed apps with paste in
>
> **5.** Third party keyboards: Block
>
![](media/image8.png)
>
> **6.** Encrypt org data: Require
>
> **7.** Sync policy managed app data with native apps or add-ins: Block
>
> **8.** Printing org data: Block
>
> **9.** Leave all other settings at their defaults.
>
> **10.** Click Next.
>
![](media/image9.png)
>
> **7.** On the Access requirements tab:
>
> **1.** PIN for access: Require
>
> **2.** PIN type: Numeric
>
> **3.** Minimum PIN length: 6
>
> **4.** Simple PIN: Block
>
> **5.** Touch ID instead of PIN: Allow
>
> **6.** Work or school account credentials for access: Not required
>
> **7.** Recheck the access requirements after (minutes of inactivity): 30
>
> **8.** Click Next.
>
![](media/image10.png)
>
> **8.** On the Conditional launch tab review the default conditions (e.g. Max PIN attempts: 5 → Reset PIN; Offline grace period: 720 minutes → Block access). Leave defaults. Click Next.
>
![](media/image11.png)
>
> **9.** On the Assignments tab:
>
> **a.** Under Included groups click + Select groups to include.
>
> **b.** Search for and select **Operations** group. If the group does not exist, create it first in Microsoft Entra ID → Groups → New group (Security, assigned membership) before returning here.
>
![](media/image12.png)
>
> **c.** Click Select, then Next.
>
![](media/image13.png)
>
> **10.** On the Review + create tab, confirm all settings are correct and click Create.
>
![](media/image14.png)

### Task 2 – Create an Android App Protection Policy

1.  Click + Create policy and select Android from the drop-down.

![](media/image15.png)

> **2.** On the Basics tab:
>
> **a.** Name: MAM-Android-M365-Policy
>
> **b.** Description: App protection policy for Android Microsoft 365 apps
>
> **c.** Click Next.
>
![](media/image16.png)

2.  On the Apps tab, set Target policy to Selected apps and add the same six apps as in Task 1 (Outlook, Teams, Word, Excel, PowerPoint, Edge). Click Next.

![](media/image17.png)

![](media/image18.png)

> **4.** On the Data protection tab, configure the following settings:
>
> **6.** On the Data protection tab, configure the following settings:
>
> **a.** Backup org data to Android services: Block
>
> **b.** Send org data to other apps: Policy managed apps
>
> **c.** Receive data from other apps: Policy managed apps
>
> **d.** Restrict cut, copy and paste between other apps: Policy managed apps with paste in
>
> Screen capture and Google Assistant: Block
>
> **e.** Approved keyboards: Not required.
>
> **f.** Encrypt org data: Require
>
> **g.** Sync policy managed app data with native apps or add-ins: Block
>
> **h.** Printing org data: Block
>
> **i.** Leave all other settings at their defaults.
>
> **j.** Click Next.
>
![](media/image19.png)
>
![](media/image20.png)
>
![](media/image21.png)

4.  On the Access requirements tab apply the following settings and click **Next.**

> **a.** PIN for access: Require
>
> **b.** PIN type: Numeric
>
> **c.** Minimum PIN length: 6
>
> **d.** Simple PIN: Block
>
> **e.** Fingerprint instead of PIN: Allow
>
> **f.** Disable app PIN when device PIN is managed: No
>
> **g.** Work or school account credentials for access: Not required
>
> **h.** Recheck the access requirements after (minutes of inactivity): 30

![](media/image22.png)

![](media/image23.png)

5.  On the Conditional launch tab leave defaults. Click Next.

![](media/image24.png)

6.  On the Assignments tab include the Operations group. Click Next → Create.

![](media/image25.png)

![](media/image26.png)

![](media/image27.png)

7.  Click **Create.**

![](media/image28.png)

> **Expected result:** MAM-Android-M365-Policy appears in the list with Platform: Android.
>
![](media/image29.png)

## Exercise 2: Create device compliance policies

Device compliance policies define the rules and settings that devices must meet to be considered compliant. Non-compliant devices can be blocked from accessing corporate resources when combined with Conditional Access (Exercise 5). In this exercise you will create compliance policies for Windows 10/11 devices.

### **Task 1: Create a Windows 10/11 Compliance Policy**

1.  In the Intune admin center, go to Devices → Compliance.

![](media/image30.png)

2.  On the compliance page, go to notifications tab and create a notification.

![](media/image31.png)

3.  Click **+ Create notification**

![](media/image32.png)

4.  Enter Name: Compliance -Alert-Email and click **Next.**

![](media/image33.png)

5.  On Header ad footer page, leave settings on default and click **Next.**

![](media/image34.png)

6.  On the Notifications messages templates page, select language as English, add the following subject, Message and click **Save**

**Subject:** Your device is not compliant – action required

**Message:** Your device does not meet corporate security requirements. Please contact the IT helpdesk or review your device settings in the Company Portal app. If not resolved within 7 days, access to corporate resources may be blocked.

![](media/image35.png)

7.  Click **Next.**

![](media/image36.png)

8.  Click **Create.**

![](media/image37.png)

![](media/image38.png)

9.  Back on compliance home, under policies tab, click **+ Create policy.**

![](media/image39.png)

> **3.** On the platform selector select the following and click **Create.**
>
> **a.** Platform: **Windows 10 and later**
>
> **b.** Profile type: **Windows 10/11 compliance policy**

![](media/image40.png)

> **4.** On the Basics tab:
>
> **a.** Name: Compliance-Windows
>
> **b.** Description: Windows 10/11 compliance policy
>
> **c.** Click Next.
>
![](media/image41.png)
>
> **5.** On the Compliance settings tab, expand each category and configure:

**Device Health:**

> **a.** Require BitLocker: Require
>
> **b.** Require Secure Boot to be enabled on the device: Require
>
> **c.** Require code integrity: Require
>
![](media/image42.png)

**Device Properties:**

> **d.** Minimum OS version: 10.0.19041.0 (Windows 10 2004 / May 2020 Update)
>
> **e.** Maximum OS version: leave blank (no upper limit)

![](media/image43.png)

**System Security:**

> **f.** Require a password to unlock mobile devices: Require
>
> **g.** Simple passwords: Block
>
> **h.** Minimum password length: 8
>
> **i.** Password type: Alphanumeric
>
> **j.** Password expiration (days): 90
>
> **k.** Number of previous passwords to prevent reuse: 5
>
> **l.** Require encryption of data storage on device: Require
>
> **m.** Firewall: Require
>
> **n.** Antivirus: Require
>
> **o.** Antispyware: Require
>
> **p.** Click **Next**
>
![](media/image44.png)
>
![](media/image45.png)
>
> **6.** On the Actions for noncompliance tab:
>
> **a.** The default action Mark device noncompliant is already present with Schedule: Immediately. Keep this.
>
> **b.** Click + Add action → Send email to end user.
>
> **c.** Set Schedule (days after noncompliance): 0 (send immediately).
>
> **d.** Under Message template, click **None selected** and select **Compliance-Alert-Email.**
>
![](media/image46.png)

5.  Click on the dropdown, select Add device to retire list, Set Schedule: 30 days and click **Next.**

![](media/image47.png)
>
> **7.** On the Assignments tab:
>
> **a.** Under **Included groups**, select **+ Select groups to include**. From here, you can choose specific groups to assign the compliance policy to, or select **All Users** and **All Devices** to apply the policy more broadly.
>
![](media/image48.png)
>
> **b.** Select Operations group. Click Select.
>
![](media/image49.png)
>
> **c.** Click Next → Create.
>
![](media/image50.png)
>
![](media/image51.png)

## Exercise 3: Deploy Microsoft 365 apps

In this exercise you will use Intune to deploy the Microsoft 365 Apps for enterprise suite (Word, Excel, PowerPoint, Outlook, Teams, and OneNote) to Windows 10/11 devices. You will configure the Office Deployment Tool settings directly within the Intune portal and assign the app to the pilot group.

> **Task 33: Add and Deploy Microsoft 365 Apps for Enterprise via Intune**

Configure a Microsoft 365 Apps deployment package in Intune and assign it to enrolled Windows devices.

### **Task 1 – Add the Microsoft 365 Apps Suite**

1.  In the Intune admin center, go to Apps → Windows.

![](media/image52.png)

2.  Click + Create.

![](media/image53.png)

> **3.** In the Select app type pane:
>
> **a.** Under App type, scroll to Microsoft 365 Apps.
>
> **b.** Select Windows 10 and later.
>
![](media/image54.png)
>
> **c.** Click Select.
>
![](media/image55.png)
>
> **4.** On the App suite information tab:
>
> **a.** Suite name: Microsoft 365 Apps
>
> **b.** Suite description: Microsoft 365 Apps for enterprise deployed via Intune
>
> **c.** Publisher: Microsoft Corporation (auto-populated)
>
> **d.** Category: Productivity
>
> **e.** Show this as a featured app in the Company Portal: Yes
>
> **f.** Information URL and Privacy URL: leave blank for lab
>
> **g.** Developer / Owner / Notes: leave blank
>
> **h.** Logo: leave the default Microsoft 365 logo
>
> **i.** Click Next.
>
![](media/image56.png)
>
![](media/image57.png)
>
> **5.** On the Configure app suite tab:
>
> **a.** Select Office apps to install: check Word, Excel, PowerPoint, Outlook, OneNote, Teams.
>
> **b.** Select the default file format to use: Office Open XML Format (.docx, .xlsx, .pptx)
>
> **c.** Update channel: Current Channel
>
> **d.** Remove other versions: Yes (removes Click-to-Run MSI installations of Office)
>
> **e.** Version to install: Latest
>
> **f.** Use shared computer activation: No (set to Yes only if deploying to shared/RDS environments)
>
> **g.** Accept the Microsoft Software License Terms on behalf of users: Yes
>
> **h.** Languages: click + Add and select English (United States). Add additional languages if required.
>
> **i.** Click Next.
>
![](media/image58.png)
>
> **6.** On the Assignments tab:
>
> **a.** Under Required, select **+Add all users**
>
> **c.** Click Next.
>
![](media/image59.png)

8.  On the Review + create tab, review all settings and click Create.

![](media/image60.png)

![](media/image61.png)

> **Expected result:** The Microsoft 365 Apps suite appears in the Windows apps list with App type: Microsoft 365 Apps (Windows 10 and later) and Assignment status showing the pilot group.

### **Task 2: Monitor the Deployment**

> **1.** Click the Microsoft 365 Apps.
>
> **2.** Click Monitor → Device install status in the left blade.
>
![](media/image62.png)
>
> **Note**: In this demonstration, no devices or users have been added to the group. As a result, the installation status will remain blank.

## Exercise 4: Configure Enterprise State Roaming

Enterprise State Roaming (ESR) allows Windows 10/11 users to securely synchronize their user settings and application data across devices using Azure cloud storage. Settings roam with the user's Entra ID (Azure AD) account, separate from consumer Microsoft accounts. In this exercise you will enable ESR in the Microsoft Entra ID portal and verify synchronization on a test device.

### **Task 1: Enable Enterprise State Roaming in Microsoft Entra ID**

1.  Open the Microsoft Entra admin center at https://entra.microsoft.com and sign in as Global Administrator or Identity Administrator.

![](media/image63.png)

2.  In the left navigation, expand Entra Id → Devices and click Enterprise State Roaming.

![](media/image64.png)

> **3.** Under Users may sync settings and app data across devices:
>
> **a.** Ensure the toggle is to **All**.

For a Windows 11 or Windows 10, version 21H2 or newer device, the device must authenticate using a Microsoft Entra identity. For devices that are joined to Microsoft Entra ID, the user’s primary sign-in identity is their Microsoft Entra identity, so no other configuration is required. For devices that use on-premises Active Directory, the IT admin must Configure Microsoft Entra hybrid joined devices.

## Summary

In this lab, participants configured a comprehensive endpoint management and security strategy using Microsoft Intune and Microsoft Entra ID across four exercises. In Exercise 1, Mobile Application Management (MAM) app protection policies were created for both iOS/iPadOS and Android platforms, targeting six Microsoft 365 apps — Outlook, Teams, Word, Excel, PowerPoint, and Edge — enforcing data-loss-prevention controls such as restricting data transfers to policy-managed apps, blocking backups, requiring encryption, and mandating a 6-digit PIN, all assigned to the Operations group without requiring full device enrollment. In Exercise 2, a Windows 10/11 device compliance policy was built to enforce security baselines including BitLocker, Secure Boot, Code Integrity, strong alphanumeric passwords, and active Firewall and Antivirus, with automated non-compliance email alerts and a 30-day device retirement action for persistent violations. Exercise 3 covered the deployment of the full Microsoft 365 Apps for Enterprise suite to Windows devices through Intune, configured to install via the Current Update Channel, remove legacy MSI Office versions, and appear as a featured app in the Company Portal for all users. Finally, Exercise 4 enabled Enterprise State Roaming in the Microsoft Entra admin center, allowing users' Windows settings and app data to securely sync across Entra ID–joined devices using Azure cloud storage. Together, these exercises established the foundation of a Zero Trust endpoint management strategy where app-level data protection, device compliance, application delivery, and user experience continuity work in concert to secure corporate resources.
