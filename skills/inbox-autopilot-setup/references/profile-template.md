# Profile template (save as `Autopilot/profile.md`)

Fill this in with the user's details. The daily run reads it every time. Never bundle a filled-in profile
inside the shared plugin.

```markdown
# Autopilot Profile

## Identity
- Full name:
- Location (city, state) shown on resume:
- Full address (only shared when explicitly requested):
- Phone:
- Email:
- LinkedIn URL:
- Timezone (IANA, e.g. America/Chicago):

## Job-search preferences
- Target roles / titles:
- Minimum contract / C2C rate (per hour) below which to skip or trash:
- Willing to relocate (Yes/No):
- Earliest start:
- Expected rate / salary answer (e.g. "negotiable"):
- Need visa sponsorship? (Yes/No):
- Work-authorization answer to give WHEN ASKED (e.g. "H1B", "US Citizen", "Green Card"):
- References line (e.g. "available on request"):
- Known true skill gaps to NEVER claim on a tailored resume (list them):

## Kids / school (optional — omit if none)
- Child 1: name, grade, school
- Child 2: name, grade, school
- Parent-comms platforms used (ParentSquare, Seesaw, Infinite Campus, etc.):

## Master resume
Paste the full plain-text master resume here (summary, core competencies, technical skills, experience with
dates, certifications, education). The daily run tailors from this and never invents skills not listed.
```

## Notes for setup

- Ask the user to paste their real resume; do not fabricate one.
- The rate floor and the sponsorship/work-authorization answers drive the Step 1 filters in the daily run —
  confirm them explicitly.
- If the user has no kids to track, skip the School/Family parts entirely; the daily run adapts.
