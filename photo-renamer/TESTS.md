# photo-renamer · Test gate (dry run B, comment dry-b-002)

Run: 2026-08-01, headless via browser_console file injection against local http.server :8602.
Fixtures: real JPEGs built locally with sips + piexif (EXIF DateTimeOriginal injected and verified by readback before the test), plus a no-EXIF copy, a garbage-bytes .jpg and an empty file. All fixtures deleted before commit.

| # | Check | Input | Expected | Result |
|---|-------|-------|----------|--------|
| 1 | Happy path batch | 5 scans (scan0001..0005): EXIF dates 2003-07-14 10:30, 2003-07-14 16:45, 1998-12-25, 2011-01-02 + 1 no-EXIF (mtime 2020-05-05) | chronological rename with per-date counters | PASS: 1998-12-25_001, 2003-07-14_001, 2003-07-14_002, 2011-01-02_001, 2020-05-05_001 |
| 2 | Same-day counter | two photos dated 2003-07-14 | _001 and _002 by time order | PASS |
| 3 | EXIF vs fallback flags | 4 EXIF + 1 no-EXIF | 4 marked exif (green), 1 marked mtime (orange warning) | PASS: summary says 1 file used file date, row flagged |
| 4 | ZIP output entry names | download all | 5 entries with the new names | PASS: entryCount=5, names exact |
| 5 | Image bytes unchanged | SHA-256 of each ZIP entry vs original upload | identical hashes | PASS: 5/5 hashMatch=true, all JPEG magic FF D8 |
| 6 | Failure: garbage bytes as .jpg | text bytes named garbage.jpg | Hebrew error row, skipped | PASS: "הקובץ לא נראה כמו תמונה תקינה" |
| 7 | Failure: empty file | 0-byte empty.jpg | Hebrew error row | PASS: "הקובץ ריק" |
| 8 | Failure: wrong type | notes.txt | Hebrew error row | PASS: "זה לא קובץ תמונה נתמך" |
| 9 | Error isolation | 1 good + 3 bad in one batch | good file renamed, ZIP still offered | PASS: 1 valid entry, dlAll visible, 3 red rows |
| 10 | Mobile viewport | 375px iframe, 3 loaded rows | no horizontal scroll, rows wrap | PASS: scrollWidth 369 <= 375, vision-verified usable |
| 11 | Design/RTL eyeball | desktop screenshot with mixed rows | shared cream/coral system, statuses distinguishable | PASS: vision check, matches brand |
| 12 | No em dashes | grep "—" index.html | 0 | PASS |

12/12 PASS.

## Bug found and fixed during the gate
exifr lite build throws "TypeError: undefined is not iterable" when called with the { pick: [...] } option, which the catch block silently converted into an mtime fallback for EVERY file (first happy-path run: 5/5 fell back, 0 EXIF reads). Without a real-EXIF test gate this would have shipped as a tool that never actually reads EXIF. Fix: plain exifr.parse(file) with an explanatory comment. Re-run: 4/4 EXIF dates read correctly.
