Developed with the following technologies by [@tarikguney](https://github.com/tarikguney)

![](./assets/logo/azure-functions.png) ![](./assets/logo/dotnet-core-logo.png)

![](./assets/manager-automation-hq-banner.png)

Table of Contents
=================

<!--ts-->
   * [Table of Contents](#table-of-contents)
   * [Manager Automation](#manager-automation)
      * [What is it?](#what-is-it)
      * [Underlying Design Principle](#underlying-design-principle)
      * [How does it look?](#how-does-it-look)
         * [In-Sprint action reminders](#in-sprint-action-reminders)
         * [End-of-sprint action reminders](#end-of-sprint-action-reminders)
         * [New Sprint action reminders](#new-sprint-action-reminders)
         * [Managers-only reminders](#managers-only-reminders)
      * [Reporting](#reporting)
      * [The Complete List of Reminders](#the-complete-list-of-reminders)
         * [Current Iteration Automation](#current-iteration-automation)
         * [End of the Sprint Reminders](#end-of-the-sprint-reminders)
         * [Retrospective Automation](#retrospective-automation)
         * [Managers-only Automation](#managers-only-automation)
      * [Setup and Configuration](#setup-and-configuration)
         * [Dependencies](#dependencies)
         * [Publishing to Azure Functions](#publishing-to-azure-functions)
         * [App Settings](#app-settings)
            * [Finding Google Chat Id (UserId)](#finding-google-chat-id-userid)
            * [Where to put the app settings?](#where-to-put-the-app-settings)

<!-- Added by: tarikguney, at: Sat Dec 26 20:57:49 MST 2020 -->

<!--te-->

# Manager Automation

## What is it?
**As an engineering manager, you realize that a lot of your time goes to repetitive tasks which also include guiding your team in keeping the sprint board up-to-date with enough level of details for an accurate work tracking.** There could be many reasons why you find yourself in this situation, but regardless of the reason, Manager Automation will take some of that burden off your shoulders. It will continuously analyze the current Sprint's board. It will remind the team of the missing information in the form of actionable reminders that are important for the smooth execution of every Sprint. **Note that these reminders play an essential role in guiding the team and new hires towards the expectations of the team and company culture.**

## Underlying Design Principle
The communication of the reminders in the solution is intentionally done in a public team chat room. Besides being public, the messages are kept straightforward too. It communicates the required actions through Google Chat (and Slack, etc., in the future) in simple and concise words. It is visible and repetitive, as these are perhaps some of the most effective factors to incorporate cultural elements into a team's daily life. Rare communication of hard-to-remember tasks will not make it into the team culture early enough. They have to be communicated frequently in a place where they are most visible to make them easy to follow by the team to become a part of the team culture.

## How does it look?

There are some screenshots of the messages below for you to see what's being communicated. Note that the source code will be the best place to check for the full list of messages and communication for now.

### In-Sprint action reminders
Manager Automation service sends daily reminders as demonstrated in the screenshots below. These series of reminders ensure the necessary actions being taken by the team members. The reminders use Google Chat's name mentioning feature to help the targeted team members to get on-time and relevant notifications through the Google Chat.

<img src="./assets/screenshots/in-sprint-alt3.png" width="800">
<img src="./assets/screenshots/in-sprint-alt2.png" width="800">
<img src="./assets/screenshots/in-sprint-alt1.png" width="800">

### End-of-sprint action reminders
Manager Automation service embeds specific situational facts in the reminder text. For example, it changes its greeting when it is the last day of the Sprint to draw people's attention to the fact that the Sprint is about to end, and there might be some missing work in the Sprint. It also guides the team members on how the incomplete work must be handled based on the time available and committed time for the work item estimated by the engineer.

<img src="./assets/screenshots/last-day-sprint-reminders.png" width="800">

### New Sprint action reminders
When it is the first day of the new Sprint, the automation service will analyze the previous Sprint one more time and bring up any issues that are left unresolved.

<img src="./assets/screenshots/new-sprint-reminders.png" width="800">

### Managers-only reminders
As an engineering manager, it is often crucial to be aware of the work's progression on time and catch the delay before becoming a big problem. There might be many reasons why work is delayed. For instance, engineering might be blocked. Regardless of the reason, if a task is taking an unreasonable amount of time, you will need to reach out and see what's going on and how you can help the team member with the progression of the work. The Manager Automation service collects this information for you -- the engineering manager -- and reports them daily in a private Google Chat room.

<img src="./assets/screenshots/managers-only-reminders.png" width="800">

## Reporting

Reporting is one of the most important capabilities of the Manager Automation tool. The daily reminders are also sent to the [**Azure Application Insights**](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) for a further review by the team and the engineering manager. You can write up reports using these logs to better understand the team's performance and improvement over time. It is up to you when it comes to how to use these reporting capabilities, but a recommended approach is to help the team grow by helping them see what areas they need to work to improve. For instance, if they are not good at estimating the work items, they can objectively see this problem area in the reports and work to improve it over a course of time.

The reporting is a fairly new addition to the manager automation, hence, the documentation lacks enough level of details and screenshots. However, it is simple to use when you integrate the Application Insights to your Azure Functions application. Ensure that you select an Application Insights instance (or create a new one) when you first create your function application.

These are the logs you will see in the Application Insights logs:

```
BOARD: Missing description for \"{workItemId}:{workItemTitle}\". Assigned to {userEmail} in {currentIteration}.

BOARD: Unclear title for \"{workItemId}:{workItemTitle}\". Assigned to {userEmail} in {currentIteration}.

BOARD: Missing story point for \"{workItemId}:{workItemTitle}\". Assigned to {userEmail} in {currentIteration}.

BOARD: Closed everything from the previous sprint by the first day of the current sprint {currentIteration}. Assigned to {userEmail}.

BOARD: Closed everything in the current sprint {currentIteration}. Assigned to {userEmail}.

BOARD: Pending in incomplete state of {currentState} for {pendingForDays} days. Story \"{workItemId}:{workItemTitle}\". Assigned to {userEmail} in {currentIteration}.

BOARD: Still open in {currentState} state. Story \"{workItemId}:{workItemTitle}\". Assigned to {userEmail} in {currentIteration}."

BOARD: Still in active state. Story \"{workItemId}:{workItemTitle}\". Assigned to {userEmail} in {currentIteration}.
```
Note that all these logs start with `BOARD` to help you distinguish these log entries from the other sort of logs easily.

The log messages above are taken directly from the code. As you might have guessed, they follow [**structural logging**](https://softwareengineering.stackexchange.com/a/312586/3613) pattern; meaning that they can easily be parsed and parameterized with the metadata they carry for each fields as represented by `{ }` as seen in `{workItemId}`.

Samples from console environment:
```
[2020-12-26T21:36:32.640Z] BOARD: Missing story point for "12:Create the profile edit page". Assigned to atarikguney@gmail.com in Sprint 3.
[2020-12-26T21:36:32.643Z] BOARD: Pending in incomplete state of Resolved for 28 days. Story "19:This is an active item". Assigned to atarikguney@gmail.com in Sprint 3.
```

More documentation and examples will follow as this is one of the most critical functionalities of Manager Automation tool.

## The Complete List of Reminders
This function application consists of three primary functions:
1. Current Iteration Automation
1. End of iteration Automation
1. Retrospective Automation
1. Managers-only Reminders

### Current Iteration Automation
Most of the reminders go out as part of the current iteration automation executions. This automation analyses the current iteration (aka. Sprint) using Azure DevOps API from various points and sends reminders to the responsible parties to make sure they are addressed. These checkpoints are:
- Missing description in work items (defects and user stories)
- Indescriptive work item titles
- Work items without story points
- Work items in Resolve state for more than 12 hours
- Assigned but not activated work items. Reminds the responsible parties to activate the work item they are working.
- Congratulate the ones with all closed work items.

These reminders are sent out twice every day, one in the morning and one in the afternoon.

### End of the Sprint Reminders
On the last day of the Sprint, the reminders change. In addition to the ones above, it also automates:

- If there are work items that are still active, it reminds the possible action items.

### Retrospective Automation
On the first day of the Sprint, this automation checks if there is any remaining work from the previous Sprint. It runs the existing checks and reports them differently, which is more appropriate for the first day of the next Sprint.

### Managers-only Automation

- Due date reminders.

## Setup and Configuration
### Dependencies
This project is developed for **[Azure Functions](https://azure.microsoft.com/en-us/services/functions) with [**.NET Core 3.1**](https://dotnet.microsoft.com/)**. Hence, some basic knowledge in this technology stack is helpful. Also, this service assumes that you are using **[Azure DevOps](https://dev.azure.com)** as the project planning/tracking environment and **[Google Chat](https://chat.google.com)** as the communication medium among your team members.
### Publishing to Azure Functions
Deploying this source code to the Azure Function is easy. This project uses the Library version of Azure Functions. Check out these steps to learn how to publish Azure Functions: [Publish to Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs#publish-to-azure).

Once you deploy this function app to Azure Functions, these are the functions that will appear on the Azure Portal:

<img src="./assets/screenshots/functions.png" width="600">

### App Settings

To complete app settings, you need various pieces of information from Azure DevOps and Google Chat.

In addition to the default app settings that come with the Azure Function creation, there are some custom settings that the source code depends on as follows. All of them are required!

```json
[
  {
    "name": "AzureDevOps__ApiKey",
    "value": "PERSONAL-ACCESS-TOKEN-FROM-AZURE-DEVOPS",
    "slotSetting": false
  },
  {
    "name": "AzureDevOps__Organization",
    "value": "ORGANIZATION-NAME-OF-AZURE-DEVOPS",
    "slotSetting": false
  },
  {
    "name": "AzureDevOps__Project",
    "value": "PROJECT-NAME-FROM-AZURE-DEVOPS",
    "slotSetting": false
  },
  {
    "name": "AzureDevOps__Team",
    "value": "TEAM-NAME-FROM-AZURE-DEVOPS",
    "slotSetting": false
  },
  {
    "name": "AzureDevOpsUsersMapToGoogleChat",
    "value": "AZURE-DEVOPS-USER1-EMAIL:GOOGLE-CHAT-ID-1;AZURE-DEVOPS-USER2-EMAIL:GOOGLE-CHAT-ID-2",
    "slotSetting": false
  },
  {
    "name": "EngineeringManagerInfo__AzureDevOpsEmail",
    "value": "ENGINEERING-MANAGER-EMAIL-FROM-AZURE-DEVOPS",
    "slotSetting": false
  },
  {
    "name": "EngineeringManagerInfo__GoogleChatUserId",
    "value": "ENGINEERING-MANAGER-GOOGLE-CHAT-ID",
    "slotSetting": false
  },
  {
    "name": "EngineeringManagerInfo__ManagerRemindersGoogleWebhookUrl",
    "value": "MANAGERS-ONLY-REMINDERS-ROOM-WEBHOOK-URL",
    "slotSetting": false
  },
  {
    "name": "GoogleChat__WebhookUrl",
    "value": "GOOGLE-CHAT-ROOM-WEBHOOK-URL",
    "slotSetting": false
  },
  {
    "name": "WEBSITE_TIME_ZONE",
    "value": "Mountain Standard Time",
    "slotSetting": false
  }
]
```

#### Finding Google Chat Id (UserId)

Google Chat Id is used in the following configuration settings and is important for notifying the right team members through Google Chat.

```json
{
  "name": "AzureDevOpsUsersMapToGoogleChat",
  "value": "AZURE-DEVOPS-USER1-EMAIL:GOOGLE-CHAT-ID-1;AZURE-DEVOPS-USER2-EMAIL:GOOGLE-CHAT-ID-2",
  "slotSetting": false
},
{
  "name": "EngineeringManagerInfo__GoogleChatUserId",
  "value": "ENGINEERING-MANAGER-GOOGLE-CHAT-ID",
  "slotSetting": false
}
```
It is not super straightforward and intuitive to find out what this value is for each team member. It is not an an exposed value on the Google Chat UI; therefore, you need to use the tools like Google Chrome Developer Tools to extract it. You have to copy the numbers next to `user/human/` value in `data-member-id` HTML attribute as shown in the screenshot below:

<img src="./assets/screenshots/google-chat-id.png" width="700">

#### Where to put the app settings?

Don't store the application settings in `appsettings.json` file. I recommend `appsettings.json` file only for local development since Azure Functions App have a better place to put the configuration settings via Azure Portal. Visit the `Configuration` link in your Functions instance as shown below:

<img src="./assets/screenshots/azure-functions-app-settings.png" width="700">

This way, you don't have re-publish the function app when you change a setting.
