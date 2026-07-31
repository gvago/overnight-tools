# TESTS · pdf-merger · dry run A (2026-08-01)

Local server: `python3 -m http.server 8601` from the tool dir. All injection done headlessly via browser_console (DataTransfer + File + change event). Test inputs: 3 real PDFs generated locally with a no-deps Python script (valid xref, verified with `file`: 1 + 2 + 1 pages), one .txt, one empty .pdf, one garbage file renamed to .pdf. Test files removed before commit.

| # | Check | Input | Expected | Result |
|---|-------|-------|----------|--------|
| 1 | Happy path load | 3 real PDFs (1+2+1 pages) | 3 rows, green status with page counts | PASS: "✓ 1 עמוד", "✓ 2 עמודים", "✓ 1 עמוד"; merge button appears |
| 2 | Reorder | move 3rd file up | list order 1,3,2 | PASS: orderAfter = [receipt-1, receipt-3, receipt-2] |
| 3 | Merge output bytes | merge the 3 files | valid PDF, 4 pages | PASS: header `%PDF-`, `%%EOF` present, 1189 bytes, re-parsed with pdf-lib: 4 pages, filename merged-2026-07-31.pdf |
| 4 | Page order in output | merge after reorder | 4 pages in chosen order | PASS: page count matches 1+1+2 in reordered sequence |
| 5 | Wrong file type | .txt file | Hebrew error, no hang | PASS: "זה לא קובץ PDF. הכלי הזה מאחד רק קבצי PDF" |
| 6 | Empty file | 0-byte .pdf | Hebrew error | PASS: "הקובץ ריק" |
| 7 | Garbage renamed to .pdf | text bytes as fake.pdf | Hebrew error (magic-byte check) | PASS: "זה לא קובץ PDF תקין" |
| 8 | Error isolation | 3 bad files after 3 good | good entries unaffected | PASS: 3 valid entries remained, error rows have no reorder arrows |
| 9 | Remove button | remove one valid entry | entry gone, merge still shown for remaining 2 | PASS |
| 10 | Mobile viewport | 375px iframe, 2 files loaded | no horizontal scroll, rows usable | PASS: scrollWidth 369 ≤ 375, buttons not overlapping, merge full width (vision-verified) |
| 11 | Design/RTL eyeball | desktop screenshot | shared cream/coral system, correct RTL | PASS: vision check, no glitches; matches heic-converter brand |
| 12 | No em dashes | grep "—" index.html | 0 | PASS |

Not tested (documented limitation): password-protected PDFs. Code path exists (pdf-lib throws on encrypted docs, mapped to Hebrew message "הקובץ מוגן בסיסמה") but no encrypted fixture was generated tonight.

**Gate verdict: 12/12 PASS. Tool approved for publish.**
