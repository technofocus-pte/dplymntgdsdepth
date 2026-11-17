# Lab 12: Classify and Protect Content with Sensitivity Labels in Microsoft Purview

## Objectives

Sensitivity labels classify and protect documents and emails wherever they go. In this lab you will turn on auditing and labels for SharePoint and OneDrive, create a custom sensitive information type, create and publish sensitivity labels, configure client-side auto-labeling, extend auto-labeling to Microsoft 365 services with an auto-labeling policy, and apply encryption to labeled content.

- Turn on auditing and sensitivity labels for Office files in SharePoint and OneDrive

- Create a custom sensitive information type

- Create sensitivity labels with encryption and content marking, and publish them

- Configure client-side auto-labeling

- Create a service-side auto-labeling policy in simulation mode

- Apply a label and verify encryption

**Prerequisites**

A lab administrator account with the **Global Administrator** or **Compliance Administrator** role and an Exchange Online mailbox.

Microsoft 365 Business Premium plus the Purview Suite (or Microsoft 365 E5). Auto-labeling is not included in Business Premium alone.

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

**Labels and label policies can take up to 24 hours to appear in Office apps. Exercise 1 starts the changes that take longest; if labels are not visible in Word yet at Exercise 5, return to it later.**

## Exercise 1: Prepare the Lab Environment

**Time - 10 minutes**

### Task 1: Turn on auditing

1.  Open the Microsoft Purview portal at https://purview.microsoft.com, select **Solutions \> Audit**. If a banner prompts you to **Start recording user and admin activity**, click it. If no banner appears, auditing is already on.

![](media/image1.png)
![](media/image2.png)
> **Note** - Auditing can take up to 60 minutes to start. Continue with the lab; later steps that need audit data say so.

### Task 2: Turn on sensitivity labels for SharePoint and OneDrive

This lets labels apply to Office files in SharePoint and OneDrive and is required for service-side auto-labeling and co-authoring encrypted files.

1.  In the Purview portal, go to **Solutions \> Information Protection \> Sensitivity labels**. If a banner says **Your organization has not turned on the ability to process content in Office online files that have encrypted sensitivity labels applied...**, click **Turn on now**.

![](media/image3.png)

**Note** - If no banner appears, the setting is already on.

2.  Alternatively, in **PowerShell 7** or **Windows PowerShell** connect to SharePoint Online and run:

```powershell
Connect-SPOService -Url https://<tenant>-admin.sharepoint.com -UseSystemBrowser $true
Set-SPOTenant -EnableAIPIntegration $true
(Get-SPOTenant).EnableAIPIntegration
```

![](media/image4.png)

## Exercise 2: Create a Sensitive Information Type

**Time - 10 minutes**

Sensitive information types (SITs) detect sensitive data such as credit card numbers. You can also create your own; here you create one for the company's employee ID format **EMP-123456**.

### Task 1: Create a custom SIT

1.  In the Purview portal, go to **Solutions \> Information Protection \> Classifiers \> Sensitive info types** (or **Data classification \> Classifiers**) and click **+ Create sensitive info type**.

![](media/image5.png)

2.  Enter **L12 Employee ID** as the **Name** and **Detects employee IDs in the format EMP-123456** as the **Description**. Click **Next**.

![](media/image6.png)

3.  Click **Create pattern**. Set **Confidence level** to **High confidence**, click **Add primary element \> Regular expression**, enter **L12-EmployeeID** as the ID and **EMP-\d{6}** as the regular expression, and click **Done**.

![](media/image7.png)

4.  Under **Supporting elements**, click **Add supporting elements \> Keyword list**, add the keywords **employee** and **staff ID**, and click **Done**. Click **Create**, then **Next**.

![](media/image8.png)

![](media/image9.png)

5.  Keep the recommended confidence level, click **Next**, review and click **Create**, then **Done**.

![](media/image10.png)

![](media/image11.png)

![](media/image12.png)

![](media/image13.png)

6.  Create the test text file containing **Employee EMP-123456**, in Notepad on the Lab VM and save it as **L12-test.txt**.

