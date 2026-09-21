# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53

**Verdict output**

Live-mode run of `issue-select` on three candidates from the Path Review repo, run
2026-09-20 with the same `rubric.md` uploaded to `tools/issue-select/`. The chosen
issue, #53, is the first entry in the read-out and the first object in the closing
JSON block, with verdict `accept`.

````
**Scope check:** all three URLs are in `codepath/pathreview-ai301-fa26-s1`, the repo named in `scope.md`. In scope. House rule noted (classmate claim comments don't block) — it didn't come into play, since none of these three has any comment.

**Repo facts gathered** (today 2026-09-20): `archived: no`; last 5 default-branch commits 2026-09-16 ×3 and 2026-08-24 ×2, all authored by Andrew Burke; latest release: none published; no `CONTRIBUTING.md`, `AI_POLICY.md`, or `AGENTS.md` anywhere in the repo; zero pull requests open or closed; maintainer first-response sample — #68 no maintainer comment, #69 no maintainer comment, #73 no comments, #52 6.0 days by Aburke225/COLLABORATOR, #43 6.0 days by Aburke225/COLLABORATOR.

**Accepted, in fit order**

**1. #53 — PII scrubber fails to redact parenthesized US phone numbers** — the best fit: a pure-Python regex fix in `safety/pii_scrubber.py` that names four already-failing tests, so I can run pytest red, fix, and run it green before opening the PR — exactly the habit my fit profile says I want to build, with no toolchain beyond Python.

| check | grade | evidence |
|---|---|---|
| repo-alive | pass | `archived: no`; newest default-branch commit 2026-09-16, 4 days before today |
| repo-in-use | pass | no releases published, so the commit clause decides: 5 of the last 5 commits are within 180 days and human-authored |
| scope-bounded | pass | one regex pattern in one file; 0 linked PRs; not a tracking list |
| maintainer-acknowledged | pass | opened by Aburke225 (COLLABORATOR); labels `bug, good first issue, safety, tier-1` |
| unclaimed | pass | `assignees: none`; the repo has no PRs at all; 0 comments, so no claim |
| ai-policy-ok | pass | no CONTRIBUTING.md or AI policy file in the repo — silence passes |
| maintainer-responsive *(preferred)* | pass | #52 and #43 each drew a COLLABORATOR reply in 6.0 days, inside the 30-day threshold |
| newcomer-signposted *(preferred)* | pass | `good first issue` label, and the body names `pii_scrubber.py` plus 4 test names |
| issue-fresh *(preferred)* | pass | opened 2026-09-10, 10 days ago |

**2. #73 — README and `.env.example` disagree about which LLM API key to set** — documentation work my profile ranks high and a stated 1–2 hour effort, but it teaches me less about the codebase than #53 and there is no test to turn green.

| check | grade | evidence |
|---|---|---|
| repo-alive | pass | same repo facts: `archived: no`, newest commit 2026-09-16 |
| repo-in-use | pass | no releases; 5 of 5 recent commits human-authored within 180 days |
| scope-bounded | pass | two files named in the body, one change; 0 linked PRs |
| maintainer-acknowledged | pass | opened by Aburke225 (COLLABORATOR); labels `bug, good first issue, docs, tier-1` |
| unclaimed | pass | `assignees: none`; no PRs in the repo; 0 comments |
| ai-policy-ok | pass | no stated contribution policy |
| maintainer-responsive *(preferred)* | pass | 6.0-day first response on #52 and #43 |
| newcomer-signposted *(preferred)* | pass | `good first issue` label; body lists `README.md` and `.env.example` |
| issue-fresh *(preferred)* | pass | opened 2026-09-16, 4 days ago |

**3. #61 — Health check DB probe passes a raw SQL string under SQLAlchemy 2.x** — accepted and probably the smallest diff of the three (wrap the string in `sqlalchemy.text()`), but it ranks last on fit: reproducing and verifying it wants a running stack and database, and my profile ranks down issues I cannot check locally.

| check | grade | evidence |
|---|---|---|
| repo-alive | pass | `archived: no`; newest default-branch commit 2026-09-16 |
| repo-in-use | pass | no releases; commit clause satisfied by 5 of 5 recent human commits |
| scope-bounded | pass | one call site in `api/routes/health.py`; 0 linked PRs |
| maintainer-acknowledged | pass | opened by Aburke225 (COLLABORATOR); labels `bug, good first issue, api, tier-1` |
| unclaimed | pass | `assignees: none`; no PRs in the repo; 0 comments |
| ai-policy-ok | pass | no stated contribution policy |
| maintainer-responsive *(preferred)* | pass | 6.0-day first response on #52 and #43 |
| newcomer-signposted *(preferred)* | pass | `good first issue` label; body names `api/routes/health.py` |
| issue-fresh *(preferred)* | pass | opened 2026-09-10, 10 days ago |

**Rejected:** none.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: no; newest default-branch commit 2026-09-16, 4 days before today"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "no releases published; 5 of last 5 default-branch commits within 180 days, authored by Andrew Burke (human)"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "one regex pattern in safety/pii_scrubber.py; 0 linked PRs; not a tracking list"},
      {"name": "maintainer-acknowledged", "grade": "pass", "evidence": "opened by Aburke225 (COLLABORATOR) with labels bug, good first issue, safety, tier-1"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; the repo has no pull requests at all; 0 comments"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "no CONTRIBUTING.md, AI_POLICY.md or AGENTS.md in the repo; silence passes"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#52 and #43 each drew a COLLABORATOR first reply in 6.0 days"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "good first issue label; body names pii_scrubber.py and 4 failing tests"},
      {"name": "issue-fresh", "grade": "pass", "evidence": "opened 2026-09-10, 10 days before today"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/73",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: no; newest default-branch commit 2026-09-16"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "no releases published; 5 of last 5 default-branch commits within 180 days, human-authored"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "two files named (README.md, .env.example) inside one change; 0 linked PRs"},
      {"name": "maintainer-acknowledged", "grade": "pass", "evidence": "opened by Aburke225 (COLLABORATOR) with labels bug, good first issue, docs, tier-1"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no pull requests in the repo; 0 comments"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "no stated contribution policy in the repo"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#52 and #43 each drew a COLLABORATOR first reply in 6.0 days"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "good first issue label; body lists the two files to edit"},
      {"name": "issue-fresh", "grade": "pass", "evidence": "opened 2026-09-16, 4 days before today"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: no; newest default-branch commit 2026-09-16"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "no releases published; 5 of last 5 default-branch commits within 180 days, human-authored"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "one call site in api/routes/health.py; 0 linked PRs"},
      {"name": "maintainer-acknowledged", "grade": "pass", "evidence": "opened by Aburke225 (COLLABORATOR) with labels bug, good first issue, api, tier-1"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no pull requests in the repo; 0 comments"},
      {"name": "ai-policy-ok", "grade": "pass", "evidence": "no stated contribution policy in the repo"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#52 and #43 each drew a COLLABORATOR first reply in 6.0 days"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "good first issue label; body names api/routes/health.py"},
      {"name": "issue-fresh", "grade": "pass", "evidence": "opened 2026-09-10, 10 days before today"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

Three eval runs, in order: **2/2, then 20/20, then 20/20.**

1. **2/2** — a smoke run on two items, `--only issue-09,issue-20`, chosen because I
   thought they were the two my rubric was most likely to get wrong. The harness printed:

   > `agreement: 2/2 scored items`

   Partial runs print no bar verdict and refuse to write `--save-run`, so this one is
   not the committed run.

2. **20/20** — the first full run of all 20 scored bundles, saved with
   `--save-run eval-run.txt`. The harness printed:

   > `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`
   >
   > `agreement: 20/20 scored items  (bar: 18/20: PASS)`

3. **20/20** — a second full run, and the one committed here. No check changed between
   runs 2 and 3. I reworded the descriptive paragraph at the top of `rubric.md`, which
   does not touch the checks table or the verdict rule, but it does change the file, and
   the header `run_eval.py` writes records a fingerprint of the rubric it graded. After
   the edit, the fingerprint in the saved run no longer matched the `rubric.md` I was
   uploading, which is exactly the mismatch that line exists to expose. So I re-ran
   rather than ship a run whose header disagreed with the file beside it. The committed
   header and file now agree:

   > `#   rubric.md  sha256:f83ca5a329c159e4`

   and the run printed:

   > `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`
   >
   > `agreement: 20/20 scored items  (bar: 18/20: PASS)`

   That last line is the agreement line in the committed `eval-run.txt`. The two full
   runs also agreed check for check, not just in total — `unclaimed` failed the same six
   bundles in both, and no accept lost a required check in either — which is a small
   piece of evidence that the 20/20 is the rubric's doing rather than a lucky sample of
   a nondeterministic grader.

There was no revise loop, because there was no disagreement to chase. The reason the
first full run landed at 20/20 is that I read all 24 bundles and `gold-labels.json`
before writing a single check, so the thresholds were designed against the evidence
instead of guessed at and corrected afterwards. The clearest example is the
`repo-in-use` clause "`latest release: none published` is not a fail by itself": issue-06
is a gold **accept** in a repo that has never cut a release, and a plain
"released within a year" check would have rejected it.

Rather than take the total on faith, I checked which check produced each reject, using
`--out`. Every reject failed on the check built for its category: the three dead-repo
issues on `repo-alive` and `repo-in-use`, the four claimed issues on `unclaimed`, three
of the four scope issues on `scope-bounded`, and issue-12 on `ai-policy-ok` alone. All
eight accepts failed nothing but preferred checks, which by the verdict rule cannot
reject.

(Separately from the eval, one live-mode run on three Path Review candidates produced
the verdict output above. Live runs are not scored against gold labels and have no
agreement score.)

**Issue analysis**

**issue-09** (conda/conda#7617, "conda config clear option"). **My rubric: `accept`.
Gold label: `accept`.**

This is the bundle that shaped my `unclaimed` check, and a simpler version of that check
would have disagreed with the gold label. The issue carries what looks like a textbook
claim, from the comment thread:

> "I'd like to take a swing at this as my first open-source contribution. Does it need to
> be assigned to me?" — MesaJonathan (NONE) on 2022-01-20

and the repo-facts block records a linked PR:

> `this issue: assignees: none; linked PRs: conda/conda#11627 (closed)`

A rubric that rejects on "any claim comment" or "any linked PR" rejects this issue and
scores a disagreement. Two facts say nobody is actually on it. The claim is dated
2022-01-20 against a capture date of 2026-08-05, which is more than four years stale.
And the maintainer's answer to that claim was an invitation, not an assignment:

> "Think you can just give it a try if you are interested. Thanks for looking into it
> Jonathan 😄" — jakirkham (MEMBER) on 2022-01-20

The linked PR is `closed`, not `open` — an abandoned attempt rather than live work. The
gold note agrees on the reasoning, not just the verdict: *"old but valid bounded feature;
the 2022 claim is stale and the maintainer invited takers"*.

So I put the staleness in the rubric rather than in my head: claim comments expire after
90 days, an open linked PR never expires. That single split is what separates issue-09
(`accept`) from issue-18 (excalidraw#9656, gold `reject`), where the claim comments run
to within three days of capture *and* three linked PRs are `open`. Same family of
evidence, opposite verdicts, decided by a date threshold instead of a judgment call.

**Check rationale**

The `unclaimed` row, quoted as currently written in the `rubric.md` uploaded to
`tools/issue-select/`:

> | `unclaimed` | The `assignees:` and `linked PRs:` fields on the "this issue" line under Repo facts, plus the Comments section. | `assignees: none` AND no linked PR is in the `open` state AND no comment announces a pull request that the thread never reports as closed or merged AND no human claim comment ("working on this", "can I take this", "I would like to work on this") dated within 90 days of capture. A closed-unmerged linked PR is an abandoned attempt, not a claim; a claim comment older than 90 days is stale and does not block; bot nudges and bot claim bookkeeping are not claims. | required |

Every clause is there because one specific bundle would have been graded wrong without
it.

- **`assignees: none`** — issue-08 (zulip#39794) records
  `assignees: piyushagarwal-55`. The cheapest possible signal, and it settles that one.
- **No linked PR in the `open` state** — issue-13 (minikube#23437) has no assignee and no
  comments at all, so both of the obvious signals read "free." Only
  `linked PRs: kubernetes/minikube#23438 (open); kubernetes/minikube#23439 (open)` reveals
  two people already racing on a good first issue that was hours old at capture.
- **No PR announced in the thread** — the evidence guide warns that not every PR is
  formally linked and that "when the sidebar and the thread disagree, believe the
  thread." Calibration bundle calib-04 (bat#1341) is exactly that: the repo-facts line
  reads `none formally linked`, while the thread has "Opened PR #3617 implementing
  --fallback-syntax." Without this clause the sidebar reads as free.
- **The 90-day expiry on claim comments** — the clause that saves issue-09, above.
- **Bot nudges are not claims** — issue-08's thread ends with zulipbot warning that the
  assignee has been quiet for 10 days and will be unassigned in 4 more. It is tempting to
  read that as the issue opening up. It is not: the assignee is still set and the PR is
  still open. The gold note makes the same point — *"the bot nudge does not clear the
  claim."*

**Trade-offs**

What this check gives up, in `unclaimed`'s own clauses:

**A case I accept it will miss.** The 90-day expiry is a guess about human behaviour, not
a fact about the issue. Someone who commented "I'll take this" 100 days ago and is still
quietly working — no PR pushed yet, nothing linked — reads as `unclaimed` to my rubric,
and I would duplicate their work. I took that deliberately: the opposite error, treating
every old claim as live, costs more, because it rejects issues like issue-09 that are
genuinely free and sat untouched for four years. I would rather occasionally collide than
systematically discard the backlog.

**A clause that is wrong on at least one real repo.** "Bot claim bookkeeping is not a
claim" is tuned to zulipbot's *unassignment* nudges, but in Zulip the bot is also the
actual claiming mechanism: `@zulipbot claim` is how a contributor takes an issue, and
zulipbot's reply is the claim record. On a live Zulip issue where someone claimed
yesterday, my clause invites the model to dismiss the only evidence there is. It does not
cost me anything on this eval set, because issue-08 is caught by the assignee field and
issue-15's zulipbot claims are all years stale, but the clause is over-broad as written.

**A reason nothing changed elsewhere.** I verified against the `--out` results rather
than assuming: `unclaimed` fired on exactly six bundles — the four claimed-category
issues (03, 08, 13, 18) plus 05 and 15, all gold rejects — and on none of the eight gold
accepts. So the 90-day window and the bot clause are not quietly rejecting accepted
issues elsewhere in the set.

**And it means something different in live mode, on purpose.** `scope.md` states a Path
Review house rule: classmates' claim comments do not block an issue there, because course
credit attaches to the PR you open rather than to whether it merges. So in the classroom
repo the claim-comment clause is suspended while the assignee and open-PR clauses still
apply. That is not hypothetical — #68 and #69 in the Path Review repo both picked up
classmate claim comments in the last two days, and under the house rule they remain
takeable.

---

## Selection rationale

**Selection rationale**

**1. Fit to my interests and to the time available.** #53 is a regex fix in
`safety/pii_scrubber.py`, and the issue body names four tests that already fail
(`test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`,
`test_phone_at_start_of_text`). That is the shape of work I want: Python I can read, and
a red-to-green loop I can run on my own machine with nothing but pytest, before I show
anyone the change. It also fits the time I have. Of the three candidates my skill
accepted, #73 was the easier pick — a documentation mismatch, 1–2 hours — but it has no
test to turn green and teaches me less about the codebase, and #61 wants a running stack
and database to verify, which I have not set up.

**2. What the verdict identified correctly, and what I weighed that the rubric could
not.** The rubric got the mechanical facts right, and those are the ones I would have
been sloppiest about by hand: nobody is on this issue (no assignee, zero PRs in the whole
repo, no comments), the repo is alive (commits four days before I looked), and there is
no contribution policy to fall foul of. What it could not weigh is that a PII scrubber is
security code, so "make the failing test pass" is not the whole job — a phone-number
regex that is too greedy starts redacting things that are not phone numbers, and I should
be checking false positives that no listed test covers. It also could not know that the
"maintainer" here is the course instructor seeding a classroom tracker, which is why the
liveness signals look healthy (6-day response times) while the repo has one star and no
releases. My rubric read those facts correctly and drew the right conclusion, but for
partly the wrong reason.

**3. The anticipated difficulty in claiming it.** Low, mechanically: the house rule in
`scope.md` says a shared issue costs nobody anything, credit attaches to the PR I open,
and #53 has no claim comments on it at all right now. The real difficulty is that I have
not written the claim comment yet — that is Unit 2's work, with the voice guide — so the
risk is posting something careless before then. The other thing I expect to be awkward is
scope discipline once I am in the file: the issue asks for one format, `(555) 123-4567`,
and it will be tempting to "fix" international formats while I am there and turn a
four-test change into a much larger one that is harder to review.

---

Related paths: `eval-run.txt` in this directory; my skill's files in
`tools/issue-select/`.
