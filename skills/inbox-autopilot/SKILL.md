---
name: inbox-autopilot
description: >-
  Run the daily draft-mode inbox and job-search autopilot. Sweep all new mail since the last run,
  prepare recruiter reply DRAFTS with tailored resumes (never auto-send), classify and file
  statements and school documents to cloud storage, keep an application tracker current, and update
  an interactive to-do board. Use this when the user says "run inbox autopilot", "run my inbox job
  autopilot", "do my morning inbox sweep", "process my recruiter mail", or when the scheduled daily
  autopilot task fires. If no config exists yet, run inbox-autopilot-setup first.
---

# Inbox + Job-Search Autopilot (daily run)

Operate in **DRAFT MODE at all times**: prepare Gmail/email drafts and file documents, but **never send
email** and never make an irreversible change. The interactive to-do board is the status surface. This
skill needs an `~~email` connector and a `~~cloud storage` connector (see the plugin's CONNECTORS.md).

## Step 0 — Load config and the run window

1. Read `autopilot_config.json` from the **Autopilot** folder in `~~cloud storage`. It holds the user's
   label IDs, folder IDs, the to-do board URL, and profile pointers. **If it is missing, stop and run the
   `inbox-autopilot-setup` skill instead** — do not guess IDs.
2. Read `profile.md` from the Autopilot folder for the user's name, master resume, family/kids (names +
   grades, if any), rate/fit thresholds, and timezone.
3. Read the to-do board's current state (its embedded `state` JSON: `updated`, `lastRun`, `items`) to find
   `lastRun.when`. The **run window** is everything since that timestamp. On the first weekday run after a
   weekend, that window spans the weekend.

Every run does the FULL job (Steps 1–5) over the whole window and ends by updating the board's banner.

## Step 1 — Job leads (every run)

Search **all** mail categories since the last run, not just the primary inbox — recruiter and job-board
mail often lands in Promotions/Updates. Run explicit category sweeps (e.g. `category:promotions (recruiter
OR job OR hiring OR position OR opportunity OR contract OR C2C OR interview OR resume OR requirement)` and
the same for `category:updates`), plus a broad primary search.

For **every** recruiter/job email, first apply the correct job label immediately (recruiter outreach →
`Job Search/Recruiters`; automated job-board alerts → `Job Search/Job Alerts`) so it is pulled out of
Promotions and can never be swept by cleanup. Skip threads already labeled `Job Search/Applied` or already
replied to.

Classify and filter each lead:
- Skip anything that says "no sponsorship" / "W2 without sponsorship" / "USC or GC only" if the profile
  needs sponsorship.
- Contract/C2C with a stated rate **below the profile's floor** → trash (recoverable). No rate stated → proceed.
- Act only on leads that are ~80%+ fit for the profile. Quarantine obvious scams.

For each qualifying lead, follow `references/resume-tailoring.md` to build a tailored resume from the
profile's master resume (reorder the role's named skills first; **never invent skills the profile does not
list**). Then:
- If `~~cloud storage` is available, create `Applications/YYYY-MM-DD_Company_Role/` and save the resume +
  `JobDescription.txt` + `notes.md`, skipping any file that already exists.
- Create an **email DRAFT reply (do not send)** with the resume attached, filling the usual submission
  fields from the profile (full name, location, earliest start, expected rate/salary, willing to relocate,
  references on request, LinkedIn). Only state work authorization when the recruiter asks or the profile
  says to.
- Label the thread `Job Search/Applied`.
- Append a row to `Applications_Tracker.xlsx` (Date, Company, Role, Recruiter, Contact, Location, Type,
  Match %, Status=Draft ready, Follow-up Due=+7 days, Rate, Notes).
- Add a to-do item (category Job, priority med, due +7 days): "Review & send draft — Company Role".

## Step 2 — Replies, follow-ups, interview prep (every run)

For `Job Search/Applied` threads, check for recruiter replies since the last run.
- **Reply received** → update the tracker Status to "Replied" (or "Interview" if they propose a call), move
  the thread to `Job Search/Interviews` when it reaches interview stage, and build a one-page `PrepPack.md`
  in that application's cloud folder (company snapshot, role must-haves mapped to the profile's matching
  experience, ~5 likely questions, 3 smart questions to ask). Add a high-priority Job to-do to prep.
