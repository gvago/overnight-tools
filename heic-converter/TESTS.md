# TESTS.md · heic-converter · 2026-07-31

Redesign (Hallmark: warm playful Hebrew, Rubik+Heebo, coral accent, cream paper) + full functional gate.

| Gate | Check | Result |
|---|---|---|
| A | Happy path: real 20MB Apple HEIC (Sonoma.heic) via injected file input | PASS · "מוכן · 2.5 MB" + download link + thumbnail |
| B | Output bytes: JPEG SOI (FF D8 FF) + EOI (FF D9), 2,612,042 bytes | PASS |
| C1 | Wrong file type (doc.txt): Hebrew error, no download link | PASS · "זה לא קובץ HEIC..." |
| C2 | Empty file (0 bytes): Hebrew error, row marked error | PASS · "הקובץ ריק" |
| D | Batch: 2 valid HEICs -> ZIP button appears, ZIP has PK magic, 2 entries, 5,224,294 bytes | PASS |
| E1 | No horizontal scroll, overflow-x: clip | PASS |
| E2 | Mobile: .item flex-wrap at <=414px, headline clamp(), overflow-wrap: anywhere | PASS (CSS verified; visual check desktop-only this run) |
| Design | Vision review: warm/made not generic, RTL correct, no overlaps | PASS |
| Copy | Em dash count in file: 0 | PASS |
| A11y | dropzone role=button + tabindex + keyboard Enter/Space, focus-visible rings, aria-live list | PASS (code-verified) |

Errors are shown per-file; one bad file never blocks the others (verified: mixed batch of 2 good + 2 bad).
