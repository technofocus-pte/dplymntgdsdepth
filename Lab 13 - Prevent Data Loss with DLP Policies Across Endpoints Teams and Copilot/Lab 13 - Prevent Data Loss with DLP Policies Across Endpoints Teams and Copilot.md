# Lab 13: Prevent Data Loss with DLP Policies Across Endpoints, Teams, and Copilot

## Objectives

Data loss prevention (DLP) policies detect sensitive information and stop it from being shared in the wrong place. In this lab you will onboard a Windows 11 device to Purview, create a DLP policy for Exchange, SharePoint, OneDrive and Teams, create an Endpoint DLP policy that blocks uploading sensitive files to cloud services, and create a DLP policy that stops Microsoft 365 Copilot and Copilot Chat from processing prompts that contain sensitive data.

- Onboard a Windows 11 device to Microsoft Purview

- Create a DLP policy for Exchange, SharePoint, OneDrive and Teams

- Create an Endpoint DLP policy and test it on the device

- Create a DLP policy for Microsoft 365 Copilot and Copilot Chat

- Review DLP alerts and activity

**Prerequisites**

A lab administrator account with the **Global Administrator** or **Compliance Administrator** role.

Microsoft 365 Business Premium plus the Purview Suite (or Microsoft 365 E5). Endpoint DLP is not included in Business Premium alone.

The Azure subscription provided with your lab environment, to create a Windows 11 VM.

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

**Testing the Copilot policy needs Microsoft 365 Copilot or Copilot Chat. If your tenant has no Copilot licence, create the policy and follow the instructor demonstration for the test.**

## Exercise 1: Prepare the Lab Environment

This lab needs auditing and a Windows 11 device onboarded to Defender, which Purview uses for Endpoint DLP.

### Task 1: Turn on auditing

1.  Open the Microsoft Purview portal at https://purview.microsoft.com, select **Solutions \> Audit**. If a banner prompts you to **Start recording user and admin activity**, click it. If no banner appears, auditing is already on.

> **Note** - Auditing can take up to 60 minutes to start. Continue with the lab; later steps that need audit data say so.

### Task 2: Create a Windows 11 VM

Complete this task from the lab VM.

