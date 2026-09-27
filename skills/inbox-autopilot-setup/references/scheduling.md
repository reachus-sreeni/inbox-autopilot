# Scheduling the daily run

The plugin cannot schedule itself — the user creates the recurring task once, because an unattended job that
reads their inbox and prepares drafts should be authorized by a person. Each firing starts a fresh cloud
session; because the plugin is installed and the `~~email` + `~~cloud storage` connectors are attached, the
daily `inbox-autopilot` skill loads and runs on its own.

## Create the scheduled task

Have the user create a scheduled task with:

- **Prompt (what fires each time):**
  `Run my inbox job autopilot for today: sweep all mail since the last run, prepare recruiter reply drafts (never send), file documents, and update my to-do board.`
  This wording triggers the `inbox-autopilot` skill. Keep it standalone — each run starts with no memory of
  previous runs.
- **Schedule:** once each weekday, early morning in the user's timezone (e.g. ~7:00 AM). Weekdays only means
  Monday's run covers the weekend's mail, which the skill handles.
- **Approvals:** the task should be allowed to run without stopping for approval, since no one is present at
  fire time. It still never sends email — that guardrail is in the skill, not the approval setting.
- **Notifications:** off. The to-do board banner is the status surface; the run does not push notifications.

In a Cowork/Claude session the user can simply say: *"Create a scheduled task that runs every weekday at
7 AM and sends it this prompt: …"* — Claude sets it up with the proper scheduled-task tool (not a local
cron). Confirm afterward which approval mode it got, and that its runs happen in the cloud (no computer
required) as long as the connectors stay attached.

## Timezone

Scheduled tasks are evaluated in UTC. Convert the user's local 7 AM to UTC when setting the schedule (and
shift the weekday range if the conversion crosses midnight). The skill itself timestamps the board in the
user's timezone from the profile.

## On-demand runs

The user can trigger a run any time by saying *"run my inbox autopilot now"* — useful for a time-sensitive
recruiter lead. It does the same full sweep for mail since the last run.

## Changing or pausing

- To change the time or prompt, edit the existing scheduled task rather than deleting it (keeps its run
  history).
- To pause, disable the task; to stop entirely, delete it. Pausing does not touch any labels, folders, or the
  board.

## First run

After scheduling, offer a **test run now** so the user sees the drafts and the populated board before the
first scheduled fire. If anything looks off (wrong labels, missing folders), re-run setup — it is additive
and safe to repeat.