- **Follow-up nudge** → for any "Sent/Draft ready" lead with no reply ~7+ days later, prepare a short,
  polite follow-up DRAFT in that thread and add/flag a Job to-do.

## Step 3 — Cleanup and categorize (every run)

Do labeling **before** any archiving so job mail sitting in Promotions is pulled out first and never swept.

- Promotions/coupons/loyalty → `Rewards & Deals` + archive. De-dupe repeated near-identical blasts (keep
  the latest). Prune expired coupons past their expiry → trash.
- Newsletters/digests → `Newsletters` + archive.
- Bank/card/utility statements and payment alerts → `Finance`.
- Immigration/attorney/legal → `Immigration`.
- School communications (district, teacher, or a parent-comms platform such as ParentSquare, Seesaw,
  ClassDojo, Remind, Infinite Campus, PowerSchool, Schoology, Bloomz) → `School` and **keep in inbox**
  (attribute each to the right child by matching the name or grade in the profile; say "both/unclear" if
  it names neither).
- Personal/community → `Personal`.
- Never archive or delete Immigration, Finance, School, recruiters (except sub-floor C2C), interviews, or
  known personal contacts. Archive = remove from inbox (reversible). Trash only where specified above.

Full taxonomy, filing rules, and the conservative auto-complete rule are in
`references/classification-and-filing.md`.

## Step 4 — Daily to-do and timelines (every run)

Scan for deadlines: payments due (amount + due date), school/kids events (forms, permission slips, dues,
conferences, closures, report cards), registrations/renewals, appointments. Recognize recurring bills to
predict the next due date. Tag each school item with the child.

Auto-file supporting documents to `~~cloud storage` per `references/classification-and-filing.md`: append
statement lines to `Finance/Statements-Log.md` and save genuine statement/receipt PDFs to
`Finance/Statements/<year>/`; save school PDFs/images to the child's `Kids/<Child>/School/<school-year>/`
folder (whole-school items to `Kids/School Documents/<school-year>/`). Skip duplicates and anything that is
not a real document (no marketing graphics, logos, banners, tracking pixels, or attachments under ~20 KB).

Build each deadline as a to-do item using the schema in `references/todo-artifact.md`, always capturing the
real due date. **Auto-complete conservatively**: mark a payment done only when a matching payment
confirmation/receipt arrives — a statement or "payment due" notice means the bill is still OPEN. Mark an
event done only after its date passes. Never mark a recruiter follow-up done until an actual reply arrives.
When in doubt, leave it open.

## Step 5 — Morning summary + board update (every run)

Update the to-do board's `state` JSON, changing ONLY that block and preserving the page's HTML/CSS/JS
exactly (see `references/todo-artifact.md`):
- Set `lastRun` to a 1–2 sentence plain-English recap of exactly what this run did (overdue/due-today
  items, school updates by child, new drafts prepared, leads skipped, follow-ups drafted, cleanup counts).
  If nothing was actionable, say so explicitly. Set `tone` to "good" if anything happened, else "quiet".
- Merge new/updated items into `state.items`. **Never resurrect a ticked item** — anything already
  `done: true` stays done.
- Republish to the same board URL. Also save a plain copy to `To-Do/YYYY-MM-DD_ToDo.md` in cloud storage.

Do **not** send a push notification — the board banner is the status surface, and the run's final message
is not delivered to the user.

## Guardrails (always)

- Auto-send stays OFF. Every recruiter reply and follow-up is a draft the user reviews and sends.
- Archiving is reversible; trash only sub-floor C2C, expired deals, and clearly stale items; never
  permanently delete and never empty trash.
- Verify every binary upload (resume .docx, tracker .xlsx) by size and, when possible, a read-back — see
  `references/resume-tailoring.md` for the base64 fidelity gotcha.
- If `~~cloud storage` is unavailable on a run, do the email work normally and note in the board banner that
  cloud filing is paused; the labels + board remain the source of truth.
