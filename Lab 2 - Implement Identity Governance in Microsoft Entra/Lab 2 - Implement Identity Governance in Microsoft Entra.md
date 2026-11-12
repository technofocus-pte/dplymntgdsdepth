# **Lab 2: Implement Identity Governance in Microsoft Entra**

## Exercise 1: Privileged Identity Management (PIM)

### Task 1: Remove standing Administrator Access 

In this task we will convert all privileged users from Active to Eligible assignments. Standing admin access is an Identity risk.

1.  On the entra admin center, Expand ID Governance and select Privileged Identity Management.

![](media/image1.png)

2.  On Privileged Identity Management page, navigate to **Manage** section and click on **Microsoft Entra roles**

![](media/image2.png)

3.  Select Assignments under Manage.

![](media/image3.png)

4.  On the Assignments page, under the Active assignments tab select Alex Morgan and click **Update.**

![](media/image4.png)

5.  On the Membership settings side page for Assignment type select Eligible and click save.

![](media/image5.png)

![](media/image6.png)

### Task 2: Configure Microsoft Entra role settings 

Provide just-in-time and time-bound access for Microsoft Entra roles, including options for activation approvals

1.  Under ID Governance select Privileged Identity Management and go to Microsoft Entra roles.

![](media/image7.png)

2.  Under manage select **Roles.**

**  
**![](media/image8.png)

3.  Select Security Administrator.

![](media/image9.png)

4.  Click on **Role settings** and select **Edit**

![](media/image10.png)

5.  Bjhgjg

![](media/image11.png)

6.  Select the checkbox beside Require Azure Multi-Factor Authentication on active assignment and click **Next**

![](media/image12.png)

7.  Notifications recipients are default to Admin, Assignee and Approver. You can make updates as needed in a real‑time scenario. Now, click on the Update button at the bottom to save the configuration.

![](media/image13.png)

### Task 3: Create Assignments for Microsoft Entra roles 

In this lab we Provide just-in-time and time-bound access for Microsoft Entra roles, including options for activation approvals

1.  In Microsoft Entra admin centre portal, navigate and click on **ID Governance.**

2.  Under ID Governance go to Privileged Identity Management and select **Microsoft Entra roles.**

![](media/image14.png)

3.  Select Assignments  
    ![](media/image15.png)

4.  On the assignments page select **+ Add Assignments**

![](media/image16.png)

5.  Click on Select role field you want to assign and select the User you want to assign to the role, then select Next  
      
    ![](media/image17.png)

6.  Ensure that the radio button beside **Eligible** is selected, then click **Assign**

![](media/image18.png)

![](media/image19.png)

![](media/image20.png)

### Task 4: Discover PIM for Groups 

PIM for Groups lets SMBs and MSPs grant time‑limited admin access via group activation with MFA and approval, reducing standing privileges while maintaining operational flexibility.

1.  In Privileged Identity Management page, click on **Groups**

![](media/image21.png)

2.  Select a group and click on **Manage groups.**

![](media/image22.png)

**Note**: if the group does not exist in the tenant, create a security group on Entra admin center to be managed here.

3.  Click **Ok** to confirm the onboarding of the group.

![](media/image23.png)

![](media/image24.png)

### Task 5: Configure PIM for Group Role settings 

Provide just-in-time and time-bound access for Microsoft Entra roles, including options for activation approvals

1.  In Microsoft Entra admin center portal, navigate and click on ID Governance

![](media/image25.png)

2.  Go to **Privileged Identity Management**, go to Groups and select **sg-Executive.**

![](media/image26.png)

3.  Select **Settings.**

![](media/image27.png)

4.  Click on the Owner role.

![](media/image28.png)

5.  On Role setting details - Owner page, click on the **Edit** button

![](media/image29.png)

6.  Click on the radio button for **Azure MFA**. Then, select the checkbox beside** Require approval to activate.**

7.  Select **Approvers** and click **Next: Assignments**

![](media/image30.png)

8.  On the Assignments pane click on the following **checkboxes** and select **Next: Notifications**  
      
    ![](media/image31.png)

9.  Notifications recipients default to Admin, Assignee and Approver. Update if necessary in real-time scenario. Now, click on the Update button at the bottom of the page

![](media/image32.png)

### Task 6: Assign eligibility for a group 

PIM for Groups lets SMBs and MSPs grant time‑limited admin access via group activation with MFA and approval, reducing standing privileges while maintaining operational flexibility.

