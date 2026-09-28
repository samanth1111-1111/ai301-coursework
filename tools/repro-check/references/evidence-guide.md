# Evidence guide: where proof lives in a reproduction package

The rubric names the checks; this file is the map they read by. Five
families, one per heading. Each says where the proof physically sits —
in an eval bundle and on GitHub — and what it means for it to be there.

Two orientations before the families:

- **A package has two candidate sides and one fixed side.** The fixed
  side is the issue: its title, body, and Thread highlights, plus the
  Repo facts block (bug-report template asks, contribution policy). The
  candidate side is the claim comment and the repro report. Every check
  reads a candidate part *against* a fixed part. A grade that never
  crossed that line — "the report is detailed" — read only one side and
  proved nothing.
- **The package is the whole world.** In eval mode the bundle text is
  all the evidence there is: do not fetch, and do not fill a gap from
  what you know about the real project. In live mode the drafts are the
  candidate side and GitHub is the fixed side, but the drafts are still
  read as a stranger on the thread will read them — the package is what
  the comments contain and quote, not other files on the author's disk.

## Environment

**Where it lives.** In the bundle: an `Environment:` line or block at
the top of the Candidate repro report, and sometimes a version named in
passing inside the steps or in the Candidate claim comment ("reproduced
on current 15.2.0") — all three count. The target it is read against is
in the Issue section (reporters usually state their version and OS) and
in the Repo facts block's bug-report template line, which names the
dimensions this project considers load-bearing. Live: the draft's
environment block, against the issue body and the repo's bug-report
template.

**What good looks like.** A version of the software under test and the
host it ran on, both nameable by a reader who was not there: `yq 4.53.3
(Homebrew), macOS 15.5 (arm64)`. Plus whatever dimension *this issue*
makes decisive — the driver on a tunnel bug that only appears under one
driver, the shell and OS on a prompt bug the reporter saw under fish on
macOS, the build profile when the issue itself says debug panics and
release wraps, the browser language on a language-priority bug. The
install method (`pacman`, `Homebrew`, `cargo install`, `release binary`)
is what separates two runs of the same version number and is worth
having.

**What absence means.** No environment record anywhere is a hard gap,
not a stylistic one, and it is not bought back by a flawless artifact: a
run nobody can place is a run nobody can compare, confirm, or bisect
against. It is easy to miss, because a package with the exact command,
the real panic, and a control run *feels* complete right up until you
look for the line that says where it happened. Look for that line
deliberately.

## Steps

**Where it lives.** The Steps section of the Candidate repro report —
often a fenced block of shell lines with a `$` prompt, sometimes a
numbered list, sometimes a sentence describing a file that was written.
Read it against the issue's own "Steps to reproduce" and against any
trigger condition the Thread highlights add (a maintainer saying "the
`--replace` flag is also required"). Live: the draft's steps against the
issue body and thread.

**What good looks like.** A stranger with the named environment can
start from the stated starting state and reach the trigger using only
what the package hands them. Three things have to be reachable: the
starting state (an empty directory, a fresh repo, a specific config),
the exact command or action, and the content of every input the command
consumes. "Recreatable" is the bar, not "pasted": *the exact 12 lines
from the issue*, *a minimal `env.yml` with a valid `dependencies:` list
plus a `category:` section*, and *the issue's two `prettier.format`
calls, rangeStart 19 / rangeEnd 32, parser babel* are all followable,
because the reader can build the input from what is written. Pointing
back at the issue's own reproduction is supplying it — the issue is part
of the package.

**What absence means.** The failure mode is an input the reader cannot
obtain: a private company monorepo, an internal `.golangci.yml` that
"is a normal block list" but is not shown, a proprietary payload, or a
step that says "set up the project" with no commands. The report may be
entirely truthful and still be unusable, because nobody can check it —
and a reproduction nobody can check is a claim, not a proof. The second
failure mode is quieter: steps that skip the element the issue names as
the trigger (running `minikube start` on an issue whose repro line is
`minikube start --driver vmware`), which produces a run that is not the
issue's run.

## Behavior shown

**Where it lives.** Fenced output blocks in the Candidate repro report:
terminal transcripts, error text, stack traces, log excerpts, a rendered
prompt, a CSS output pane, a described screenshot. Read them against the
Issue section's own actual-behavior block, which is usually quoted
verbatim there and is the thing to compare to, token for token. Live:
the same blocks in the draft, against the issue's rendered body.

**What good looks like.** The artifact shows *this* issue's failure
mode, at the severity the issue reports. Line the two up and ask what
changed:

- **Same trigger?** The issue's input is `intdict = { 1 = {} }`; the
  report ran `intdict = { 1: {} }`. One character, and the run is now
  about HCL's key/value separator instead of about non-string keys. The
  issue's range is `:-18446744073709551614` (offset from the end); the
  report ran `18446744073709551614:` (a prefix range). Read the command
  character by character before reading the output.
- **Same failure?** A panic with a Go or Rust stack trace and exit 101
  is not the same event as a graceful `error: Invalid value for
  '--line-range'` and exit 1. A compile error saying `$b is not defined`
  is not the `Invalid path expression` the issue reports. Both are "an
  error"; only one is the bug.
- **Same severity?** An issue titled *crashes the terminal* is not
  confirmed by output that ends "the window remained open afterward and
  the prompt returned." Garbled output and a dead process are different
  outcomes, and the artifact's own words will tell you which happened.
- **Is this an artifact at all?** `zellij --version` printing a version
  banner, `zellij ls` listing a session, or a screenshot of three tabs
  rendering shows that the program runs. The issue is about a pane that
  never renders; nothing here shows a pane, blank or otherwise. Setup
  evidence is not result evidence. Likewise a paragraph of confident
  mechanism ("the RTE's debounced save is cancelled by `loadNote`") with
  no transcript behind it is a hypothesis wearing a report's clothes.

**The negative result is a real result.** A report that says "I could
not reproduce this" and shows what happened instead — the marker file
that came out in the wrong-for-the-bug order, the prompt that rendered
when it should have vanished — has shown behavior. It passes this
family outright. The evidence is the output of the attempt; the value is
that it bounds where the bug is not.

## Honesty

**Where it lives.** At the seam between three things: the report's
Expected/Actual or concluding sentences, the artifact directly above
them, and the claim comment's summary of the whole. Honesty is never a
property of one sentence; it is whether the sentences and the output
agree. Live: the same seam in the draft.

**What good looks like.** Each stated conclusion is the size of the
evidence under it. "Matches the report on a different OS and install
method" sits on two shown runs and names its own limits. "I did not test
scenario 1; this report is about scenario 2 only" fences off what was
not done. "What differed from the report's conditions: OS, shell, and my
prompt shows the resolved physical path" turns a failure to reproduce
into information a maintainer can use. An honest cannot-reproduce that
names what it tried, what it saw, and what it thinks is missing is one
of the strongest packages in this family, and grading it down for
lacking a confirmation is the single easiest mistake to make here.

**What overclaiming looks like.** Volume and certainty standing in for
output. "I ran it ten times with identical results" and "confirmed on
two separate machines" and "100% reproducible" and "guaranteed
reproducible on my end" are all assertions about runs that were never
shown; they add confidence without adding evidence, and they appear most
often in exactly the packages whose one shown artifact is the wrong one.
Watch for three specific shapes: a conclusion that contradicts the
artifact above it; an Expected/Actual pair stated backwards, where
"Expected" describes the bug and "Actual" describes the software working
normally; and a root cause announced as verified ("I verified this race
condition is the cause") with nothing between the claim and the reader.
When words and output disagree, the output is what happened.

## Comms

**Where it lives.** Two candidate texts against two fixed surfaces. The
Candidate claim comment against the Issue section. Both candidate
comments against the Repo facts block — the `contribution policy` line
(CONTRIBUTING.md, AI_POLICY.md, AI_USAGE_POLICY.md and whatever they
link) and the `bug reports` template line. Live: the drafts against the
issue page, `CONTRIBUTING.md`, `.github/` templates, and any AI policy
file those link to; on Path Review, also against `scope.md`'s house
rules, which change what claim comments mean there.

**What a specific, honest claim looks like.** It could not be pasted on
another issue without edits, because it names this one: the failing
command, the file or function it will read, the version it checked, the
symptom in its own words, or a pointer a maintainer left in the thread
("per the pointer above I'll start reading the standard printer in
grep-printer"). And it promises only what its author controls —
investigating, reading, testing, reporting back. The tell for the
opposite is interchangeability: *+1 also seeing this!! any updates??*,
*I want to help with this one*, *Kindly assign it to me, I will fix it
within 2 days guaranteed* would all serve on any issue in any repo, and
the last one additionally promises a date and an outcome nobody can
guarantee — maintainer review time is not the author's to schedule.
Self-assignment stated as done ("Assigning myself to this") claims an
authority a commenter does not have.

**Reading a contribution policy.** Take the words literally and ask only
what they require *of an issue comment*. Three distinct shapes recur,
and they are easy to blur:

- **Disclosure required.** "All AI usage in any form must be disclosed,
  stating the tool used and the extent of the assistance" — then a
  comment that does not say so fails, no matter how good the
  reproduction underneath it is. **Every package graded here is
  AI-assisted work**, in eval mode and live mode alike, so this
  requirement is always live; never reason that the comment looks
  human-written and therefore no disclosure was owed. What satisfies it
  looks like p5.js's package: *"Per the AI usage policy: I used an AI
  assistant to help me organize this report; I ran and verified every
  step myself and I understand what I'm reporting."* Tool, extent,
  ownership.
- **Own words required.** ripgrep: "comments to maintainers must be
  written by humans in their own words, and AI-generated comments may be
  hidden." This asks for voice, not disclosure. A comment that reads as
  a person writing about this specific issue satisfies it; generated
  filler does not. Do not convert it into a disclosure ask.
- **Conditions that do not reach issue comments.** fd asks contributors
  to state the tool and extent *in the pull request* and says outright
  there is no disclosure ask for issue comments. conda asks you to
  review and understand AI-generated content before including it in a
  pull request. prettier asks you to only submit code you understand and
  have tested. These are terms for later beats; an issue comment that
  says nothing about AI satisfies all of them. Silence on AI in a repo
  with no AI policy at all is likewise fine.

Conditions are terms to follow, not bans. The only failure here is a
stated ask, reaching the comments in front of you, that the comments
leave unmet.
