---
title: "Export user data from Microsoft Planner"
ms.author: sshenk
author: sshenk
audience: Admin
layout: LandingPage
ms.service: o365-administration
localization_priority: Priority
description: "IThis article describes how an Office 365 global admin can export data for a specific user from Microsoft Planner.  "
---

# Export user data from Microsoft Planner

This article describes how an Office 365 global admin can export data for a specific user from Microsoft Planner. The exported data will include data about the user contained in Planner, and also data contained in plans that the user was a part of. The exporting process is done through Windows PowerShell.

> [!NOTE]
> An Office 365 Global admin can export Microsoft Planner user telemetry data through the [Data Log Export Tool](https://go.microsoft.com/fwlink/?linkid=872273) on the [Microsoft Service Trust Portal](https://go.microsoft.com/fwlink/?linkid=872274).

## Requirements

You need to do the following first:
- You must be an Office 365 Global admin to run the PowerShell script for exporting your Planner user data.
- You need to download and unzip the [Planner user data export files](https://go.microsoft.com/fwlink/?linkid=871954). These three files are the PowerShell modules and the PowerShell script file needed to export your users data.

    > [!NOTE]
    > By downloading this package, you agree to the enclosed license and terms.

## Unblock your files

You will need to "unblock" three of the files you downloaded in the Planner User Data Export script package in order to user them in PowerShell. This is because by default, executing scripts downloaded from the Internet is not allowed. The files you need to unblock are:

- plannertenantadmin.psm1
- microsoft.identitymodel.clients.activedirectory.dll
- microsoft.identitymodel.clients.activedirectory.windowsforms.dll

Do the following to unblock these files:

1. In File Explorer, go to the location in which you unzipped your files.
2. Right-click on one of the unzipped files noted above, and click Properties.
3. On the General tab, select **Unblock**.

    ![unblock-files](media/unblock-files.png) 

4. Click **OK**.

5. Repeat these steps for the remaining two files.

## To export user content from Planner

After unblocking your files, do the following to export the user data from Planner:

1. tart Windows PowerShell. In PowerShell, type the following to enable running scripts downloaded from the internet for this session only. It may prompt you to confirm by typing "Y".

   `PS> Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Process`

2. Type the following to run the PlannerTenantAdmin PowerShell script. This will import a module with the required cmdlet to run the export.

   `PS> Import-module "<location of the .psm1 file>"`

   For example, if you file is stored in C:\AdminScript, you would type:

   `PS> Import-module "C:\AdminScript\PlannerTenantAdmin.psm1"`

3. After the script completes, use the Export-PlannerUserContent cmdlet to export your user's content from Planner.

   `PS> Export-PlannerUserContent -UserAadIdOrPrincipalName <user's AADId or UPN> -ExportDirectory <output location>`

    |Parameter|Description|
    |---|---|
    |-UserAadIdOrPrincipalName|Use either the Azure Active Directory ID or the UPN of the user for which you want to export content.|
    |-ExportDirectory|Location to store your output files. The folder should already exist.|
    |-HostName|You only need to use this parameter if you access Planner though a host name other than *task.</span>office.</span>com*. For example, if you access Planner through *tasks.</span>office365.</span>us*, include *-HostName tasks.</span>office365</span>.us* in your command.|
    
    For example, the following will export Adam Barr's user information from Planner using his UPN, and will download the export files to the location C:\PlannerExportAdamBarr.

    `PS> Export-PlannerUserContent -UserAadIdOrPrincipalName adambarr@contoso.onmicrosoft.com -ExportDirectory C:\PlannerExportAdamBarr `

4. You'll be prompted to authenticate. Log in as yourself (the Office 365 Global admin), not the user you want to export.

5. After the PowerShell cmdlet runs successfully, go to your export location to view your user's exported data files.

## What gets exported and how to read it

After running the PowerShell cmdlet to export your user's data from Planner, you will receive two types of files in your download location folder:

- A single user file in json format with information about the user.
- One json file for each plan in which the user:
    - Has a task assigned to them.
    - Has a task created by them.

## How to read your exported files

You can use the information in this section to help you understand the properties you will see in both the user and plan json files you received.

## User file

The user file name will be prefixed with "User" and the Microsoft Planner ID of the user. It will have the following properties:

|Property|Description|
|---|---|
|User.Id|Microsoft Planner ID of the user.|
|User.ExternalId|Azure Active Directory ID of the user.|
|User.DisplayName|Display name of the user.|
|User.InternalDisplayName|Microsoft Planner display name of the user.|
|User.UserPrincipalName|User Principal Name (UPN) of the user.|
|User.PrincipalType|Value is always "User".|
|User.UserDetailsId|Unique identifier of the details object for the user.|
|User.ICalendarPublishEnabled|If True, ICalendar sharing is enabled for the plan. Go to [See your Planner calendar in Outlook](https://support.office.com/en-us/article/see-your-planner-calendar-in-outlook-5dcccce5-2750-49b5-991b-1837379d96c7) for more information.|
|User.OptedInNotifications|Notifications for which the user opted in.|
|User.OptedOutNotifications|Notifications for which the user opted out.|
|User.FavoritePlans|Bookmark for plans the user has favorited.|
|User.FavoritePlans.Id|Microsoft Planner ID of the plan.|
|User.FavoritePlans.BookmarkName|Name assigned to the bookmark.|
|User.FavoritePlans.OrderHint|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|User.RecentPlans|Plans recently opened by the user.|
|User.RecentPlans.Id|Microsoft Planner ID of the plan.|
|User.RecentPlans.BookmarkName|Name assigned to the bookmark.|
|User.RecentPlans.LastAccess|When the plan was last opened.|
|User.UserData|Custom data from the Planner Web Client.|
|User.UserData.Key|Custom data key.|
|User.UserData.Value|Custom data value.|
|User.AssignedTaskOrdering|Sorting order for tasks assigned to the user.|
|User.AssignedTaskOrdering.PlanId|Microsoft Planner ID of the plan that contains the task.|
|User.AssignedTaskOrdering.Id|Microsoft Planner ID of the task.|
|User.AssignedTaskOrdering.Order|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|User.AssignedTaskOrdering.Title|The title of the task.|

## Plan files

Each plan file name will be prefixed with "Plan" and the Microsoft Planner ID of the plan. Each file will have the following properties:

|Property |Description |
|---|---|
|Plan.Id|Microsoft Planner ID of the plan. |
|Plan.Title|Title of the plan.|
|Plan.Owner|Owner of the plan (a Group or User entity).|
|Plan.Owner.Id|Microsoft Planner ID of the entity (Group or User). |
|Plan.Owner.ExternalId|Azure Active Directory ID of the entity (Group or User).|
|Plan.Owner.DisplayName|Display name of the owner (Group or User).|
|Plan.Owner.UserPrincipalName|User Principal Name (UPN) if the owner is a user.  |
|Plan.Owner.PrincipalType|The entity type (Group or User).|
|Plan.CreatedDate|Date and time the plan was created.|
|Plan.CreatedBy|User that created the plan. See User properties for more detail.|
|Plan.ModifiedDate|Date and time the plan was last updated.|
|Plan.ModifiedBy|Name of the user that last updated the plan. See User properties for more detail.|
|Plan.PlanDetailsId|Unique identifier of the plan details object. |
|Plan.ICalendarPublishEnabled|If True, ICalendar sharing is enabled for the plan. See [View your tasks on a calendar](https://support.office.com/en-us/article/use-schedule-view-to-see-tasks-on-a-calendar-8647d2c9-9bc7-466a-b5b3-74b3596fade2) for more information.|
|Plan.CreateTaskCommentWhen|Events that will cause a comment to be created for a task in the plan.|
|Plan.ReferencesToPlan|External systems that link to the plan. For example, embedding a Microsoft Planner plan in Project Online Desktop Client.|
|Plan.ReferencesToPlan.ExternalId|External System's ID for this plan.|
|Plan.ReferencesToPlan.AssociationType|The type of link to the plan, specified by the external app.|
|Plan.ReferencesToPlan.CreatedDate|Date and time the reference object was created.|
|Plan.ReferencesToPlan.OwnerAppId|ID of the app that created the reference.|
|Plan.ReferencesToPlan.DisplayNameSegments|Breadcrumbs of the location that describes what references this plan.|
|Plan.ReferencesToPlan.Url|Direct link to the app that references the plan.|
|Plan.CategoryDescriptions|The full set of categories for the plan.  There are six of these, which may or may not be in use.|
|Plan.CategoryDescriptions.Index|The index of the category description (0 through 5).|
|Plan.CategoryDescriptions.Description|The label text for the corresponding category description index value.|
|Plan.PlanFollowers|Users who follow the plan.
|Plan.TimelineId|The feature has been deprecated.|
|Plan.TimelineDisplaySettings|The feature has been deprecated.|
|Plan.TimelineLockedWidth|The feature has been deprecated.|
|Plan.Tasks|Tasks objects for the plan.|
|Plan.Tasks.Id  |Unique identifier of the task.|
|Plan.Tasks.Title|Name of the task.|
|Plan.Tasks.BucketId|The Microsoft Planner ID of the bucket the task is in.|
|Plan.Tasks.BucketName|Name of the bucket.|
|Plan.Tasks.PercentComplete|Completion status of the task, from 0 to 100. |
|Plan.Tasks.StartDate|Date the task is scheduled to start.|
|Plan.Tasks.DueDate|Date the task is scheduled to complete.|
|Plan.Tasks.ConversationThreadId|Conversation unique identifier from Microsoft Exchange.|
|Plan.Tasks.PreviewType|Preview that is displayed on the task card.|
|Plan.Tasks.OrderHint|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|Plan.Tasks.CreatedBy|User that created the task. See User properties for more detail.|
|Plan.Tasks.CreatedDate|Date the task was created.|
|Plan.Tasks.CompletedBy|User that completed the task. See User properties for more detail.|
|Plan.Tasks.CompletedDate|Date the task was completed.|
|Plan.Tasks.ModifiedBy|User that last updated the task. See User properties for more detail.|
|Plan.Tasks.ModifiedDate|Date the task was last updated.|
|Plan.Tasks.AppliedCategories |The labels selected from the CategoryDescriptions index for the plan.|
|Plan.Tasks.TaskDetailsId |Unique identifier of the details object for the task.|
|Plan.Tasks.Description|Description of the task.|
|Plan.Tasks.AssignedToTaskBoardFormatId|Unique identifier for the object that is the task board format.|
|Plan.Tasks.AssignedToTaskBoardFormatUnassignedOrderHint|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|Plan.Tasks.AssignedToTaskBoardFormatOrderHintsByAssignee|Order hint for each of the assignees.|
|Plan.Tasks.AssignedToTaskBoardFormatOrderHintsByAssignee.AssignedTo:|The user that is assigned the task. See User properties for more detail.|
|Plan.Tasks.AssignedToTaskBoardFormatOrderHintsByAssignee.Order|Ordering of the tasks specified by assignee in the Assigned To view.|
|Plan.Tasks.BucketTaskBoardFormatId|Unique identifier for the object that is the bucket task board format.|
|Plan.Tasks.BucketTaskBoardFormatOrderHint|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|Plan.Tasks.ProgressTaskBoardFormatId|Unique identifier for the object when grouped by progress instead of bucket format. |
|Plan.Tasks.ProgressTaskBoardFormatOrderHint|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|Plan.Tasks.TimelineFormatId|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatShowOnTimeline|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatAnchorPosition|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatCalloutHeight|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatColor|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatDrawingStyle|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatLabelOffsetX|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatLabelOffsetY|The feature has been deprecated.|
|Plan.Tasks.TimelineFormatSwimlane|The feature has been deprecated.|
|Plan.Tasks.References|External links.|
|Plan.Tasks.References.Url|URL of the link.|
|Plan.Tasks.References.Alias|Text description of the link.|
|Plan.Tasks.References.Type|The type of file that is being linked to.|
|Plan.Tasks.References.ModifiedBy|The user that last updated the link. See User properties for more detail.|
|Plan.Tasks.References.ModifiedDate|Date the link was last updated.|
|Plan.Tasks.References.PreviewPriority|Represents the priority of a reference to be shown as a preview on the task in the UI. Microsoft Planner only shows the highest priority item.|
|Plan.Tasks.Assignments|Task assignments.|
|Plan.Tasks.Assignments.AssignedTo|The user that task is assigned to. See User properties for more detail.|
|Plan.Tasks.Assignments.AssignedBy|The user that assigned the task. See User properties for more detail.|
|Plan.Tasks.Assignments.Order|Order of the assignments if the task is assigned to multiple entities.
|Plan.Tasks.Checklist|The checklist for the task.|
|Plan.Tasks.Checklist.Id|Unique identifier of a checklist item.|
|Plan.Tasks.Checklist.Title|Name of the checklist item.|
|Plan.Tasks.Checklist.OrderHint|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|Plan.Tasks.Checklist.IsChecked|If true, the checklist item has been completed.|
|Plan.Tasks.Checklist.ModifiedBy|The user that last updated the checklist. See [User properties](#user-properties-in-the-plans.json-file) for more detail.|
|Plan.Tasks.Checklist.ModifiedDate|Date the checklist was last updated.|
|Plan.Tasks.UserContentLastModifiedBy|The user that last updated the task or task details. See [User properties](#user-properties-in-the-plans.json-file) for more detail.|
|Plan.Tasks.UserContentLastModifiedDate|Date the task or task details was last updated.|
|Plan.Buckets  |Bucket objects for the plan.|
|Plan.Buckets.Id|Unique identifier for the bucket.|
|Plan.Buckets.Title|Name of the bucket.|
|Plan.Buckets.OrderHint|Used for sorting order. See [Using order hints in Microsoft Planner](https://go.microsoft.com/fwlink/?linkid=872173).|
|Plan.Buckets.Createdby|The user that created the bucket. See [User properties](#user-properties-in-the-plans.json-file) for more detail.|
|Plan.Buckets.CreatedDate|Date the bucket was created.|
|Plan.Buckets.ModifiedBy|The user that last updated the bucket. See [User properties](#user-properties-in-the-plans.json-file) for more detail.|
|Plan.Buckets.ModifiedDate|Date the bucket was last updated.|

### User properties in the Plans.json file

There are a number of objects in Plans.json data that represent a Microsoft Planner user and will have similar properties. These objects include:

- Plan.CreatedBy
- Plan.ModifiedBy
- Plan.PlanFollowers
- Plan.Tasks.CreatedBy
- Plan.Tasks.CompletedBy
- Plan.Tasks.ModifiedBy
- Plan.Tasks.AssignedToTaskBoardFormatOrderHintsByAssignee.AssignedTo
- Plan.Tasks.References.ModifiedBy
- Plan.Tasks.Assignments.AssignedTo
- Plan.Tasks.Assignments.AssignedBy
- Plan.Tasks.Checklists.ModifiedBy
- Plan.Bucket.Createdby
- Plan.Bucket.Modifiedby

Each of the above will have the following properties:

|Property|Description|
|---|---|
|Id|Microsoft Planner ID of the user.|
|ExternalId|Azure Active Directory ID of the user.|
|DisplayName|Display name of the user.|
|UserPrincipalName|User Principal Name (UPN) of the user.  |
|PrincipalType|The entity type (User or Group).|

## See Also

[Delete user data in Microsoft Planner](delete-user-data.md)