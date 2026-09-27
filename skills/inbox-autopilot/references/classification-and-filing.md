# Classification, filing, and cleanup rules

## Label taxonomy

Create/reuse these labels (the setup skill creates them and records their IDs in `autopilot_config.json`):

- `Job Search/Recruiters` — direct recruiter outreach.
- `Job Search/Job Alerts` — automated job-board/alert blasts.
- `Job Search/Applied` — leads a draft/reply was prepared for.
- `Job Search/Interviews` — interview-stage threads.
- `Finance` — bank/card/utility statements, payment alerts.
- `Immigration` — attorney/USCIS/legal.
- `School` — kids' school + parent-comms platforms (kept in inbox).
- `Personal` — personal/community.
- `Rewards & Deals` — promotions/coupons/loyalty (archived).
- `Newsletters` — digests/newsletters (archived).
- `To-Do` — open action emails.

Always label job/recruiter mail **before** archiving anything, so it is pulled out of Promotions and can
never be swept as marketing.

## What to archive vs. keep vs. trash

- Archive (remove from inbox, reversible): promotions → `Rewards & Deals`; newsletters → `Newsletters`;
  job-board alerts → `Job Search/Job Alerts`.
- Keep in inbox: `School`, `Finance` action items, `Immigration`, recruiter threads being acted on,
  anything the user must act on.
- Trash (recoverable) only: contract/C2C below the profile's rate floor, expired coupons past expiry,
  and clearly past-season/stale recurring blasts. Never permanently delete; never empty trash.
- Never archive or delete Immigration, Finance, School, recruiters (except sub-floor C2C), interviews, or
  known personal contacts.

Note: some accounts have filters that move mail to Trash unexpectedly when labels change. If a message you
only meant to archive shows a `TRASH` label afterward, untrash it and re-apply the intended label so it
ends up archived, not trashed.

## Auto-filing documents to cloud storage

Keep it lean and idempotent — list the target folder and skip if a same-named file already exists. Only save
genuine documents (real statement/receipt/school PDFs or document images). **Never** save marketing
graphics, logos, email banners, tracking pixels, or any attachment under ~20 KB.

- **Statements/receipts:** append a line to `Finance/Statements-Log.md` (date, source, type, amount/balance,
  due date) and save genuine PDF attachments to `Finance/Statements/<year>/`.
- **School documents:** save PDFs/images by child to `Kids/<Child>/School/<school-year>/`; whole-school or
  shared items to `Kids/School Documents/<school-year>/`.
- The small text indexes (`Statements-Log.md`, `Applications_Tracker.xlsx`, the daily To-Do md) are the
  durable record and must be updated even on a run where no binary is saved.

## Conservative auto-complete (protects trust)

- Mark a **payment** item done ONLY when a matching payment-confirmation/receipt email arrives ("thank you
  for your payment", "payment received/processed"). A statement, "your statement is available", or "payment
  due" notice means the bill is still OPEN and UNPAID.
- Mark an **event** done only after its date has passed.
- Never mark a **recruiter follow-up** done until an actual reply arrives.
- When in any doubt, leave the item open.

## Storage-smart cleanup fallback

Personal cloud accounts share storage across mail + files + photos, so watch quota. If usage is high or an
upload fails with a storage/quota error: de-dupe identical files (keep one); move statement PDFs older than
~18 months to Trash (recoverable) while KEEPING their `Statements-Log.md` lines; if still constrained, pause
binary uploads and keep only the text log/tracker/To-Do entries, and note in the board banner that filing is
paused pending cleanup. Never empty Trash and never permanently delete.
