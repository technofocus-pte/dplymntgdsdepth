#  Lab 7: Configure Exchange Online Mail Protection, Alerts, and Audit Settings

## Objectives

Email is the most common way attackers get in, and auto-forwarding rules are a favourite way to steal data after an account is compromised. In this lab you will make sure auditing is on, apply Microsoft's Standard preset security policy to every mailbox, block automatic external forwarding, configure alert policies, test the controls with a forwarding rule, and use Configuration analyzer to find settings that fall short of Microsoft's recommendations.

- Verify and turn on Microsoft Purview auditing

- Apply the Standard preset security policy to all recipients

- Block automatic external email forwarding with the outbound spam policy

- Configure default and custom alert policies

- Test forwarding protection and alerting

- Use Configuration analyzer to compare policies with Standard and Strict recommendations

**Prerequisites**

A lab administrator account with the **Global Administrator** role and an Exchange Online mailbox (Business Premium or E5 licence).

Access to Outlook on the web (https://outlook.office.com) for the lab administrator.

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

## Exercise 1: Turn On Auditing

**Time - 10 minutes**

Auditing is not turned on by default for Business Premium tenants. Many features in this lab, including alert policies and Configuration analyzer drift history, depend on it.

### Task 1: Verify and turn on auditing in Microsoft Purview

1.  Sign in to the Microsoft Purview portal at <https://purview.microsoft.com>

![](media/image1.png)

2.  Select **View all solutions** and then **Audit**.

![](media/image2.png)
>
![](media/image3.png)

3.  If a banner prompts you to **Start recording user and admin activity**, click it. If no banner appears, auditing is already on.

![](media/image4.png)
>
![](media/image5.png)
>
> **Note** - Auditing can take up to 60 minutes to start, and events can take several hours to become searchable.

### Task 2: Confirm auditing with PowerShell

1.  On the lab VM, open **Windows PowerShell** as administrator and connect to Exchange Online by running the following command.  
    enter tenant credentials when prompted.

```powershell
Install-Module ExchangeOnlineManagement -Scope CurrentUser -Force
Connect-ExchangeOnline
```

![](media/image6.png)

2.  Check the auditing status. **True** means auditing is on:

```powershell
Get-AdminAuditLogConfig | Format-List UnifiedAuditLogIngestionEnabled
```

> **Note** - If the value is **False**, we can still proceed to execute exercise 2.  
> Alternatively you can also run **Set-AdminAuditLogConfig -UnifiedAuditLogIngestionEnabled \$true**. Run this check in Exchange Online PowerShell, not Security & Compliance PowerShell, which always shows False.

## Exercise 2: Turn On the Standard Preset Security Policy

**Time - 10 minutes**

Preset security policies apply Microsoft's recommended anti-spam, anti-malware, anti-phishing, Safe Links and Safe Attachments settings in one step, and Microsoft updates \`them automatically as threats change.

### Task 1: Apply Standard protection to all recipients

1.  Open the Microsoft Defender portal at https://security.microsoft.com and go to **Email & collaboration \> Policies & rules \> Threat policies**. Under **Templated policies**, click **Preset Security Policies**.

![](media/image7.png)
>
![](media/image8.png)

2.  Click **Manage protection settings**.

![](media/image9.png)

3.  On **Apply Exchange Online Protection**, select **All recipients** and click **Next**.

![](media/image10.png)

4.  On **Apply Defender for Office 365 protection**, select **All recipients** and click **Next**.

![](media/image11.png)

5.  On **Impersonation protection**, click **Next**. On **Add email addresses to flag when impersonated by attackers**, add your lab administrator (as a sample executive) and click **Next**.

![](media/image12.png)

6.  Click **Next** on the domain and trusted-sender pages, review the settings.

7.  On the **Policy mode** page, select Turn on the policy when finished and click **Next**.

![](media/image13.png)

8.  On the **Review and confirm your changes** pages, click **Confirm** and then select Done

![](media/image14.png)

![](media/image15.png)

9.  When you finish the protection settings wizard, **Standard protection** is turned on automatically.

![](media/image16.png)

> **Note** - Strict protection, if applied to anyone, always takes priority over Standard. Preset policies also take priority over any custom threat policies.

## Exercise 3: Block Automatic External Forwarding

**Time - 10 minutes**

The default setting, **Automatic - System-controlled**, varies by tenant. Microsoft recommends choosing an explicit setting.

### Task 1: Configure the default outbound spam policy

1.  In the Defender portal, go to **Email & collaboration \> Policies & rules \> Threat policies \> Anti-spam** (or browse to https://security.microsoft.com/antispam).

![](media/image17.png)

2.  Click the **Anti-spam outbound policy (Default)** row (not the check box) to open the details flyout.

![](media/image18.png)

3.  In **Protection settings**, click **Edit protection settings**.

![](media/image19.png)

4.  Under **Forwarding rules**, set **Automatic forwarding rules** to **Off - Forwarding is disabled**.

![](media/image20.png)

5.  Under **Message limits**, review the external, internal and daily limits and set **Restriction placed on users who reach the message limit** to **Restrict the user from sending mail until the following day**. Click **Save**.

![](media/image21.png)
>
> **Note** - Internal forwarding is not affected. To allow specific users to forward externally, create a custom outbound policy with forwarding **On** for just those users.

### Task 2: Verify the setting with PowerShell

1.  In the Exchange Online PowerShell session, run:

```powershell
Get-HostedOutboundSpamFilterPolicy -Identity Default | Format-List Name, AutoForwardingMode, RecipientLimitExternalPerHour, ActionWhenThresholdReached
```

![](media/image22.png)

**Note** - **AutoForwardingMode** should be **Off**.

## Exercise 4: Configure Alert Policies

Alert policies watch the audit log for risky user and admin activity. Default policies are on automatically; you can change their recipients and add your own.

### Task 1: Review the default forwarding alerts

1.  In the Defender portal, go to **Email & collaboration \> Policies & rules \> Alert policy** (or browse to https://security.microsoft.com/alertpolicies).

![](media/image23.png)

2.  Search for **forward**. Open **Creation of forwarding/redirect rule** and **Suspicious email forwarding activity** and confirm both are **On**.

![](media/image24.png)

3.  Open **Creation of forwarding/redirect rule**, click **Edit policy**, go to the notification page, add your lab administrator email address as a recipient, and click **Submit**.

![](media/image25.png)
>
![](media/image26.png)
>
![](media/image27.png)

### Task 2: Create a custom alert policy

This policy alerts when malware is found in a file in SharePoint, OneDrive or Teams.

1.  In the Defender portal, go to **Email & collaboration \> Policies & rules \> Alert policy**

2.  On the **Alert policy** page, click **+ New alert policy**.

![](media/image28.png)

3.  Enter **L7-Malicious-Files-Detected** as the **Name**, set **Severity** to **High** and **Category** to **Threat management**. Click **Next**.

![](media/image29.png)

4.  Under **Activity is**, search for and select **Detected malware in file**. Set **How do you want the alert to be triggered?** to **Every time an activity matches the rule**. Click **Next**.

![](media/image30.png)

5.  Keep **Opt-in for email notifications** selected, add your email address, set **Daily notification limit** to **No limit**, and click **Next**.

![](media/image31.png)

6.  Select **Yes, turn it on right away** and click **Submit**, then **Done**.

![](media/image32.png)
>
![](media/image33.png)

**Note** - New and changed alert policies can take up to 24 hours to become active.

![](media/image34.png)

## Exercise 5: Test Forwarding Protection

**Time - 10 minutes**

You will create an inbox rule that forwards mail to an external address and confirm that Online blocks it.

### Task 1: Create a forwarding inbox rule

1.  Open Outlook on the web at https://outlook.office.com as the lab administrator. Select **Settings** (gear) **\> Mail \> Rules** and click **+ Add new rule**.

![](media/image35.png)
>
![](media/image36.png)

2.  Name the rule **L7 Test Forward**, add the condition **Subject includes** **forward-test**, add the action **Forward to** and enter an external address such as **test@outlook.com**. Click **Save**.

![](media/image37.png)

### Task 2: Trigger and verify the block

1.  Send a new email to yourself with the subject **forward-test**.

![](media/image38.png)

2.  Within a few minutes you receive a non-delivery report. Open it and confirm it shows **550 5.7.520 Access denied, Your organization does not allow external forwarding**.

3.  In the Defender portal, go to **Incidents & alerts \> Alerts** and look for **Creation of forwarding/redirect rule**.

> **Note** - Alerts depend on auditing, which you turned on in Exercise 1. If the alert does not appear within this session, check again later.

4.  In Outlook on the web, delete the **L7 Test Forward** rule.

![](media/image39.png)

**\[Screenshot pending: E5T2S4\]**

## Exercise 6: Review Recommendations with Configuration Analyzer

**Time - 10 minutes**

Configuration analyzer compares your threat policies with the Standard and Strict preset values and shows settings that fall short.

### Task 1: Review and apply recommendations

1.  In the Defender portal, go to **Email & collaboration \> Policies & rules \> Threat policies**. Under **Templated policies**, click **Configuration analyzer**.

![](media/image40.png)

2.  On the **Standard recommendations** tab, review the **Summary** counts by policy type and the table of settings that need improvement.

![](media/image41.png)
>
> **Note** - Because Standard protection is on for all recipients, few or no items may appear here. Settings in default and custom policies can still be listed.

3.  Select the **Strict recommendations** tab. Select one recommendation (for example, an anti-spam setting in the default policy) and click **Apply recommendation**. Confirm when prompted, then click **Refresh**.

4.  Select the **Configuration drift analysis and history** tab and find the change you just made, including the old and new values and whether security **Increased** or **Decreased**.

## Summary

In this lab you turned on and verified auditing, applied the Standard preset security policy to all recipients, blocked automatic external forwarding, reviewed the default forwarding alerts and created a custom alert policy, proved that forwarding is blocked with an NDR, and used Configuration analyzer to review recommendations and configuration drift.

## Clean up resources

1.  Confirm the **L7 Test Forward** inbox rule is deleted.

2.  Leave the preset policy, outbound policy and alert policies in place; they do not affect other labs.
