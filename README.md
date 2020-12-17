## DevOps Automation

Automating your repetitive management tasks to give you more time for other strategic wins. 

This function application consists of three major functions:
1. Current Iteration Automation 
1. End of iteration Automation
1. Restrospective Automation 
1. Managers-only Reminders

### Curent Iteration Automation

Most of the reminders go out as part of the current iteration automation executions. This automation analyses the current iteration (aka. Sprint) using Azure DevOps API from various points and send reminders to the responsible parties to make sure they are addressed. These check points are:
1. Missing description in work items (defects and user stories)
1. Indescriptive work item titles
1. Work items without story points
1. Work items in Resolve state for more than 12 hours
1. Assigned but not activated work items. Reminds the responsible parties to activate the work item they are working on.
1. Congratulate the ones with all closed work items.

These reminders are sent out twice every day; one in the morning and one in afternoon.

### End of the Sprint Reminders

On the last day of the sprint, the reminders change. In addition to the ones above, it also automates:

1. If there are work items that are still active, it reminds the possible action items.

### Restrospective Automation

In the first day of the sprint, this automation checks if there is any remaining work from the previous sprint. It simply runs the existing checks and report them differently that is more appropriate for the first day of the next sprint.

### Managers-only Automation

I will document offline.