1.  In this task we will create agroup sg-Retail in Microsoft Entra admin center.

![](media/image33.png)

2.  In Microsoft Entra admin center portal, navigate and click on **ID Governance.**

3.  In the **Privileged Identity Management** page, click on **Groups**

![](media/image34.png)

4.  Select sg-Retail

![](media/image35.png)

5.  Go to Assignments tab and select + add assignments.

![](media/image36.png)

7.  Select role as Owner and select Admin as the member then click Next.

![](media/image37.png)

8.  Ensure the assignment type is set to **Eligible** and click **Assign**.

![](media/image38.png)

![](media/image39.png)

### Task 7: Configure security alerts for PIM roles

PIM alerts you when risky admin activity happens so you can quickly see who or what needs attention. Severity has the following meaning:

- High: Requires immediate action because of a policy violation.

- Medium: Doesn't require immediate action but signals a potential policy violation.

- Low: Doesn't require immediate action but suggests a preferable policy change.

1.  On the privileged identity management page, go to Microsoft Entra roles.

![](media/image40.png)

2.  On the Entra roles page, go to **Alerts.**

![](media/image41.png)

3.  From the top navigation bar, click **Settings.**

![](media/image42.png)

4.  In the Alert setting page, navigate and click on **There are too many global administrator**

![](media/image43.png)

5.  On the Edit alert settings pane make the following changes and click **save**.

Number of Global Administrators set it to **3**  
and percentage of Global Administrators to trigger an alert set it to **2**.

![](media/image44.png)

![](media/image45.png)

## Exercise 2: Access Reviews

### Task 1: Create and Enable Access Review of groups 

Access Reviews help organizations regularly confirm that only the right users—employees, partners, or guests—keep access to apps, groups, and roles, reducing security risk and meeting compliance needs. The steps below outline a general process for creating an access review and can be adapted to your org’s needs.

1.  In this task we will need to have finance team, Create a group by name Finance team in the Microsoft Entra admin center.

![](media/image46.png)

2.  Under ID Governance select **Access reviews.**

![](media/image47.png)

3.  Select New access review to create a new access review.

![](media/image48.png)

4.  On the Access reviews template screen, select Review access to a resource type

![](media/image49.png)

5.  Navigate and click on **Select Review** field, click on the radio to select **Teams+ groups  
    **![](media/image50.png)

6.  For groups click + Groups and select Finance teams  
    ![](media/image51.png)

7.  Click on the radio button beside **All users**

![](media/image52.png)

8.  Click **Next:** **Reviews.**

![](media/image53.png)

9.  Click on the field beside **Select reviewers,** Choose who approves access. Here we select **Group owners.**

![](media/image54.png)

10. Click on **Select fallback reviewers** link and select **admin**.

![](media/image55.png)

11. On the review recurrence select **quarterly.**

![](media/image56.png)

12. Enter an end date, end on specific date and click **Next: Settings**

![](media/image57.png)

13. Select the checkbox beside **Auto apply results to resource**

14. Click on the field beside **If reviewers don't respond** select **Remove access.**

15. Click **Next: Review + Create.**

![](media/image58.png)

16. In the **Review name** field, enter the following details and click Create.  
      
    Name: **Periodic Access Review – Identity Governance**.  
    Description:** This access review is conducted to comply with organizational identity governance and audit requirements. Reviewers confirm whether each user still needs access to the selected group.   
    **

![](media/image59.png)
>
![](media/image60.png)

### Task 2: Enable Access Review of Apps

Access Reviews help organizations regularly confirm that only the right users—employees, partners, or guests—keep access to apps, groups, and roles, reducing security risk and meeting compliance needs. The steps provided in this demo are general process for creating an access review and can be adapted to your org’s needs.

1.  In Microsoft Entra admin center, expand **Entra ID.** Select **Enterprise apps**, then **All applications** \> **+ New applications**.

![](media/image61.png)

2.  Search and select Linkedin

![](media/image62.png)

3.  Click create on the side pane.

![](media/image63.png)

![](media/image64.png)

4.  Similarly, create the box and browserstack applications

![](media/image65.png)

5.  In Microsoft Entra admin center portal, navigate and click on **ID Governance**

6.  Then, select Access reviews

![](media/image66.png)

7.  Now, click on **New access review**

![](media/image67.png)

8.  On **Review access to a resource type **tile, click on **Select**

![](media/image68.png)

9.  Click on Select Review field and select Applications.

![](media/image69.png)

