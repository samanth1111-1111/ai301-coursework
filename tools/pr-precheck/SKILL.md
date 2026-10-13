---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer one question about one PR package: **is this ready to
submit?** A PR package is a candidate pull request (its title, its
description, its commit list, its diff, and its test evidence), read
against the plan it claims to implement (scope, files, test plan, and
any deviation notes) and the issue that plan belongs to (the bug, the
thread, and the repo's stated PR template and contribution policy).

Do not grade anything else. Do not judge whether the plan itself was a
good plan, whether the issue was worth taking, or whether you would
have written the fix differently. Grade one package per run, and only
by executing the files in this directory.

## Inputs and modes

Run in exactly one of two modes.

**Live mode** (the student's own PR, before it is opened). Read these
inputs, and only these:

1. `plan.md` in the working directory, including its `## Deviations`
   section if there is one. This is the plan the PR must implement.
2. The diff on the branch: run `git diff main...HEAD` (three dots)
   from the working copy, plus `git diff main...HEAD --stat` and
   `git log main..HEAD --oneline` for the file list and commit list.
   Only committed changes count; uncommitted edits are not part of the
   PR. Never treat `plan.md`, `comment.md`, `pr_draft.md`, or
   `test_evidence.md` as part of the diff.
3. `pr_draft.md` in the working directory: the first line is the PR
   title, everything below it is the PR description.
4. `test_evidence.md` in the working directory: the captured output of
   the before/after repro re-run and the repo's own checks.
5. The issue named in the request: read its body and thread with
   `gh issue view <number> --repo <scoped repo> --comments` (or the
   web if `gh` is unavailable).
6. The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and
   `docs/CONTRIBUTING.md` in the working copy (including any AI-use
   policy they state). The course always requires an AI-use
   disclosure, so treat it as a stated ask in live mode.

A house-chain student reads the house issue, the house plan, and the
house repro pack in place of their own; the same checks grade the
same things.

**Eval mode** (an eval package bundle). The bundle is the whole world.
Every fact comes from the bundle text: its Repo facts, Issue, Thread
highlights, Plan context, and Candidate PR (Title, Description,
Commits, Diff, Test evidence). Fetch nothing, run nothing, and read no
other file in the working directory. Eval mode always grades a
complete package: every check in the rubric, the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else.

- If its `Repo:` line still carries the placeholder
  (`<ORG>/<PATH-REVIEW-REPO>` or any angle-bracket text), stop without
  grading. Tell the student to fill the `Repo:` line in `scope.md`
  with their section's Path Review repo (shown in the install step of
  the activity page), and emit nothing else.
- If the issue or the working copy's `origin`/upstream belongs to a
  different repo than the scoped one, refuse to grade and say which
  repo is in scope. Never guess a scope.
- Apply the house rules it lists as part of the bar: the branch name
  must be `<type>/<issue-number>-<slug>`, the PR delivers one plan for
  one issue, and every template section gets real content.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after the inputs. Hold the PR
title and description in `pr_draft.md` against each of its rules and
its "Things I never post" list. In the summary, under a "Voice"
heading, list each rule the draft breaks, quote the rule, and quote
the sentence that breaks it. If none break, say so. The voice guide
never changes the verdict on its own: it is the student's personal
style, and the rubric's checks decide the verdict.

In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Read the components in this order, then work:

1. `rubric.md`: the checks table (Check, Evidence, Pass condition,
   Weight) and the verdict rule. It decides **what** passes.
2. `references/evidence-guide.md`: where each family of evidence lives
   in an eval bundle and in a live working copy, and what good looks
   like. It decides **where** to look.
3. `procedure.md`: the operating steps (Read order, Evidence
   gathering, Check execution, Verdict assembly). It decides **how**
   to run. Execute it as written, step by step.

Where the procedure is silent on a step you need, do not improvise
around it: make the smallest reasonable move, and name the gap in the
summary under "Procedure gaps" so the student can fix the file.

If `rubric.md` has no filled-in checks or no verdict rule, or
`procedure.md` has no filled-in steps (instruction comments do not
count as content), refuse to grade: say which file is empty and that
this tool cannot grade without a rubric and a procedure. Do not invent
checks at runtime.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit;
`reject` means hold it and fix what failed. There is no third verdict
and no score; put reservations in the evidence lines.

Before the JSON block, print a short readable summary: one line per
check (name, grade, the deciding evidence), the deciding check for a
reject, and in live mode the Voice notes and any Procedure gaps. Then
end the reply with this fenced JSON block, filled in, valid, and last.
Nothing follows it.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

In live mode, `item` is the issue URL (the PR does not exist yet); in
eval mode, it is the bundle id (for example `pkg-07`). List every
rubric check, in rubric order, using the rubric's check names.

## Grading discipline

- **Evidence first.** Never grade a check without naming the quote,
  diff line, or output line that decided it. "Looks fine" is not
  evidence.
- **Grade the thing, not the polish.** A terse PR that matches its
  plan and shows real output can be ready; a confident, well-formatted
  one can be hiding drift. Read the diff against the plan and the
  evidence against the test plan; never grade length or headings.
- **A description's claim is not proof.** "Tests pass", "matches
  exactly", or "no changes beyond the plan" counts only when the diff
  or the pasted output shows it.
- **Honest shortfalls can be ready.** A PR that does less than the
  plan, and says so in the plan's deviation notes and in its
  description, is not drift.
- **The rubric decides, not the run.** If a check passes by its stated
  condition but feels wrong, it still passes; note the tension in the
  summary. The fix belongs in the rubric.
- **The procedure decides how, not the run.** Follow `procedure.md`
  as written and report its gaps.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's
  verdict rule says. Where it is silent, an unverifiable claim is a
  failing one: a PR you cannot verify from the package is not ready to
  submit.
