# Lab 8: Deploy Collaboration Security Best Practices for Teams, SharePoint, and OneDrive

## Objectives

Files and chats in Teams, SharePoint and OneDrive are as attractive to attackers as email. In this lab you will restrict third-party cloud storage in Teams, turn on zero-hour auto purge (ZAP) and user reporting for Teams, control guest and external access, and turn on Safe Attachments for SharePoint, OneDrive and Teams so that malicious files are detected and blocked.

- Block third-party cloud storage providers in Teams

- Turn on ZAP, user reporting and Safe Links protection for Teams

- Configure guest access and external access for Teams

- Turn on Safe Attachments for SharePoint, OneDrive and Teams and block downloads of infected files

- Create an alert for malicious files and verify all settings with PowerShell

**Prerequisites**

A lab administrator account with the **Global Administrator** role (or **Teams Administrator**, **SharePoint Administrator** and **Security Administrator**).

Microsoft Defender for Office 365 Plan 1 or Plan 2 (Plan 1 is included in Business Premium; ZAP for Teams requires Plan 2, included in the Defender Suite).

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

## Exercise 1: Prepare the Lab Environment

**Time - 5 minutes**

This lab uses PowerShell to verify settings. In this exercise you install the modules you need.

### Task 1: Install the PowerShell modules

1.  On the lab VM, open **Windows PowerShell** as administrator and install the modules:

```powershell
Install-Module ExchangeOnlineManagement -Scope CurrentUser -Force
Install-Module MicrosoftTeams -Scope CurrentUser -Force
Install-Module Microsoft.Online.SharePoint.PowerShell -Scope CurrentUser -Force
```

![](media/image1.png)

**Note** - Answer **Y** if asked to trust the PSGallery repository or install NuGet.

## Exercise 2: Block Third-Party Cloud Storage in Teams

**Time - 10 minutes**

By default, users can add Dropbox, Box, Google Drive, Citrix Files and Egnyte as file storage in Teams. Turning these off keeps files in SharePoint and OneDrive, where Microsoft 365 data protection applies.

### Task 1: Turn off third-party storage providers

1.  Sign in to the Teams admin center at https://admin.teams.microsoft.com and go to **Teams \> Teams settings**.

![](media/image2.png)
>
> **Note** - The Teams admin center is moving to a unified **Settings & policies** page. If you don't see **Teams settings**, search for **Files** in the admin center search box.

2.  In the **Files** section, turn **Off** **Citrix files**, **DropBox**, **Box**, **Google Drive** and **Egnyte**. Click **Save** and **Confirm**.

![](media/image3.png)
>
![](media/image4.png)

### Task 2: Verify with PowerShell

1.  In PowerShell, connect to Teams and check the settings. All values should be **False**:

```powershell
Connect-MicrosoftTeams
Get-CsTeamsClientConfiguration -Identity Global |
Select-Object AllowDropBox, AllowBox, AllowGoogleDrive, AllowShareFile, AllowEgnyte
```

![](media/image5.png)
>
> **Note** - If any value is still True, run **Set-CsTeamsClientConfiguration -Identity Global -AllowDropBox \$false -AllowBox \$false -AllowGoogleDrive \$false -AllowShareFile \$false -AllowEgnyte \$false**. Changes can take a few hours to reach Teams clients.

## Exercise 3: Protect Teams with ZAP, User Reporting, and Safe Links

**Time - 15 minutes**

Zero-hour auto purge (ZAP) for Teams quarantines chat messages that are found to be malware or high-confidence phishing after they were delivered. User reporting lets people report suspicious messages for analysis.

### Task 1: Turn on ZAP for Teams

1.  Open the Microsoft Defender portal at https://security.microsoft.com and go to **Settings \> Email & collaboration \> Microsoft Teams protection**.

![](media/image6.png)
>
![](media/image7.png)

2.  In the **Zero-hour auto purge (ZAP)** section, confirm the toggle is **On**. If it is **Off**, turn it on and click **Save**.

![](media/image8.png)
>
> **Note** - Quarantined Teams messages go to **admin-only quarantine** by default. Admins review them in **Email & collaboration \> Review \> Quarantine** on the **Teams messages** tab.

### Task 2: Turn on user reporting for Teams