![](media/image14.png)

7.  Select **L12 Employee ID**, click **Test**, upload a text file containing **Employee EMP-123456**, and confirm the match.

![](media/image15.png)
![](media/image16.png)
![](media/image17.png)
![](media/image18.png)
![](media/image19.png)

## Exercise 3: Create and Publish Sensitivity Labels

You create two labels: **L12-General** with a footer only, and **L12-Confidential** with encryption, a watermark and client-side auto-labeling.

### Task 1: Create the L12-General label

1.  Go to **Information Protection \> Sensitivity labels** and click **+ Create a label**.

![](media/image20.png)

2.  Enter **L12-General** as the **Name** and **Display name**, and **Business data that is not for public release** as the **Description for users**. Click **Next**.

![](media/image21.png)

3.  On **Define the scope for this label**, select **Files & other data assets** and **Emails**. Click **Next**.

![](media/image22.png)

4.  On **Choose protection settings for the types of items you selected**, select **Apply content marking**. Click **Next**.

![](media/image23.png)

5.  Turn on **Add a footer**, click **Customize text**, enter **Classification: General**, and click **Save**. Click **Next** through the remaining pages and click **Create label**, then **Don't create a policy yet** and **Done**.

![](media/image24.png)

![](media/image25.png)

![](media/image26.png)

![](media/image27.png)

### Task 2: Create the L12-Confidential label with encryption and auto-labeling

1.  Click **+ Create a label**. Enter **L12-Confidential** as the **Name** and **Display name**, and **Sensitive business data; only employees can open it** as the description. Click **Next**.

![](media/image28.png)

2.  Select the scopes **Files & other data assets** and **Emails**. Click **Next**.

![](media/image29.png)

3.  Select **Control access** and **Apply content marking**. Click **Next**.

![](media/image30.png)

4.  On the Access control page, On **Access control**, select **Configure access control settings**, then set:

- **Assign permissions now or let users decide?**: **Assign permissions now**

- **User access to content expires**: **Never**

- **Allow offline access**: **Always**

![](media/image31.png)

5.  Scroll down to **Assign permissions to specific users and groups** and select **Assign permissions**. Select **+ Add all users and groups in your organization**, choose **Co-Author** under **Choose permissions**, and select **Save**.

![](media/image32.png)
![](media/image33.png)
![](media/image34.png)

6.  On the Content marking page, enable **Content marking** and Select **Add a footer**, select **Customize text**, enter **Confidential - Internal use only**, and select **Save**. Then select **Next**.![](media/image35.png)

7.  On **Auto-labeling for files and emails**, turn **On** auto-labeling. Click **Add condition \> Content contains \> Sensitive info types**.

![](media/image36.png)

8.  Add **L12 Employee ID** and **Credit Card Number**. Set **When content matches these conditions** to **Recommend that the user applies the label**, enter the message **This document contains employee or payment data. Apply the Confidential label.**, and click **Next**.

![](media/image37.png)

![](media/image38.png)

9.  Click **Next** through the remaining pages, click **Create label**, select **Don't create a policy yet**, and click **Done**.

![](media/image39.png)

![](media/image40.png)

**Note** - Client-side auto-labeling runs in Word, Excel, PowerPoint and Outlook while users work. **Recommend** shows a prompt; **Automatically apply** labels the content and notifies the user.

### Task 3: Publish the labels

1.  Go to **Information Protection \> Label Publishing policies** and click **Publish label**.

![](media/image41.png)

2.  Click **Choose sensitivity labels to publish**, select **L12-General** and **L12-Confidential**, click **Add**, then **Next**. Keep **Full directory** on **Assign admin units** and click **Next**.

![](media/image42.png)

![](media/image43.png)

![](media/image44.png)

3.  On **Publish to users and groups**, make sure **Exchange email** is selected. In the **Exchange email** row, select **Edit**. Choose the option to include specific users and groups, add your lab administrator and select **Done**. Then select **Next**.

![](media/image45.png)

![](media/image46.png)

