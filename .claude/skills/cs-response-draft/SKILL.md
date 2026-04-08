---
name: response-draft
description: >
  Draft a customer support response for a ticket or request from HubSpot, Jira, or Slack.
  Use this skill whenever the user wants to reply to a customer, write a support response,
  handle a ticket, answer a customer question, or draft a message to a client about an issue
  or feature. Triggers for: 'draft a response', 'respond to this ticket', 'write a reply',
  'customer asked about', 'help me answer this', 'what do I say to this customer', 'response-draft'.
  Always use this skill when a customer message, ticket, or support request is present and the
  user wants to respond — even if they don't say 'draft' explicitly.
---

# Response Draft

## What this skill does

Helps you write a clear, professional, and educational reply to a customer ticket or request —
in about 10 minutes instead of 30. It reads the ticket, finds the relevant help center article,
checks similar past tickets, and drafts a response you can send or tweak.

## Step 1: Get the ticket

If the user has already pasted or described the ticket in this conversation, use that.
If not, ask:

> "Please paste the ticket or customer message you want to respond to — or describe the issue in a few words."

## Step 2: Summarise the ticket

Before doing anything else, write a short internal summary (2–4 sentences, not shown to the customer):
- What is the customer asking or reporting?
- What is the core issue or confusion?
- What outcome does the customer need?

Show this summary to the user with a header like `**Issue summary:**` so they can confirm you understood it correctly before you go further.

## Step 3: Research in parallel

Fire ALL of the following at the same time — do not wait for one before starting the next:

| Source | What to do |
|---|---|
| **Swingvy Help Center** | Use `web_search` or `WebFetch` to search `https://help.swingvy.com/knowledge` for articles matching the topic. Try 2–3 search queries if the first doesn't yield strong results. Capture article titles and URLs. |
| **HubSpot similar tickets** | Use `search_crm_objects` to search HubSpot conversations or tickets for similar past cases. Search by keyword (e.g., the feature name or error described). Aim for 2–3 relevant past tickets. Note how those were resolved. |

Wait for both to complete, then move to Step 4.

## Step 4: Draft the response

Using the ticket summary + research findings, write a draft reply to the customer.

### Tone and style
- **Empathetic**: acknowledge the customer's situation or frustration briefly before jumping to the solution
- **Simple**: write as if explaining to a smart non-technical colleague; avoid jargon
- **Educational**: explain the *why* briefly, not just the steps — customers learn and ask fewer follow-ups when they understand what's happening
- **Professional but warm**: like a helpful colleague, not a bot or a lawyer

### Structure (use this order)
1. **Opening** — acknowledge the customer and their issue (1 sentence)
2. **Answer / explanation** — the actual answer or next steps, clearly broken into steps if needed
3. **Help center link** — point to the most relevant article if found: "You can also find more detail in our Help Center: [article title](url)"
4. **Closing** — invite them to follow up if anything is unclear (1 sentence)

### What to avoid
- Don't open with "I hope this email finds you well" or similar filler
- Don't be overly apologetic — one acknowledgment is enough
- Don't paste long walls of text — use short paragraphs or numbered steps
- Don't make promises about timelines or fixes unless confirmed

## Step 5: Show the output

Present the response in this format:

```
---
**Issue summary:** [2–3 sentence internal summary]

**Sources used:**
- Help Center: [article title](url) — or "No relevant article found"
- Similar tickets: [brief description of 1–2 past tickets] — or "No similar tickets found"

---

**Draft response:**

[The actual customer-facing draft, ready to copy-paste or edit]

---
```

After showing the draft, ask:
> "Does this look right? I can adjust the tone, add more detail, or shorten it — just let me know."

## Error handling

- **Help Center returns nothing relevant**: note "No closely matching Help Center article found — response based on general knowledge." and continue
- **HubSpot not connected or no similar tickets**: note "HubSpot not available / no similar tickets found." and draft from help center + general knowledge
- **Ticket is vague or unclear**: ask one focused clarifying question before proceeding — don't guess
- **Issue involves a bug or technical problem**: flag it clearly in the draft: "I've flagged this internally for our team to investigate further" — and note this to the user so they can create a bug ticket if needed
