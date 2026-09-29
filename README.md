# 🤖 Meeting Notes Agent

An AI-powered Meeting Notes Agent built using **n8n** and **Google Gemini**.

The workflow takes a meeting transcript through a simple form, uses AI to extract the key information, stores action items in Google Sheets, and automatically sends meeting minutes to attendees through Gmail. A Slack alert workflow is also configured for relevant action items.

---

## 📌 Project Overview

Taking meeting notes manually can be time-consuming and important action items can easily be missed.

This project automates the meeting-notes process using an AI-powered workflow.

The user submits:

- Meeting Transcript
- Meeting Date
- Attendee Email Addresses

The workflow then processes the transcript and extracts:

- Meeting Summary
- Key Decisions
- Action Items
- Action Item Owner
- Deadline

The extracted action items are stored in Google Sheets and the meeting summary is sent to the attendees through Gmail.

---

## 🔄 Workflow

```text
Meeting Transcript
        ↓
n8n Form Submission
        ↓
Google Gemini
        ↓
Structured JSON
        ↓
Parse Meeting Data
        ↓
Split Action Items
        ↓
   ┌────┴─────┐
   ↓          ↓
Google      IF Condition
Sheets          ↓
                ↓
        Slack Alert Branch
        (configured)
        
Meeting Information
        ↓
Gmail
        ↓
Meeting Minutes





Meeting Minutes
