# ACORD XML Triage Log

9 files reviewed, 8 rejected. Originals in `acord-xml/samples/` are unchanged; corrected copies are in `acord-xml/fixed/`.

**Counts:** Fixed 6 (SUB-1007 flagged for confirmation), Needs human review 1, Clean 1, Clean but rejected 1.

| File | Rejection message | Actual root cause | Fix applied | Status |
|---|---|---|---|---|
| SUB-1001 | Not in the rejection log | None found | None | Clean |
| SUB-1002 | Building Number is missing | Second `SubLocation` (Warehouse) has no `id`, so `SubLocationRef="L1B2"` points at nothing | Added `id="L1B2"` | Fixed |
| SUB-1003 | Building Number is missing | `SubLocation id=""` is empty; the only ref is `L1B1` | Set `id="L1B1"` | Fixed |
| SUB-1004 | Building Number is missing | `id` is `"L1 B1"` (space), so it doesn't match `L1B1` | Changed `id` to `L1B1` | Fixed |
| SUB-1005 | Building Number is missing | Both `SubLocation` elements (Residence hall, Dining and activity center) lack an `id`; refs `L1B1` and `L1B2` are unmatched | None | Needs human review |
| SUB-1006 | Unable to process submission | XML doesn't parse: bare `&` in `Hansen & Sons Hardware` | Changed to `&amp;` | Fixed |
| SUB-1007 | Building Number is missing | `LocationRef="L2"` but the only location is `L1`; building ref `L1B1` belongs to L1 | Changed ref to `L1` | Fixed (please confirm) |
| SUB-1008 | Invalid policy effective date | `EffectiveDt` is `11/01/2026`, not `YYYY-MM-DD` | Changed to `2026-11-01` | Fixed |
| SUB-1009 | Building Number is missing | None found; ids, refs and dates are valid and the XML parses | None | Clean but rejected (investigate) |

## Validation

PowerShell `[xml]` confirmed that each fixed file parses, that every `LocationRef` and `SubLocationRef` matches an existing `id`, and that no `SubLocation` has an empty or missing `id`. All six fixed files passed. Each fix is a single-line change, checked by diff against the original, and md5 hashes confirmed the originals were not modified.

## Items needing a human

- **SUB-1005:** Two buildings have no id and two refs need ids, and the file doesn't say which building is B1 and which is B2. Document order would give Residence hall = `L1B1` (construction code MNC) and Dining and activity center = `L1B2` (construction code F). The refs carry only construction codes, so no guess was made. Confirm with the broker or source system.
- **SUB-1007:** `L2` was changed to `L1` because only one location exists and the building ref is `L1B1`. An L2 location may instead have been dropped upstream. Please confirm.
- **SUB-1008:** The date was read as month/day. The agent's stated reason ("11 can't be a day") was wrong, since `11/01/2026` could be January 11. The file's `ExpirationDt` of `2027-11-01` confirms November 1.
- **SUB-1009:** The file has no defect. Check the downstream side (stale or duplicate load, a rule on their end, or a log row for the wrong file). Resubmitting unchanged may be worth a try.

## Patterns

- Building-id defects were the most common cause (SUB-1002 to 1005), plus one broken location ref (SUB-1007). "Building Number is missing" also appeared on SUB-1009, where the file is clean.
- Other causes: one unescaped `&` (SUB-1006) and one non-ISO date (SUB-1008).
- Prevention:
  - Generate building ids programmatically as `L<n>B<m>`; never let users type them.
  - Run a pre-submission check that every ref matches an id, with no empty or missing ids and no spaces.
  - Use a real XML serializer so special characters are escaped.
  - Emit dates in ISO format only.
  - Make downstream error messages specific. "Building Number is missing" covered four different causes, and "Unable to process submission" hid a parse error.
