# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

- Where it lives: eval bundle: the `## Plan context` block's "Plan:"
  paragraph (its "Scope, one bounded change", "Not in scope", and
  "Files:" lines, plus any "Known limit" or deferral note), read
  against the `### Diff` file headers (`--- a/` / `+++ b/`) and hunks,
  and the `### Description` claims. Live: the scope, files, and
  `## Deviations` sections of `plan.md`, read against
  `git diff main...HEAD` and `--stat`, and the claims in
  `pr_draft.md`.
- What good looks like: every file in the diff is on the plan's Files
  list (or in a named folder or kind, such as "a changelog entry"),
  every planned change is in the diff, and every description claim is
  true of the diff. calib-03 fails: `src/language-js/print/property.js`
  is rewritten, the plan never names it, and the description says "no
  changes beyond it". pkg-17 fails the other direction: the plan
  promises a docs update, the diff has none, and the description says
  the docs "now document the limitation". pkg-13 passes: `%VAR%` is
  deferred in the plan's note and the description restates it.

## Test evidence (harness category: not-tested)

- Where it lives: eval bundle: the `### Test evidence` section (and
  any Before/After in the description), read against the plan's
  "Test plan:" line and the "Repro evidence" paragraph. Live:
  `test_evidence.md` (the unit 2 repro re-run on main and on the
  branch, plus the output of `make test-unit`, `make
  test-integration`, `make lint`, `make typecheck`), read against
  the test plan in `plan.md`; the same output pasted into the
  Testing section of `pr_draft.md`.
- What good looks like: the failing case from the repro is re-run on
  the branch with the command shown, and the before and after are
  specific output (calib-04's after: `error: ... exceeds the maximum
  of 4096`, exit `2`; pkg-19's `diff ... && echo IDENTICAL`). Every
  failure case the test plan names is re-run (calib-04 is held: repro
  2, `-j 200000 -x echo .`, is never re-run). The repo's suite is
  reported with its command and result (`go test ./...` passes (4108
  tests)). Not good: "tested locally, colors work now" (pkg-04),
  "verified working on my machine" (pkg-10), or a run of the control
  or unchanged path instead of the failing case (pkg-07, pkg-14). In
  live mode, `make test-integration` stopping with "no tests ran" is
  expected and counts when pasted as is.

## Diff quality (harness category: unreviewable)

- Where it lives: eval bundle: every `+` and `-` line in `### Diff`,
  and the `### Commits` list. Live: `git diff main...HEAD` and
  `git log main..HEAD --oneline`.
- What good looks like: every hunk is the fix, its test, or its
  changelog/docs line, and nothing else. Debris tells: `// dbg!`,
  `eprintln!("DEBUG ...")`, `print(` or `console.log` left in;
  commented-out earlier attempts; dead helpers (`_unused_buffer_probe`,
  `count_open_fds` behind `#[allow(dead_code)]`); new TODOs; and churn
  (re-indented blocks, reshuffled imports, identical lines removed and
  re-added). pkg-12 and pkg-15 fail on these; their "wip" and "misc
  cleanups while debugging" commits are a hint, not the reason.

## Standards and comms (harness category: standards-wall)

- Where it lives: eval bundle: the `## Repo facts` block's "pull
  requests:" line (template sections, checklist items, changelog or
  whatsnew asks) and "contribution policy" line (AI policy), read
  against the `### Description` and the `### Diff` file list. Live:
  `.github/PULL_REQUEST_TEMPLATE.md` (Summary, Issue with `Closes #`,
  Changes, Testing, Screenshots / Demo, Notes for Reviewers) and
  `docs/CONTRIBUTING.md`, read against `pr_draft.md`; the course
  always requires an AI-use disclosure, which goes in Notes for
  Reviewers.
- What good looks like: each stated ask is met. pkg-01 fails: pandas'
  checklist asks for `closes #xxxx` and a whatsnew entry, and the
  description has no issue link and the diff has no
  `doc/source/whatsnew/` file. pkg-20 fails: ghostty's policy requires
  disclosing all AI use and the description has none. pkg-11 passes:
  "Per the repo's AI policy: this change was AI-assisted with me
  reviewing and understanding every line, and this text is in my own
  words." A repo with no template and no AI policy has nothing to
  wall on; that check passes.
