# Lab 4: Configure Device Enrollment in Microsoft Intune

## Objectives

Before Microsoft Intune can manage, secure, and evaluate a device, the device must be enrolled. In Microsoft 365 Business Premium the simplest way to enroll a Windows device is to join it to Microsoft Entra ID, which enrolls it into Intune automatically. In this lab you will prepare the tenant for Windows enrollment, configure the tenant-wide compliance settings, create enrollment restrictions, and then join and enroll a Windows 11 virtual machine and verify it in Intune and Entra ID.

- Prepare a pilot group and user licensed for Intune

- Configure Microsoft Entra device settings and Windows automatic MDM enrollment

- Configure the default (tenant-wide) compliance policy settings

- Create device platform and device limit enrollment restrictions

- Join a Windows 11 device to Microsoft Entra ID, enroll it in Intune, and verify the result

**Prerequisites**

A lab administrator account with the **Global Administrator** or **Intune Administrator** role.

A **Windows 11 Pro or Enterprise** lab VM on which you can sign in as a local administrator. The VM must not already be joined to Active Directory or Microsoft Entra ID.

One unassigned licence that includes Intune for the pilot user: **Microsoft 365 Business Premium** or **Microsoft 365 E5**.

The Microsoft Authenticator app on your phone, in case your tenant requires MFA at sign-in.

**This lab is self-contained. It does not depend on any other lab in the course. If an object already exists in your tenant, skip that step.**

## Exercise 1: Device Enrollment Prerequisites for Supported Platforms

Automatic enrollment for Windows needs three things: a user who holds an Intune licence, permission for that user to join devices to Microsoft Entra ID, and an MDM user scope that includes the user. In this exercise you configure all three.

### Task 1: Create the pilot user and group

This task creates the user who will enroll the device and the group that all enrollment settings target.

1.  Sign in to the Microsoft Entra admin center at https://entra.microsoft.com. Expand **Entra ID**, select **Users \> All users**, and click **+ New user \> Create new user**.

![](media/image1.png)

2.  Enter the following details and click **Review + create**, then **Create**:

    - **User principal name**: **intunepilot**

    - **Display name**: **Intune Pilot User**

    - **Password**: clear **Auto-generate password** and enter a password of your choice, then record it

    - **Usage location** (on the **Properties** tab): select your country

![](media/image2.png)
>
> **Note** - Usage location is required before a licence can be assigned.
>
![](media/image3.png)

3.  Open the **Microsoft 365 admin center** at https://admin.microsoft.com, go to **Users \> Active users**, select **Intune Pilot User**, open the **Licenses and apps** tab, select **Microsoft 365 Business Premium** (or **Microsoft 365 E5** if no Business Premium licence is free) and click **Save changes**.

![](media/image4.png)
>
![](media/image5.png)
>
> **Note** - If the licence cannot be assigned, the SKU may have no free units in the sandbox tenant. In **Billing \> Licenses**, compare **Assigned** with **Available** and free a licence from an unused account.

4.  In the Entra admin center, select **Groups \> All groups** and click **New group**. Set **Group type** to **Security**, **Group name** to **SG-Intune-Pilot**, **Membership type** to **Assigned**, add **Intune Pilot User** under **Members**, and click **Create**.

![](media/image6.png)
>
![](media/image7.png)
>
![](media/image8.png)

### Task 2: Review Microsoft Entra device join settings

This task confirms that users are allowed to join devices to Microsoft Entra ID.

1.  In the Entra admin center, go to **Entra ID \> Devices \> Device settings**.

![](media/image9.png)

2.  Confirm that **Users may join devices to Microsoft Entra** is set to **All** (or **Selected**, with **SG-Intune-Pilot** included).

![](media/image10.png)

3.  Review **Maximum number of devices per user** (default **50**) and **Require Multifactor Authentication to register or join devices with Microsoft Entra**. Leave the MFA setting at **No**. Click **Save** if you changed anything.

![](media/image11.png)
>
> **Note** - Microsoft recommends enforcing MFA for device registration with a Conditional Access policy (user action **Register or join devices**) rather than with this tenant-wide switch.

### Task 3: Configure Windows automatic enrollment

This task sets the MDM user scope so that devices joined by pilot users enroll in Intune automatically.

1.  Sign in to the Microsoft Intune admin center at https://intune.microsoft.com and go to **Devices \> Enrollment**.

![](media/image12.png)

