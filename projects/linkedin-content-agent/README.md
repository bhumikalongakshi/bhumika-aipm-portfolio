# LinkedIn Content Workflow

An AI-powered workflow that helps me turn rough ideas into a LinkedIn post along with a matching image.

---

## Project Snapshot

**Status:** ✅ Completed

**Built With**
- n8n
- OpenAI
- Pollinations AI
- Gmail

**Skills Practiced**
- Workflow Automation
- Prompt Engineering
- AI Content Generation
- API Integration

---

## Why I Built This

I enjoy sharing what I learn about AI on LinkedIn, but I often spend more time organizing my thoughts than actually writing the post.

I wanted to build a workflow that could take my rough notes, create a first draft, generate a matching image, and send everything to me for review. The goal wasn't to replace writing—it was to reduce the repetitive work and give me a better starting point.

---

## The Problem

Creating a LinkedIn post usually involves several manual steps:

- Organizing scattered notes
- Writing the first draft
- Thinking of an image idea
- Generating an image
- Copying everything into LinkedIn

Even for a short post, this takes longer than expected.

---

## Solution

This workflow automates the first draft of the content creation process.

Starting with rough notes, it generates:
- a LinkedIn post,
- an image prompt,
- an AI-generated image,
- and sends everything to my email for review before publishing.

I still review and edit the final post before posting it.

---

## Workflow

                  LinkedIn Content Workflow

                 Rough Notes / Ideas
                         │
                         ▼
                 OpenAI Content Agent
                         │
         ┌───────────────┴───────────────┐
         ▼                               ▼
 LinkedIn Draft                 Image Prompt
         │                               │
         └───────────────┬───────────────┘
                         ▼
                Pollinations AI
                         │
                         ▼
                Generated Image
                         │
                         ▼
                 Gmail Notification
                         │
                         ▼
                  Human Review
                         │
                         ▼
                 Publish to LinkedIn

![Workflow](assets/workflow.png)

---

## Demo

*(Insert 30–45 second demo video here)*

[Demo Video](assets/demo.mp4)

---

## Screenshots

### Workflow in n8n

![Workflow Screenshot](assets/workflow-screenshot.png)

### Generated LinkedIn Post

![Generated Post](assets/generated-post.png)

### Generated Image

![Generated Image](assets/generated-image.png)

### Email Output

![Email Output](assets/email-output.png)

---

## Challenges

One of the challenges was coordinating outputs between different AI steps.

Initially, I tried generating everything in one prompt, but separating content generation and image prompt generation produced much better results.

I also spent time refining prompts so the generated content sounded closer to my writing style instead of feeling overly generic.

---

## What I Learned

- Breaking a large task into smaller AI tasks improves consistency.
- Prompt quality has a significant impact on output quality.
- Keeping a human review step is important for content accuracy.
- Simple workflows become easier to maintain than highly complex ones.

---

## Future Improvements

- Add multiple writing styles
- Generate carousel posts
- Support different social media platforms
- Store previous posts for context
- Add approval directly inside the workflow

---

## Reflection

If I were building this for real users, I'd focus on authentication, user preferences, prompt versioning, analytics, and scheduling posts directly from the workflow.
