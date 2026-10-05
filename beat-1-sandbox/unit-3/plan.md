# Plan: issue #53 — PII scrubber misses parenthesized US phone numbers

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53
My repro report: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53#issuecomment-5864558308
Base commit: `f89c06f` (main)
Branch: `fix/53-pii-parenthesized-phone`

## Diagnosis

The cause is the `phone_us` regex at `safety/pii_scrubber.py:16`:

```python
"phone_us": r"\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b",
```

It has two defects. Both are confirmed by runs against this pattern.

1. **The separator class `[-.]` has no space.** In `(555) 123-4567` the character after
   `)` is a space, so the match fails there. Isolated with `re.findall` on
   `'(555) 123-4567'` at `f89c06f`:

   ```
   as-is      : []
   no lead \b : []
   space sep  : [('555', '123', '4567')]
   ```

2. **The leading `\b` can't sit in front of `(`.** Both `(` and the character before it
   are non-word characters, so there is no word boundary there. Once the space is
   allowed, the match starts at `555` and leaves the `(` behind. I checked this
   on `f89c06f` by swapping in `[-. ]?` without touching the file:

   ```
   scrub('Call me at (555) 123-4567 or 555-123-4567') -> 'Call me at ([REDACTED] or [REDACTED]'
   detect('Call me at (555) 123-4567') -> [{'type': 'phone_us', 'value': '555) 123-4567', 'start': 12, 'end': 25}]
   ```

   That misses the expected result in my repro, where `detect()` reports
   `'(555) 123-4567'` at start 11. So the leading `\b` doesn't block whether a match
   happens (row 2 above), but it does change where the match starts.

This fits everything the repro showed. The dashed form `555-123-4567` has no space and
no paren, so it already matches. The parenthesized form fails on the space, which is
why `detect()` returns `[]` and the four phone tests fail with
`assert '[REDACTED]' in 'Contact: (555) 123-4567'` and `assert 0 > 0`.

## Scope

**In scope:**
- Fix the `phone_us` pattern so `(555) 123-4567` is matched whole, opening paren
  included.
- Remove the four `xfail(strict=True)` markers that belong to this bug. Because they
  are strict, leaving them in place would make the suite fail with `XPASS(strict)`.

**Not in scope:**
- `test_mixed_pii_and_text` keeps its xfail marker. Its input contains no parenthesized
  phone number. It fails because the `street_address` pattern
  (`safety/pii_scrubber.py:19`) eats ordinary prose:
  `re.findall(street_address, 'I worked at TechCorp for 5 years developing Python applications.', flags=re.IGNORECASE)`
  returns `['5 years developing Python appl']`. That's a different bug, so I'm leaving
  it for its own issue and not fixing it here.
- No changes to the other patterns (`phone_intl`, `ssn`, `email`, `street_address`), to
  `scrub()`/`detect()` logic, or to the unused-`pii_type` weakness noted in the code.
- No refactor, no new phone formats beyond the separators the tests already list, and
  no dependency or CI changes.

## Files to touch

- `safety/pii_scrubber.py`: line 16 only, the `phone_us` entry in `PII_PATTERNS`.
- `tests/unit/test_pii_scrubber.py`: delete the `@pytest.mark.xfail(...)` decorators on
  `test_us_phone_number_redaction` (line 34), `test_us_phone_formats` (line 46),
  `test_detect_phone_pii` (line 130), and `test_phone_at_start_of_text` (line 191).

## Approach

Replace line 16 with:

```python
"phone_us": r"(?<!\w)(?:\+?1[-. ]?)?(?:\(([0-9]{3})\)|([0-9]{3}))[-. ]?([0-9]{3})[-. ]?([0-9]{4})\b",
```

- `[-.]?` → `[-. ]?` at all three separators lets a single space through. That covers
  `(555) 123-4567` and `+1 555 123 4567`. A literal space, not `\s`, so a number can't
  match across a newline.
- The leading `\b` → `(?<!\w)` means "not preceded by a word character." In front of a
  digit it behaves exactly like `\b`, and it also allows a match to start on `(` or `+`.
- `\(?…\)?` → `(?:\(([0-9]{3})\)|([0-9]{3}))` takes a balanced `(555)` or a bare
  `555`, so the whole `(555)` lands inside the match. `scrub()`/`detect()` only use
  `match.group()`, so changing the capture groups affects nothing else.

I dry-ran this pattern on `f89c06f` with a pytest plugin that patched
`PII_PATTERNS["phone_us"]`, without editing the file:

```
scrub('Call me at (555) 123-4567 or 555-123-4567') -> 'Call me at [REDACTED] or [REDACTED]'
detect('Call me at (555) 123-4567') -> [{'type': 'phone_us', 'value': '(555) 123-4567', 'start': 11, 'end': 25}]
detect('Call me at 555-123-4567')   -> [{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
pytest tests/unit/test_pii_scrubber.py -m unit --runxfail -> 1 failed, 24 passed
  (the 1 failure is test_mixed_pii_and_text, the street_address bug)
```

Order of work: (1) edit line 16, (2) re-run the repro snippet, (3) remove the four
markers, (4) run the test plan below.

## Test plan

Re-run my unit 2 repro steps from my repro comment against the fix:

1. Minimal reproduction:
   ```console
   $ .venv/Scripts/python -c "
   from safety.pii_scrubber import PIIScrubber
   s = PIIScrubber()
   print(repr(s.scrub('Call me at (555) 123-4567 or 555-123-4567')))
   print(repr(s.detect('Call me at (555) 123-4567')))
   print(repr(s.detect('Call me at 555-123-4567')))
   "
   ```
   **Before** (from my repro):
   ```
   'Call me at (555) 123-4567 or [REDACTED]'
   []
   [{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
   ```
   **Expected after:**
   ```
   'Call me at [REDACTED] or [REDACTED]'
   [{'type': 'phone_us', 'value': '(555) 123-4567', 'start': 11, 'end': 25}]
   [{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
   ```

2. Test file:
   ```console
   $ .venv/Scripts/python -m pytest tests/unit/test_pii_scrubber.py -v -m unit
   ```
   **Before:** `20 passed, 5 xfailed`.
   **Expected after:** `24 passed, 1 xfailed`. `test_us_phone_number_redaction`,
   `test_us_phone_formats`, `test_detect_phone_pii` and `test_phone_at_start_of_text`
   show `PASSED`, and only `test_mixed_pii_and_text` stays `XFAIL`. My repro's
   Expected line said `25 passed`. That was wrong, because the fifth xfail is the
   separate street-address bug, so 24 is the correct target.

3. Regression guards, which must stay `PASSED`: `test_international_phone_redaction`,
   `test_phone_at_end_of_text`, `test_detect_no_false_positives`, and the SSN tests
   (`test_ssn_redaction`, `test_ssn_variations`, `test_detect_ssn_pii`).

## Risks and unknowns

- **Wider matches.** Allowing a space means any `3 digits␠3 digits␠4 digits` run, such
  as `Order 123 456 7890`, is now redacted as a phone number. For a PII scrubber, a
  false positive is cheaper than leaking a number, but I haven't checked how often that
  shape shows up in real portfolio text.
- **Unbalanced parens.** `555) 123-4567` still matches via the bare `555` branch and
  leaves the stray `)`, and `(555 123-4567` matches from `555` and leaves the `(`.
  The old pattern accepted these mismatched forms too. I'm not changing that here.
- **Not verified:** formats with more than one space or a tab between groups
  (`(555)  123-4567`), and extensions (`x123`). Neither is in the issue or the tests,
  and both stay unmatched/partially matched.
- I have only run this on Windows 11 / Python 3.13.7 / pytest 9.1.1, per my repro
  environment.

## Deviations

The code change held exactly as planned. Commit `0cb2c83` on
`fix/53-pii-parenthesized-phone` changes line 16 of `safety/pii_scrubber.py` to the pattern
in Approach and removes the four xfail markers. That's 1 insertion and 17 deletions across
those two files and nothing else. `test_mixed_pii_and_text` kept its marker, as scoped.

The results matched the test plan. The repro snippet now prints
`'Call me at [REDACTED] or [REDACTED]'` and detects `'(555) 123-4567'` at 11–25. The
scrubber tests went from `20 passed, 5 xfailed` to `24 passed, 1 xfailed`, and the
regression guards stayed `PASSED`. I also ran the whole unit suite, which wasn't in the plan:
`379 passed, 49 xfailed`, with no `XPASS(strict)` failures.

Two process changes, neither to the code:
- My clone was pointing straight at the course repo, so I forked it before building and
  pushed the branch to `samanth1111-1111/pathreview-ai301-fa26-s1`, which is where the plan
  said it would be.
- I have not posted the plan comment on #53, so the thread doesn't have this plan yet.
