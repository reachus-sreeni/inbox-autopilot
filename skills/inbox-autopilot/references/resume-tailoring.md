# Resume tailoring + reliable binary uploads

## Building a tailored resume (.docx)

Build from the **master resume in the user's `profile.md`**. Tailor per role; never invent skills the
profile does not list (leave true gaps out — do not claim them).

Use `python-docx`. Formatting conventions (match these for a clean, ATS-friendly result):
- Font: Calibri throughout; body ~10.5pt.
- Name line: ~17pt, bold, navy `#1F3864`.
- One-line italic tagline under the name that leads with the role's named skills.
- Contact line with only the email and LinkedIn underlined.
- Section headings: UPPERCASE, bold, navy, with a thin bottom border.
- Concise bullets. Reorder the CORE COMPETENCIES and TECHNICAL SKILLS so the role's named skills come first.
- Set each run's `noProof` so the resume does not render with spell-check squiggles.

Save/attach as `Resume_<FullNameNoSpaces>.docx`. Fill submission fields from the profile: full name,
location, earliest start, expected rate/salary, willing to relocate, references (on request), LinkedIn.
State work authorization only when asked or when the profile says to.

## The base64 fidelity gotcha (important)

Cloud-storage and email connectors accept binary files as base64 inline. Reproducing a very large base64
string exactly is unreliable, so:

1. **Minimize the .docx first.** A default python-docx file is ~35–40 KB because of theme/styles bloat.
   Strip `stylesWithEffects.xml`, the thumbnail, and custom XML, and replace `styles.xml` with a minimal
   version (docDefaults + Normal + a List Bullet style). This gets the file to ~11 KB (base64 ~15 KB),
   which is far more reliable to transmit.
2. **Verify by exact byte size.** After upload, compare the connector's reported `fileSize` to the local
   file's byte size. An exact match means the base64 round-tripped intact (any single-character error would
   change the decoded length or fail to decode). When possible, also read the file back to confirm content.
3. **Reuse the verified string.** Once an upload's size matches, reuse that exact base64 for the email
   attachment — it is known-good.
4. **Never trust a large transcription blindly.** If a size check fails, do not retry the same blob; shrink
   the file further or regenerate.

## Rebuilding the application tracker (.xlsx)

The tracker is `Applications_Tracker.xlsx` at the Autopilot root. There is usually no "update file content"
connector call, so to add rows: read the existing rows (as text), rebuild the whole workbook from that data
plus the new rows with `openpyxl`, upload it as a new file with the same name and location, verify the byte
size, then trash the old copy. Rebuilding from the already-read text avoids transcribing a large base64
blob. Columns: Date, Company, Role, Recruiter, Contact, Location, Type, Match %, Status, Follow-up Due,
Rate, Notes.
