# Lab 18: Extend Purview DSPM, DLP, and Audit Controls to AI Agents

## Objectives

Microsoft Purview treats Agent 365 agents much like users: their interactions are audited automatically, sensitive data in prompts and responses is classified, and agent instances can be added to DLP, insider risk and retention policies. In this lab you will review agents in DSPM AI observability, search the audit log for agent activity, add agents to a DLP policy, configure a sensitivity label that agents can use, and review AI activity.

- Prepare auditing and a test agent

- Review agents and their risks in DSPM AI observability

- Search the audit log for Agent 365 activities

- Add agents to a DLP policy

- Configure a sensitivity label that grants agents the usage rights they need

**Prerequisites**

A lab administrator account with the **Global Administrator** or **Compliance Administrator** role.

Microsoft 365 Business Premium plus the Purview Suite (or Microsoft 365 E5), and a **Microsoft Agent 365** licence.

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

**Agent data in Purview appears only after agents have been used. In a new lab tenant some pages may be empty; focus on where each control is and how it's configured.**

## Exercise 1: Prepare the Lab Environment

**Time - 20 minutes**

### Task 1: Turn on auditing

1. Open the Microsoft Purview portal at https://purview.microsoft.com, select **Solutions > Audit**. If a banner prompts you to **Start recording user and admin activity**, click it. If no banner appears, auditing is already on.

> **Note** - Auditing can take up to 60 minutes to start. Continue with the lab; later steps that need audit data say so.

### Task 2: Confirm the Agent 365 licence

