# Overnight run protocol

The overnight cron agent MUST follow this for every comment in queue/comments.json with status="pending".

## Capture layer (footage + audit trail)
For each tool, create inside the tool directory:
1. `RUNLOG.md` — timestamped narrative: the comment, the plan (2-3 lines), each build/test/publish step with its outcome, total elapsed time. Written as it happens, not reconstructed.
2. `TESTS.md` — the full test gate table (see pipeline skill step 2c).
3. `shots/` — screenshots: the working tool desktop, the working tool after test-gate injection (success + error rows visible), and the live URL after deploy.
4. Update `queue/comments.json`: status="done" or "failed", tool_dir, reply (the Hebrew FB reply text).

## Per-comment flow
1. Load skill `overnight-tools-pipeline` and follow it exactly (design pass with hallmark + full test gate are mandatory).
2. Build in /Users/ofri/overnight-tools/<tool-name>/index.html (kebab-case English name).
3. Serve locally (background python3 -m http.server, port 8600+N), test via browser_console file injection, validate output bytes.
4. Commit + push to origin main (this auto-deploys to https://tools.vago.co.il/<tool-name>/).
5. Poll the live URL until it serves the new content (curl grep for a unique string, up to 3 min).
6. Write the Hebrew reply into the queue entry: ✅ מוכן + live link + 2 lines usage + privacy line if client-side + "(נבנה על ידי הסוכן בזמן שגיא ישן. הקוד פתוח: github.com/gvago/overnight-tools)".

## Failure policy
A tool that fails its test gate is NEVER published as working. Mark status="failed", write what broke in RUNLOG.md, write an honest Hebrew reply draft acknowledging it. One failed comment must not stop the remaining comments.

## Morning report
After all comments processed, write queue/morning-report.md: table of comment → tool → status → live URL → elapsed, plus "what broke and why" section. This file is Guy's review artifact and the raw material for the results comment on Facebook.

## Hard rules
- No em dashes in any Hebrew copy.
- Never run commands needing interactive consent (no gh api POST/PATCH/PUT, no repo settings). Only: build files, local server, browser testing, git add/commit/push.
- Client-side single-file tools strongly preferred. If a request truly cannot be client-side, mark status="needs-human" with a note, do not build a half-tool.
- Remove all test input files before committing.