![](media/image47.png)

4.  On **Policy settings**, select **Users must provide a justification to remove a label or lower its classification**. Click **Next**.

![](media/image48.png)

5.  On **Default settings for documents**, choose **L12-General** as the default label. Accept the defaults on the remaining pages, enter **L12-Label-Policy** as the name, and click **Submit**, then **Done**.

![](media/image49.png)
![](media/image50.png)
![](media/image51.png)
![](media/image52.png)
> **Note** - Allow up to 24 hours for the policy to reach Office apps. Office for the web often shows new labels within about an hour.
![](media/image53.png)

## Exercise 4: Extend Auto-Labeling to Microsoft 365 Services

Auto-labeling policies label files at rest in SharePoint and OneDrive and email in transit, without depending on the user's app. They always run in simulation first.

### Task 1: Create an auto-labeling policy in simulation mode

1.  In the Purview portal, go to Settings \> Roles and scopes \> Role groups.

2.  Open Information Protection Admins (or Compliance Administrator), select Edit, add your lab account (odl_user…), and save.

3.  Wait 15–30 minutes, sign out of Purview, sign back in, and create the policy again.

4.  Go to **Information Protection \> Policies \> Auto-labeling policies** and click **+ Create auto-labeling policy**. Use the following link if the option is not visible under policies  
    **https://purview.microsoft.com/informationprotection/autolabeling**

![](media/image54.png)

5.  On **Choose info you want this label applied to**, select **Automatically apply labels only\>** **Custom \> Custom policy** and click **Next**. ![](media/image55.png)

![](media/image56.png)

6.  Enter **L12-AutoLabel-EmployeeID** as the name and click **Next**.

![](media/image57.png)

7.  Click **Choose a label**, select **L12-Confidential**, click **Add**, and click **Next**. Keep **Full directory** and click **Next**.

![](media/image58.png)

8.  On **Choose locations where you want to apply the label**, keep **Exchange email**, **SharePoint sites** and **OneDrive accounts** set to **All**. Click **Next**.

![](media/image59.png)

9.  On **Set up common or advanced rules**, keep **Common rules** and click **Next**. Click **+ New rule**, name it **L12-AutoLabel-EmployeeID** add the condition **Content contains \> Sensitive info types \> L12 Employee ID**, and click **Save**, then **Next**

![](media/image60.png)
![](media/image61.png)
![](media/image62.png)

10. Keep the additional settings, click **Next**, select **Turn on policy**, click **Next**, then **Create policy** and **Done**.

![](media/image63.png)
![](media/image64.png)

## Exercise 5: Apply Encryption to Labeled Content

### Task 1: Label and encrypt a document

1.  Open https://www.office.com (or https://m365.cloud.microsoft) as the lab administrator, create a new blank **Word** document, and type **Employee EMP-123456 has been assigned a new laptop.**

![](media/image65.png)

![](media/image66.png)

2.  Wait a few seconds. Word shows the auto-labeling recommendation to apply **L12-Confidential**. Click **Apply** (or select **Sensitivity \> L12-Confidential** on the **Home** ribbon).

> **Note** - If the **Sensitivity** button or the labels are not shown yet, the label policy has not reached your account. Continue later or follow the instructor demonstration.

3.  Confirm the **CONFIDENTIAL** watermark and **Classification: Confidential** footer are added.

![](media/image67.png)

### Task 2: Review labeling activity

1.  In the Purview portal, go to **Solutions \> Information Protection \> Explorers \> Activity explorer** (or **DSPM \> Discover \> Activity explorer**) and filter **Activity** to **Label applied**.

![](media/image68.png)

**Note** - Activity can take 60-90 minutes to appear.

## Summary

In this lab you turned on auditing and labels for SharePoint and OneDrive, created a custom sensitive information type, created and published two sensitivity labels with content marking and encryption, configured client-side auto-labeling, created a service-side auto-labeling policy in simulation, and applied encryption to a document and an email.

## Clean up resources

1.  Leave the labels and policies in place; they use **L12-** names and do not affect other labs.
