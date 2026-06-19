# Starter Prompt — Weekly Brief setup

The **Weekly Brief** is a companion to the daily automation. It runs once a week (Friday 1pm) and
researches the **last seven days** of Data & AI news fresh (a 7-day lookback), rather than rolling up
the saved daily briefings — so it captures the week's biggest stories even as the picture shifts day
to day. It writes to a **`Weekly` subfolder** of your briefings folder so it never collides with the
daily run.

Paste everything in the fenced block below into Microsoft Scout (Copilot) on the machine where you
want it to run. It interviews you for your briefings folder and accounts, fills in `Weekly-Brief.json`,
then sets up and tests the automation.

---

```text
I want to set up the "Weekly Brief" Data SE news automation on this machine using the definition in
Weekly-Brief.json (in this repo). It ships as a TEMPLATE with placeholders that must be filled in for
ME before it can run. It is the weekly companion to the daily "Daily Brief" automation: it runs Friday
at 1pm, researches the last 7 days of news (when:7d), and writes to a \Weekly subfolder of my briefings
folder. Do ALL of the following, in order, and report what you changed at each step.

0. LOCATE — Find Weekly-Brief.json in this repo folder. Read it. Confirm it still contains the
   placeholders __BRIEFINGS_FOLDER__ and __ACCOUNTS__. (If it has already been filled in, ask me
   whether to re-run setup with new values or stop.)

1. ASK ME FOR MY FILE PATH — Ask me: "Where is your BASE briefings folder? Give me the full folder
   path (e.g. C:\Users\<me>\OneDrive\Documents\Data SE Briefings) — the same one your daily brief
   uses. The weekly run will automatically write to a \Weekly subfolder of it."
   - If I just press enter / say 'default', use $env:OneDrive\Documents\Data SE Briefings.
   - Expand any environment variables I give you to a concrete absolute path. Do NOT add \Weekly
     yourself — the template already appends it.
   - Create the base folder (and its \Weekly subfolder) if they do not exist.
   - In Weekly-Brief.json, replace EVERY occurrence of __BRIEFINGS_FOLDER__ with that absolute base
     path. It appears in all six step skip-checks, the research handoff path, AND the Save step —
     there should be 11 occurrences (each is already followed by \Weekly in the template). Use double
     backslashes in the JSON string (it is JSON-escaped).

2. ASK ME FOR MY ACCOUNTS — Ask me: "What is your book of business? Paste your account names
   separated by semicolons (e.g. Contoso Corp; Fabrikam Inc; Northwind Traders)." (If you already set
   this up for the daily brief, offer to reuse the same list.)
   - If I don't have a list handy, ask whether to (a) pull it from a file I point you to, or
     (b) skip account-matching — in which case set the accounts value to the single word NONE and
     tell me Step 4 ("Match news to accounts") will report "No accounts configured" and do nothing.
   - In Weekly-Brief.json, replace the single placeholder __ACCOUNTS__ with my semicolon-separated list.

3. SAVE THE FILLED-IN JSON — Write the updated Weekly-Brief.json back to disk. Re-read it and confirm
   it is still valid JSON and that NO __BRIEFINGS_FOLDER__ / __ACCOUNTS__ placeholders remain.

4. INTERESTS (optional) — The research/curation steps are tuned to Microsoft Fabric / Power BI /
   Azure data. Ask me if that matches my focus or if I want to adjust the topics and search queries,
   and apply any changes I ask for to steps 1 and 2. (The weekly window is when:7d; widen to when:14d
   only if a query is thin.)

5. CREATE THE AUTOMATION — Import the filled-in Weekly-Brief.json as a new automation called
   "Weekly Brief" (every Friday at 1pm). Keep it DISABLED for now. Leave my existing "Daily Brief"
   automation untouched.

6. PREREQS — Verify Pandoc is installed (if not: winget install JohnMacFarlane.Pandoc), that
   Microsoft Edge is present, and that I'm signed into M365. The env-var paths for Pandoc
   ($env:LOCALAPPDATA\Pandoc\pandoc.exe) and WorkIQ ($env:USERPROFILE\.copilot\bin\workiq.cmd)
   already resolve for my user — no change needed. (If I already set up the daily brief, these are
   done.)

7. PERMISSIONS (avoid repeated "Allow" clicks) — This automation runs UNATTENDED on a schedule, so
   if every PowerShell command, file write, web fetch, WorkIQ call and email send needs a manual
   "Allow", the Friday run can stall with nobody there to approve it. If I already granted these for
   the daily brief they cover this too; otherwise set up one-time pre-approval BEFORE the test run:
   - Tell me this requires Settings → Permissions, and that "Allow AI to request permission changes"
     must be ON before you can request anything (it is OFF by default).
   - Then request, via a single consent card each, the MINIMUM the automation needs:
     - autoApproveReadOnly = true (read-only shell + read-only tool calls such as the Google News /
       DuckDuckGo web fetches and listing files).
     - Shell allow-list patterns for PowerShell file operations on the briefings folder, pandoc, and
       msedge headless PDF print.
     - Per-server auto-approve for the `filesystem` and `workiq` servers.
   - Show me what each card grants and let me click Allow ONCE per card. Explain it is one-time setup.

8. TEST — With the automation still disabled, archive any existing weekly briefing for today in the
   \Weekly subfolder, then do one manual run. Verify: the .md and a branded PDF land in the \Weekly
   subfolder, and I receive an email with a clickable link to the PDF. Show me the result.

9. ENABLE — If the test passes, ask me to confirm, then enable the automation on its Friday-1pm
   schedule.

Email delivery already auto-targets whoever is signed into M365 (via m365_get_my_profile), so no
email address needs changing. Report what you changed at each step.
```
