# AI Meeting Notes & Action Item Automation Agent

An AI-powered agent that reads a meeting transcript, generates a summary, extracts action items with owners and deadlines, logs everything in Google Sheets, emails the Minutes of Meeting (MoM) to attendees, and sends a Slack alert if any task is missing an owner or deadline.

This project was built to demonstrate **Agentic AI** — an AI system that doesn't just generate text, but takes multiple real actions (logging data, sending emails, raising alerts) based on its own understanding of the input.

---

## Problem Statement

- Teams spend hours in meetings every week.
- Notes are taken manually and are often incomplete.
- Action items and their owners get forgotten.
- Follow-up emails are written by hand, which takes time.

**Business Impact:** Missed deadlines, repeated meetings for the same topic, lost productivity, and no clear record of decisions.

## Solution

An AI agent that:
1. Takes a meeting transcript as input.
2. Generates a summary and lists key decisions.
3. Extracts action items with a task, owner, and deadline for each.
4. Logs every action item in Google Sheets.
5. Emails a formatted MoM to all attendees.
6. Sends a Slack alert if any task has a missing owner or deadline, so nothing falls through the cracks.

---

## Why This Is Agentic AI (not just GenAI)

- **GenAI** (the LLM alone) can only read the transcript and generate a summary — it stops there.
- **This Agent** goes further: it decides *what to do* with that information — save it, notify people by email, and alert the team if something is incomplete — without a human manually doing each step.

---

## Tech Stack

| Component | Tool Used |
|---|---|
| Workflow Automation | [n8n](https://n8n.io) (no-code, cloud) |
| AI Engine | Google Gemini API |
| Data Storage | Google Sheets |
| Notifications | Gmail (MoM email), Slack (missing-info alerts) |
| Deployment | n8n Cloud (can also be self-hosted on GCP/AWS/Azure) |

---

## Workflow Architecture

```
On Form Submission (Meeting Transcript, Date, Attendee Emails)
        │
        ▼
  Gemini AI Node ── generates summary, decisions, action items (as JSON)
        │
        ▼
  Edit Fields (Set) ── parses the AI's JSON response
        │
        ├──────────────► Gmail ── sends MoM email to attendees
        │
        ▼
  Split Out ── splits action_items into individual rows
        │
        ├──────────────► Google Sheets ── logs every task (Task, Owner, Deadline, Status, Meeting Date)
        │
        ▼
  IF Node ── checks if owner or deadline is "Not specified"
        │
        ├── True ──► HTTP Request (Slack Webhook) ── sends alert to #meeting-alerts
        └── False ─► No action needed (task already logged)
```

---

## How It Works — Step by Step

1. **Trigger:** A user fills a short form with the meeting transcript, meeting date, and attendee emails.
2. **AI Processing:** The transcript is sent to Gemini with a structured prompt asking for a JSON response containing a summary, decisions, and action items (task, owner, deadline).
3. **Parsing:** The AI's raw text response is parsed into a usable JSON object.
4. **Email:** A formatted MoM email is sent to all attendees automatically.
5. **Logging:** Each action item becomes its own row in a Google Sheet ("Meeting Task Log").
6. **Quality Check:** Each task is checked — if the owner or deadline says "Not specified," a Slack alert is sent so the team can follow up.

---

## Sample Input / Output

**Input transcript:**
> "Someone needs to prepare the campaign report. Priya will share the sales data by tomorrow. Team agreed to proceed with the new marketing campaign."

**AI Output (JSON):**
```json
{
  "summary": "The team agreed to proceed with the new marketing campaign...",
  "decisions": ["Proceed with the new marketing campaign"],
  "action_items": [
    {"task": "Prepare the campaign report", "owner": "Not specified", "deadline": "Not specified"},
    {"task": "Share the sales data", "owner": "Priya", "deadline": "Tomorrow"}
  ]
}
```

**Result:**
- Both tasks logged in Google Sheets.
- MoM email sent to attendees.
- Slack alert sent for the first task (missing owner and deadline).

---

## Setup Instructions

1. Import `Meeting_Notes_Agent.json` into your n8n instance (Workflows → Import from File).
2. Add your own credentials:
   - Google Gemini API key
   - Google Sheets account (create a sheet with columns: Task, Owner, Deadline, Status, Meeting Date)
   - Gmail account
   - Slack Incoming Webhook URL
3. Replace the placeholder `YOUR_SLACK_WEBHOOK_URL` in the HTTP Request node with your own webhook URL.
4. Activate the workflow and open the form trigger URL to test it.

---

## Business Value

- Saves 20–30 minutes of manual note-taking per meeting.
- Ensures every action item has a clear owner and deadline.
- Creates a searchable record of decisions.
- Improves accountability across teams.

## Future Scope

- Live audio transcription support (Zoom/Google Meet).
- Auto-create tasks in Jira or Trello.
- Multi-language meeting support.
- Analytics dashboard for meeting trends.

---

**Note:** All API keys and webhook URLs have been removed from the shared workflow file for security. Replace the placeholders with your own credentials before running.