2.  Select the **Windows** tab, then click **Automatic Enrollment**. If prompted, select **Microsoft Intune**.

![](media/image13.png)

3.  Set **MDM user scope** to **Some**, click **Select groups**, choose **SG-Intune-Pilot**, and click **Select**.

![](media/image14.png)
>
> **Note** - In production most Business Premium tenants set MDM user scope to **All**. Using a pilot group lets you roll out enrollment in stages.

4.  Set **Windows Information Protection (WIP) user scope** to **None**, leave the MDM URLs at their defaults, and click **Save**.

![](media/image15.png)
>
> **If a user is in both the MDM and WIP scope, a personally owned device registers for WIP only and does not enroll in Intune.**

## Exercise 2: Default Compliance Policy Settings

Compliance policy settings are tenant-wide settings that behave like a built-in compliance policy every device receives. They decide how Intune treats devices that have no compliance policy assigned and how long a compliance result remains valid.

### Task 1: Configure compliance policy settings

1.  In the Intune admin center, go to **Endpoint security \> Device compliance**.![](media/image16.png)

2.  Select **Compliance policy settings**, set **Mark devices with no compliance policy assigned as** to **Not compliant**.

![](media/image17.png)
>
![](media/image18.png)
>
> **Note** - The default is **Compliant**, which means a device with no policy passes any Conditional Access check that requires a compliant device. Set it to **Not compliant** whenever you use device-based Conditional Access.

3.  Leave **Compliance status validity period (days)** at **30** (allowed range 1-120). A device that has not reported compliance within this period is treated as noncompliant.

4.  Click **Save**.

> **Note** - Your enrolled device will show **Not compliant** until a Windows compliance policy is assigned to it. This is expected in this lab.

## Exercise 3: Device Enrollment Restrictions

Enrollment restrictions control which platforms, OS versions and ownership types can enroll, and how many devices a user can enroll. Each restriction type has a default policy that applies to everyone; you create higher-priority policies for specific groups.

### Task 1: Create a Windows device platform restriction

This task allows only Windows 11 devices to enroll for the pilot group.

1.  In the Intune admin center, go to **Devices \> Enrollment**. Under **Enrollment options**, select **Device platform restriction**.

![](media/image19.png)

2.  Select the **Windows restrictions** tab and click **Create restriction**.

![](media/image20.png)

3.  On **Basics**, enter **LAB-Windows-Platform-Restriction** as the **Name** and **Allow Windows 11 for the pilot group** as the **Description**. Click **Next**.

![](media/image21.png)

4.  On **Platform settings**, set **MDM** to **Allow**, **Personally owned devices** to **Allow**, and **Allow min/max range** **Min** to **10.0.22000.0**. Leave **Max** empty and click **Next**.

![](media/image22.png)
>
> **Do not block personally owned Windows devices in this lab. When personal devices are blocked, Intune also blocks Microsoft Entra join from Windows Settings unless the device is registered with Windows Autopilot, so the enrollment in Exercise 4 would fail.**

5.  On **Scope tags**, click **Next**. On **Assignments**, click **Add groups**, select **SG-Intune-Pilot**, and click **Next**.

![](media/image23.png)
>
![](media/image24.png)

6.  On **Review + create**, click **Create**. The new restriction appears above the **Default** restriction with **Priority 1**.

![](media/image25.png)
>
![](media/image26.png)
>
> **Note** - Assignment changes can take about 15 minutes to take effect. Complete Task 2 while you wait.

### Task 2: Create a device limit restriction

This task limits how many devices each pilot user can enroll.

1.  In **Devices \> Enrollment**, select the **Windows** tab and click **Device limit restriction**.

![](media/image27.png)

2.  Click **Create restriction**. On **Basics**, enter **LAB-Device-Limit-5** as the **Name** and click **Next**.

![](media/image28.png)
>
![](media/image29.png)

3.  On **Device limit**, select **5** and click **Next**. On **Scope tags**, click **Next**.

![](media/image30.png)

4.  On **Assignments**, click **Select groups to include**, choose **SG-Intune-Pilot**, click **Select**, then **Next**. Click **Create**.

![](media/image31.png)
>
![](media/image32.png)
>
> **Note** - Windows device limits do not apply to Microsoft Entra joined, Autopilot, co-managed or device enrollment manager enrollments. For Entra joined devices, the limit comes from **Maximum number of devices per user** in Entra device settings (Exercise 1, Task 2).

### Task 3: Review restriction priority

