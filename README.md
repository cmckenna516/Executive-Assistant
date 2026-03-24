# Executive Assistant — Morning Brief Skill

An AI-powered executive assistant skill for [Claude Cowork](https://claude.ai) that delivers a personalized morning briefing each day and acts as an always-available assistant for calendar management, email, and task tracking throughout the day.

---

## What It Does

Every morning (or on demand), this skill pulls data from your Gmail, Google Calendar, and job application tracker to deliver a fully personalized briefing that includes:

- **Priority Emails** — scored against your goals, VIP contacts, and urgency signals
- **Today's Schedule** — time-ordered with conflict flags and travel-time warnings
- **Meeting Prep Cards** — attendee bios, prior email context, talking points, linked docs
- **Proactive Suggestions** — cross-signal nudges (unanswered emails, unprepped meetings, approaching deadlines)
- **Week Preview** — high-stakes items and prep recommendations for the next 3–7 days
- **Job Tracker Sync** — auto-creates follow-up calendar events for applications in "Applied" status, and cleans up follow-up events when a role is rejected

The full briefing is rendered as a clean dark-mode HTML page and optionally emailed to you.

---

## Skill Capabilities

| Mode | Trigger | What Happens |
|---|---|---|
| Full Brief | `/morning-brief` or "give me my brief" | Runs the complete briefing pipeline |
| Test/Dry Run | `test` or "dry run" | Runs the brief without sending the email |
| Calendar | "reschedule / cancel / check my calendar" | Fetches, updates, or cancels GCal events |
| Email | "draft a reply to..." / "send a follow-up" | Finds thread, drafts reply, waits for approval before sending |
| Research | "research [topic]" | Runs web searches, synthesizes findings into a doc |
| Remember | "remember [note]" | Appends a memory to the persistent memory log |
| Profile | `profile` | Shows your current profile and flags stale fields |
| Schedule | `/morning-brief schedule` | Registers a daily automated task at your configured time |

---

## Integrations Required

This skill uses the following MCP connectors:

- **Gmail** — read emails, send replies, create drafts
- **Google Calendar** — read/create/update/delete events
- **Google Drive** — fetch linked documents from calendar invites
- **Claude in Chrome** — browser automation for reading the Google Sheets job tracker

---

## Setup

### 1. Install the Skill

1. Download `morning-brief.skill` from this repo
2. Open Claude Cowork
3. Go to **Settings > Skills > Install from file**
4. Select `morning-brief.skill`

### 2. Connect Integrations

In Claude Cowork, connect the following integrations:
- Gmail
- Google Calendar
- Google Drive
- Claude in Chrome (for job tracker sync)

### 3. First Run - Onboarding

The first time you invoke the skill, it will walk you through a quick setup:

1. Your name and email
2. Timezone
3. Work hours
4. Current goals (professional focus areas)
5. VIP contacts
6. Work location
7. Any personal context (partner, standing commitments, etc.)

Your profile is saved locally and used to personalize every briefing.

### 4. (Optional) Schedule Daily Delivery

To have your brief delivered automatically every morning, run:

`/morning-brief schedule`

This registers a daily task at the time configured in your profile (default: 7:00 AM).

---

## Job Tracker Sync

The skill integrates with a Google Sheets job application tracker. Each morning it:

1. Fetches the tracker (removing any active filters to ensure all rows are visible)
2. For every row with **Status = "Applied"**:
   - Creates a **Follow-Up 1** calendar event at applied_date + 7 days (if missing)
   - Creates a **Follow-Up 2** calendar event at applied_date + 14 days (if missing)
   - Each event includes a copy-paste ready email draft with the role title, company, and applied date
3. For any row with a rejection status (Rejected, Declined, Not Moving Forward, etc.):
   - Finds and deletes all associated follow-up calendar events for that company

To configure, add your tracker URL to your profile under `job_search.tracker_url`.

---

## Profile Schema

Your profile is stored at `~/.claude/morning-brief/profile.json`. Key fields:

- `name`, `email`, `timezone`, `brief_send_time`
- `work_hours` (start/end in HH:MM)
- `goals` (list of current professional focus areas)
- `vip_senders`, `vip_keywords`, `vip_domains`
- `key_people` (name, relationship, notes)
- `office_locations`
- `family` (partner name/notes, children)
- `fitness` (null unless you want fitness context in your brief)
- `job_search` (tracker_url, tracker_sheet_id, tracker_gid)

---

## File Structure

```
morning-brief/
+-- SKILL.md              # Skill definition loaded by Claude Cowork
+-- morning-brief.skill   # Packaged skill file (install this)
+-- README.md             # This file
```

---

## Notes

- Email is never sent without showing you the full draft and receiving explicit approval
- Calendar events are never modified without confirmation
- The job tracker is read-only -- the skill only writes to Google Calendar
- Memories and action logs are stored locally in `~/.claude/morning-brief/`

---

Built with [Claude Cowork](https://claude.ai) and the [Claude Agent SDK](https://docs.anthropic.com).
