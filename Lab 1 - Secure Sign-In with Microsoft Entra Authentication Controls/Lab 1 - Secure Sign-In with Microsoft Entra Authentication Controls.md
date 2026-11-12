# Lab 1: Secure Sign-In with Microsoft Entra Authentication Controls

## Scenario

This lab walks you through configuring Microsoft Entra ID to establish a secure sign-in baseline for your organization. You will protect user and admin accounts using multi-factor authentication, enforce access policies with Conditional Access, enable self-service password recovery, configure emergency access accounts, and activate real-time risk-based identity protection.

Strong identity controls are the foundation of Zero Trust security. Every other security layer — device management, email protection, data security — depends on knowing that the person signing in is who they claim to be.

## Lab Objectives

- Configure Microsoft Authenticator as the primary authentication method

- Set up Conditional Access policies using built-in Zero Trust templates

- Enable Self-Service Password Reset (SSPR) for cloud-only accounts

- Create Emergency Access (break-glass) accounts to prevent admin lockout

- Enable Entra ID Protection with risk-based Conditional Access policies

## Prerequisites 

| **Requirement**          | **Details**                                                                                                         |
|--------------------------|---------------------------------------------------------------------------------------------------------------------|
| **Role Required**        | Global Administrator or Security Administrator on the Microsoft 365 tenant                                          |
| **License**              | Microsoft 365 Business Premium (all exercises); Defender Suite add-on required for Entra ID Protection (Exercise 5) |
| **Users**                | At least one test user account created in Microsoft Entra ID for policy validation                                  |
| **Authenticator App**    | Microsoft Authenticator installed on a mobile device (iOS or Android)                                               |
| **Break-glass Accounts** | Two cloud-only admin accounts should be created before deploying Conditional Access (covered in Exercise 4)         |

![](media/image1.png)

## Exercise 1 — Configure Microsoft Authenticator

Microsoft Authenticator enables users to sign in using a time-based one-time passcode (TOTP) or a number-matching push notification rather than a password alone. Enabling passwordless phone sign-in with Authenticator is the recommended step toward phishing-resistant authentication. The authentication strength hierarchy is: Password only (Poor) → SMS/Voice (Fair) → Authenticator push (Better) → Passwordless phone sign-in (Better) → Phishing-resistant FIDO2/Windows Hello (Best).

### Task 1 — Enable Microsoft Authenticator in the Authentication Methods Policy

This enables both MFA push notifications and passwordless phone sign-in for your organization.

1.  Sign in to Microsoft Entra admin center(entra.microsoft.com)

![](media/image2.png)

2.  From the left navigation pane, click on Users. Select **All users \> +New user**

![](media/image3.png)

3.  Enter the following details on the create new user page, and click **Next: Properties\>**

User principal name: **Cindy**

Display name: **Cindy White**

Password: [**P@ssw.rd1234**](mailto:P@ssw.rd1234)

![](media/image4.png)

4.  Click Next:\>Assignments.

![](media/image5.png)

5.  On the Assignments pane, click on Add role and select Global administrator

![](media/image6.png)

6.  On the Review + create page click **Create.**

![](media/image7.png)

![](media/image8.png)

![](media/image9.png)

7.  Under **Entra ID** \> Authentication methods \> Policies.

![](media/image10.png)

8.  Select Microsoft Authenticator from the list of methods.

![](media/image11.png)

9.  Toggle the method to **Enabled**. Confirm the Authentication mode is set to **Passwordless** and click **Save**. This allows users to sign in with passwordless phone sign-in.

![](media/image12.png)

![](media/image13.png)

### Task 2: Enforce MFA across Microsoft Admin portals

Microsoft mandates MFA for all users signing into Azure Portal, Entra Admin Center, Intune Admin Center, and Microsoft 365 Admin Center. This step verifies and accelerates that enforcement.

1.  Go to Microsoft 365 Admin Center (admin.microsoft.com) \> Setup \> Under 'Sign-in and Security', select Configure multifactor authentication (MFA).

![](media/image14.png)

![](media/image15.png)

![](media/image16.png)

**Note: If setup page does not load and instead downloads a htm file, then refresh the page wait for 2-3minutes and browse Setup in a new tab**

![](media/image17.png)

2.  Select Get started and follow the step-by-step MFA setup wizard.

![](media/image18.png)

3.  Click Next on MFA overview page.

![](media/image19.png)

4.  Click **Next**

![](media/image20.png)

![](media/image21.png)

5.  On the **Review and Finish** page select **Save configuration** and click **Done.**

![](media/image22.png)

![](media/image23.png)

### Task 3: User Registration: Add Authenticator as a Sign-In Method

After the authentication method policy is enabled, guide users to register the Authenticator app through the combined registration portal.

1.  User signs in to My Sign-Ins at mysignins.microsoft.com.

![](media/image24.png)

2.  Select Security info \> Add sign-in method \> Authenticator app \> do the set up and complete adding the app.

