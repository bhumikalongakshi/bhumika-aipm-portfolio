# Voice Email Agent

A voice-enabled AI workflow that listens to a user's request, extracts the required information, and automatically drafts and sends an email.

---

## Project Snapshot

Status: ✅ Completed

Built With

- ElevenLabs
- n8n
- Gmail
- Webhooks
- OpenAI

Skills Practiced

- Voice AI
- Webhooks
- Workflow Automation
- API Integration

---

## Why I Built This

I wanted to understand how voice interfaces interact with automation tools.

Instead of building another chatbot, I experimented with creating a voice assistant that could collect information naturally and trigger an automated workflow to send an email.

This project helped me connect conversational AI with backend automation.

---

## Problem

Sending emails still requires multiple manual steps.

Could a user simply speak, and have an email prepared automatically?

This project explores that idea.

---

## Solution

The voice assistant listens to the user's request.

The conversation is passed to an n8n workflow through a webhook.

The workflow extracts the important information, generates an email draft, and sends it using Gmail.

The workflow can easily be extended to support different email templates and business use cases.

---

## Workflow

(Add workflow image)

## Demo

(Add demo)

## Screenshots

Voice Agent

Webhook

n8n Workflow

Generated Email

---

## Challenges

Getting the voice agent and webhook to communicate reliably required several rounds of testing.

I also learned how important structured outputs are when passing information between AI and automation tools.

---

## Lessons Learned

- Voice interfaces require clear prompts.
- Webhooks make different systems easy to connect.
- AI works better when each component has a focused responsibility.

---

## Future Improvements

- Multiple email templates
- Calendar integration
- CRM integration
- User confirmation before sending