10. Select the checkboxes beside **Box, BrowserStack** and **Linkedin** and click **Select.**

![](media/image70.png)

11. Select the radio button beside All users, and click on Next: Reviews button

![](media/image71.png)

12. For Select reviewers pick **Selected user(s) or group(s).**

![](media/image72.png)

13. Now, click on Select reviewers, in the Select reviewers search bar, select and enter the admin user.

![](media/image73.png)

14. Click on the field beside Review recurrence select Monthly.

![](media/image74.png)

15. In the End date field, select a date 2 weeks from the current date and click Next: Settings.

![](media/image75.png)

16. Select the checkbox beside **Auto apply results to resource** and for, If reviewer don't respond select **Remove access from the list** and click on **Next: Review + Create** button

![](media/image76.png)

17. In the Name field, enter Security Review – **Application Access Validation**, and in the Description field, enter **This access review ensures that only authorized users retain access to enterprise applications. The review helps minimize security risk caused by stale or excessive application permissions.** And click Create.

![](media/image77.png)

![](media/image78.png)

## Exercise 3: Entitlement Management

### Task 1: Create an Access Package

> Access Packages replace manual access requests with simple, approved, time‑limited access—especially for contractors and partners. Microsoft defines an access package as a bundle of resources (groups, apps, sites) governed by policies that control who can request access, who approves it, and how long it lasts.

1.  Navigate to Microsoft Entra admin center, under Entra ID \> groups create a new group sg-sales and marketing to be used in this task.

![](media/image79.png)

2.  On the left navigation pane navigate and click on ID Governance, select Entitlement management.

![](media/image80.png)

3.  Under Entitlement management go to **Access packages**

![](media/image81.png)

4.  Click + **New access package,** to create a new access package.

![](media/image82.png)

5.  Enter the following details and click **Next: Resource roles \>**

Name: **New Employee Access Automatically package**

Description: **Assign Employee access automatically.**

**  
**![](media/image83.png)

4.  In the New access package page, click on Groups and Teams and select** sg-Sales and Marketing group.**

![](media/image84.png)

5.  Now, click on the Applications tab, select **box** and click Select.

![](media/image85.png)

6.  Click on **SharePoint sites, s**elect the checkbox beside See all SharePoint Site(s) not in the General catalog and select **Communication site** from the list.

![](media/image86.png)

7.  Select the following for the roles:

&nbsp;

1.  **sg-Sales and Marketing** and select Member.

2.  **Box** select **User** from the list.

3.  **Communication site,** Select **Communication site Members** from the list.

![](media/image87.png)

8.  Click on **Next: Requests **button

9.  In **Who can get access** section, select the radio button beside **For users, service principals, and agent identities in your directory**

![](media/image88.png)

10. In Select specific scope section, select the radio button beside **All users (including agents)**

![](media/image89.png)

11. Now, click on **Add fallback **link

![](media/image90.png)

12. In the Fallback pane search and select admin user **ODL_User.**

![](media/image91.png)

13. Now, click on **Next: Requestor Information **button.

![](media/image92.png)

14. In the Enter question field, enter the following question, **Why do you need access to XX resource**

15. Click on the **Answer format** field Select **Short text** from the list and click on **Next: Lifecycle** button.

![](media/image93.png)

16. Select the checkbox beside **Require access reviews** and then click on **Show advanced access review settings** link.

![](media/image94.png)

17. Review the details, then click on the **Next: Rules** button

![](media/image95.png)

18. Click on **Next: Review + create** button

![](media/image96.png)

19. Now, click on the **Create** button.

![](media/image97.png)

![](media/image98.png)

## Summary:

In this lab, you successfully implemented the three core pillars of identity governance in Microsoft Entra.

In **Exercise 1**, you completed the configuration of Privileged Identity Management (PIM) by removing standing admin access and replacing it with just-in-time role assignments. You configured role settings, created eligible assignments for Entra roles, extended PIM to groups, set group role settings, assigned group eligibility, and set up security alerts to detect risky privileged activity.

In **Exercise 2**, you enabled and configured Access Reviews to continuously validate who has access — completing reviews for both groups and applications to ensure stale or unnecessary access is regularly identified and revoked.

In **Exercise 3**, you successfully created an Access Package using Entitlement Management, establishing a self-service, policy-governed workflow for users to request bundled resource access with built-in approvals and expiration. By completing this lab, you have established a least-privilege, auditable access model across roles, groups, and applications within the tenant.