![](media/image25.png)

![](media/image26.png)

![](media/image27.png)

## Exercise 2: Set Up Conditional Access via Built-In Templates

**Scenario:** Conditional Access is the Microsoft Zero Trust policy engine. It evaluates every access request in real-time using signals from identity, device, location, and application — then either allows, blocks, or challenges the request with MFA. Built-in templates in the Microsoft 365 Admin Center make it fast to deploy a complete, recommended baseline of Conditional Access policies without configuring each one from scratch.

### Task 1: Deploy Conditional Access Policies from Zero Trust Templates

1.  Go to Microsoft 365 Admin Center (admin.microsoft.com) \> Setup \> Under 'Sign-in and Security', select Deploy Conditional Access (CA) Policy templates.

![](media/image28.png)

2.  Click on Review guide to proceed with the deployment of conditional access with policy templates.

![](media/image29.png)

3.  In Overview section, scroll to Template categories, click on the dropdown beside Select a security pillar- **Zero Trust (Recommended)** and click **Next.**

![](media/image30.png)

4.  In the Configuration \| Zero trust page, click on Require multifactor authentication for admins

![](media/image31.png)

5.  Similarly, turn on the other options of Zero trust and click **Next.**

![](media/image32.png)

6.  Leave Authentication methods in default state and click on the Next button.

![](media/image33.png)

7.  On the Review and finish page, go through all the details, then click on the Save configuration button

![](media/image34.png)

![](media/image35.png)

## Exercise 3 : Enable Self-Service Password Reset (Cloud-Only)

Self-Service Password Reset (SSPR) lets users securely reset their own passwords without calling the helpdesk. Users register one or more authentication methods (Authenticator app, email, phone) and can then reset their password at any time from the login screen or the My Account portal. This is the cloud-only configuration for Microsoft 365 Business Premium tenants not using on-premises Active Directory sync.

### Task 1 : Enable users reset their own passwords

1.  On Microsoft 365 admin center portal, navigate and click on **Setup**

![](media/image36.png)

2.  Under Sign-in and Security, select Let users reset their own passwords

![](media/image37.png)

3.  On **Let users reset their own passwords **page, click on **Get started** button

![](media/image38.png)

4.  Under **Self service password reset enabled, **click on** All** and select **Save**

![](media/image39.png)

**Note :** If SSPR is configured but users haven't registered authentication methods, they will be prompted to register the next time they sign in. Ensure this prompt is not blocked by Conditional Access policies targeting the registration flow.

## Exercise 4: Configure Emergency Access (Break-glass) accounts

Emergency access accounts — also called break-glass accounts — are highly privileged cloud-only administrator accounts used exclusively when normal administrative access is unavailable. Without them, a misconfigured Conditional Access policy, MFA system outage, or accidental permission removal could permanently lock your organization out of the Microsoft 365 tenant. Microsoft recommends creating at least two of these accounts.

### Task 1 — Create Break-Glass Admin Accounts

1.  In Microsoft Entra admin center portal, navigate and click on **Users**

![](media/image40.png)

2.  Enter the following details and click **Next: Properties\>**

User principal name: **alex.morgan**

Display name: **Alex Morgan**

Password: **P@55w.rd1234**

![](media/image41.png)

3.  Enter the following details and click **Next: Assignments\>**

First name: **Alex**

Last name: **Morgan**

Job title: **Break -Glass Administration**

Company: **Emergency Access Account**

Department: **Emergency Access Account**

Employee ID: **SEC-Adm-009**

![](media/image42.png)

4.  Click **+ Add role** and in the Directory roles search and select **Global Administrator**.

![](media/image43.png)

5.  Click **Review + create.**

![](media/image44.png)

6.  Click **Create.**

![](media/image45.png)

![](media/image46.png)

## Exercise 5 — Enable Entra ID Protection

MFA alone is not sufficient to stop modern attacks. Attackers exploit stolen credentials, MFA fatigue (tricking users into approving fraudulent push notifications), and unusual sign-in patterns. Entra ID Protection adds a real-time risk intelligence layer: it continuously evaluates sign-ins and user accounts against Microsoft's global threat intelligence, flags anomalies, and can automatically block or challenge risky access — even if MFA was approved.

This exercise deploys four capabilities: risk alerts for administrators, MFA registration enforcement, user risk-based Conditional Access, and sign-in risk-based Conditional Access.

### Task 1: Configure MFA registration policy

1.  In Microsoft Entra admin portal, navigate and click on **ID Protection.**

![](media/image47.png)

2.  Click on Dashboard.

![](media/image48.png)

3.  Under **Protect**, select **Multifactor authentication registration policy**

![](media/image49.png)

4.  Under Assignments, click on **All users**. Under **Include**, select **All users **or Select **individuals and groups **if limiting your rollout.

![](media/image50.png)

5.  Under **Exclude**, select **Users and groups **and choose your organization's emergency access or break-glass accounts. Click on **0 users and groups selected** link.

