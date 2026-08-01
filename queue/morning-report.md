# Morning report · Overnight tools dry run B (batch of 3)

Run date: 2026-08-01. Agent: Hermes cron, unattended. Repo: gvago/overnight-tools, branch main.

## Summary table

| Comment | Request | Tool | Status | Live URL | Tests | Elapsed |
|---------|---------|------|--------|----------|-------|---------|
| dry-b-001 (אבי) | word search להדפסה מרשימת מילים באנגלית | word-search-generator | done | https://tools.vago.co.il/word-search-generator/ | 16/16 PASS | 6:38 |
| dry-b-002 (מיכל) | שינוי שם לסריקות לפי תאריך EXIF | photo-renamer | done | https://tools.vago.co.il/photo-renamer/ | 12/12 PASS | 8:17 |
| dry-b-003 (יוסי) | בוט סיכום יומי אוטומטי לקבוצות וואטסאפ | none | needs-human | none | not applicable | ~3 min (analysis + reply) |

Total run: about 20 minutes for the full batch, including both deploys and live-URL verification.

## Per-tool notes

### word-search-generator (dry-b-001)
Client-side puzzle generator: paste up to 30 English words, auto-sized grid (up to 24x24), optional diagonals and reversed words, print stylesheet that strips all chrome and prints only the sheet (title, grid, word bank), teacher answer-key mode. Verification centerpiece: an independent JS solver in the test harness scanned the rendered grid in all 8 directions and had to find every word. It did, on the happy path, on a 30-word max load, and across 20 consecutive regenerations (0 failures). Failure modes (Hebrew words, empty input, over limit, over-length word) all produce clear Hebrew errors. Live URL verified end-to-end: generated a grid on production and the solver confirmed it.

### photo-renamer (dry-b-002)
Client-side batch renamer: exifr reads the capture date, files sort chronologically, names become YYYY-MM-DD_NNN.ext with a per-date running counter, jszip packs the original untouched bytes under the new names. No-EXIF files fall back to file date with an orange per-row note plus a summary count. Test fixtures were real JPEGs with EXIF injected locally (sips + piexif, verified by readback before use). ZIP output validated: exact expected entry names, and SHA-256 of every entry identical to the uploaded originals (bytes untouched). Live URL verified with a real EXIF file injected on production: scan0001.jpg became 2003-07-14_001.jpg from EXIF.

### WhatsApp summary bot (dry-b-003): honest no
Marked needs-human, nothing built. The request needs three things this project deliberately refuses: persistent access to a personal WhatsApp account, an always-on server for the daily 18:00 send, and shipping group-chat content to an LLM. WhatsApp also has no official API for personal accounts; the unofficial libraries violate ToS and risk getting the number banned. The reply says exactly that, and offers the honest alternative that IS buildable here: a local summarizer for exported chat .txt files, manual trigger, nothing leaves the machine. This is the honest-failure content for the series.

## What broke and why (the night's most important find)

**exifr's lite build silently broke EXIF reading.** The first photo-renamer happy-path run showed all 5 fixtures falling back to file-date: exifr.parse(file, { pick: [...] }) throws "undefined is not iterable" in the lite CDN build, and the catch block converted that into the mtime fallback for every file. The tool looked perfectly functional in the UI. Without real EXIF fixtures and an assertion on source=exif, this would have shipped as an EXIF renamer that never reads EXIF. Isolated with A/B parse calls, fixed by dropping the pick option (comment left in the code). This is the strongest argument yet for the mandatory real-input test gate.

Minor: sips -s creation cannot inject EXIF on macOS (produced no file); uv run --with piexif was the working path for fixture creation, no venv or pip needed.

## Machinery validation (the point of dry run B)

- Sequential batching: 3 comments processed in order, no state leakage between tools. PASS
- Failure isolation: the infeasible comment produced a needs-human entry and did not disturb the two builds (and the exifr bug inside tool 2 did not stop tool 3). PASS
- Deploy poll (updated rule): contiguous unique string + HTTP 200 fallback. Both deploys detected live at exactly 45s, zero false negatives this time. PASS
- Consent gates hit: none. Only file writes, local servers, browser tests, git push. PASS

## Runtime math for a real 10-comment night

Built tools took 6:38 and 8:17 wall-clock each (build, full test gate, screenshots, deploy, live verification); the infeasible one took about 3 minutes of analysis and reply writing. Call it 8 minutes average per buildable tool at dry-run complexity. Ten comments at this pace: roughly 80 minutes, worst case with a couple of debugging detours like tonight's exifr hunt maybe 2 to 2.5 hours. A 7-hour night leaves headroom of 3x or more, so per-tool complexity can grow substantially (or the test gates can get even stricter) before the night becomes the constraint. The earlier 40-min/tool estimate was very conservative; real bottleneck will be comment triage quality, not build time.

## Suggested FB replies (verbatim, also in queue/comments.json)

See comments.json entries dry-b-001, dry-b-002 (done) and dry-b-003 (needs-human, honest explanation).
