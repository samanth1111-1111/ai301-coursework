# Rubric: is this pull request ready to submit?

<!--
Built from the group worksheet on calib-03. Each "no" there became a
check: the file the plan never names (property.js) is plan-scope; the
description's "exactly, no changes beyond it" claim is
description-fidelity; the missing control and nested-chain tests and
the "# after: matches byte for byte" lines are repro-evidence and
repo-checks. The four failure families: silent drift = plan-scope,
plan-complete, description-fidelity; not tested = repro-evidence,
repo-checks; unreviewable = clean-diff; standards wall =
template-asks, ai-disclosure.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-scope | Every file and every hunk in the diff, read against the plan's scope line, its "Files:" list, its "Not in scope" line, and any deviation note | Passes if every changed file is named by the plan (the file, its folder, or a named kind such as "a changelog entry" or "tests") or by a deviation note, AND no hunk does work the plan scopes out or never mentions: a rewrite or refactor of code the fix does not need, a renaming pass, a new option, flag, or setting, or a new feature. A hunk inside a named file still fails if it does unplanned work (for example, a new flag added to the same CLI file). Fails on any changed file or hunk outside the plan with no deviation note covering it. | required |
| plan-complete | Each change the plan commits to (code change, tests, docs, changelog entry), read against the diff, with the plan's deviation or deferral notes and the description | Passes if every change the plan commits to appears in the diff, OR the missing part is disclosed as deferred or dropped in the plan's deviation/deferral note or in the description. An honestly disclosed shortfall passes. Fails if a planned change is absent from the diff and nothing says so. | required |
| description-fidelity | Every factual claim in the PR title and description about what the change does, adds, or leaves out ("implements the plan exactly", "no functional changes beyond X", "docs updated", "changelog added", "no options added"), read against the diff | Passes if every such claim is true of the diff. A negative claim ("no options added") is true when nothing in the diff adds it. Fails if any claim says the PR does more than the diff delivers (claims docs, tests, or a file the diff lacks) or claims fidelity the diff contradicts (claims "exactly as planned" or "no changes beyond the plan" while plan-scope or plan-complete fails). | required |
| repro-evidence | The test-evidence section (and any before/after in the description), read against the plan's test plan and the repro steps it names | Passes if the plan's repro (the failing case the issue reports) is re-run on the branch with the command or steps named, and both the before and the after result are stated as specific observable output: printed text, an error message, an exit code, a table, or a named passing test case. An annotated transcript (`# after: no error, searches normally`) is acceptable when it names the observable result. Every distinct failure case the plan's test plan names must be re-run, except a case the plan or description explicitly defers. Fails if the evidence is a claim with nothing observable ("tested locally, works now", "verified on my machine for a day"), if it only exercises a control or an unchanged path instead of the failing case, if a named failure case in the test plan is not re-run, or if no before/after is shown. | required |
| repo-checks | The test-evidence section, read against the plan's test plan and the repo facts (test command, lint or format checks, or, live, the checks the PR template lists) | Passes if the repo's own test suite or test command the plan names is reported as run with its outcome (passed, a count, or a failure disclosed honestly with its reason). In live mode, every check the PR template lists (make test-unit, make test-integration, make lint, make typecheck) must appear with its pasted output. Fails if no repo check or suite run is reported at all, or a check's result is hidden or misstated. | required |
| clean-diff | Every added and removed line in the diff, and the commit list | Passes if every hunk is part of the fix, its tests, or its stated docs/changelog. Fails if the diff carries debris: debug prints or logging left in (`eprintln!("DEBUG`, `console.log`, `print(`), commented-out code or a commented-out earlier attempt, dead functions or `allow(dead_code)`/`_unused` helpers, new TODO/FIXME notes, or churn the fix does not need (re-indenting, re-ordering or restructuring imports, re-printing identical lines, reformatting untouched code). Commit messages like "wip" or "misc cleanups while debugging" are supporting evidence of debris, never a fail on their own. | required |
| template-asks | The repo facts' PR-template and contributing asks (required sections, checklist items such as "closes #xxxx", a changelog or whatsnew entry, tests added), read against the description and the diff | Passes if every ask the repo states is honored: each required template section is present with real content, an issue link is present where the template asks for one, and a changelog/whatsnew/release-notes entry the repo asks for appears in the diff (or the description says why it does not apply). If the repo states no template and no asks, this passes. Grade only stated asks, and only whether they are met or visibly missing, never the wording quality. Fails if any stated ask is visibly ignored. | required |
| ai-disclosure | The repo facts' contribution policy (AI policy) read against the description; in live mode, the course's standing disclosure ask | Treat every package as AI-assisted work. Passes if the repo states no AI policy that requires disclosure (eval), OR the description discloses AI use in the author's own words, naming the tool or the extent of help, where the policy (or, live, the course) requires it. Fails if the policy requires disclosure of AI use and the description contains none. | required |
| commit-hygiene | The commit list | Passes if each commit message describes the change it makes. Fails on messages like "wip", "fix", or "stuff". Reported only. | preferred |

## Verdict rule

Accept (ready to submit) only if every required check is `pass`.
Reject (hold) if any required check is `fail` or `unclear`: an
`unclear` on a required check counts as `fail`, because a PR we cannot
verify from the package is not ready to submit. Preferred checks are
reported but never change the verdict. An honestly disclosed shortfall
(less than the plan, said so in the plan's notes and the description)
is not a fail by itself.