1.  In the Azure portal (https://portal.azure.com), go to **Virtual machines \> + Create \> Azure virtual machine**.

2.  On the **Basics** tab enter: **Resource group** the lab resource group (for example **smb-rg**);

- **Virtual machine name** **L13-W11-XXXX**;

- **Image** **Windows 11 Enterprise, version 24H2 - x64 Gen2** (or later);

- **Size** **Standard_D2s_v5** (or **Standard_B2ms**);

- **Username** **l13admin** with the following password- <P@55w.rd1234>;

- Select the multi-tenant hosting licence confirmation.

![](media/image1.png)
![](media/image2.png)
![](media/image3.png)

3.  Click **Review + create**, then **Create**, and wait for the deployment to complete.

![](media/image4.png)
![](media/image5.png)

**Note** - If an onboarded Windows 11 VM from another lab already exists in today's tenant, you can reuse it and skip this task.

4.  Click **Go to resource \> Connect \> Connect**, select **Native RDP**, download the RDP file and sign in as **.\l13admin**.  
    Password - P@55w.rd1234

![](media/image6.png)
![](media/image7.png)

### Task 3: Onboard the VM to Defender

1.  In the Microsoft Defender portal (https://security.microsoft.com), go to **Assets \> Devices** and click **Onboard** (or **System \> Settings \> Endpoints \> Onboarding**). Select **Windows 10 and 11**, **Connectivity type** **Streamlined**, **Deployment method** **Local Script (for up to 10 devices)**, and click **Download onboarding package**.

![](media/image8.png)
![](media/image9.png)

2.  Copy the ZIP into the RDP session to **L13-W11-XXXX**, extract it to the **Desktop** and run the script. Type **Y** when prompted.

```powershell
& "$env:USERPROFILE\Desktop\WindowsDefenderATPOnboardingPackage\WindowsDefenderATPLocalOnboardingScript.cmd"
```

![](media/image10.png)

![](media/image11.png)

![](media/image12.png)

3.  After 5-15 minutes, confirm **L13-W11-XXXX** shows **Onboarded** in **Assets \> Devices**.

![](media/image13.png)

### Task 4: Turn on device onboarding in Purview

1.  In the Purview portal (https://purview.microsoft.com), go to **Settings \> Device onboarding \> Devices** and click **Turn on device onboarding** if it is shown. Confirm when prompted.

![](media/image14.png)
> **Note** - Devices already onboarded to Defender for Endpoint appear in Purview automatically. Allow up to 30 minutes.

2.  Confirm **L13-W11-XXXX** appears in the **Devices** list with **Configuration status** **Updated** and **Policy sync status** **Updated**.

![](media/image15.png)

## Exercise 2: Create a DLP Policy for Email, Files, and Teams

### Task 1: Create the policy

1.  In the Purview portal, go to **Solutions \> Data Loss Prevention \> Policies** and click **+ Create policy** and select **Enterprise applications & devices.**

![](media/image16.png)

2.  Select **Data stored in connected sources** (if asked), choose the **Custom** category and **Custom policy** template, and click **Next**.

![](media/image17.png)

3.  Enter **L13-DLP-Financial-Data** as the name and **Blocks sharing of payment card data** as the description. Click **Next**. Keep **Full directory** and click **Next**.

![](media/image18.png)

4.  On **Choose where to apply the policy**, select **Exchange email**, **SharePoint sites**, **OneDrive accounts** and **Teams chat and channel messages**. Clear all other locations. Click **Next**.

![](media/image19.png)

5.  Select **Create or customize advanced DLP rules** and click **Next**. Click **+ Create rule** and name it **Credit card shared externally**.

![](media/image20.png)

6.  Under **Conditions**, click **Add condition \> Content contains \> Add \> Sensitive info types**, select **Credit Card Number**, and click **Add**.

![](media/image21.png)

7.  Under **Actions**, click **Add an action \> Restrict access or encrypt the content in Microsoft 365 locations**, select **Block everyone**.

![](media/image22.png)

8.  Under **User notifications**, turn **On** notifications and select **Notify the user who sent, shared or last modified the content**. Turn on **Policy tips**. Under **Incident reports**, set **Severity** to **High** and turn on **Send an alert to admins when a rule match occurs**. Click **Save**, then **Next**.

![](media/image23.png)

![](media/image24.png)

9.  On **Policy mode**, select **Turn the policy on immediately** and click **Next**, then **Submit** and **Done**.

![](media/image25.png)

![](media/image26.png)

![](media/image27.png)

**Note** - In production, start with **Run the policy in simulation mode** and review matches first. Policies take up to an hour to take effect.

### Task 2: Test the policy in Teams

1.  Open Teams on the web (https://teams.microsoft.com) as the lab administrator, and start trial. In a chat with another user, send **Card for the booking: 4111 1111 1111 1111 exp 12/29**.

![](media/image28.png)
> **Note** - 4111 1111 1111 1111 is a public test card number. If the policy is not active yet, try again after an hour.

2.  Confirm the message shows **This message was blocked** (the sender sees a policy tip; recipients see that the message was blocked).

![](media/image29.png)

## Exercise 3: Create an Endpoint DLP Policy

Endpoint DLP monitors and controls what users do with sensitive files on Windows devices, such as copying them to USB or uploading them to cloud services.

### Task 1: Create the policy

1.  In the Purview portal, go to Settings \> Roles and scopes \> Role groups.

2.  Open Compliance Administrator, select Edit, add your lab account (odl_user…), and save.

3.  Wait 15–30 minutes, sign out of Purview, sign back in, and create the policy again.

4.  Go to **Data Loss Prevention \> Policies** and click **+ Create policy**. Choose **Custom \> Custom policy** and click **Next**.

![](media/image30.png)
![](media/image31.png)

5.  Enter **L13-Endpoint-DLP** as the name and click **Next**. Keep **Full directory** and click **Next**.

![](media/image32.png)

6.  On **Choose where to apply the policy**, select only **Devices**. Click **Next**.

![](media/image33.png)
> **Note:** If **Devices** isn’t listed, turn on device onboarding (**Settings \> Device onboarding \> Devices**) and try again. The option is greyed out unless your account has an E5 or Purview Suite licence.
![](media/image34.png)
![](media/image35.png)

7.  Select **Create or customize advanced DLP rules**, click **+ Create rule**, and name it **Block upload of card data**. Add the condition **Content contains \> Sensitive info types \> Credit Card Number**.

![](media/image36.png)
![](media/image37.png)

8.  Under **Actions**, click **Add an action \> Audit or restrict activities on devices**. Under **File activities for all apps**, select **Apply restrictions to specific activity**, select unallowed browsers and uploads set to **Block** select **Copy to a network share** set to **Block**, and **Copy to a removable USB device** set to **Block**.

![](media/image38.png)

![](media/image39.png)

![](media/image40.png)

![](media/image41.png)

9.  Turn on **User notifications** and **Incident reports** (severity **Medium**). Click **Save**, **Next**, select **Turn it on right away**, and click **Submit**.

![](media/image42.png)

### Task 2: Test on the device

Complete this task in the RDP session to the Windows 11 VM.

1.  Open **Notepad**, type **Card 4111 1111 1111 1111 exp 12/29 CVV 123**, and save the file to the **Desktop** as **L13-card.txt**.

![](media/image43.png)

2.  Open **Microsoft Edge**, browse to https://www.file.io/ (or any web file upload site) and try to upload **L13-card.txt**.

![](media/image44.png)

**Note** - Endpoint DLP policies can take up to an hour to reach the device.

3.  Confirm a Windows notification says the upload was **blocked by your organization**.

## Exercise 4: Create a DLP Policy for Microsoft 365 Copilot

DLP for Microsoft 365 Copilot and Copilot Chat can stop Copilot from responding to prompts that contain sensitive information, and stop Copilot from processing labeled content.

### Task 1: Create the Copilot policy

1.  Go to **Data Loss Prevention \> Policies**, click **+ Create policy**, choose **Custom \> Custom policy**, and click **Next**.

2.  Enter **L13-DLP-Copilot-Prompts** as the name and click **Next**. Keep **Full directory** and click **Next**.

![](media/image45.png)

3.  On **Choose where to apply the policy**, select **Microsoft Copilot, Copilot Chat, and Copilot Cowork** (shown as **Microsoft 365 Copilot and Copilot Chat** in some tenants). All other locations are disabled automatically. Click **Next**.

![](media/image46.png)

**Note** - This location is available only with the **Custom** template.

4.  Click **+ Create rule** and name it **Block prompts with card data**. Add the condition **Content contains \> Sensitive info types \> Credit Card Number**.

![](media/image47.png)

5.  Click **Add an action \> Prevent Copilot from processing content** and select **Processing prompts**. Click **Save**, then **Next**.

![](media/image48.png)

6.  Select **Turn it on right away** (or simulation mode), click **Next**, then **Submit**.

![](media/image49.png)

![](media/image50.png)

![](media/image51.png)

> **Note** - A single rule can't combine sensitive info type and sensitivity label conditions. To stop Copilot from using labeled files, create a second rule with **Content contains \> Sensitivity labels** in the same policy. Changes can take up to four hours.

### Task 2: Test the Copilot policy (if Copilot is available)

1.  Open https://m365.cloud.microsoft/chat and enter the prompt **Is 4111 1111 1111 1111 a valid card number?**

2.  Confirm Copilot doesn't answer and shows a message that the prompt contains sensitive information blocked by your organization.

![](media/image52.png)

## Exercise 5: Review DLP Alerts and Activity

### Task 1: Review alerts and activity

1.  In the Purview portal, go to **Data Loss Prevention \> Alerts** and open any alert from this lab.

![](media/image53.png)

2.  Go to **Data Loss Prevention \> Explorers \> Activity explorer.**

![](media/image54.png)

**Note** - Alerts and activities can take 30-60 minutes to appear.

## Summary

In this lab you onboarded a Windows 11 device to Purview, created DLP policies for Exchange, SharePoint, OneDrive and Teams, for devices, and for Microsoft 365 Copilot and Copilot Chat, tested them, and reviewed DLP alerts and activity.

## Clean up resources

1.  Set **L13-DLP-Financial-Data**, **L13-Endpoint-DLP** and **L13-DLP-Copilot-Prompts** to **Turn it off** if other labs today need to share card data, and stop (deallocate) the Windows 11 VM at the end of the day.