1. In the Microsoft 365 admin center (https://admin.microsoft.com), go to **Billing > Licenses**, open **Agent 365** (or **Microsoft Agent 365**), and confirm a licence is assigned to your lab administrator. If not, click **Assign licenses**, add your lab administrator, and click **Assign**.

> **Note** - Agent 365 features can take up to a few hours to appear after the licence is assigned. Agent 365 is evolving quickly; menu names in this lab may differ slightly in your tenant.

### Task 3: Create and use a test agent

1. Open a new InPrivate window, browse to https://m365.cloud.microsoft/chat and sign in with your lab administrator account.

![](images/image1.png)

2. In the left pane select **Agents > New agent** (shown as **Create agent** in some tenants).

![](images/image2.png)

![](images/image3.png)

3. On the **Configure** tab, enter **L18 Policy Agent** as the **Name**, **Answers questions about company IT policies** as the **Description**, and a short instruction such as **Answer IT policy questions politely and say when you do not know.** Click **Create**.

![](images/image4.png)

4. When the agent is created, select **Share** and share it with **Anyone in your organization** (or a test user), then click **Save**.

![](images/image5.png)

![](images/image6.png)

> **Note** - If **New agent** is not available, Agent Builder is turned off or not licensed in your tenant. Use any agent already listed in **Microsoft 365 admin center > Agents > All agents** for the rest of the lab, or follow the instructor demonstration.

5. In the Microsoft 365 admin center (https://admin.microsoft.com), go to **Agents > All agents** and confirm **L18 Policy Agent** appears in the **Registry** view.

![](images/image7.png)

> **Note** - New agents can take up to 30 minutes to appear in the admin center.

6. In Copilot Chat, open **L18 Policy Agent** and ask two or three questions, such as **What is a strong password?**, so that the agent generates activity.

![](images/image8.png)

![](images/image9.png)

### Task 4: Create a group for agents

DLP and other Purview policies can target agents directly or through a security group that contains agent instances.

1. In the Entra admin center, create a **Security** group named **SG-L18-Agents** with **Membership type** **Assigned**. Add any agent instances or agent users listed under **Entra ID > Agents** as members.

![](images/image10.png)

> **Note** - If your tenant has no agent identities yet, create the group empty. Members can be added later.

## Exercise 2: Review Agents in DSPM

Use the current **DSPM** experience. The classic DSPM for AI doesn't support Agent 365.

### Task 1: Review AI observability

1. In the Purview portal, go to **Solutions > DSPM > AI observability**.

![](images/image11.png)

2. Review the agents with activity, their risk levels, and the top risky activities (oversharing, exfiltration, unethical behavior).

![](images/image12.png)

3. Select an agent and review the **Details** (Entra-enabled status, owner, agent user ID), **Activities** and **Recommendations** tabs.

![](images/image13.png)

### Task 2: Review AI activities

1. Go to **DSPM > Discover > Activity explorer** and select the **AI activities** tab. Review prompts, responses and any DLP matches for agent interactions.

![](images/image14.png)

> **Note** - Activity can take up to a day to appear in DSPM.

## Exercise 3: Search the Audit Log for Agent Activity

All agent-to-human, human-to-agent, agent-to-tool and agent-to-agent interactions of Agent 365 agents are audited automatically.

### Task 1: Run an audit search

1. In the Purview portal, go to **Solutions > Audit** and select **Search**.

![](images/image15.png)

2. Set the date range to the last 7 days. In **Activities - friendly names**, search for **Agent** and select the Agent 365 and Copilot interaction activities listed. Click **Search**.

![](images/image16.png)

3. When the search completes, open it and review entries for **L18 Policy Agent**, including who interacted with the agent and which resources it accessed.

> **Note** - Audit searches can take several minutes. Interactions can take up to 60-90 minutes to become searchable.

## Exercise 4: Add Agents to a DLP Policy

DLP can block or audit agent-to-human and human-to-agent interactions in Teams, SharePoint, OneDrive and email. Agents aren't aware of block actions, so agent owners must monitor DLP results.

### Task 1: Create the policy

1. Go to **Solutions > Data Loss Prevention > Policies** and click **+ Create policy**.

![](images/image17.png)

2. Choose **Custom > Custom policy**, enter **L18-DLP-Agents**, and click **Next**. Keep **Full directory** and click **Next**.

![](images/image18.png)

![](images/image19.png)

3. Select **Teams chat and channel messages**, **Exchange email**, **SharePoint sites** and **OneDrive accounts**.

4. For **Teams** and **Exchange**, click **Edit** under **Included** and select the group **SG-L18-Agents** (or specific agent instances). Click **Next**.

![](images/image20.png)

![](images/image21.png)

![](images/image22.png)

5. Click **Next** on the **Define policy settings** page.

![](images/image23.png)

6. Create a rule **Agent shares card data** with the condition **Content contains > Sensitive info types > Credit Card Number** and the action **Restrict access or encrypt the content in Microsoft 365 locations > Block everyone**. Turn on **Incident reports**. Click **Save**, then **Next**.

![](images/image24.png)

![](images/image25.png)

![](images/image26.png)

![](images/image27.png)

7. Select **Run the policy in simulation mode**, click **Next**, then **Submit**.

![](images/image28.png)

![](images/image29.png)

## Exercise 5: Configure a Label That Agents Can Use

**Time - 10 minutes**

Encrypted files are available to an agent only if the file is shared with the agent and the label's encryption explicitly grants it the **VIEW** and **EXTRACT** usage rights. Settings like **Add all users and groups in your organization** aren't enough.

### Task 1: Create the label

1. Go to **Information Protection > Sensitivity labels** and click **+ Create a label**.

![](images/image30.png)

2. Enter name and display name as **L18-Confidential-Agents**, give the following description **Confidential business data that only employees and approved AI agents can open**, and click **Next**.

![](images/image31.png)

3. Select the **Files & other data assets** and **Emails** scopes.

![](images/image32.png)

4. Select **Apply or remove encryption**, choose **Assign permissions now**, and click **Assign permissions**.

![](images/image33.png)

5. On **Access control**, select **Configure access control settings**, then set:

    - Assign permissions now or let users decide?: **Assign permissions now**

    - User access to content expires: **Never**

    - Allow offline access: **Always**

6. Scroll down to **Assign permissions to specific users and groups** and select **Assign permissions**.

![](images/image34.png)

7. Add **All users and groups in your organization** with **Editor**, click **Save**, and click **Next**.

![](images/image35.png)

8. Click **Next** on **Auto-labeling for files and emails**.

9. Click **Next** on **Define protection settings for groups and sites**.

10. Finish the wizard and click **Create label**, then **Don't create a policy yet**.

![](images/image36.png)

![](images/image37.png)

![](images/image38.png)

> **Note** - Publish the label with a label policy (as in Lab 12) before users can apply it.

## Summary

In this lab you reviewed agents and their risks in DSPM AI observability and Activity explorer, searched the audit log for agent activity, added agents to a DLP policy, and created a sensitivity label whose encryption grants agents the usage rights they need.

## Clean up resources

1. Turn off or delete **L18-DLP-Agents**, delete the test agent and **SG-L18-Agents** if they aren't needed by other labs today.
