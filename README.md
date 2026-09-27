# Job & Inbox Autopilot

A **draft-mode** daily assistant for active job seekers. Every weekday morning it sweeps your new mail,
prepares tailored recruiter reply **drafts** (it never sends anything on its own), files your statements and
school documents to cloud storage, keeps an application tracker current, and updates a private interactive
to-do board you can tick off.

> **Safety first:** Auto-send is always off. Every recruiter reply and follow-up is prepared as a draft for
> you to review and send. The plugin only makes additive, reversible changes (labels, folders, drafts, a
> private board). It never deletes permanently and never empties trash.

## What it does each run (Steps 1–5)

1. **Job leads** — finds recruiter/job mail across all inbox categories, labels it, filters by fit and your
   rate floor, builds a tailored resume, files an application folder, and prepares a draft reply.
2. **Replies & follow-ups** — spots recruiter replies (builds an interview prep pack), and drafts polite
   nudges for leads that have gone quiet ~7+ days.
3. **Cleanup** — files promotions, newsletters, statements, immigration, school, and personal mail into the
   right labels; archives the noise; keeps important mail in your inbox.
4. **To-do & timelines** — captures deadlines (bills, school events, renewals, appointments) with real due
   dates, and files statement/school PDFs. It only marks a bill done when a real payment confirmation arrives.
5. **Morning summary** — writes a one-paragraph recap into your to-do board's banner and saves a plain-text
   copy of the day's list.

## Requirements

- **Claude app in Cowork mode**, with plugin installation allowed by your organization (some orgs disable it).
- **Scheduled tasks** available on your account (needed for the daily automatic run).
- Two connectors (see `CONNECTORS.md`): an **email** account (Gmail is the reference) and a **cloud storage**
  account (Google Drive is the reference). You connect these inside your Claude app.

## Installation — download from GitHub and install in Claude

This plugin is distributed as a downloadable `.plugin` file attached to each GitHub Release.

**1. Download the plugin file from GitHub**

- Open the repository on GitHub and go to the **Releases** page (right-hand sidebar, or `…/releases`).
- Under the latest release's **Assets**, download **`inbox-autopilot.plugin`**.
- Some browsers save it as `inbox-autopilot.zip`. If that happens, **rename it back to
  `inbox-autopilot.plugin`** (the file is the same either way).

**2. Install it in Claude**

- Open the **Claude desktop app** and make sure you're in **Cowork mode**.
- Start a chat and **attach the `inbox-autopilot.plugin` file** to a message (drag it in or use the
  attach/＋ control).
- Claude shows a **plugin preview card** for the file. Click **Save plugin** to install it.
- If you don't see the card or a Save button, your organization may not allow plugin installs — check with
  your admin.

**3. Confirm it installed**

- The plugin's two skills should now be available: **`inbox-autopilot-setup`** and **`inbox-autopilot`**.
- You can verify by typing `/` and looking for them, or just proceed to setup below.

## Getting started (first-time setup)

1. **Connect your accounts** — connect your **email** (Gmail/Outlook) and **cloud storage**
   (Drive/OneDrive/Dropbox/Box), and grant permission to read mail, label/foldering, create drafts, and
   upload files. Do this **before** setup.
2. **Set it up** — say *"set up inbox autopilot"*. Claude collects your profile (including your master
   resume, rate floor, work-authorization answer, and optional kids/school details), creates your mail
   labels and cloud folders, publishes your private to-do board, and saves your config. This runs once.
3. **Schedule it** — during setup Claude gives you the exact steps to create a weekday-morning scheduled
   task. The plugin can't schedule itself; you authorize the recurring run once.
4. **Test it** — say *"run my inbox autopilot now"* to watch the drafts and to-do board populate before the
   first scheduled fire.

## Using it day to day

- Review the **drafts** in your email each morning and send the ones you want.
- Open your **to-do board** to see what's due; tick items off — your ticks are saved to the page.
- Run it **on demand** any time ("run my inbox autopilot now"), e.g. for a time-sensitive lead.
- **Change or pause** the schedule by editing or disabling the scheduled task; your labels, folders, and
  board are untouched.

## Updating to a newer version

Because this is a downloadable release (not a live marketplace), updates are manual: download the newer
`.plugin` from the repo's Releases page and install it the same way. Your profile, labels, folders, and
to-do board live in your own accounts and carry over — reinstalling the plugin does not touch them.

## What's inside

- **Skill: `inbox-autopilot-setup`** — one-time onboarding (profile, labels, folders, board, config,
  scheduling steps).
- **Skill: `inbox-autopilot`** — the daily draft-mode run (Steps 1–5), with references for resume tailoring,
  classification/filing, and the to-do board template.

## Privacy

Your profile and resume are stored only in **your** cloud storage, never inside the shared plugin. Nothing
runs on anyone else's behalf — each person authorizes their own accounts, and the plugin carries no personal
data.

## Troubleshooting

- **No "Save plugin" button / can't install** — plugin installs may be disabled for your org, or you're not
  in Cowork mode. Check with your admin.
- **Setup can't find a connector** — connect the email and cloud-storage accounts first, then re-run setup.
- **Daily run didn't fire** — confirm the scheduled task exists and is enabled, and that scheduled tasks are
  available on your plan.
- **Cloud storage unavailable one day** — the run still does all the email work and notes in the board banner
  that filing is paused; it resumes when storage is back.
