# Meeting-Workflow-Automation-Using-n8n
I built an automated system that takes raw meeting transcripts, figures out what needs to be done, creates Jira tickets for the team, and posts a clean summary to Slack. I used n8n to tie everything together.


The Pipeline Steps:

1.Manual Trigger ➔ Read File ➔ Extract Text ➔ Code (Construct Payload) ➔ HTTP Request (Groq AI) ➔ Code (Parse JSON) ➔ HTTP Request (Jira Cloud API) ➔ Aggregate Results ➔ Code (Format Slack Layout) ➔ HTTP Request (Slack Webhook) 

Why I Picked These Tools:

1.n8n: The visual workflow makes it really easy to see how data flows, handle repetitive tasks, and write custom JavaScript when needed.
Groq API (LLaMA 3.3 70B): It’s incredibly fast and reliable at returning clean JSON data. It's also great at picking up on tasks people mention casually during meetings.
2.Jira API (via HTTP): I used direct HTTP requests instead of the built-in Jira node because it gave me much better control over exact fields and custom data.
Slack Webhooks: Combined with Slack's Block Kit, this allows us to send beautifully formatted, easy-to-read update messages.

2. Key Assumptions
Matching Names to Jira IDs: I set up a simple lookup list to match names mentioned in the meeting (like "Akash" or "Priya") to their actual Jira account IDs.
Reading Between the Lines: If someone says something like "we should probably check the logs," the AI assumes that's a real task and captures it.
Jira Project: The system assumes there is already a standard Scrum project (Key: PAT) ready to accept these tickets.

Handling Mistakes and Errors:

1.Missing Team Members: If the AI assigns a task to someone who isn't on our Jira lookup list, the system simply drops the assignee field. This way, Jira still creates the ticket as "Unassigned" instead of throwing an error and breaking the pipeline.
2.API Glitches: If Jira or Groq is temporarily down or busy, the HTTP steps are set to automatically retry twice (waiting 1 second between tries).
3.Messy Data: I added some backup code right before the Slack step so that even if the data comes through looking a bit weird, the system can still parse it and send the message.

Next Steps 

1.Automatic Triggers: Instead of clicking "Run" manually, set it up to start automatically the moment a new transcript file is dropped into Google Drive or Dropbox.
2.Smart User Lookup: Replace the hardcoded list of names with a live search of Jira's user directory so it automatically finds new team members.
3.Two-Way Sync: Set up a listener so that when someone moves or closes a ticket in Jira, the original meeting notes document updates itself automatically.
