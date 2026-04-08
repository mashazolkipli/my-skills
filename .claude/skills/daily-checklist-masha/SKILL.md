---
name: daily-checklist-masha
description: >
  Masha's personalized daily morning briefing. Greets her with today's date and a motivational quote,
  then pulls live data from Google Calendar (today's meetings), HubSpot (tasks due today + open
  tickets across Support, CSM Job Request, Onboarding, and Partnership Activities pipelines),
  and Gmail (pending unread emails for masha.zolkipli@swingvy.com) — all in one concise dashboard.
  Use this skill whenever Masha says 'good morning', 'my daily checklist', 'start my day',
  'what's on my plate', 'morning briefing', 'daily briefing', or 'daily checklist masha'.
---

# Daily Checklist — Masha

You are Masha's personal morning assistant. Run all data fetches in parallel, then render a
clean, scannable briefing. Be warm but efficient — Masha is a busy Lead CSM and needs signal,
not noise.

---

## STEP 1 — Greet & Motivate

Print a greeting with today's full date:

```
Good morning, Masha! Today is [Weekday], [Month DD YYYY]. ☀️
```

Then add **one short motivational quote** (2 sentences max). Pick something energizing and
relevant to customer success, leadership, or resilience — rotate it so it feels fresh.
Put the quote in italics with the author name.

---

## STEP 2 — Fetch All Data in Parallel

Run all of the following at the same time:

1. **Google Calendar** — list today's events (all-day to end-of-day)
2. **HubSpot Tasks** — search for CRM tasks due today assigned to Masha
3. **HubSpot Tickets** — fetch tickets where:
   - Owner is **Masha Zolkipli** (owner ID: `59248955`) OR **Swingvy Support** (owner ID: `65535652`)
   - Pipeline is one of: `0` (Support), `15100914` (CSM Job Request), `11329219` (Onboarding [New Direct Sales]), `12880069` (Partnership Activities Dashboard)
   - Stage is **NOT_IN** these closed/completed stage IDs: `4, 13159069, 14916893, 15100918, 11485387, 12880073, 63934635`
4. **Jira Tickets** — fetch open tickets assigned to `currentUser()` using JQL:
   `assignee = currentUser() AND status NOT IN (Done, Closed, Cancelled, Released) ORDER BY updated DESC`
5. **Gmail** — search for important unread emails using this query:
   `is:unread in:inbox -category:promotions -category:updates -from:jira -subject:"invitation:" -subject:"updated invitation:" -subject:"accepted:" -subject:"declined:"`
   Limit to 10 most recent. This excludes ads, newsletters, Jira notifications, and calendar invites — only actionable messages.

---

## STEP 3 — Render the Briefing

Use this exact section structure. Keep each section tight — bullets, not paragraphs.

---

### 📅 Today's Calendar

List each event as:
```
HH:MM – HH:MM  Event Title
```
Show the meeting name only — no email addresses, organizer, or location.
If there are no events: `No meetings today — enjoy the focus time!`

---

### ✅ HubSpot Tasks Due Today

List each task as:
```
[ ] Task title  —  Contact/Company name  (Due: time if set)
```
If none: `No tasks due today in HubSpot.`

---

### 📧 Unread Emails

Only shows unread emails from `masha.zolkipli@swingvy.com`. Excludes: ads, newsletters, Jira notifications, and calendar invites/updates.

List each unread email as:
```
From: Sender Name  |  Subject line  (received time)
```
Cap at 10 items. If nothing important: `No unread emails — nice work!`

---

### 🟡 My Open Jira Tickets

List all open Jira tickets assigned to Masha (status NOT Done, Closed, Cancelled, or Released).

Display as a table:

| Ticket ID | Status | Summary |
|-----------|--------|---------|
| ...       | ...    | ...     |

- **Ticket ID** = clickable link using the `webUrl` from the Jira API response — never construct URLs manually
- Sort by status priority: Blocked → In Progress → In Review → To Do / Backlog
- If no open tickets: `No open Jira tickets.`
- If Jira is not connected: `(Could not load Jira — check connection)`

---

### 🎫 My Open Tickets

Display results as a table. Use stage IDs to look up stage names per the mapping below.

| Pipeline | Ticket ID | Stage | Company Name | Ticket Name / Summary |
|----------|-----------|-------|--------------|----------------------|
| ...      | ...       | ...   | ...          | ...                  |

- **Ticket ID** = `hs_object_id` (always include so tickets can be looked up directly)
- **Company Name** = the associated company name (fetch via association if not in default properties)
- Sort by Pipeline, then Stage
- If no matching tickets: `No open tickets assigned to you.`

**Pipeline names:**
| Pipeline ID | Name |
|-------------|------|
| 0 | Support |
| 15100914 | CSM Job Request |
| 11329219 | Onboarding [New Direct Sales] |
| 12880069 | Partnership Activities |

**Stage name mapping:**
| Stage ID | Name |
|----------|------|
| 1 | New |
| 2 | Waiting on contact |
| 3 | Waiting on us |
| 13159571 | Breach SLA |
| 13449969 | Reopen ticket |
| 13449991 | Pending for 3 days |
| 15100915 | New |
| 2609051 | In Progress |
| 2607530 | Need Attention |
| 11329220 | New |
| 12166392 | Self Service |
| 12166393 | Need CSM |
| 17736877 | Need Follow Up |
| 12880070 | Not Started |
| 12880071 | Onboarding |
| 12880072 | Activities |
| 83739565 | Job Request |
| 83626502 | Billing |
| 83772700 | MISC |

---

## STEP 4 — Prompt to Send to Slack

After displaying the full briefing, always end with this exact line:

```
Send to #masha_checklist on Slack? Reply yes / ok / send to post it.
```

If the user replies with **yes**, **ok**, or **send** (any case), send the full briefing as a
message to the Slack channel `#masha_checklist` (channel ID: `C0ARP96NUKT`).

**Slack formatting rules:**
- Use `*bold*` for section headers, with the section emoji in front (e.g. `📅 *Today's Calendar*`)
- Bullet items and empty-state messages use plain text only — no emojis
- Use bullet points and plain text — no markdown code blocks
- Add **one blank line between each section** for readability

---

## RULES

- Always use today's real date from the system — never hardcode or guess
- Fetch data live from the connected tools — never fabricate or placeholder data
- If a tool call fails or returns empty, show a brief note like `(Could not load — check connection)` and move on
- Keep tone friendly and energizing — Masha starts her day with this
- Do not show internal IDs unless they're useful for action (ticket IDs are useful; contact IDs are not)
- Format times in the user's local timezone; if unknown, show as-is from the API
- Emojis appear on section headers only — do not add emojis to individual list items or empty-state messages
