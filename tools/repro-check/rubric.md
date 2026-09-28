# Rubric: is this reproduction package ready to post?

Six required checks, one per way a reproduction wastes a maintainer's
time: nobody can tell where it ran, nobody can re-run it, nothing was
shown, it was shown somewhere the issue does not live, the words promise
more than the work, or the repo's own rules were ignored. Two preferred
checks rank what survives. Every check reads the artifact against the
issue; none of them reads the write-up's shape, its length, or whether
it used the template's headings.

Two standing facts every check is read under:

- **The issue is the target.** "The behavior the issue describes" means
  the failure mode the issue names — the same error, the same exit
  condition, the same symptom at the same severity — not merely
  "something went wrong in the same tool."
- **This work is AI-assisted.** Every package graded here was produced
  with AI assistance, in eval mode and in live mode alike. So a repo
  policy that requires disclosing AI use is always triggered; the
  question is never *was AI used* but *does the comment say so*.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `env-recorded` | The environment record in the repro report (the environment line or block, plus any version or platform named inside the steps or the claim comment), read against the environment the issue states and against the dimensions the issue itself makes decisive. | The package names BOTH a version of the software under test AND the host it ran on (OS, distribution, browser, or equivalent runtime), AND any further dimension the issue itself treats as decisive: the driver on a driver-specific bug, the shell on a shell-specific bug, the build profile when the issue says debug and release differ, the browser language on a language-priority bug. Fail when no environment record exists anywhere in the package, even when the artifact is perfect — an unplaced run cannot be compared to anyone else's, and on an issue whose trigger depends on a dimension left blank it cannot even be read. A record carried in the claim comment instead of the report counts. | required |
| `steps-rerunnable` | The reproduction steps in the repro report, read against the inputs they consume and against the trigger the issue names. | A stranger holding only this package (the repro report, the claim comment, and the issue text they point at) plus the named environment could run the same thing: the starting state, the exact command or action, and the content of every input file, config, or payload are either shown, or named precisely enough to recreate ("the exact 12 lines from the issue", "a minimal env.yml with a valid dependencies list plus a category: section"). Fail when a step depends on something the package does not supply and the reader cannot obtain — a private repository, an unshared config, an internal tool, "set up the project" — or when the steps omit an element the issue names as required to trigger the bug. Pointing at the issue's own reproduction is supplying it; pointing at the author's employer is not. | required |
| `behavior-shown` | The artifacts the run produced (output excerpts, error text, logs, prompt captures, rendered output) read side by side with the behavior the issue describes. | One of two things is true. REPRODUCED: the package contains at least one artifact produced by this run, and what that artifact shows is the issue's failure mode, at the severity the issue reports. CANNOT REPRODUCE: the report says plainly that the behavior did not occur and shows the artifact of the attempt (the output that appeared instead) — this is a full pass, not a partial one; an evidenced negative result is a real result. Fail when there is no artifact at all; when the artifact shows only that the software is installed, launched, or running (a version banner, a session list, a screenshot of the app up); when the artifact shows a different failure than the one reported (a graceful validation or compile error where the issue reports a panic or crash, a process still alive where the issue reports it dying); or when the report's stated conclusion contradicts its own artifact, including an expected-versus-actual pair stated backwards. A root cause asserted with no run behind it is not an artifact, however plausible the mechanism sounds. | required |
| `environment-faithful` | The version, platform, and build conditions the run used, read against the versions, platforms, and build conditions the issue and its thread establish the bug on. | Pass when the run's conditions match what the issue targets, OR the run differs and the report names the difference out loud ("filed against 13.0.0; unchanged on 15.2.0"; "the report is macOS + fish, mine is Linux + zsh"), OR the issue names no target conditions to deviate from. Fail on a silent deviation: testing an older version than the one the issue confirms the bug on, or a different build or distribution channel than the one the issue and its thread point at, with no acknowledgment anywhere in the package — and especially when that unacknowledged deviation is then presented as strengthening the finding. A named difference is evidence about scope; an unnamed one makes the artifact a fact about some other software. | required |
| `claim-specific-and-honest` | The candidate claim comment, read against the issue's own specifics and against what its author can actually control. | Both halves hold. SPECIFIC: the comment names something only a reader of this issue could name — the failing command, the file or function, the exact symptom, the version it was checked on, or a pointer someone left in the thread. Fail a comment that would fit any issue in any repo unchanged ("+1, any updates?", "Kindly assign it to me", "I want to help with this one"). HONEST: what it commits to is the author's own next action — investigating, reading a named path, testing, reporting back — and what it says about its own results is no stronger than the evidence in the package. Fail a promise of an outcome the author cannot guarantee (a fix, a merge, a delivery date, "guaranteed"), a self-assignment stated as an accomplished fact, or a certainty the report does not back. | required |
| `conventions-respected` | The contribution-policy line and the bug-report template asks in the repo-facts block (live mode: CONTRIBUTING.md, any AI policy file it links, and the issue templates), read against both candidate comments as written. | Take the stated policy at its word and satisfy what it asks of an issue comment. If the policy requires disclosing AI use in contributions or comments, at least one of the two comments states that AI was used and to what extent — this work is always AI-assisted, so the requirement always applies and silence is a fail however good the reproduction is. If the policy requires comments to be in the contributor's own words, the comments read as a person writing about this particular issue rather than generated filler. If the policy's conditions do not reach issue comments (disclosure asked only in the pull request, "understand and take responsibility for your changes", "only submit code you have tested", "low-quality AI content is closed"), or the repo states nothing about AI at all, this check passes. Conditions are terms to follow, not bans; the only fail is a stated ask the comments leave unmet. | required |
| `control-run` | The artifacts, looking for a second run beside the failing one. | A contrast run isolates the trigger: the same command with the trigger removed, a neighbouring input that behaves correctly, or the prior version. Never gates the verdict; it is what turns "it broke" into "this is what breaks it". | preferred |
| `thread-read` | The claim comment and the repro report, read against the Thread highlights section. | The package engages with something in the thread rather than the issue body alone: a maintainer's pointer at a file, a linked upstream issue or prior fix, a test someone suggested, or an environment someone else already ruled in or out. When the thread has no comments there is nothing to read, and the check passes with that as its evidence. Never gates the verdict; it separates an author who read the room from one who read the title. | preferred |

## Verdict rule

Accept when all six required checks grade `pass`. Any required check
grading `fail` produces reject. `unclear` on a required check counts as
`fail`: proof I cannot verify from the package in front of me is proof
that is not ready to go up on a stranger's issue. The verdict space is
binary; there is no hold-and-see.

The two preferred checks never change a verdict in either direction.
Grade and report them anyway — between two packages that both pass, the
one with a control run and a thread read is the one worth posting first.

Claim-only drafts, live mode only. When the package is a claim comment
with no repro report yet, only `claim-specific-and-honest` and
`conventions-respected` are graded, and those two decide the verdict.
The whole proof side — `env-recorded`, `steps-rerunnable`,
`behavior-shown`, `environment-faithful`, `control-run`, `thread-read` —
reports `unclear` with evidence `not yet applicable: claim-only draft`
and is left out of the verdict rule. The verdict then answers only: is
this claim comment ready to post? Eval mode never takes this branch; it
always grades a complete package against all eight checks.
