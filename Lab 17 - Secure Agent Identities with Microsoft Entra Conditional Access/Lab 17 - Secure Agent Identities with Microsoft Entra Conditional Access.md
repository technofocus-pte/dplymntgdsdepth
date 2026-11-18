# Lab 17: Secure Agent Identities with Microsoft Entra Conditional Access

## Objectives

Agents in Agent 365 get their own identities in Microsoft Entra (Entra Agent ID). Conditional Access can now target those agent identities, so you can block agents that Microsoft Entra considers risky, just as you block risky users. In this lab you will review agent identities in Entra, create Conditional Access policies for agents in report-only mode, protect users who authorize agents with MFA, and review the results in the sign-in logs.

- Review agent identities and blueprints in Microsoft Entra

- Create a Conditional Access policy that blocks high-risk agent identities

- Create a policy for agents' user accounts

- Require MFA for users before they authorize agents

- Review Conditional Access results for agents

**Prerequisites**

A lab administrator account with the **Global Administrator** or **Conditional Access Administrator** role.

A **Microsoft Agent 365** licence together with Microsoft Entra ID P1 (included in Business Premium) or Microsoft 365 E7.

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

**Conditional Access for agents is partly in preview. All policies in this lab are created in Report-only mode so they can't block anything in the lab tenant.**

## Exercise 1: Prepare the Lab Environment

### Task 1: Confirm the Agent 365 licence

1.  In the Microsoft 365 admin center (https://admin.microsoft.com), go to **Billing \> Licenses**, open **Agent 365** (or **Microsoft Agent 365**), and confirm a licence is assigned to your lab administrator.

> **Note** - Agent 365 features can take up to a few hours to appear after the licence is assigned. Agent 365 is evolving quickly; menu names in this lab may differ slightly in your tenant.

## Exercise 2: Review Agent Identities in Microsoft Entra

Agent identities are created when agents are onboarded to Agent 365 (for example Copilot Studio, Microsoft Foundry and Agent 365 SDK agents). Agent identity blueprints act as templates that new agent identities are created from.

### Task 1: Browse agent identities

1.  In the Entra admin center (https://entra.microsoft.com), go to **Entra ID \> Agents**.

![](media/image1.png)

2.  Review the list of agent identities and agent identity blueprints, their status, owners and sponsors.

> **Note** - A new lab tenant may have no agent identities yet. You can still create the policies in this lab; they apply to agent identities when they are created.

## Exercise 3: Block High-Risk Agent Identities

For agent identities the only supported condition is **Agent risk** and the only grant control is **Block access**.

### Task 1: Create the policy

1.  Go to **Entra ID \> Conditional Access \> Policies** and click **+ New policy**.

![](media/image2.png)

2.  Enter **L17-Block-High-Risk-Agents** as the **Name**.

![](media/image3.png)

3.  Under **Users or agents**, set **What does this policy apply to** to **Agents**, and select **All agent identities**.

![](media/image4.png)

4.  Under **Target resources**, select **Include \> All resources (formerly 'All cloud apps')**.

![](media/image5.png)

5.  Under **Conditions \> Agent risk (Preview)**, set **Configure** to **Yes**, select **High**, and click **Done**.

![](media/image6.png)

6.  Under **Grant**, select **Block access** and click **Select**.

![](media/image7.png)

7.  Set **Enable policy** to **Report-only** and click **Create**.

![](media/image8.png)

**Targeting all agent identities includes agents created later from any blueprint.**

## Exercise 4: Protect Agents' User Accounts and the Users Who Authorize Agents

Some agents also have a user account (agent users), and many agents act on behalf of a signed-in user. Policies that target all users don't include agent user accounts, so they need their own policy.

### Task 1: Create a policy for agent users

1.  Click **+ New policy** and name it **L17-Agent-Users-Block-Risky**.

![](media/image9.png)
![](media/image10.png)

2.  Under **Users or agents**, select **Agents** and then **All agent users**.

![](media/image11.png)

3.  Under **Target resources**, include **All resources**. Under **Conditions**, configure **Agent risk (Preview)** with **High** (if available for agent users).

![](media/image12.png)

![](media/image13.png)

4.  Under **Grant**, select **Block access**. Set **Enable policy** to **Report-only** and click **Create**.

![](media/image14.png)

![](media/image15.png)

### Task 2: Require MFA before users authorize agents

**Heads-up:** You’ve already built two Conditional Access policies step by step in this lab, so this task is a practice exercise. Create the policy on your own with the settings below, using the same steps as before. Screenshots aren’t included for this task.

Select **+ New policy** and configure:

| **Setting**          | **Value**                                                                                                             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------|
| **Name**             | **L17-MFA-For-Agent-Authorization**                                                                                   |
| **Users**            | Include **All users**. Exclude your lab administrator                                                                 |
| **Target resources** | **Select resources** **\>Select specific resources\>** **Office 365**, the resources agents access on behalf of users |
| **Grant**            | **Grant access** \> **Require multifactor authentication**                                                            |
| **Enable policy**    | **Report-only**                                                                                                       |

Select **Create**, then confirm **L17-MFA-For-Agent-Authorization** appears in the policy list with the state **Report-only**.

![](media/image16.png)

> **Note:** **Report-only** records what the policy would do without blocking anyone, which is the safe way to test a new policy before turning it on in production.

## Exercise 5: Review Conditional Access Results

**Time - 10 minutes**

### Task 1: Review sign-in logs

1.  Go to **Entra ID \> Monitoring & health \> Sign-in logs**. This is where you review sign-ins by agents and the Conditional Access policies applied to them.

![](media/image17.png)

2.  On the **Report-only** tab, review the result for **L17-Block-High-Risk-Agents** (for example **Report-only: Not applied** when the agent isn't risky).

![](media/image18.png)
> **Note** - If there are no agent sign-ins in the lab tenant yet, review the **User sign-ins** tab for the MFA policy instead.

### Task 2: Review policy impact

1.  Open **L17-MFA-For-Agent-Authorization** and select **Policy impact** (if shown) to see how many sign-ins would have been affected.

![](media/image19.png)

## Summary

In this lab you reviewed agent identities and blueprints in Microsoft Entra, created report-only Conditional Access policies that block high-risk agent identities and agent users, required MFA for users accessing the resources agents use, and reviewed the results in the sign-in logs.

## Clean up resources

1.  Leave the **L17-** policies in **Report-only**, or delete them. Don't switch them to **On** in the lab tenant.
