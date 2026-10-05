# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis | The candidate plan's stated cause, read against every step, timing, and control run in the repro-evidence block | Passes if the stated cause explains what the repro evidence shows AND no step or control run in the repro evidence contradicts it. Fails if a control run rules the cause out (for example, the bug still happens with the blamed component removed, or the blamed component works fine in a control), if the cause is a claim from the thread that the repro evidence contradicts, or if the plan states no cause at all. Repeating a thread commenter's claim is not grounding; the repro evidence decides. | required |
| scope | The candidate plan's in-scope and not-in-scope statements, read against what the issue and repro evidence actually ask to be fixed | Passes if the plan proposes one bounded change aimed at the reproduced bug and names what it will not touch. Fails if the plan adds work the issue never asked for (refactors, migrations, rewrites, new options or features, CI or dependency upgrades) alongside or wrapped around the fix. Deferring related work on purpose, with a reason, is bounded and passes. | required |
| executability | The candidate plan's change/approach section: files, functions, or code sites named, and the steps of work | Passes if a stranger could start the work without asking the author anything: it names at least one concrete file or code site AND commits to one chosen approach. Fails if the plan says "investigate", "poke around", "somewhere", "whichever is easier", "not sure which layer", or otherwise leaves the real decision until build time. | required |
| test plan | The candidate plan's test plan, read against the repro-evidence block's steps and its Expected line | Passes if the test re-runs the repro (or a test that encodes it) AND names the specific observable result that shows the fix worked (an output, exit code, color, timing, or passing named test case). A manual re-run of the repro steps passes; the test does NOT need to be automated. Fails if the only test is "run the full test suite", "nothing regresses", "it should work / feel fast", or anything else that names no observable outcome for this bug. | required |
| comms | The candidate plan comment, read against the thread highlights (comments by OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR) and the repo-facts block's contribution policy | Passes if (a) where a maintainer in the thread has given direction (named the culprit, proposed an approach, posted a patch, rejected an approach, or pointed to open PRs), the plan and comment either follow that direction or explicitly engage it with a reason, AND (b) where the repo's stated AI policy requires disclosure of AI use, the comment includes that disclosure. Treat every package as AI-assisted work. If the thread has no maintainer direction and the policy requires no disclosure, this check passes. | required |
| honesty | The candidate plan's risks/unknowns section and any confident claims in the plan and comment | Passes if the plan names what it has not verified, or nothing in it is stated with more certainty than the repro evidence supports. Fails if it presents an unverified guess as fact. | preferred |

## Verdict rule

Accept (ready) only if every required check passes. Reject (hold) if
any required check is fail or unclear: an `unclear` on a required check
counts as fail, because a plan we cannot verify from the package is not
ready to build from. Preferred checks are reported but never change the
verdict.