1.  In the Teams admin center, open **Messaging settings** (https://admin.teams.microsoft.com/messaging/settings). In the **Messaging safety** section, confirm **Report incorrect security detections** is **On**.

![](media/image9.png)

2.  Open **Messaging policies** (or **Settings & policies \> Messaging**) and edit the **Global (Org-wide default)** policy. Confirm **Report a security concern** is **On** and click **Save**.

3.  In the Defender portal, go to **Settings \> Email & collaboration \> Teams User reported settings**. In the **Microsoft Teams** section, select **Monitor reported items in Microsoft Teams**.

![](media/image10.png)

### Task 3: Confirm Safe Links protects Teams

1.  In the Defender portal, go to **Email & collaboration \> Policies & rules \> Threat policies \> Safe Links**.

![](media/image11.png)

2.  Open each policy that has a **Priority** and confirm the **Teams** section of **Protection settings** shows **On**. If none exist, the **Built-in protection** policy already protects Teams links.

![](media/image12.png)

## Exercise 4: Configure Guest Access and External Access

**Time - 15 minutes**

**Guest access** adds people from outside your organization to your teams. **External access** lets your users chat and meet with people in other organizations without adding them as guests.

### Task 1: Review guest access

1.  In the Teams admin center, go to **External collaboration** \> **Guest access**. Confirm **Guest access** in Teams is **On**.

![](media/image13.png)

2.  Review the guest **Calling**, **Meeting** and **Messaging** settings: turn **Off** **Make private calls** and **Delete sent messages** for guests, then click **Save**.

![](media/image14.png)

![](media/image15.png)

> **Note** - Guest access in Teams also depends on Microsoft Entra **External collaboration settings** and SharePoint sharing settings. All three must allow guests.

### Task 2: Restrict external access to trusted domains

1.  In **External collaboration**, open **External access** and edit the organization-wide settings.

![](media/image16.png)

2.  Select **Allow only specific external domains**, click **Allow domains**, add **fabrikam.com** (a sample partner) and click **Done**.

![](media/image17.png)

3.  Turn **Off** **People in my organization can communicate with unmanaged Teams accounts** (shown as personal Teams accounts in some tenants). Click **Save**.

![](media/image18.png)
>
![](media/image19.png)
>
> **Blocking unmanaged Teams accounts reduces phishing through Teams chat from personal accounts, a technique used by several ransomware groups.**

### Task 3: Verify external access with PowerShell

1.  Run the following and confirm **AllowFederatedUsers** is **True**, the allowed domain list contains **fabrikam.com**, and **AllowTeamsConsumer** is **False**:

```powershell
Get-CsTenantFederationConfiguration |
Select-Object AllowFederatedUsers, AllowedDomains, AllowTeamsConsumer, AllowTeamsConsumerInbound
```

![](media/image20.png)

## Exercise 5: Turn On Safe Attachments for SharePoint, OneDrive, and Teams

**Time - 15 minutes**

Safe Attachments for SharePoint, OneDrive and Teams detects malicious files shared in these services and locks them so that users can't open, copy or share them.

### Task 1: Turn on the global setting

1.  In the Defender portal, go to **Email & collaboration \> Policies & rules \> Threat policies \> Safe Attachments** (or <https://security.microsoft.com/safeattachmentv2>).

![](media/image21.png)

2.  Click **Global settings** (gear icon). In **Protect files in SharePoint, OneDrive, and Microsoft Teams**, turn **On** **Turn on Defender for Office 365 for SharePoint, OneDrive, and Microsoft Teams** and click **Save**.

![](media/image22.png)
>
> **Note** - Settings can take up to 30 minutes to take effect.

### Task 2: Block downloads of infected files

By default users can still download a detected file. This task blocks that.

1.  In PowerShell, connect to SharePoint Online. Replace **\<tenant\>** with your tenant name (the part before .onmicrosoft.com):

```powershell
Connect-SPOService -Url https://<tenant>-admin.sharepoint.com -UseSystemBrowser $true
```

![](media/image23.png)

2.  Block downloads of infected files and verify:

```powershell
Set-SPOTenant -DisallowInfectedFileDownload $true
Get-SPOTenant | Format-List DisallowInfectedFileDownload
```

![](media/image24.png)

> **Note** - This setting applies to users and admins. People can still delete infected files.

### Task 3: Create an alert for malicious files

1.  In the Defender portal, go to **Email & collaboration \> Policies & rules \> Alert policy** and click **+ New alert policy**.

![](media/image25.png)

2.  Enter **L8-Malicious-Files-in-Libraries**, set **Severity** to **High** and **Category** to **Threat management**, and click **Next**.

![](media/image26.png)

3.  Set **Activity is** to **Detected malware in file** and the trigger to **Every time an activity matches the rule**. Click **Next**.

![](media/image27.png)

4.  Add your email address as a recipient, click **Next**, select **Yes, turn it on right away**, and click **Submit**.

![](media/image28.png)
>
![](media/image29.png)
>
![](media/image30.png)

### Task 4: Verify the Safe Attachments settings

1.  Connect to Exchange Online and confirm the setting is **True**:

```powershell
Connect-ExchangeOnline -DisableWAMGet-AtpPolicyForO365 | Format-List EnableATPForSPOTeamsODB
```

![](media/image31.png)

2.  In the Defender portal, go to **Reports \> Email & collaboration \> Email & collaboration reports**. This page brings together the reports on email and collaboration security in your organization. They include **Threat protection status** (malware, phishing and spam detected and blocked), **Top senders and recipients**, **Mailflow status** (how messages were delivered, junked or blocked), and reports on URL protection, quarantine and user-reported messages. Security teams use these reports to spot trends, find the users who are targeted most, and check that policies are working.

![](media/image32.png)
>
![](media/image33.png)
>
> **Note:** This is a new test tenant with very little mail traffic, so most reports will show little or no data. That's expected. Use this step to learn where each report is and what it shows. In a production tenant, these reports fill up with real detections over days and weeks.

## Summary

In this lab you blocked third-party cloud storage in Teams, turned on ZAP, user reporting and Safe Links protection for Teams, restricted external access to trusted domains and unmanaged accounts, reviewed guest access, and turned on Safe Attachments for SharePoint, OneDrive and Teams with infected-file download blocking and alerting.