1.  Return to **Device platform restriction \> Windows restrictions** and confirm **LAB-Windows-Platform-Restriction** has a higher priority than **All Users** (Default). Restrictions are evaluated from the top; drag a row to change its priority.

![](media/image33.png)

## Exercise 4: Enroll and Verify a Windows 11 Device

> In this exercise you create a Windows 11 VM in Azure, then join it to Microsoft Entra ID yourself as the pilot user. Because automatic enrollment is set up for the pilot group, the device enrolls in Intune during the join.

### Task 1: Create the Windows 11 VM

> Complete this task from the lab jump box.

1.  In the Azure portal (<https://portal.azure.com>), go to **Virtual machines \> + Create \> Azure virtual machine**. On the **Basics** tab, enter:

    - **Resource group**: the lab resource group (for example **smb-rg**)

    - **Virtual machine name**: **L4-W11-XXXX**

    - **Image**: **Windows 11 Enterprise, version 25H2 - x64 Gen2**

    - **Size**: **Standard_D2s_v5** (or **Standard_B2ms**)

    - **Username**: **l4admin**, with the following password- P@55w.rd1234

    - **Public inbound ports**: **RDP (3389)**

    - **Licensing**: tick **I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights**

2.  Click **Review + create**, then **Create**.

![](media/image34.png)

3.  When the deployment completes, click **Go to resource \> Connect \> Connect**, download the **Native RDP** file, and sign in as **.\l4admin**.

![](media/image35.png)

### Task 2: Join the device to Microsoft Entra ID

> Complete these steps in the RDP session to **L4-W11-XXXX**, signed in as the local administrator **l4admin**.

1.  Open **Settings \> Accounts \> Access work or school** and click **Connect**.

![](media/image36.png)
>
![](media/image37.png)

2.  In the **Set up a work or school account** window, don’t type in the email box. Under **Alternate actions**, click **Join this device to Microsoft Entra ID**.  
    ![](media/image38.png)

3.  Sign in as **intunepilot@\<your-tenant\>.onmicrosoft.com** with the password you recorded, and complete the MFA prompt if asked.

4.  On **Make sure this is your organization**, check the tenant name and click **Join**. When **You’re all set!** appears, click **Done**.

![](media/image39.png)
>
![](media/image40.png)

5.  Restart the VM. When you reconnect over RDP, sign in as **AzureAD\intunepilot@\<your-tenant\>.onmicrosoft.com** with the pilot user’s password.

### Task 3: Verify enrollment on the device

1.  Open **Terminal** and run the following command:

```powershell
dsregcmd /status
```

![](media/image41.png)
>
![](media/image42.png)
>
> **Note** - Under **Device State**, **AzureAdJoined** should be **YES**. Under **Tenant Details**, **MdmUrl** should point to **enrollment.manage.microsoft.com**.

2.  Run the following prompt in the terminal, to enroll the device into Intune  
    **start "ms-device-enrollment:?mode=mdm&username=intunepilot@otuwamsbXXXXXX.onmicrosoft.com"**

### Task 4: Verify the device in Intune and Microsoft Entra ID

1.  In the Intune admin center, go to **Devices \> Windows** (or **Devices \> All devices**) and click **Refresh**.

2.  Select the device and confirm **Primary user** is **Intune Pilot User**, **Ownership** is **Corporate**, and **Compliance** is **Not compliant**.

![](media/image43.png)
>
> **Note** - Entra joined devices are recorded as corporate. The device is not compliant because no compliance policy is assigned yet and you set unassigned devices to **Not compliant** in Exercise 2.

3.  In the Entra admin center, go to **Entra ID \> Devices \> All devices**, find the device, and confirm **Join type** is **Microsoft Entra joined** and **MDM** is **Microsoft Intune**.

![](media/image44.png)

## Summary

In this lab you prepared Microsoft 365 Business Premium for Windows device enrollment. You created a licensed pilot user and group, confirmed Entra ID device join settings, and set the Windows MDM user scope. You set unassigned devices to Not compliant, created a Windows platform restriction and a device limit restriction for the pilot group, then joined a Windows 11 VM to Microsoft Entra ID, verified that it enrolled automatically in Intune, and learned how to troubleshoot common enrollment failures.

## Clean up resources

1.  Leave the device, group and restrictions in place; they do not affect other labs. To reset, delete **LAB-Windows-Platform-Restriction** and **LAB-Device-Limit-5** and set **MDM user scope** back to **All**.
