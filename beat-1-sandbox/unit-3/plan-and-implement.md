# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

samanth1111-1111

**Plan comment**

Not posted. I drafted the plan comment (below) and my plan-check skill graded it `accept`,
but I did not post it on issue #53, so there's no comment link.

Draft text:

## Plan for #53

First contribution here. Following up on my [repro report](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53#issuecomment-5864558308). Base commit `f89c06f`, branch `fix/53-pii-parenthesized-phone` on my fork.

**Diagnosis.** The `phone_us` pattern at `safety/pii_scrubber.py:16` has two defects:

1. Its separator class `[-.]` doesn't allow a space, so `(555) 123-4567` fails right after the `)`. I isolated this with `re.findall` on `'(555) 123-4567'` at `f89c06f`:
   ```
   as-is      : []
   no lead \b : []
   space sep  : [('555', '123', '4567')]
   ```
   Dropping the leading `\b` alone changes nothing. Widening to `[-. ]?` makes it match.
2. The leading `\b` can't match in front of `(`. With only the separator widened, the `(` is left behind: `scrub()` gives `'Call me at ([REDACTED] or …'` and `detect()` reports `'555) 123-4567'` at start 12, not `'(555) 123-4567'` at 11 as my repro's Expected section shows. So `\b` doesn't block whether a match happens, but it does change where the match starts.

**Change (one line + test markers):**

```python
"phone_us": r"(?<!\w)(?:\+?1[-. ]?)?(?:\(([0-9]{3})\)|([0-9]{3}))[-. ]?([0-9]{3})[-. ]?([0-9]{4})\b",
```

- Allow a single space at each separator: a literal space, not `\s`, so a number can't match across a newline.
- `(?<!\w)` in place of `\b`, so a match can start on `(` or `+`.
- A balanced `(555)` or a bare `555` for the area code.
- In `tests/unit/test_pii_scrubber.py`, remove the four `xfail(strict=True)` markers for this bug: `test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`, `test_phone_at_start_of_text`.

**Not in scope:** `test_mixed_pii_and_text` keeps its xfail. Its input has no parenthesized number. It fails because `street_address` (line 19) eats ordinary prose, which is a separate bug: `re.findall(street_address, 'I worked at TechCorp for 5 years developing Python applications.', flags=re.IGNORECASE)` returns `['5 years developing Python appl']`. My repro report didn't quote this failure. Running that test alone with `--runxfail` at `f89c06f` gives `assert 'Python' in "...I worked at TechCorp for [REDACTED]ications. ..."`. I'm not changing other patterns, the `scrub()`/`detect()` logic, or CI.

**Test plan.** Re-run my repro snippet. Expected after the fix:

```
'Call me at [REDACTED] or [REDACTED]'
[{'type': 'phone_us', 'value': '(555) 123-4567', 'start': 11, 'end': 25}]
[{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
```

Then `pytest tests/unit/test_pii_scrubber.py -v -m unit` should go from `20 passed, 5 xfailed` to `24 passed, 1 xfailed`, with only `test_mixed_pii_and_text` still XFAIL. That's 24, not the `25 passed` in my repro's Expected section, because the fifth xfail is the street-address bug. A dry run of this pattern, patched in without editing the file, gave exactly that output, and `--runxfail` gave `1 failed, 24 passed`. `test_detect_no_false_positives`, `test_international_phone_redaction`, `test_phone_at_end_of_text` and the SSN tests must stay green.

**Risks / not verified:**
- Space-separated 3-3-4 digit runs like `123 456 7890` will now be redacted.
- Unbalanced parens (`555) 123-4567`) still match partially, as they did before.
- I haven't tested multiple spaces, tabs, or extensions.
- Tested only on Windows 11 / Python 3.13.7.

Disclosure: I used an AI assistant (Claude Code) to help check the regex and draft this plan. The outputs above are from runs on my checkout at `f89c06f`, and I understand what each one shows.

---

## Your branch

**Branch**

fix/53-pii-parenthesized-phone

(Fork: https://github.com/samanth1111-1111/pathreview-ai301-fa26-s1/tree/fix/53-pii-parenthesized-phone, commit `0cb2c83`.)

**Evidence**

Windows 11, Python 3.13.7, pytest 9.1.1, run from the top of my fork's clone. The before is
on `main` at `f89c06f`. The after is on `fix/53-pii-parenthesized-phone` at `0cb2c83`.

Command 1, the repro snippet from my unit 2 report:

```console
$ .venv/Scripts/python -c "
from safety.pii_scrubber import PIIScrubber
s = PIIScrubber()
print(repr(s.scrub('Call me at (555) 123-4567 or 555-123-4567')))
print(repr(s.detect('Call me at (555) 123-4567')))
print(repr(s.detect('Call me at 555-123-4567')))
"
```

Before (`f89c06f`):

```
'Call me at (555) 123-4567 or [REDACTED]'
2026-10-05 00:48:38 [info     ] pii_detected                   count=0 types=0
[]
2026-10-05 00:48:38 [info     ] pii_detected                   count=1 types=1
[{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
```

After (`0cb2c83`):

```
'Call me at [REDACTED] or [REDACTED]'
2026-10-05 00:59:45 [info     ] pii_detected                   count=1 types=1
[{'type': 'phone_us', 'value': '(555) 123-4567', 'start': 11, 'end': 25}]
2026-10-05 00:59:45 [info     ] pii_detected                   count=1 types=1
[{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
```

Command 2, the test file from my unit 2 report:

```console
$ .venv/Scripts/python -m pytest tests/unit/test_pii_scrubber.py -v -m unit
```

Before (`f89c06f`):

```
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_email_redaction PASSED [  4%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_multiple_emails_redacted PASSED [  8%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction XFAIL [ 12%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats XFAIL [ 16%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_international_phone_redaction PASSED [ 20%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_ssn_redaction PASSED [ 24%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_ssn_variations PASSED [ 28%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_street_address_redaction PASSED [ 32%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_text_with_no_pii PASSED [ 36%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_returns_list_of_pii PASSED [ 40%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_email_pii PASSED [ 44%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii XFAIL [ 48%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_ssn_pii PASSED [ 52%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_positions_accurate PASSED [ 56%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_multiple_pii_items PASSED [ 60%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_case_insensitive_email_matching PASSED [ 64%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_complex_email_addresses PASSED [ 68%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text XFAIL [ 72%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_end_of_text PASSED [ 76%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_address_variations PASSED [ 80%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_empty_text PASSED [ 84%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_whitespace_only PASSED [ 88%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_mixed_pii_and_text XFAIL [ 92%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_scrub_idempotent PASSED [ 96%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_no_false_positives PASSED [100%]
======================== 20 passed, 5 xfailed in 0.34s ========================
```

After (`0cb2c83`):

```
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_email_redaction PASSED [  4%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_multiple_emails_redacted PASSED [  8%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction PASSED [ 12%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats PASSED [ 16%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_international_phone_redaction PASSED [ 20%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_ssn_redaction PASSED [ 24%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_ssn_variations PASSED [ 28%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_street_address_redaction PASSED [ 32%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_text_with_no_pii PASSED [ 36%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_returns_list_of_pii PASSED [ 40%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_email_pii PASSED [ 44%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii PASSED [ 48%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_ssn_pii PASSED [ 52%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_positions_accurate PASSED [ 56%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_multiple_pii_items PASSED [ 60%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_case_insensitive_email_matching PASSED [ 64%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_complex_email_addresses PASSED [ 68%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text PASSED [ 72%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_end_of_text PASSED [ 76%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_address_variations PASSED [ 80%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_empty_text PASSED [ 84%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_whitespace_only PASSED [ 88%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_mixed_pii_and_text XFAIL [ 92%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_scrub_idempotent PASSED [ 96%]
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_no_false_positives PASSED [100%]
======================== 24 passed, 1 xfailed in 0.29s ========================
```

The four phone tests went from XFAIL to PASSED. `test_mixed_pii_and_text` stays XFAIL; that's the
separate street-address bug, as scoped. Whole unit suite after the fix
(`.venv/Scripts/python -m pytest tests/unit -q`): `379 passed, 49 xfailed, 1 warning in 10.84s`.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Calibration-only partial run (`--only calib-01,calib-02,calib-03,calib-04 --include-calibration`).
   Calibration packages are never scored, so this run has no agreement score. All 4 matched
   the gold labels (calib-01 accept; calib-02, calib-03 and calib-04 reject).
2. First full run: it crashed before printing a score. Windows' default text encoding couldn't
   handle a character in one package. I re-ran in Python UTF-8 mode (`PYTHONUTF8=1`), with no
   change to any skill file.
3. Full run: **agreement: 19/20 scored items (bar: 18/20: PASS)**, with
   `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.
   This is the run in `eval-run.txt`.

Scores in order: 19/20.

**Package analysis**

pkg-14 (zellij-org/zellij#5174). My rubric said **reject**; the gold label is **accept**.

The skill passed diagnosis, scope, test plan and comms. It failed the required executability
check with this evidence: "Names only crate-level areas; 'exact functions to be pinned in the
PR after tracing' leaves the code site undecided until build time". It also failed the
preferred honesty check, which doesn't affect the verdict.

My executability check reads "names at least one concrete file or code site AND commits to one
chosen approach", and it fails anything that "leaves the real decision until build time". pkg-14
names the area and the approach (fix the reattach handshake) but leaves the exact functions for
later. My check treated that the same as pkg-18's "recover() 'somewhere'". The gold label sees it
differently: the approach is chosen and the plan is honestly scoped down, so a stranger could
still start work. My check can't tell "approach chosen, exact function not yet traced" apart
from "no approach chosen".

**Check rationale**

> | test plan | The candidate plan's test plan, read against the repro-evidence block's steps and its Expected line | Passes if the test re-runs the repro (or a test that encodes it) AND names the specific observable result that shows the fix worked (an output, exit code, color, timing, or passing named test case). A manual re-run of the repro steps passes; the test does NOT need to be automated. Fails if the only test is "run the full test suite", "nothing regresses", "it should work / feel fast", or anything else that names no observable outcome for this bug. | required |

It reads this way because of the class activity. Our group's first version said "pass if the
user has ran tests and it connects to the issue", and the sample rubric said "passes if the plan
adds an automated test". With that, we failed calib-01 because its test was manual (re-run the
repro and watch for the color flip at step 3), but calib-01's correct verdict is ready. The
sample rubric's wording also gave calib-03 a fail for the wrong reason, when calib-03 does plan
a smoke test. So I dropped "automated" and wrote "A manual re-run of the repro steps passes".
The check now asks whether the test names an observable result. The fail list ("run the full
test suite", "nothing regresses") is there so calib-04, whose test plan is only
`cargo test --workspace` "and make sure nothing regresses", still fails. That matches its gold
reject.

**Trade-offs**

The test plan check gives up a good plan whose only test is the project's own suite. calib-04
is the case it changes. Its diagnosis follows the owner's analysis, its scope is one bounded
change in `crates/core/flags/hiargs.rs`, and its comment follows ripgrep's AI policy. But its
test plan is "Run the full test suite (`cargo test --workspace`) and make sure nothing
regresses", so this check fails it and the whole package is held. That matches the gold label
(reject), and the gold note says calib-04 is "defensible both ways". A maintainer might accept
"the suite passes" from a careful contributor, but my rubric won't. I accept that miss, because
a plan that never says what you'll see once this bug is fixed can't show that the fix worked.

Allowing manual tests has a cost too. A plan that only says "re-run the repro and watch the
color flip" passes even though nothing stops the bug coming back later. I accepted that because
calib-01's gold verdict is ready with exactly that kind of test.

Nothing else changed in the scored set. In my full run, the test plan check didn't decide any
disagreement. The only miss, pkg-14, passed it ("5 consecutive SSH reattach cycles with no rgb
strings in any pane") and failed on executability instead.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
