# Starter Prompt — Daily Brief setup

Paste everything in the fenced block below into Microsoft Scout (Copilot) on the machine where you
want the automation to run. It will interview you for your **output file path** and your **accounts**,
write those values into `Daily-Brief.json`, then set up and test the automation.

---

```text
I want to set up the "Daily Brief" Data SE news automation on this machine using the definition in
Daily-Brief.json (in this repo). It ships as a TEMPLATE with placeholders that must be filled in for
ME before it can run. Do ALL of the following, in order, and report what you changed at each step.

0. LOCATE — Find Daily-Brief.json in this repo folder. Read it. Confirm it still contains the
   placeholders __BRIEFINGS_FOLDER__ and __ACCOUNTS__. (If it has already been filled in, ask me
   whether to re-run setup with new values or stop.)

1. ASK ME FOR MY FILE PATH — Ask me: "Where should briefings be saved? Give me the full folder path
   (e.g. C:\Users\<me>\OneDrive\Documents\Data SE Briefings)."
   - If I just press enter / say 'default', use $env:OneDrive\Documents\Data SE Briefings.
   - Expand any environment variables I give you to a concrete absolute path.
   - Create the folder if it does not exist.
   - In Daily-Brief.json, replace EVERY occurrence of __BRIEFINGS_FOLDER__ with that absolute path.
     It appears in all six step skip-checks, the research handoff path, AND the Save step — there
     should be 11 occurrences. Use double backslashes in the JSON string (it is JSON-escaped).

2. ASK ME FOR MY ACCOUNTS — Ask me: "What is your book of business? Paste your account names
   separated by semicolons (e.g. Contoso Corp; Fabrikam Inc; Northwind Traders)."
   - If I don't have a list handy, ask whether to (a) pull it from a file I point you to, or
     (b) skip account-matching — in which case set the accounts value to the single word NONE and
     tell me Step 4 ("Match news to accounts") will report "No accounts configured" and do nothing.
   - In Daily-Brief.json, replace the single placeholder __ACCOUNTS__ with my semicolon-separated list.

3. SAVE THE FILLED-IN JSON — Write the updated Daily-Brief.json back to disk. Re-read it and confirm
   it is still valid JSON and that NO __BRIEFINGS_FOLDER__ / __ACCOUNTS__ placeholders remain.

4. ROLE & TAILORING — This automation is currently tuned for a Microsoft Data Solutions Engineer
   (the research queries, curation buckets and report sections are oriented around Microsoft Fabric /
   Power BI / Azure data). Ask me, in these exact words:
   "This is currently tuned for a Data SE automation, would you like to tweak this to better match
   your role? Tell me your role, and what you would like this automation to do differently."
   - If I want to keep it as-is, change nothing and continue.
   - If I give you a role and/or changes, ADAPT the definition to fit: rewrite the Step 1 Google News
     search queries to my domain, adjust the Step 2 curation buckets / section names and ranking
     criteria, update the Step 3 report section headers to match, and re-frame the Step 4
     account-matching themes accordingly. Keep the overall structure, the Step 1 output contract, the
     handoff file, and the PDF/email mechanics unchanged. Summarize what you changed before moving on.

5. CREATE THE AUTOMATION — Import the filled-in Daily-Brief.json as a new automation called
   "Daily Brief" (weekday 8am schedule). Keep it DISABLED for now.

6. PREREQS — Verify Pandoc is installed (if not: winget install JohnMacFarlane.Pandoc), that
   Microsoft Edge is present, and that I'm signed into M365. The env-var paths for Pandoc
   ($env:LOCALAPPDATA\Pandoc\pandoc.exe) and WorkIQ ($env:USERPROFILE\.copilot\bin\workiq.cmd)
   already resolve for my user — no change needed.

7. PERMISSIONS (avoid repeated "Allow" clicks) — This automation runs UNATTENDED on a schedule, so
   if every PowerShell command, file write, web fetch, WorkIQ call and email send needs a manual
   "Allow", the 8am run can stall with nobody there to approve it. Set up one-time pre-approval so
   the scheduled run executes cleanly. Do this BEFORE the test run:
   - Tell me this requires Settings → Permissions, and that "Allow AI to request permission changes"
     must be ON before you can request anything (it is OFF by default). If it is off, ask me to
     enable it (or point me to Settings → Permissions to do so).
   - Then request, via a single consent card each, the MINIMUM the automation needs:
     - autoApproveReadOnly = true (auto-approves read-only shell + read-only tool calls such as the
       Google News / DuckDuckGo web fetches and listing files).
     - Shell allow-list patterns for the specific binaries this automation runs: PowerShell file
       and Get-ChildItem operations on the briefings folder, pandoc, and msedge headless PDF print.
     - Per-server auto-approve for the `filesystem` and `workiq` servers (briefing file writes and
       the M365 email/profile/search calls).
   - Show me exactly what each consent card grants and let me click Allow ONCE per card. Explain that
     this is a one-time setup, not per-run. If I decline, warn me that the scheduled run will likely
     pause on Allow prompts and may not complete unattended.

8. TEST — With the automation still disabled, archive any existing briefing for today, then do one
   manual run. Verify: the .md and a branded PDF land in my briefings folder, and I receive an email
   with a clickable link to the PDF. Show me the result. (If Allow prompts still appear during the
   test, note which tool triggered them so we can widen the pre-approval in step 7.)

9. ENABLE — If the test passes, ask me to confirm, then enable the automation on its weekday-8am
   schedule.

Email delivery already auto-targets whoever is signed into M365 (via m365_get_my_profile), so no
email address needs changing. Report what you changed at each step.
```
