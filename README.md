Developed with the following technologies by [@tarikguney](https://github.com/tarikguney)

![](./assets/logo/azure-functions.png) ![](./assets/logo/dotnet-core-logo.png)

![](./assets/banner/manager-automation-hq-banner.png)

# Manager Automation

## What is it?
**As an engineering manager, you realize that a lot of your time goes to repetitive tasks which also include guiding your team in keeping the sprint board up-to-date with enough level of details for an accurate work tracking.** There could be many reasons why you find yourself in this situation, but regardless of the reason, Manager Automation will take some of that burden off your shoulders. It will continuously analyze the current Sprint's board. It will remind the team of the missing information in the form of actionable reminders that are important for the smooth execution of every Sprint. **Note that these reminders play an essential role in guiding the team and new hires towards the expectations of the team and company culture.**

## Underlying Design Principle
The communication of the reminders in the solution is intentionally done in a public team chat room. Besides being public, the messages are kept straightforward too. It communicates the required actions through Google Chat (and Slack, etc., in the future) in simple and concise words. It is visible and repetitive, as these are perhaps some of the most effective factors to incorporate cultural elements into a team's daily life. Rare communication of hard-to-remember tasks will not make it into the team culture early enough. They have to be communicated frequently in a place where they are most visible to make them easy to follow by the team to become a part of the team culture.

## How does it look?

There are some screenshots of the messages below for you to see what's being communicated. Note that the source code will be the best place to check for the full list of messages and communication for now.

### In-Sprint action reminders
Manager Automation service sends daily reminders as demonstrated in the screenshots below. These series of reminders ensure the necessary actions being taken by the team members. The reminders use Google Chat's name mentioning feature to help the targeted team members to get on-time and relevant notifications through the Google Chat.

![](./assets/screenshots/in-sprint-alt3.png)

![](./assets/screenshots/in-sprint-alt2.png)

![](./assets/screenshots/in-sprint-alt1.png)

### End-of-sprint action reminders
Manager Automation service embeds specific situational facts in the reminder text. For example, it changes its greeting when it is the last day of the Sprint to draw people's attention to the fact that the Sprint is about to end, and there might be some missing work in the Sprint. It also guides the team members on how the incomplete work must be handled based on the time available and committed time for the work item estimated by the engineer.

![](./assets/screenshots/last-day-sprint-reminders.png)

### New Sprint action reminders
When it is the first day of the new Sprint, the automation service will analyze the previous Sprint one more time and bring up any issues that are left unresolved.

![](./assets/screenshots/new-sprint-reminders.png)

## Managers-only reminders
As an engineering manager, it is often crucial to be aware of the work's progression on time and catch the delay before becoming a big problem. There might be many reasons why work is delayed. For instance, engineering might be blocked. Regardless of the reason, if a task is taking an unreasonable amount of time, you will need to reach out and see what's going on and how you can help the team member with the progression of the work. The Manager Automation service collects this information for you -- the engineering manager -- and reports them daily in a private Google Chat room. 

![](./assets/screenshots/managers-only-reminders.png)


## The Complete List of Reminders 
This function application consists of three primary functions:
1. Current Iteration Automation 
1. End of iteration Automation
1. Restrospective Automation 
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

### Restrospective Automation
On the first day of the Sprint, this automation checks if there is any remaining work from the previous Sprint. It runs the existing checks and reports them differently, which is more appropriate for the first day of the next Sprint.

### Managers-only Automation

- Due date reminders. 

## Setup and Configuration
### Dependencies
This project is developed for **[Azure Functions](https://azure.microsoft.com/en-us/services/functions)** with **.[NET 5, (originally .NET Core 3.x)](https://dotnet.microsoft.com/)**. Hence, some basic knowledge in this technology stack is helpful. Also, this service assumes that you are using **[Azure DevOps](https://dev.azure.com)** as the project planning/tracking environment and **[Google Chat](https://chat.google.com)** as the communication medium among your team members.
### Publishing to Azure Functions
Deploying this source code to the Azure Function is easy. This project uses the Library version of Azure Functions. Check out these steps to learn how to publish Azure Functions: [Publish to Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs#publish-to-azure).

Once you deploy this function app to Azure Functions, these are the functions that will appear on the Azure Portal:

![](./assets/screenshots/functions.png)

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
