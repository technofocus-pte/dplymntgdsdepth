# Lab 9: Deploy Advanced MDO Plan 2 Protection and Investigation Capabilities

## Objectives

Microsoft Defender for Office 365 Plan 2, included in the Microsoft Defender Suite for Business Premium, adds priority account protection, Threat Explorer, automated investigation and response (AIR), and Attack simulation training. In this lab you will protect your most targeted users with priority account tags and the Strict preset policy, run a phishing simulation, hunt for email threats with Threat Explorer and Advanced hunting, and launch an automated investigation.

- Tag priority accounts and apply the Strict preset security policy to them

- Run a credential-harvest phishing simulation and review the results

- Hunt for email threats with Threat Explorer and Advanced hunting

- Take remediation action and start an automated investigation

- Review threat discovery reports

**Prerequisites**

A lab administrator account with the **Global Administrator** role and an Exchange Online mailbox.

Microsoft Defender for Office 365 Plan 2 (included in the Defender Suite for Business Premium or Microsoft 365 E5).

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

## Exercise 1: Protect Priority Accounts

**Time - 15 minutes**

Executives and their assistants receive the most targeted attacks. Priority account tags add extra heuristics and let you filter alerts, reports and investigations for these users.

### Task 1: Tag a priority account

1.  Open the Microsoft Defender portal at https://security.microsoft.com and go to **Settings \> Email & collaboration \> User tags**.

![](media/image1.png)

2.  Select the **Priority account** row and click **Edit**.

![](media/image2.png)

3.  On **Assign members**, click **Add members**, add your lab administrator account (acting as the CEO in this lab), and click **Next**, then **Submit**

![](media/image3.png)
>
![](media/image4.png) ![](media/image5.png)![](media/image6.png)![](media/image7.png).
>
> **Note** - Tags can take up to eight hours to apply fully. Up to 250 priority accounts are supported.

### Task 2: Apply the Strict preset security policy to priority accounts

1.  Go to **Email & collaboration \> Policies & rules \> Threat policies \> Preset Security Policies**.

![](media/image8.png)

2.  To turn **Strict protection** **On**, click on **Manage protection settings**.

![](media/image9.png)

3.  On **Apply Exchange Online Protection**, select **Specific recipients**, choose **Users**, and add your lab administrator. Click **Next**.

![](media/image10.png)

4.  On **Apply Defender for Office 365 protection**, select **Previously selected recipients** and click **Next**.

![](media/image11.png)

5.  On **Impersonation protection**, add your lab administrator as a user to protect. Click **Next** through the remaining pages, then **Confirm** and **Done**.

![](media/image12.png)
>
![](media/image13.png)
>
![](media/image14.png)
>
![](media/image15.png)
>
![](media/image16.png)
>
> **Note** - Strict always wins over Standard. In Strict, spam and bulk mail go to quarantine rather than Junk, so priority users see less risky mail.
>
![](media/image17.png)

## Exercise 2: Run a Phishing Simulation

Attack simulation training sends realistic but harmless phishing emails to users and automatically assigns security awareness training to anyone who clicks a link or enters credentials. To complete this exercise, your account needs the **Attack Simulation Administrator** role (or **Global Administrator** or **Security Administrator**), and a Microsoft Defender for Office 365 Plan 2 licence.

### Task 1: Launch a credential harvest simulation

1.  Go to **Email & collaboration \> Attack simulation training**, select the **Simulations** tab, and click **Launch a simulation**.

![](media/image18.png)

2.  On **Select technique**, select **Credential Harvest** and click **Next**.

![](media/image19.png)

3.  On **Name simulation**, enter **L9-Credential-Harvest-Test** and click **Next**.

![](media/image20.png)

4.  On **Select payload and login page**, choose any payload from the **Global** tab (for example a password-expiry or document-share payload) and click **Next**.

![](media/image21.png)

5.  On **Target users**, select **Include only specific users and groups**, click **Add users**, add your lab administrator, and click **Next**. Click **Next** on **Exclude users**.

![](media/image22.png)
>
![](media/image23.png)

6.  On **Assign training**, keep **Microsoft training experience (Recommended)** with **Assign training for me** and click **Next**. Keep the default landing page and click **Next**.

![](media/image24.png)
>
![](media/image25.png)

7.  On **Select end user notification**, keep **Microsoft default notification (recommended),** select delivery preferences and click **Next**.

![](media/image26.png)
>
![](media/image27.png)

