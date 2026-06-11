# Daily Brief — Data SE News Automation

A Microsoft Scout (Copilot) automation that, every weekday at 8am, researches the latest
Data & AI news, curates it for a Microsoft Data Solutions Engineer, matches stories to your
book of business using your own M365 signals, and emails you a branded PDF briefing with a
clickable link.

## What it does

Six sequential steps:

1. **Conduct data & AI research** — discovers stories via Google News RSS and recovers canonical
   article URLs.
2. **Curate data news** — dedupes, filters, buckets and ranks stories into five sections.
3. **Write briefing report** — produces a structured Markdown report (no emojis, validated URLs).
4. **Match news to accounts** — cross-references top stories against your accounts using emails,
   chats and meetings, and inserts an "Account Opportunity Matches" section.
5. **Save report** — writes the working `.md` to your briefings folder.
6. **Convert, link & email** — renames the file, renders a branded Microsoft PDF (Pandoc + Edge),
   creates a OneDrive share link, and emails the briefing to you.

See [`examples/`](examples/) for a sample generated PDF.

## Repo contents

| File | Purpose |
|------|---------|
| `Daily-Brief.json` | The automation definition. Ships as a **template** with `__BRIEFINGS_FOLDER__` and `__ACCOUNTS__` placeholders. |
| `starter-prompt.md` | Paste this into Scout to set it up — it asks for your file path and accounts and fills in the JSON. |
| `PATCH-NOTES.md` | History of fixes already applied to the definition. |
| `examples/` | A **redacted** sample briefing — PDF plus its source `.md`. Account names and internal signals are removed. |

## Setup

1. Clone or download this repo onto the machine where Scout runs.
2. Open [`starter-prompt.md`](starter-prompt.md) and paste the fenced prompt into Scout.
3. Answer the two questions it asks — your **briefings folder path** and your **accounts**.
   Scout fills those into `Daily-Brief.json`, imports the automation, tests it, and (on your
   confirmation) enables the weekday-8am schedule.

## Placeholders

The template keeps only two user-specific values as placeholders; everything else uses
environment variables that resolve per-user automatically:

- `__BRIEFINGS_FOLDER__` — absolute path to your output folder (7 occurrences).
- `__ACCOUNTS__` — your semicolon-separated book of business (1 occurrence).
- Pandoc fallback uses `$env:LOCALAPPDATA\Pandoc\pandoc.exe`.
- WorkIQ uses `$env:USERPROFILE\.copilot\bin\workiq.cmd`.

## Field notes & tips

### Recommended model: Claude Opus 4.8
Run this automation with **Claude Opus 4.8**. It handles the long, multi-step run (web research,
canonical-URL recovery, M365 account matching, PDF rendering, email) reliably and follows the Step 1
output contract that keeps the research result a short one-line confirmation — avoiding the
`[object Object]` step-result failure documented in `PATCH-NOTES.md`. Set it as the automation's model
(or your default model) before enabling the schedule.

### A capable model can generalize the paths for you
The template uses placeholders, but you don't always have to hand-edit a username. When the starter
prompt is run with a strong default LLM (a colleague reported this with **Claude Sonnet**), the model
rewrites the hardcoded paths into portable, machine-independent forms automatically — so there's no
need to swap in your own username. For example, it turns the Step 0 skip-check into:

```powershell
### STEP 0 — SKIP CHECK (run this BEFORE anything else in this step)
$today = Get-Date -Format 'yyyy-MM-dd'
$folder = Join-Path $env:OneDrive 'Documents\Data SE Briefings'
$cutoff = (Get-Date).AddMinutes(-10)
$existing = Get-ChildItem -Path $folder -Filter "$today*.md" -File -ErrorAction SilentlyContinue | Where-Object { $_.LastWriteTime -lt $cutoff } | Select-Object -First 1
if ($existing) { "SKIP: $($existing.FullName)" } else { "PROCEED" }
```

> Output handling: if it starts with `SKIP:`, the step emits only
> `Skipped: Data SE Briefing for <today> already exists at <full path from SKIP line>.` and ends.
> If it prints `PROCEED`, the step continues.

Using `Join-Path $env:OneDrive 'Documents\Data SE Briefings'` is preferred over a literal
`C:\Users\<name>\...` path because it resolves on any machine and account.

### Clear out the briefings directory between runs
Every step starts with the Step 0 skip-check above. If a file matching `<today>*.md` already exists
in the briefings folder, the **entire run is skipped** (this is by design, to avoid duplicate
briefings). So when you're **testing or re-running on the same day**, archive or delete that day's
`.md`/`.pdf` first — otherwise the run will short-circuit and produce nothing new.

## Prerequisites

- Microsoft Scout signed into M365.
- [Pandoc](https://pandoc.org/) (`winget install JohnMacFarlane.Pandoc`).
- Microsoft Edge (used headless to print the PDF).
