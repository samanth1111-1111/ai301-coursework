# Procedure: how this skill grades a plan package

## Read order

1. Read the issue first. Write down, in one line, the bug the reporter
   saw and what they expected instead.
2. Read the repro-evidence block second, before the plan. Write down
   every step, timing, and control run, and what each one shows. For
   each control run, write down what it rules out (for example "with
   colors off the same pager is fast, so the pager keys are not the
   cost"). This comes before the plan so the plan's confident wording
   cannot shape what you think the evidence says.
3. Read the thread highlights third. Write down every comment from an
   OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR that names a culprit,
   proposes or rejects an approach, posts a patch, or points to open
   PRs. Mark comments from NONE as claims, not direction.
4. Read the repo-facts block fourth. Write down the contribution
   policy's AI rule, word for word, and whether it requires disclosure.
5. Read the candidate plan, then the candidate plan comment, last.

## Evidence gathering

1. diagnosis: copy the plan's stated cause. Next to it, list the
   repro steps and control runs from Read order step 2 that support it
   and any that contradict it.
2. scope: copy the plan's in-scope and not-in-scope lines. List every
   piece of work the plan proposes, and mark each one as "fixes the
   reproduced bug" or "extra work the issue did not ask for".
3. executability: copy every file, function, or code site the plan
   names, and the approach steps. Copy any hedge words ("investigate",
   "somewhere", "maybe", "whichever", "not sure").
4. test plan: copy the plan's test plan. Copy the repro evidence's
   Expected line next to it.
5. comms: copy the maintainer direction from Read order step 3 and the
   AI rule from step 4. Copy the sentences in the plan comment that
   respond to each, or write "none".
6. honesty: copy the plan's risks or unknowns section, or write
   "none stated", and copy any claim stated as certain that the repro
   evidence does not show.

## Check execution

1. Run the checks in table order: diagnosis, scope, executability,
   test plan, comms, honesty.
2. For each check, apply only that row's pass condition to the
   evidence gathered for it. Grade `pass`, `fail`, or `unclear`.
3. Grade `fail` when the package clearly breaks the pass condition.
   Grade `unclear` only when the evidence the check needs is genuinely
   missing from the package (for example, no test plan section at
   all). Missing evidence the check requires is not a reason to pass.
4. Do not fail a check for how the plan is written. A short plan can
   pass every check; a long, polished plan can fail. Judge what the
   plan says it will do, not its length or headings.
5. For diagnosis: a cause taken from a thread commenter still fails if
   any repro step or control contradicts it.
6. For test plan: a manual re-run of the repro steps with a named
   observable result passes. Do not require an automated test.
7. Record one line of evidence per check: the quote or fact that
   decided it.
8. Grade every check, even after one has already failed, so the
   output shows every problem.

## Verdict assembly

1. Apply the rubric's verdict rule: accept only if every required
   check is `pass`. Any required check at `fail` or `unclear` means
   reject.
2. Ignore preferred checks when deciding the verdict; still report
   them.
3. For a reject, quote in the summary the evidence line of the first
   required check that failed, so the author knows what to fix first.
4. Emit the JSON block from SKILL.md last, with every check in table
   order.
