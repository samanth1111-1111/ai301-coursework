# Procedure: how this tool grades a PR package

## Read order

1. Read the repo facts first (eval: the `## Repo facts` block; live:
   `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`).
   Write down, as a list, every ask the repo states: each template
   section, each checklist item (issue link, tests, changelog or
   whatsnew entry, checks to run), and the AI policy word for word
   with whether it requires disclosure. In live mode, add the course's
   AI-use disclosure to the list.
2. Read the issue and thread second. Write down in one line the bug
   and the failing case the reporter showed.
3. Read the plan third, before any part of the PR (eval: the
   `## Plan context` block; live: `plan.md` with its `## Deviations`).
   Write down: (a) the "Files:" list and any folder or kind it names
   ("tests", "a changelog entry"); (b) the scope line and every
   "Not in scope" item; (c) every change the plan commits to, one per
   line; (d) every test-plan item, one per line, marking which ones
   are failing cases from the repro and which are controls or suites;
   (e) every deviation or deferral note. The plan comes before the
   diff so you compare the diff to the plan, not to what the
   description says the diff does.
4. Read the diff fourth (eval: `### Diff`; live:
   `git diff main...HEAD`, `--stat`, and `git log main..HEAD
   --oneline`). Write down every changed file and, for each, a
   one-line note on what each hunk does.
5. Read the test evidence fifth (eval: `### Test evidence`; live:
   `test_evidence.md`).
6. Read the PR title and description last (eval: `### Title` and
   `### Description`; live: `pr_draft.md`). The description is read
   last on purpose: it is a set of claims to check against what you
   already wrote down, not a guide to what the diff contains.

## Evidence gathering

1. plan-scope: make a two-column table. Left: every changed file from
   Read order step 4. Right: the plan's Files entry, folder, or kind
   that names it, the deviation note that covers it, or "not named".
   Then, for each hunk, mark "the planned fix", "planned tests/docs/
   changelog", or "unplanned work" (rewrite, rename, new option or
   flag, new feature). Copy the first line of any unplanned hunk.
2. plan-complete: take the list of planned changes from Read order
   step 3c. Next to each, write the diff file and line that delivers
   it, or "absent". For each "absent", copy any note in the plan or
   description that discloses it, or write "undisclosed".
3. description-fidelity: copy every factual claim in the title and
   description about what the PR does, adds, or leaves out. Next to
   each, write the diff line that makes it true, or "contradicted"
   with the line or absence that contradicts it. Claims of fidelity
   ("exactly", "no changes beyond") take their answer from steps 1-2.
4. repro-evidence: take the test-plan items from Read order step 3d.
   Next to each failing-case item, copy the command run on the branch
   and its before and after output, or write "not re-run". For each,
   note whether the result is observable output or only a claim
   ("works now", "verified"). Note whether the run exercises the
   failing case or only a control or unchanged path.
5. repo-checks: copy each suite or check result reported (command and
   outcome). In live mode, list the template's four checks and copy
   each one's output, or write "missing".
6. clean-diff: scan every added and removed line. Copy each line that
   is a debug print, commented-out code, a dead or unused function, a
   new TODO/FIXME, or churn (re-indent, import reshuffle, re-printed
   identical lines, reformatting untouched code). Copy the commit list.
7. template-asks: take the asks list from Read order step 1. Next to
   each, write where the description or diff honors it (section
   heading, `Closes #N` line, changelog file in the diff), or
   "missing".
8. ai-disclosure: copy the AI policy line and whether it requires
   disclosure. Copy the description's disclosure sentence, or write
   "none".
9. commit-hygiene: copy the commit messages.

## Check execution

1. Run the checks in rubric order: plan-scope, plan-complete,
   description-fidelity, repro-evidence, repo-checks, clean-diff,
   template-asks, ai-disclosure, commit-hygiene.
2. For each check, apply only that row's pass condition to the
   evidence gathered for it. Grade `pass`, `fail`, or `unclear`.
3. Grade `fail` when the gathered evidence clearly breaks the pass
   condition. Grade `unclear` only when the part of the package the
   check needs is genuinely absent (for example, no test-evidence
   section at all). Absence of required proof is never a pass.
4. A description's sentence is never proof for repro-evidence or
   repo-checks on its own when it describes the failing case; the
   failing case needs a named command or step and an observable
   result. A stated suite outcome with its command ("`cargo test -p
   ignore` passes (214 tests)") counts for repo-checks.
5. Do not let one finding count twice by mistake, but do grade every
   check that the finding genuinely breaks: an unplanned new flag
   fails plan-scope, and also fails description-fidelity if the
   description says "exactly as planned".
6. A shortfall the plan or description discloses (a deferred case, a
   dropped part) passes plan-complete and does not need to appear in
   repro-evidence.
7. Do not grade length, tone, or headings. A terse PR can pass every
   check; a polished one can fail.
8. Record one line of evidence per check: the quote, diff line, or
   output line that decided it.
9. Grade every check, even after one has failed, so the output shows
   every problem.

## Verdict assembly

1. Apply the rubric's verdict rule: accept only if every required
   check is `pass`. Any required check at `fail` or `unclear` means
   reject.
2. Ignore preferred checks (commit-hygiene) when deciding the
   verdict; still report them.
3. For a reject, the deciding check is the first failing required
   check in rubric order. Quote its evidence line in the summary as
   "Deciding check: <name>: <evidence>", so the author knows what to
   fix first.
4. In live mode, add the Voice notes and any Procedure gaps after the
   per-check summary.
5. Emit the JSON block from SKILL.md last, with every check in rubric
   order and nothing after it.
