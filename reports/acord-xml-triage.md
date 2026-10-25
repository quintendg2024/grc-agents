# ACORD XML Triage Log

## Summary

| Metric | Count |
|---|---|
| Files reviewed (`acord-xml/samples/`) | 9 |
| Rejected (rows in rejection log, all matched to a file) | 8 |
| Fixed (corrected copy in `acord-xml/fixed/`) | 6 (SUB-1002, 1003, 1004, 1006, 1007, 1008) |
| Needs human review | 1 (SUB-1005) |
| Clean | 1 (SUB-1001, not rejected) |
| Clean but rejected (investigate) | 1 (SUB-1009) |

Originals in `acord-xml/samples/` are unchanged (md5 checked before and after; git status clean for samples).

## Triage table

| File | Rejection message | Actual root cause | Fix applied | Validation | Status |
|---|---|---|---|---|---|
| SUB-1001 | (not in log) | None found | None | Parses; refs match; dates valid | Clean |
| SUB-1002 | Building Number is missing | Second `SubLocation` (Warehouse) has no `id` (pattern 1); `SubLocationRef="L1B2"` points at nothing (pattern 4). One orphan ref and one id-less building, so the match is unambiguous | Added `id="L1B2"` | Parses; all refs match (L1, L1B1, L1B2) | Fixed |
| SUB-1003 | Building Number is missing | `SubLocation id=""` (pattern 2); only ref is `L1B1` and only one building exists | Set `id="L1B1"` | Parses; all refs match | Fixed |
| SUB-1004 | Building Number is missing | `id="L1 B1"` contains a space (pattern 3), so it doesn't match `L1B1` | Changed `id` to `L1B1` | Parses; all refs match | Fixed |
| SUB-1005 | Building Number is missing | Both `SubLocation` elements (Residence hall; Dining and activity center) have no `id`; refs `L1B1` and `L1B2` match nothing (patterns 1 and 4) | None | Original parses; 2 refs unresolved (L1B1, L1B2) | Needs human review |
| SUB-1006 | Unable to process submission | XML does not parse: bare `&` in `Hansen & Sons Hardware` (pattern 5) | Changed to `&amp;` | Parses; all refs match | Fixed |
| SUB-1007 | Building Number is missing | `LocationRef="L2"` but the only location is `L1`; `SubLocationRef="L1B1"` belongs to L1 (pattern 4) | Changed `LocationRef` to `L1` | Parses; all refs match | Fixed (please confirm) |
| SUB-1008 | Invalid policy effective date | `EffectiveDt` is `11/01/2026` (pattern 6) | Changed to `2026-11-01` | Parses; refs match; both dates YYYY-MM-DD | Fixed |
| SUB-1009 | Building Number is missing | None found: ids, refs and dates are valid and the file parses | None | Parses; refs match; dates valid | Clean but rejected (investigate) |

Fixed files each differ from the original by one line (verified with diff).

## Items for a human

- **SUB-1005:** Two buildings lack ids and two refs need them, but nothing in the file says which building is B1 and which is B2. Options: (a) Residence hall = L1B1, Dining and activity center = L1B2 (document order); (b) the reverse. The refs carry only construction codes (MNC for L1B1, F for L1B2), which don't identify a building. No guess made. Confirm with the broker or source system.
- **SUB-1007:** Changed `L2` to `L1` because only one location exists and the building ref is `L1B1`. Alternative: an L2 location was dropped upstream and the building ref is the wrong one. Please confirm.
- **SUB-1008:** Date assumed US month/day/year, so 11/01/2026 became 2026-11-01. Day/month would give 2026-01-11, but `ExpirationDt` of 2027-11-01 (a one-year term) supports November 1.
- **SUB-1009:** No defect in the file. Check the downstream side (stale or duplicate load, a rule on their end, a log row tied to the wrong file) and consider resubmitting unchanged.

## Patterns

- Most common: building id problems (missing, empty, malformed) and the resulting unmatched refs. They caused 4 of 8 rejections (1002, 1003, 1004, 1005), plus 1007 from a bad location ref. All surfaced as "Building Number is missing", so the message hides the cause.
- Single instances: unescaped `&` (1006, reported only as "Unable to process"), non-ISO date (1008).
- Upstream changes: generate building ids in the source system and never export a building without one; build refs from the same id source instead of retyping; validate ids against `L<n>B<n>` and reject spaces or empties before export; use an XML serializer so special characters are escaped; format dates as ISO YYYY-MM-DD at export; run a ref-integrity check pre-submission; ask the downstream system for more specific error messages.