![](media/image51.png)

![](media/image52.png)

6.  Navigate to Policy enforcement and click on **Enabled**

**  
**![](media/image53.png)

![](media/image54.png)

### Task 2: Configure users at risk detected alerts

Microsoft Entra ID Protection sends a simple alert email when a user’s risk reaches your set level (for example, Medium). The email links directly to the Users flagged for risk report so admins can quickly review and take action, with alerts consolidated to avoid inbox noise and skipped if the risk is already remediated.

1.  In Microsoft Entra admin center portal, under ID Protection go to Dashboard.

![](media/image55.png)

2.  **In the Identity Protection \| Dashboard page, to** configure users at risk detected alerts, click on **Users at risk detected alerts.**

3.  As a [**Security administrator**](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/permissions-reference), you can set the user risk level that triggers the generation of this email. By default, the risk level is set to "**High**" risk. Now, click on **Medium**

![](media/image56.png)

4.  Click **Save.**

![](media/image57.png)

5.  To configure weekly digest email, select Weekly digest Information: As a Security administrator, you can switch sending a weekly digest email on or off and choose the users assigned to receive the email.

![](media/image58.png)

### Task 3: Enable Sign-In Risk Policy

Require Microsoft Entra multifactor authentication for Medium or High sign‑in risk to verify the user and automatically block risky access. This helps small and mid‑size businesses quickly stop compromised sign‑ins without added IT overhead.

1.  In Microsoft Entra admin portal, navigate and Expand **Entra ID** and then, click on **Conditional Access**

![](media/image59.png)

2.  On the Conditional Access \| Overview page, click on Create new policy.

![](media/image60.png)

3.  In the Name field, enter Require MFA for Risky Sign-Ins by clicking on the Paste button

In the **Name** field, enter **Require MFA for Risky Sign-Ins **

4.  Under Users or agent, click on **0 users or agents selected** link

![](media/image61.png)

5.  Click on the radio button to select All users.

![](media/image62.png)

6.  Click on the exclude tab and select users and groups.

![](media/image62.png)

![](media/image63.png)

7.  Navigate to **Target resources **section and click on **No target resources selected,** and select **All resources** in the side menu.

![](media/image64.png)

8.  Under Conditions, click on **0 conditions selected**

![](media/image65.png)

9.  Click on **Not configured** under Sign-in risk, select **Yes** to Configure, select High and medium for sign-in risk.

![](media/image66.png)

10. Navigate to Grant section and then click on 0 controls selected

![](media/image67.png)

11. Under Session, click on **0 controls selected**

![](media/image68.png)

12. Under **Enable policy**, click on the **On **button and click **Create.**

![](media/image69.png)

### Task 4: Update passwordless sign-in risk policy

1.  In Microsoft Entra admin center portal, navigate and click on **Conditional Access** and select **Policies**

![](media/image70.png)

2.  Click on **+ New policy.  
    **![](media/image71.png)

3.  In the **Name** field, enter **Passwordless Sign-in Risk Policy**

![](media/image72.png)

4.  Under User or agents section, click on **0 users or agents selected** link and select **All users**

![](media/image73.png)

5.  On **Exclude users** tab click on the radio button for **Select users and groups**

![](media/image74.png)

6.  Select Cindy white.

![](media/image75.png)

7.  Under Target resources, click on the** No target resources selected** link and select **All resources (formerly ‘All cloud apps’)**

![](media/image76.png)

8.  Under **Conditions**, click on **0 conditions selected **link

![](media/image77.png)

9.  Under Sign-in risk, click on **Not configured** link, then in the side pane toggle Yes for Configure, select High, Medium and click Done.

![](media/image78.png)

10. Under Grant, click on** 0 controls selected** link

11. In the **Grant** pane that appears on the right side, select the checkbox beside **Require authentication strength,** select **Phishing-resistant MFA** and click **Select**

![](media/image79.png)

12. Under Session, click on 0 controls selected link, Select the checkbox beside **Sign-in frequency.**

13. Select the radio button beside **Every time.  
    **![](media/image80.png)

14. Under **Enable policy**, select the **on **button to enable policy and click **Create.**

![](media/image81.png)
>
![](media/image82.png)

## Summary

This lab guides learners through configuring a secure sign-in baseline in Microsoft Entra ID using five core controls: Microsoft Authenticator for passwordless MFA, Conditional Access policies via Zero Trust templates, Self-Service Password Reset for cloud-only accounts, break-glass emergency access accounts, and Entra ID Protection with risk-based Conditional Access. Together these controls form the identity security foundation of a Zero Trust architecture for Microsoft 365 Business Premium tenants. The exercises progress logically from basic MFA setup through advanced risk detection, making it suitable for security administrators managing SMB environments. Completion of all five exercises gives learners a production-ready, defense-in-depth identity configuration aligned with Microsoft's recommended baseline.
