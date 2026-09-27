# Connectors

## How tool references work

This plugin's files use `~~category` as a placeholder for whatever tool the user connects in that category.
The plugin is tool-agnostic — it describes workflows in terms of categories rather than a specific product,
so it works for people on different email and storage providers.

## Connectors for this plugin

| Category      | Placeholder       | Options (examples)                         |
| ------------- | ----------------- | ------------------------------------------ |
| Email         | `~~email`         | Gmail, Microsoft Outlook                    |
| Cloud storage | `~~cloud storage` | Google Drive, OneDrive, Dropbox, Box        |

## What the plugin needs each connector to do

- **`~~email`** — search mail across categories, read messages and attachments, apply/remove labels or
  folders, archive, move to trash, and **create drafts** (it never sends). The reference implementation and
  richest behavior is with **Gmail** (label IDs, search operators, category sweeps). Other providers work as
  long as they support searching, labeling/foldering, and draft creation.
- **`~~cloud storage`** — create folders, upload and download files, list a folder, and move files to trash.
  The reference implementation is **Google Drive**. Any provider that supports create/list/upload/trash works.

Both connectors must be attached and authorized before running setup. If cloud storage is unavailable on a
given day, the daily run still does all the email work and notes that filing is paused.
