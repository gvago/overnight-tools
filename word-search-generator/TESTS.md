# word-search-generator · Test gate (dry run B, comment dry-b-001)

Run: 2026-08-01, headless via browser_console against local http.server :8601.

| # | Check | Input | Expected | Result |
|---|-------|-------|----------|--------|
| 1 | Happy path | real 12-word weekly vocab list (apple..morning), title "Unit 3 Vocabulary", all 8 directions on | grid generated, success message | PASS: 14x14 grid, ok message, title rendered |
| 2 | Output validity (solver) | independent JS solver scans rendered grid in all 8 directions for every word | 12/12 words findable | PASS: allFound=true, missing=[] |
| 3 | Grid integrity | every cell content | single A-Z letter, no empty/multi-char cells | PASS: badCells=0 |
| 4 | Placement cross-check | declared placements vs rendered grid letters | 0 mismatches | PASS |
| 5 | Stress: 20 regenerations | click generate x20, solver re-run each time | all words findable in all 20 grids | PASS: 0 failures |
| 6 | Word bank | rendered pills | 12 pills, sorted, uppercase | PASS: bankCount=12 |
| 7 | Failure: Hebrew words | "שלום, תפוח" | Hebrew error, no crash | PASS: red error names the offending words |
| 8 | Failure: empty input | whitespace only | Hebrew error | PASS |
| 9 | Failure: >30 words | 35 words | Hebrew error with count and max | PASS: "יותר מדי מילים (35)" |
| 10 | Failure: word >18 letters | 26-letter string | Hebrew error | PASS |
| 11 | Comma-separated input | "cat, dog, bird, fish" | parsed, 8x8 grid, all findable | PASS |
| 12 | Max load | exactly 30 three-letter words | grid generated, 30/30 findable | PASS: 15x15, missing=[] |
| 13 | Mobile viewport | 375px iframe, generated 14x14 grid | no horizontal scroll, grid fits | PASS: scrollWidth 369 <= 375, table 287px, vision-verified usable |
| 14 | Design/RTL eyeball | desktop screenshot | shared cream/coral system, correct RTL | PASS: vision check, matches heic-converter/pdf-merger brand |
| 15 | Print CSS | @media print rules | input card/header/footer hidden, sheet-only, key printable | PASS by inspection: print stylesheet hides all chrome, print-color-adjust set for key |
| 16 | No em dashes | grep "—" index.html | 0 | PASS |

16/16 PASS. Teacher answer key mode (solution highlight) included via checkbox, verified visually in desktop shot.
