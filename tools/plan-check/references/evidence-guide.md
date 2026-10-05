# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

- Where it lives: eval bundle: the candidate plan's "Cause" or
  "Diagnosis" line, read against the `## Repro evidence` block's
  Steps, timings, Control runs, and Actual line. Live: the cause in
  plan.md, read against the student's posted repro comment on the
  issue (or the house repro pack quoted in the drafts).
- What good looks like: the stated cause explains every step of the
  repro evidence, and no control run rules it out. If a control shows
  the bug without the blamed component (calib-03: 26 s with no pager
  at all), or the blamed component working in the same build, the
  diagnosis is wrong however confident it sounds.

## Scope

- Where it lives: eval bundle: the candidate plan's "Scope" or
  "Change" section, especially the "In:" / "Not in scope:" / "Out:"
  lines, and the list of approach steps. Live: the scope section of
  plan.md.
- What good looks like: one change aimed at the reproduced bug, plus
  a line naming what it will not touch. A drive-by rewrite adds
  refactors, migrations, new options, dependency upgrades, or CI
  changes the issue never asked for. Deferring related work on purpose
  with a reason is still bounded.

## Executability

- Where it lives: eval bundle: the candidate plan's "Change" or
  "Approach" section and any file paths in backticks. Live: the files
  and approach sections of plan.md.
- What good looks like: at least one named file or code site
  (calib-01: `pkg/gui/controllers/sync_controller.go`, push completion
  callback) and one chosen approach. "Poke around the editor code",
  "somewhere", or "whichever is easier" means a stranger could not
  start.

## Test plan

- Where it lives: eval bundle: the candidate plan's "Test" or "Test
  plan" line, read against the repro evidence's Steps and Expected
  line. Live: the test plan section of plan.md, read against the
  student's unit 2 repro steps.
- What good looks like: the repro steps re-run (manual is fine) with
  the specific result that shows the fix worked (calib-01: "at step 3
  the color must flip without leaving the view"). "Run the full test
  suite", "nothing regresses", or "should feel fast" names no
  observable result for this bug.

## Honesty

- Where it lives: eval bundle: the candidate plan's "Risk",
  "Unknowns", or "Open question" lines, and any certainty words in the
  plan and the plan comment. Live: the risks and unknowns section and
  the `## Deviations` section of plan.md.
- What good looks like: what was not measured or not checked is said
  out loud (pkg-20: "I have not yet measured the per-print cost").
  False confidence states an untested guess as fact. A build that
  changed course records what changed and why under Deviations.

## Comms

- Where it lives: eval bundle: the `## Candidate plan comment`
  section, read against `## Thread highlights` (comments marked OWNER,
  MEMBER, COLLABORATOR, or CONTRIBUTOR) and the "contribution policy"
  line of `## Repo facts`. Live: the comment.md draft, read against
  the issue thread on GitHub and the repo's CONTRIBUTING.md and any AI
  policy it links.
- What good looks like: when a maintainer has named a culprit,
  proposed or rejected an approach, posted a patch, or pointed at open
  PRs, the comment follows that direction or says why not (pkg-04
  fails: the owner pointed at `src/tui/light_windows.go` and posted a
  patch, but the comment plans docs only and never mentions it). When
  the AI policy requires disclosure, the comment discloses the tool
  and how much it helped (pkg-20 fails: ghostty requires disclosure,
  the comment has none). Treat every package as AI-assisted work.
