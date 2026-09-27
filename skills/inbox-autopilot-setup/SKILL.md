---
name: inbox-autopilot-setup
description: >-
  One-time setup for the inbox + job-search autopilot. Collect the user's profile, create the mail
  labels and cloud-storage folder structure, publish the interactive to-do board, save the config, and
  hand the user the exact steps to schedule the daily run. Use when the user says "set up inbox
  autopilot", "install my inbox autopilot", "configure inbox autopilot", "get inbox autopilot running",
  or when the daily inbox-autopilot skill runs and finds no config.
---

# Inbox Autopilot — one-time setup

Run this once per user. It prepares everything the daily `inbox-autopilot` skill depends on. Keep the
conversation in plain language; do not expose IDs or file paths unless asked. This needs an `~~email`
connector and a `~~cloud storage` connector connected first (see the plugin's CONNECTORS.md).

## Step 1 — Confirm connectors

Confirm the user has connected an `~~email` account and a `~~cloud storage` account and granted permission
to read mail, apply labels, create drafts, and upload files. If either is missing, tell them what to
connect and stop.

## Step 2 — Collect the profile

Walk the user through `references/profile-template.md` using AskUserQuestion (or free text). At minimum
capture: full name, contact line (email, phone, LinkedIn, location), the master resume text, whether they
need visa sponsorship and what to say about work authorization, the minimum acceptable contract/C2C rate,
relocation preference, and their timezone. If they have kids to track, capture each child's name and grade.
Do not invent a resume — ask them to paste theirs.

Save the profile as `profile.md` in a new **Autopilot** folder in `~~cloud storage`. Never store it inside
the shared plugin, and never copy one user's profile into another's.

## Step 3 — Create the folder structure

In `~~cloud storage`, create the **Autopilot** root and these subfolders (skip any that already exist):

```
Autopilot/
  Applications/                     (per-application folders go here)
  Finance/  Statements/             (statement PDFs by year)
  Kids/     School Documents/       (+ one subfolder per child, if any)
  To-Do/                            (daily To-Do md copies)
```

Also create an empty `Applications_Tracker.xlsx` at the Autopilot root with the header row: Date, Company,
Role, Recruiter, Contact, Location, Type, Match %, Status, Follow-up Due, Rate, Notes.

## Step 4 — Create the mail labels

Create the labels from `references/classification-and-filing.md` (in the daily skill) if they do not already
exist: `Job Search/Recruiters`, `Job Search/Job Alerts`, `Job Search/Applied`, `Job Search/Interviews`,
`Finance`, `Immigration`, `School`, `Personal`, `Rewards & Deals`, `Newsletters`, `To-Do`. Record each
label's ID.

## Step 5 — Publish the to-do board

Publish the board template from the daily skill's `references/todo-artifact.md` as an artifact with
`capabilities {"artifact": {}}`, starting with an empty `items` array and a "Set up" banner. Keep its URL.

## Step 6 — Save the config

Write `autopilot_config.json` to the Autopilot root with everything the daily run needs to avoid rediscovery:

```json
{
  "version": 1,
  "profile_path": "Autopilot/profile.md",
  "folders": {"root":"<id>","applications":"<id>","finance":"<id>","statements":"<id>","kids":"<id>","school_docs":"<id>","todo":"<id>"},
  "tracker_file_id": "<id>",
  "labels": {"recruiters":"<id>","job_alerts":"<id>","applied":"<id>","interviews":"<id>","finance":"<id>","immigration":"<id>","school":"<id>","personal":"<id>","rewards":"<id>","newsletters":"<id>","todo":"<id>"},
  "board_url": "<artifact url>",
  "timezone": "<IANA tz>",
  "rate_floor_per_hour": 70
}
```

## Step 7 — Schedule the daily run

Walk the user through `references/scheduling.md` to create the recurring task themselves (the plugin cannot
schedule itself — a human authorizes any unattended job that touches their inbox). Then offer to do one
**test run now** by invoking the `inbox-autopilot` skill, so they can see drafts and the board populate
before the first scheduled fire.

## Guardrails

Everything runs in DRAFT MODE — no email is ever sent automatically. Say this plainly so the user knows they
review and send every recruiter reply. Setup makes only additive, reversible changes (new folders, labels,
and a private board).
