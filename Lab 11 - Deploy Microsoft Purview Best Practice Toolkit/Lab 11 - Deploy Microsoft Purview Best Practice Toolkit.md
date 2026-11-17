# Lab 11: Deploy Microsoft Purview Best Practice Toolkit

## Objectives

Microsoft publishes a Best Practice deployment toolkit (https://github.com/microsoft/BestPractice_Deploy-Scripts) that applies its recommended data security baseline to a Microsoft 365 Business Premium tenant with PowerShell. The Purview script turns on key tenant settings, creates a standard sensitivity label taxonomy (Public, General, Confidential, Highly Confidential), creates DLP policies in simulation mode, and produces a deployment plan and report. In this lab you will prepare PowerShell 7, preview the deployment with -WhatIf, apply the baseline, validate it, and review what was created in the Purview portal.

- Install PowerShell 7 and the modules required by the toolkit

- Download the Microsoft Best Practice deployment scripts

- Preview the Purview baseline with -WhatIf and review the deployment plan

- Apply the baseline and review the end-of-run report

- Validate the tenant configuration and review the labels and DLP policies in the Purview portal

**Prerequisites**

A lab administrator account with the **Global Administrator** role (or Compliance Administrator, Compliance Data Administrator, SharePoint Administrator and Exchange Administrator).

Microsoft 365 Business Premium. With the Purview Suite or E5, the toolkit also creates an Endpoint DLP policy.

Internet access from the lab jump box to github.com and the PowerShell Gallery.

## Exercise 1: Prepare the Lab Environment

**Time - 15 minutes**

The toolkit requires PowerShell 7 or later; Windows PowerShell 5.1 is not supported. Complete this exercise on the lab jump box.

### Task 1: Install PowerShell 7

1.  Install PowerShell 7: run the following command.

```powershell
iex "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI -Quiet"
```
![](media/image1.png)

2.  Close Windows PowerShell. From **Start**, open **PowerShell 7 (x64)** as administrator. All remaining commands in this lab run in **PowerShell 7**.

![](media/image2.png)

### Task 2: Install the required modules

1.  In **PowerShell 7**, install the modules used by the toolkit:

```powershell
Install-Module ExchangeOnlineManagement -Scope CurrentUser -Force
Install-Module Microsoft.Online.SharePoint.PowerShell -Scope CurrentUser -Force
Install-Module Microsoft.Graph.Authentication -Scope CurrentUser -Force
Install-Module Microsoft.Graph.Beta.Identity.DirectoryManagement -Scope CurrentUser -Force
```

![](media/image3.png)

> **Note** - Answer **Y** if asked to trust the PSGallery repository.

### Task 3: Download the toolkit

1.  In Microsoft Edge, open https://github.com/microsoft/BestPractice_Deploy-Scripts, click **Code \> Download ZIP**, and save it to **Downloads**.

![](media/image4.png)

2.  Create a folder BP in the C drive and then extract the downloaded ZIP file to **C:\BP**. In **PowerShell 7**, unblock the files and locate the Purview script:

```powershell
Get-ChildItem C:\BP -Recurse | Unblock-File
Get-ChildItem C:\BP -Recurse -Filter Deploy-PurviewBestPractice.ps1 | Select-Object FullName
```

![](media/image5.png)
![](media/image6.png)
![](media/image7.png)
![](media/image8.png)
![](media/image9.png)
> **Note** - The folder layout can change between toolkit versions. Use the folder returned by this command in the next exercises.

3.  Change to the folder that contains **Deploy-PurviewBestPractice.ps1**, for example:

```powershell
Set-Location (Split-Path (Get-ChildItem C:\BP -Recurse -Filter Deploy-PurviewBestPractice.ps1).FullName)
```

![](media/image10.png)

## Exercise 2: Preview the Baseline

Running the toolkit with **-WhatIf** shows every change it would make without changing the tenant, and writes a deployment plan you can review with stakeholders.

### Task 1: Run the toolkit in WhatIf mode

1.  Run the toolkit in preview mode. Replace the **admin@\<tenant\>.onmicrosoft.com** with your lab tenant:

```powershell
.\Deploy-PurviewBestPractice.ps1 -TenantAdminUpn user@<tenant>.onmicrosoft.com -WhatIf
```

![](media/image11.png)
![](media/image12.png)
![](media/image13.png)
![](media/image14.png)
![](media/image15.png)
![](media/image16.png)
![](media/image17.png)
![](media/image18.png)
> **Note** - Sign in when prompted and grant consent. If the toolkit offers to install missing modules, answer **Y**.

![](media/image19.png)

2.  Review the console output. Each state-changing action is listed with **What if:**. Items that depend on labels not yet created show as **would be created earlier in this run**.

![](media/image20.png)

3.  Open the **Deploy-PurviewBestPractice-Plan-PUR-\<id\>.html** file written to the current folder and review the planned tenant settings, sensitivity labels, DLP policies and their simulation mode.

![](media/image21.png)
![](media/image22.png)
> **Note** - The plan and report files contain tenant details. Store them only in approved private locations.

### Task 2: Apply the baseline

1.  Run the toolkit without **-WhatIf**:

```powershell
.\Deploy-PurviewBestPractice.ps1 -TenantAdminUpn user@<tenant>.onmicrosoft.com
```

> **Note:** Replace \<tenant admin UPN\> with the username from the **Resources** tab of your lab environment, for example **odl_user_XXXXXXX@\<tenant\>.onmicrosoft.com**.

![](media/image23.png)

2.  When the preflight summary asks for confirmation, type **y** and press **Enter**. Wait for the run to finish.

![](media/image24.png)

3.  Open the end-of-run **HTML report** and review the status of each action (**Succeeded**, **Skipped** with reason, or **Failed**).

![](media/image25.png)
> **Note** - If an action fails because a tenant feature is still being provisioned (for example audit or Exchange organization customization), wait 30 minutes and run the same command again. The toolkit skips objects it already created.

### Task 3: Review the results in the Purview portal

1.  In the Purview portal (https://purview.microsoft.com), go to **Solutions \> Information Protection \> Sensitivity labels** and review the **Public**, **General**, **Confidential** and **Highly Confidential** labels and their sublabels.

![](media/image26.png)

![](media/image27.png)

**Note** - Labels can take up to 24 hours to appear in Office apps.

2.  Go to **Information Protection \> Label Publishing policies** and review the label policy created by the toolkit.

![](media/image28.png)

3.  Go to **Solutions \> Data Loss Prevention \> Policies** and review the DLP policies created by the toolkit. Confirm they are in **simulation mode**.

![](media/image29.png)
> **Note** - The toolkit leaves DLP in simulation. Promote policies to enforcement only after reviewing simulation results, following the toolkit's DLP Simulation Exit Runbook.

## Summary

In this lab you prepared PowerShell 7 and the required modules, downloaded Microsoft's Best Practice deployment toolkit, previewed the Purview baseline with -WhatIf, applied it, validated the tenant against the deployment plan, and reviewed the sensitivity labels, label policy and simulation-mode DLP policies it created.

## Clean up resources

1.  Leave the baseline in place. The toolkit's labels and DLP policies use their own names and do not conflict with the **L12-** and **L13-** objects created in other labs.
