# Patch Notes

History of fixes applied to `Daily-Brief.json`. These are already incorporated into the
shipped definition — this file documents them for reference.

## 6e — Closing summary must be plain text (applied)

**Problem:** The final step ("Convert, link & email") sometimes returned its closing status as a
structured object instead of plain text. This made the run history show `[object Object]` and made
the runner treat the step as failed, triggering a wasteful duplicate re-run of the whole automation.

**Fix:** Step 6's "6e — Closing summary" section now reads:

> #### 6e — Closing summary (MANDATORY — PLAIN TEXT ONLY)
> Your FINAL message for this step MUST be plain Markdown TEXT — never a JSON object, array, or
> other structured/serialized value. Returning an object makes the run history show "[object Object]"
> and makes the runner treat the step as FAILED, which triggers a wasteful duplicate re-run of the
> whole automation. Do NOT return data; type the status block below literally as your final text
> response:
>
> ```
> ## Step 6 complete
> - Rename: <ok with new filename | fail with reason>
> - PDF: <ok with path | fail with reason>
> - Share link: <ok with url | fail with reason>
> - Email: <ok to which address | fail with reason>
> ```
>
> Never end this step with an empty or internal-only turn. If you have nothing else to add, still
> output the status block above as plain text. At an absolute minimum, output the single line:
> `Step 6 finished without further detail.`

All other steps were left unchanged.

## Step 1 — Output contract + handoff file (applied)

**Problem:** When Step 1 ("Conduct data & AI research") took the PROCEED (research) path, the runner
stored its result as a non-string value and the run history showed `[object Object]`, marking the run
failed. Hardening only the wording of Step 1's text output did not fix it — the trigger was Step 1
returning a large, tool-heavy payload as its result value.

**Fix (two parts):**

1. **Output contract.** Step 1 now must end with a SHORT, one-line plain-text response and must never
   end on a tool call or structured object. A `Research failed: <reason>` fallback line is required if
   research cannot complete.

2. **Handoff file.** The detailed research is no longer passed inline. Step 1 writes the full
   `{headline, canonical_url, publisher, why_matters}` bullet list to
   `__BRIEFINGS_FOLDER__\.handoff\research-<today>.md` and returns only:

   > `Research complete: <N> stories saved to __BRIEFINGS_FOLDER__\.handoff\research-<today>.md. PROCEED to curation.`

   Step 2 ("Curate data news") now reads that handoff file as its input, with a fallback to the inline
   "Output from previous step" text if the file is missing. Keeping the Step 1 result small eliminates
   the `[object Object]` failure on the research path.

Only Steps 1 and 2 were changed for this fix; Steps 3–6 were left unchanged.
