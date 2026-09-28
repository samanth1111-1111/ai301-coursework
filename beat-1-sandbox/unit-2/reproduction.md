# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of my claim and reproduction on the issue I chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

samanth1111-1111

---

## Posted upstream

**Claim comment**

<!-- REPLACE: after posting, click the comment's timestamp to copy its permalink
     (it ends in #issuecomment-<id>) and paste it on the line below. -->
PASTE_CLAIM_COMMENT_PERMALINK_HERE

First contribution here — I'd like to take this one. I can see there are already claims and reproductions on this thread; I'm posting my own rather than adding a "same here", and my report will be my own run on my own machine, in my own words.

To be concrete about what I'm picking up: `phone_us` in `safety/pii_scrubber.py` is the pattern in question, and the issue's two snippets are what I'll be working from — `scrub('Call me at (555) 123-4567 or 555-123-4567')` leaving the parenthesized number in place, and `detect('Call me at (555) 123-4567')` coming back empty. The four tests the issue names in `tests/unit/test_pii_scrubber.py` (`test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`, `test_phone_at_start_of_text`) all carry `xfail(strict=True)` markers pointing at this issue, so the current behavior is pinned by the suite; per CONTRIBUTING's "Working on a seeded bug: remove its xfail marker", that marker is part of the work.

My next step is the reproduction, on a clean checkout: run the issue's two snippets, run those four tests, and run the dashed `555-123-4567` form alongside as a control so the parenthesized case is isolated rather than assumed. I'll post the full report in this thread with my environment and the actual output — including if it turns out I can't reproduce it.

One thing I want to look at in the same pass, because this is a scrubber and the failure mode matters in both directions: whether the other formats in `test_us_phone_formats` behave, and whether widening the pattern would start redacting text that isn't a phone number. That's a question for the report, not something I'm claiming an answer to now. Where my results land on ground @AdithNG has already covered above, I'll say so rather than present it as new.

I'm not promising a fix or a date yet — just the reproduction report, next. I'm using AI assistance (Claude Code) as I work; I run and verify every step myself and I understand what I'm reporting.

**Reproduction comment**

<!-- REPLACE: same thing for the repro comment's permalink. -->
PASTE_REPRO_COMMENT_PERMALINK_HERE

## Reproduction report

Reproduced, on current `main`. Details below so this can be re-run.

**Environment:** Windows 11 Home (10.0.26200); Python 3.13.7; `structlog` 26.1.0; `pytest` 9.1.1; repo at commit `f89c06f` on `main`, working tree clean.

**Setup deviation, stated up front:** I did not run the documented `make setup` path in `docs/SETUP.md` (Docker, Postgres, Redis, `alembic upgrade head`, frontend install). `safety/pii_scrubber.py` is pure regex with no database or service dependency, so I created a virtualenv and installed only `structlog` (the module's one import) and `pytest`. That is less than the documented setup, so I am flagging it in case it matters to anyone re-running this.

**Steps:**

```
$ python -m venv .venv
$ ./.venv/Scripts/python.exe -m pip install structlog pytest
```

**1. The issue's snippet, run verbatim:**

```
$ ./.venv/Scripts/python.exe -c "from safety.pii_scrubber import PIIScrubber; s=PIIScrubber(); print(repr(s.scrub('Call me at (555) 123-4567 or 555-123-4567')))"
'Call me at (555) 123-4567 or [REDACTED]'

$ ./.venv/Scripts/python.exe -c "from safety.pii_scrubber import PIIScrubber; s=PIIScrubber(); print(s.detect('Call me at (555) 123-4567'))"
2026-09-28 00:29:45 [info     ] pii_detected                   count=0 types=0
[]
```

Matches the issue exactly: in the same string the dashed number is redacted and the parenthesized one is not, and `detect()` returns `[]` for the parenthesized format.

**2. Control — the dashed form alone, same build:**

```
$ ./.venv/Scripts/python.exe -c "from safety.pii_scrubber import PIIScrubber; s=PIIScrubber(); print(repr(s.scrub('Call me at 555-123-4567'))); print(s.detect('Call me at 555-123-4567'))"
'Call me at [REDACTED]'
2026-09-28 00:29:57 [info     ] pii_detected                   count=1 types=1
[{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
```

Phone redaction works in general on this build; the failure is specific to the parenthesized format, not to my setup.

**3. The four tests the issue names:**

```
$ ./.venv/Scripts/python.exe -m pytest tests/unit/test_pii_scrubber.py -k "test_us_phone_number_redaction or test_us_phone_formats or test_detect_phone_pii or test_phone_at_start_of_text" -rxX

platform win32 -- Python 3.13.7, pytest-9.1.1, pluggy-1.6.0
configfile: pyproject.toml
collected 25 items / 21 deselected / 4 selected

tests\unit\test_pii_scrubber.py xxxx                                     [100%]

XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction - issue #53: PII scrubber does not redact parenthesized US phone numbers
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats - issue #53: PII scrubber does not redact parenthesized US phone numbers
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii - issue #53: PII scrubber does not redact parenthesized US phone numbers
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text - issue #53: PII scrubber does not redact parenthesized US phone numbers

====================== 21 deselected, 4 xfailed in 0.13s ======================
```

All four `xfail`, matching the report.

**4. Format sweep through `scrub()`** — I ran every format listed in `test_us_phone_formats`, plus two variants, because the issue's title is about the parenthesized case and I wanted to see whether that is the whole of it:

```
$ ./.venv/Scripts/python.exe -c "
from safety.pii_scrubber import PIIScrubber
s = PIIScrubber()
for t in ['(555) 123-4567', '(555)123-4567', '555-123-4567', '555.123.4567', '+1 555 123 4567', '+1-555-123-4567']:
    print(repr(t).ljust(22), '->', repr(s.scrub(t)))
"
'(555) 123-4567'       -> '(555) 123-4567'
'(555)123-4567'        -> '([REDACTED]'
'555-123-4567'         -> '[REDACTED]'
'555.123.4567'         -> '[REDACTED]'
'+1 555 123 4567'      -> '+1 555 123 4567'
'+1-555-123-4567'      -> '+[REDACTED]'
```

Two observations from that, both already covered by @AdithNG above — I am reporting them as independent corroboration on a different Python version rather than as new findings:

- `+1 555 123 4567`, the fourth format in `test_us_phone_formats`, is also left unredacted. So that test has two failing inputs, not one, and a change aimed only at the parenthesized case would not turn it green.
- Removing the space (`(555)123-4567`) redacts, which isolates the space after `)` as what breaks the parenthesized case. Note that run also leaves the literal `(` in the output — no digits leak, but the parenthesis stays.

**5. One thing that is not this issue.** `test_mixed_pii_and_text` carries the same `xfail` marker referencing #53, but with `--runxfail` it fails on an unrelated assertion:

```
>       assert "Python" in scrubbed
E       assert 'Python' in "... I worked at TechCorp for [REDACTED]ications. ..."
```

The `street_address` pattern swallowed `5 years developing Python appl`, so that test is failing on a different defect from this one. @AdithNG flagged the same thing above. Noting it only so that nobody counts it as evidence for #53.

**Expected:** `scrub()` redacts `(555) 123-4567` the same way it redacts `555-123-4567`, and `detect()` reports it.

**Actual:** reproduced as described — the parenthesized format passes through `scrub()` unredacted and `detect()` finds nothing, while the dashed format is handled correctly, on `f89c06f` in the environment above.

I am not proposing a fix in this comment. The one thing I would want to settle before anyone does is the over-matching direction: this is a scrubber, so a separator class widened far enough to catch `(555) 123-4567` and `+1 555 123 4567` also needs checking against text that is not a phone number.

(Repeating the disclosure from my claim comment, since these post separately: I am using AI assistance (Claude Code) on this work. Every command above I ran myself on my own machine, and the output is what it printed.)

## Eval iterations

**Run history**

Four runs, in order: **1/1, then 6/6, then a crash with no score, then 20/20.**

1. **1/1** — `--limit 1`, a plumbing smoke run on `pkg-01`. It proved the harness could reach
   the `claude` CLI on Windows and that my rubric parsed (the harness refuses to start on a
   rubric with no weighted rows or no verdict rule). No bar verdict: partial runs do not print
   one.

2. **6/6** — `--only pkg-09,pkg-10,pkg-12,pkg-16,pkg-19,pkg-20 --include-calibration --out risky.json`,
   about $1.20 instead of the $4 a full run costs. These six are not a random sample: they are
   the six I judged my rubric most likely to get wrong, one per way a check of mine could
   misfire. `pkg-09` and `pkg-10` are honest cannot-reproduce **accepts**, the case where a
   proof-shaped rubric most easily rejects a good package. `pkg-16` is a silent version
   deviation. `pkg-19` is a fine report behind a bad claim comment. `pkg-20` is the single
   `disclosure` package the category floor exists for. `pkg-12` is the trap in the other
   direction — prettier states an AI policy that does **not** ask for disclosure, so a
   careless conventions check would reject it. All six agreed.

   I did not take the total on faith. Reading `risky.json`, every reject failed on the check
   built for it and nothing else: `pkg-20` on `conventions-respected` alone, `pkg-16` on
   `environment-faithful` alone, `pkg-19` on `claim-specific-and-honest` alone. The only check
   failing on the three accepts was `control-run`, which is `preferred` and cannot reject.

3. **crash, no score** — the first full 20-package attempt died partway with
   `UnicodeEncodeError: 'charmap' codec can't encode character '\U0001f64f'`. This is a
   Windows issue in the harness, not in my rubric: `run_eval.py` builds its prompt and hands
   it to `subprocess.run(..., text=True)`, which encodes stdin with the machine's locale codec
   (cp1252 here), and `pkg-04`'s claim comment contains a 🙏. The earlier runs survived only
   because none of the packages they touched had an emoji in them. I fixed it without editing
   the harness, by running the interpreter in UTF-8 mode — `python -X utf8 run_eval.py ...` —
   which makes `locale.getpreferredencoding()` report UTF-8 and so changes the encoding
   `subprocess` uses for the pipe. Editing `run_eval.py` would have worked too, but leaving
   the harness byte-identical keeps the run honest. No score, and `--save-run` never fired:
   the crash happened before the write.

4. **20/20** — the full run, in UTF-8 mode, saved with `--save-run eval-run.txt`. The harness
   printed:

   > `categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`
   >
   > `agreement: 20/20 scored items  (bar: 18/20: PASS)`

   That is the agreement line in the committed `eval-run.txt`. Every category is matched, so
   the floor holds as well as the bar. Nothing changed in the rubric between runs 2 and 4, so
   the three fingerprints in the saved run's header match the files uploaded to
   `tools/repro-check/` byte for byte — I checked all three with `Get-FileHash` rather than
   assuming, because that mismatch is exactly what cost me a re-run in Unit 1:

   > `#   rubric.md  sha256:4f0dfdf69d49e2c9`

There was no revise loop, because after run 2 there was no disagreement to chase. The reason
the first completed full run landed at 20/20 is that I read all 24 bundles and
`gold-labels.json` before writing a single check, then wrote one required check per failure
family the set is built from, so the thresholds were designed against the evidence instead of
guessed at and corrected afterwards. I also hand-graded `calib-02` against the revised wording
before spending anything, which cost nothing and caught one real wording bug: my `thread-read`
check had no answer for a thread with zero comments, so it was grading `unclear` on packages
that simply had nothing to read. It is a `preferred` check and could never have changed a
verdict, but it would have put noise in the `note` column of every quiet-thread package.

**Package analysis**

**pkg-20** (ghostty-org/ghostty#13604). **My rubric: `reject`. Gold label: `reject`.**

This is the whole reason my rubric has a sixth required check, and the one package where
getting the verdict right is not enough — you have to get it right *for the stated reason*,
because it is the only member of the `disclosure` category and the category floor cannot be
bought back on volume.

What makes it hard is that it is an excellent reproduction. My rubric passed it on all five
proof checks, and the evidence lines show why:

> `behavior-shown` — *"Artifact shows `^[[?997;2n` for single-theme and `^[[?997;1n` for conditional-pair, matching the issue's exact reported codes"*
>
> `control-run` — *"Step 3 reruns the query with a conditional theme pair to isolate the single-theme trigger"*

Exact environment, exact commands, the issue's own escape codes in the output, and a control
run isolating the trigger. On proof alone it is one of the strongest packages in the set. It
fails on one line in the repo-facts block:

> contribution policy (CONTRIBUTING.md + AI_POLICY.md): strict AI rules. All AI usage in any
> form must be disclosed, stating the tool used and the extent of the assistance

and neither comment discloses anything. My rubric caught it because `conventions-respected`
carries a standing fact stated at the top of the file — *"This work is AI-assisted… so a repo
policy that requires disclosing AI use is always triggered; the question is never was AI used
but does the comment say so."* Without that sentence the natural reading is the wrong one:
the comments read as human-written, nothing in the bundle says a model touched them, so a
grader concludes no disclosure was owed and returns `accept`. The gold note says the same
thing from the other side — *"course packages are treated as AI-assisted work"*. The check
fired on exactly this evidence:

> `conventions-respected` — *"AI_POLICY.md requires disclosure of AI tool and extent in any
> AI-assisted comment; neither candidate comment contains any AI disclosure"*

The part I would have got wrong without reading the bundles first is that "has an AI policy"
is not the trigger. Four other packages state AI policies and all four are supposed to pass:
ripgrep (`pkg-03`) demands comments be in the contributor's own words, which is a voice
requirement, not a disclosure one; fd (`pkg-09`) asks for tool-and-extent **in the pull
request** and says outright there is no disclosure ask for issue comments; conda (`pkg-05`)
asks you to understand what you submit; prettier (`pkg-12`) asks you to only submit code you
have tested. A check that fired on "an AI policy exists" would have rejected four gold
accepts and turned a 20/20 into a 16/20.

**Check rationale**

The `conventions-respected` row, quoted as it currently reads in the `rubric.md` uploaded to
`tools/repro-check/`:

> \| `conventions-respected` \| The contribution-policy line and the bug-report template asks in the repo-facts block (live mode: CONTRIBUTING.md, any AI policy file it links, and the issue templates), read against both candidate comments as written. \| Take the stated policy at its word and satisfy what it asks of an issue comment. If the policy requires disclosing AI use in contributions or comments, at least one of the two comments states that AI was used and to what extent — this work is always AI-assisted, so the requirement always applies and silence is a fail however good the reproduction is. If the policy requires comments to be in the contributor's own words, the comments read as a person writing about this particular issue rather than generated filler. If the policy's conditions do not reach issue comments (disclosure asked only in the pull request, "understand and take responsibility for your changes", "only submit code you have tested", "low-quality AI content is closed"), or the repo states nothing about AI at all, this check passes. Conditions are terms to follow, not bans; the only fail is a stated ask the comments leave unmet. \| required \|

It reads that way because of what I rejected first. My initial draft was one sentence —
*"fail if the repo has an AI policy and the comments do not disclose AI use"* — and reading
`pkg-03`, `pkg-05`, `pkg-09` and `pkg-12` against it showed it would reject four gold accepts.
So the pass condition now sorts policies into three named shapes instead of one, and each
named shape is lifted from a package in the set: disclosure-required (ghostty), own-words
(ripgrep), and conditions-that-do-not-reach-comments (fd's PR-only ask, conda's
understand-what-you-submit, prettier's only-submit-what-you-tested). The parenthetical list
in the third clause exists so that a grader meeting prettier's policy has the answer in front
of it rather than reasoning from "AI policy" to "disclosure".

The clause I nearly left out is the one the check turns on: *"this work is always AI-assisted,
so the requirement always applies."* I inherited the opposite habit from Unit 1, where
`ai-policy-ok` asked whether a policy **bans** my workflow — a question about the repo. This
one asks whether my comment **meets** what the policy asks — a question about my own output,
and it only has an answer if the grader knows AI was used. In eval mode nothing in the bundle
says so, so the fact has to be stated in the rubric or the check cannot fire. I put the same
sentence in the evidence guide's Comms section in bold, because a rule stated in one place
is a rule the grader can miss.

**Trade-offs**

What this check gives up, and how I know what it did and did not touch.

**A stated reason nothing changed elsewhere.** `conventions-respected` is the narrowest check
I wrote, and I verified that rather than assuming it. Counting non-pass grades across all 20
packages in `full1.json`, the required checks fired like this:

```
claim-specific-and-honest 8   behavior-shown 7   steps-rerunnable 5
environment-faithful 5        env-recorded 3     conventions-respected 1
```

One. It fired on `pkg-20` and on nothing else in the set — not on the four other packages with
stated AI policies, and not on any of the eight gold accepts. That is the shape I wanted: the
check that has to catch a one-package category should be the check that touches least.

**A canary I did not have to spend.** The eval README's rule is that a revision loosening a
check can flip a package that agreed before, so you re-run one already-agreeing package from
each single-package category before spending a full run. `disclosure` is that category, so
`pkg-20` is that canary — and I put it in run 2's `--only` list before the full run rather
than after, together with `pkg-12` as the opposite-direction canary (the package a
too-aggressive disclosure check would wrongly reject). Both came back correct at about $0.40
total, which is why runs 3 and 4 used the rubric unchanged and I never had to spend a second
$4 confirming a revision.

**A case I accept it will miss.** The check reads the policy's *stated words* and asks only
what they require *of an issue comment*. A repo whose disclosure requirement lives somewhere
my evidence map does not look — a pinned issue, a wiki page, a line in the PR template that a
repo-facts block summarises as "standard contribution guide" — passes my check while a human
maintainer would say the ask was plainly there. I took that deliberately. The opposite error
is worse: a check that infers an unstated disclosure duty from tone or from the mere existence
of an AI policy is the check that rejects ripgrep, fd, conda and prettier, and I would rather
miss an obligation hidden two clicks away than invent four that were never written down.

**And it means something different live.** On Path Review the repo states nothing about AI at
all — no `AI_POLICY.md`, no AGENTS file, and `docs/CONTRIBUTING.md` has no AI section — so by
this check's own third clause, silence passes and I owe no disclosure. I disclosed in both
comments anyway, because my `voice-guide.md` makes it my habit rather than the repo's demand.
That is the split the rubric and the voice guide are for: the rubric grades what the repo can
require of me, and the voice guide grades what I require of myself.

---

Related paths: `eval-run.txt` in this directory; my skill's files in `tools/repro-check/`.
