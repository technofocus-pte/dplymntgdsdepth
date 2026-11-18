# Lab 16: Discover and Govern AI Agents Using the Agent Registry

## Objectives

The Agent Registry in the Microsoft 365 admin center is the inventory of every agent in your organization: Microsoft agents, agents built with Agent Builder and Copilot Studio, and third-party agents. In this lab you will create a test agent, find it in the registry, review its details, assign and add owners, control who can use it, block and unblock it, and delete and restore it.

- Create a test agent with Agent Builder

- Discover agents in the registry and review their details

- Assign and manage agent owners

- Control access to an agent and block or unblock it

- Delete, restore and export agents

**Prerequisites**

A lab administrator account with the **Global Administrator** or **AI Administrator** role.

At least one **Microsoft Agent 365** licence in the tenant, and a second user to act as a new owner.

**This lab is self-contained. It does not depend on any other lab in the course; Exercise 1 creates everything the lab needs. If an object already exists in your tenant (for example because another lab created it earlier today), skip that step.**

**Agent 365 is evolving quickly. Menu names can differ slightly in your tenant.**

## Exercise 1: Prepare the Lab Environment

**Time - 20 minutes**

### Task 1: Confirm the Agent 365 licence

1.  In the Microsoft 365 admin center (https://admin.microsoft.com), go to **Billing \> Licenses**, open **Agent 365** (or **Microsoft Agent 365**), and confirm a licence is assigned to your lab administrator. If not, click **Assign licenses**, add your lab administrator, and click **Assign**.

![](media/image1.png)

> **Note** - Agent 365 features can take up to a few hours to appear after the licence is assigned. Agent 365 is evolving quickly; menu names in this lab may differ slightly in your tenant.

### Task 2: Create a second owner

1.  In the Entra admin center, create the user **l16owner** (**Display name** **L16 Agent Owner**) with a password you record.

![](media/image2.png)

![](media/image3.png)

> **Note** - If another lab already created a user you can use as an owner, you can reuse it.

### Task 3: Create a test agent

1.  Open a new InPrivate window, browse to https://m365.cloud.microsoft/chat and sign in with your lab administrator account.

![](media/image4.png)

2.  In the left pane select **Agents \> New agent**. ![](media/image5.png)

3.  Click **Skip**

![](media/image6.png)

4.  On the **Configure** tab, enter **L16 IT Helpdesk Agent** as the **Name**, **Answers questions about company IT policies** as the **Description**, and a short instruction such as **Answer IT policy questions politely and say when you do not know.**

![](media/image7.png)
![](media/image8.png)

5.  Click **Create**. When the agent is created.

![](media/image9.png)
![](media/image10.png)

6.  Select **Share**. In **Share “L16 IT Helpdesk Agent”**, turn on **Org-wide sharing for chat access**.

![](media/image11.png)
![](media/image12.png)
> **Note** - If **New agent** is not available, Agent Builder is turned off or not licensed in your tenant. Use any agent already listed in **Microsoft 365 admin center \> Agents \> All agents** for the rest of the lab.

7.  In the Microsoft 365 admin center (https://admin.microsoft.com), go to **Agents \> All agents** and confirm **L16 IT Helpdesk Agent** appears in the **Registry** view.

![](media/image13.png)

> **Note** - New agents can take up to 30 minutes to appear in the admin center.

## Exercise 2: Discover Agents in the Registry

**Time - 15 minutes**

### Task 1: Browse the registry

1.  In the Microsoft 365 admin center, go to **Agents \> All agents** and select the **Registry** view.

![](media/image14.png)

2.  Use the **Platform** filter to show **Agent Builder in Microsoft 365 Copilot** agents, then the **Status** filter to show **Available** agents. Note how Microsoft agents, Agent Builder agents and Copilot Studio agents are listed.

![](media/image15.png)

3.  Click **Export to Excel** (shown as **Export** in some tenants) and open the export to see the full inventory.

![](media/image16.png)

![](media/image17.png)

### Task 2: Review an agent's details

1.  Select **L16 IT Helpdesk Agent**. Review the **Details** tab: description, platform, publisher, **Created by**, **Owner**, creation date and capabilities.

![](media/image18.png)

2.  Review the other tabs, such as permissions, data sources, usage and risks, if shown.

## Exercise 3: Govern Agent Ownership

Every agent should have an accountable owner. Ownerless agents appear as a governance gap on the Agents overview.

### Task 1: Add an owner

1.  On **L16 IT Helpdesk Agent**, select the **Details** tab and click the user link next to **Owner**.

![](media/image19.png)

2.  In the **Owners** pane, click **Add owner**, search for **L16 Agent Owner**, and click **Add**.

![](media/image20.png)

![](media/image21.png)

![](media/image22.png)

**Note** - Owners have equal rights. Groups can't be owners.

### Task 2: Assign a new owner

1.  Select the agent and click **Assign new owner**, enter **L16 Agent Owner**, and click **Assign**.

![](media/image23.png)

![](media/image24.png)

**The new owner gets full edit and delete rights and previous owners lose access. Use this when an owner leaves the company.**

2.  Confirm **Owner** now shows **L16 Agent Owner** only.

![](media/image25.png)

![](media/image26.png)

## Exercise 4: Control Access and Lifecycle

### Task 1: Block and unblock the agent

1.  Select **L16 IT Helpdesk Agent** and click **Block**. In the **Block agent** pane, click on the checkbox to block agent and then **Confirm**.

![](media/image27.png)

![](media/image28.png)

2.  In the user InPrivate window from Exercise 1, refresh https://m365.cloud.microsoft/chat and confirm the agent is no longer available.

**Note** - Blocking can take a few minutes to take effect.

3.  Back in the admin center, select the agent, click **Unblock**, and click **Confirm**.

### Task 2: Delete and restore the agent

1.  Select the agent, click **Delete agent**, and click **Delete**.

![](media/image29.png)

> **Note** - This is a soft delete. Deleted agents can be restored for 30 days and the action is recorded in the audit log.

2.  Select the **Deleted** view, select **L16 IT Helpdesk Agent**, click **Restore agent**, and click **Restore**. Confirm the agent is back in the **Registry**.

![](media/image30.png)
![](media/image31.png)

### Task 3: Review requests

1.  Select the **Requests** tab. Review any agents users have requested, and the **Allow activation** status for agents that need admin approval.

![](media/image32.png)

## Summary

In this lab you created a test agent, found and exported agents in the Agent Registry, reviewed agent details, added and reassigned owners, blocked and unblocked the agent, deleted and restored it, and reviewed agent requests.

## Clean up resources

1.  Delete **L16 IT Helpdesk Agent** and the user **l16owner** if they aren't needed by other labs today.
