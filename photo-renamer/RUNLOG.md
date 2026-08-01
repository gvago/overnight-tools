# RUNLOG · photo-renamer (dry run B, comment dry-b-002)

**Comment**: מיכל: מאות סריקות בשם scan0001.jpg, רוצה שמות לפי תאריך הצילום מתוך ה-EXIF, בפורמט 2003-07-14_001.jpg, בלי להתקין תוכנות.

**Plan**: single-file client-side page. Drag & drop batch of JPG/TIFF, exifr (CDN) reads DateTimeOriginal/CreateDate/ModifyDate, chronological sort, per-date running counter, jszip packs the untouched original bytes under the new names. No-EXIF files fall back to file mtime with a clear orange Hebrew note per row and in the summary.

- 15:15 - Test fixtures first: sips (base JPEG from a system image) + uv run piexif to inject DateTimeOriginal into 4 copies (2003-07-14 x2 same day, 1998-12-25, 2011-01-02), verified by piexif readback. Plus noexif.jpg, garbage-bytes fake.jpg, empty.jpg.
- 15:17 - index.html written (~15.6KB), shared design system. window.__pr exposes rename plan for automated verification.
- 15:18 - Local server :8602. First happy-path run FAILED the EXIF check: all 5 files fell back to mtime.
- 15:19 - Root cause isolated with three A/B parse calls: exifr lite build throws on the { pick: [...] } option; the catch block masked it as a fallback. Removed pick, added warning comment. This is exactly the failure class the real-fixture test gate exists for: the tool looked like it worked.
- 15:20 - Re-run: 4/4 EXIF dates read, chronological order correct, same-day counter _001/_002 correct, no-EXIF file flagged orange with mtime date.
- 15:21 - ZIP validated: 5 entries, exact expected names, SHA-256 of every entry identical to the uploaded original bytes (photos untouched, only names change).
- 15:22 - Failure modes: garbage bytes, empty file, .txt all get red Hebrew rows and are skipped; 1 good file in the same batch still renamed and downloadable (error isolation). Desktop vision QA pass.
- 15:23 - Mobile 375px iframe: scrollWidth 369, rows wrap, vision-verified. Fixtures deleted from the tool dir before commit.
- 15:24 - TESTS.md 12/12 PASS. Commit + push + live poll.

**Elapsed (build+test, incl. the exifr bug hunt)**: ~10 min.