8.  On **Launch details**, select **Launch this simulation as soon as I'm done** and keep the default end date. Click **Next**, review, and click **Submit**.

![](media/image28.png)
>
![](media/image29.png)
>
![](media/image30.png)

### Task 2: Respond to the simulation and review the report

1.  Open Outlook on the web at https://outlook.office.com. Within a few minutes the simulation email arrives. Click the link in the email and, on the fake sign-in page, enter any text (do not use a real password).

![](media/image31.png)
>
![](media/image32.png)
>
> **Note** - The simulation records that the link was clicked and the credentials page submitted; nothing you type is stored.

2.  Back in **Attack simulation training \> Simulations**, open **L9-Credential-Harvest-Test** and review the **Report** and **Users** tabs. Your account should show as **Compromised**, with training assigned.

![](media/image33.png)

**Note** - The report may take several minutes to update after you click.

## Exercise 3: Hunt and Investigate with Threat Explorer

**Time - 20 minutes**

Threat Explorer shows every message processed by Defender for Office 365 with its delivery action and location, and lets you remediate messages after delivery.

### Task 1: Explore email activity

1.  Go to **Email & collaboration \> Explorer** (or browse to https://security.microsoft.com/threatexplorerv3) and select **All email**.

![](media/image34.png)

2.  Set the date range to the **last 2 days** and add the filter **Recipient** equals your lab administrator address. Click **Refresh**.

3.  Add the filter **Recipient tags** equals **Priority account** to see how the tag from Exercise 1 is used in investigations.

> **Note** - If the tag has not applied yet, this filter returns no results. Remove it and continue.

4.  Select a message (for example the simulation email or a Microsoft notification), then open the **email entity page**. Review **Delivery action**, **Original delivery location**, **Latest delivery location**, authentication results and URLs.

### Task 2: Remediate and start an automated investigation

1.  In Explorer, select the check box next to a test message you received and click **Take action**.

![](media/image35.png)

2.  Choose **Move to mailbox folder \> Junk** (or **Soft delete**), and select **Initiate automated investigation**. Click **Next**, give a name and click **Submit**.

![](media/image36.png)
>
![](media/image37.png)
>
![](media/image38.png)
>
> **Note** - Choose a message you don't need, such as the simulation email. Soft-deleted messages can be recovered from Recoverable Items.

3.  Go to **Actions & submissions \> Action center** and review the action on the **Pending** or **History** tab.

![](media/image39.png)
>
![](media/image40.png)

4.  Go to **Emails & collaborations \> Investigations** (or **Automated investigations**) and open the investigation you started. Review the **Investigation graph**, **Evidence** and **Actions** tabs.

![](media/image41.png)
>
> **Note:** It can take 5–15 minutes after **Take action** for the investigation to appear. Click **Refresh** if it isn’t listed yet.  
> **If nothing appears under Investigations:** check that you selected **Initiate automated investigation** on the **Take action** wizard in Threat Explorer. If you didn’t, go back to **Explorer**, select the message, choose **Take action**, and tick that option.

## Exercise 4: Review Threat Discovery Reports

**Time - 15 minutes**

Reports and Advanced hunting help you find what got through and which users are most targeted.

### Task 1: Review email threat reports

1.  Go to **Reports \> Email & collaboration \> Email & collaboration reports** and open **Threat protection status**. ![](media/image42.png)

![](media/image43.png)

2.  Open the **Top senders and recipients** report and review the most targeted recipients.

### Task 2: Hunt with Advanced hunting

1.  Go to **Investigation & response \> Hunting \> Advanced hunting** and run the following query to list recent email with threats or overrides:

```kusto
EmailEvents
| where Timestamp > ago(2d)
| summarize Messages = count() by DeliveryAction, ThreatTypes, EmailDirection
| order by Messages desc
```

![](media/image44.png)

2.  Run a second query to show the URL clicks from the simulation:

```kusto
UrlClickEvents
| where Timestamp > ago(2d)
| project Timestamp, AccountUpn, Url, ActionType, IsClickedThrough
```

![](media/image45.png)
>
> **Note** - Data can take 10-15 minutes to appear in Advanced hunting.

## Summary

In this lab you tagged a priority account and applied the Strict preset policy to it, ran a credential harvest simulation and reviewed the results, hunted for email in Threat Explorer, remediated a message and started an automated investigation, and reviewed threat reports and Advanced hunting data.

## Clean up resources

1.  Optionally remove your lab administrator from **Strict protection** and the **Priority account** tag. These settings do not affect other labs.
