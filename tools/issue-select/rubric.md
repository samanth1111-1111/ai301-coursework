# Rubric: is this a good first issue?

Six required checks, one per way a first contribution stalls: the repo is
no longer maintained, the software is not shipping, the work is not one
change, nobody has agreed the change should be made, someone else is
already on it, or the project rejects the way I work. Three preferred
checks rank what remains. Every date threshold is measured against the
bundle's capture date in eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-alive` | The `archived:` flag on the repo line and the dates in "last 5 default-branch commits" under Repo facts. | `archived: no` AND the newest default-branch commit is dated within 180 days of the capture date. An archived repo is read-only and fails outright. | required |
| `repo-in-use` | The "latest release" line and the "last 5 default-branch commits" dates under Repo facts. | A release dated within 365 days of capture, OR at least 3 of the last 5 default-branch commits dated within 180 days of capture. `latest release: none published` is not a fail by itself: a repo that never cuts releases passes on the commit clause. Bot-authored commits count only when at least one of the 3 is human-authored or is a bot merging a human pull request. | required |
| `scope-bounded` | The issue body, and the "linked PRs" list under Repo facts. | The issue asks for one change that a single pull request could close. Fail if ANY of these hold: the body is a tracking list of other issue numbers or calls itself a mega-issue, meta-issue, or umbrella; the body asks for open-ended incremental work across the codebase or invites many pull requests; the issue has 3 or more linked PRs; the issue has 2 or more linked PRs that are closed and unmerged (repeated abandoned attempts mean the work is harder than the writeup admits); a maintainer says in the thread that the fix requires redesigning core internals. A numbered list of files, causes, sub-steps, acceptance criteria, or several instances of the same small fix inside ONE change is not an umbrella, and a terse one-line body is not a scope failure. | required |
| `maintainer-acknowledged` | The labels on the issue's "opened by" line, the opener's author_association, and the author_association of each commenter in the Comments section. | At least one label on the issue, OR the opener is OWNER, MEMBER, or COLLABORATOR, OR an OWNER, MEMBER, or COLLABORATOR comment endorses making the change. Fail when an unlabelled request sits untouched by any maintainer: nobody has agreed the change should be made, so the spec, and the product decision behind it, are still mine to invent. | required |
| `unclaimed` | The `assignees:` and `linked PRs:` fields on the "this issue" line under Repo facts, plus the Comments section. | `assignees: none` AND no linked PR is in the `open` state AND no comment announces a pull request that the thread never reports as closed or merged AND no human claim comment ("working on this", "can I take this", "I would like to work on this") dated within 90 days of capture. A closed-unmerged linked PR is an abandoned attempt, not a claim; a claim comment older than 90 days is stale and does not block; bot nudges and bot claim bookkeeping are not claims. | required |
| `ai-policy-ok` | The "contribution policy" line under Repo facts (live mode: CONTRIBUTING.md, any AI policy file it links, and PR or issue templates). | Pass when no policy is stated, or when AI-assisted work is permitted subject to conditions such as disclosure, human review, personal understanding, testing, or a ban on fully-generated pull requests. Fail ONLY on an outright ban on AI-assisted contribution, such as "we do not accept AI-generated code or documentation" with no allowance for assisted work. Conditions are terms to follow, not bans. | required |
| `maintainer-responsive` | The "maintainer first-response sample" under Repo facts. | At least one sampled issue drew a first owner, member, or collaborator comment within 30 days. | preferred |
| `newcomer-signposted` | The labels on the issue line and the issue body. | A `good first issue`, `help wanted`, or `easy` label, OR the body names the files, directories, or steps to start from. | preferred |
| `issue-fresh` | The issue's opened date against the capture date. | Opened within 365 days of capture. | preferred |

## Verdict rule

Accept when all six required checks grade `pass`. Any required check
grading `fail` rejects the issue; `unclear` on a required check counts as
`fail`, because a first issue I cannot verify is not one I should take.

The three preferred checks never change a verdict. Grade and report them
anyway: among accepted issues, the one passing more of them ranks higher,
with `maintainer-responsive` breaking ties first (an unreviewed pull
request is the slowest way to learn nothing).
