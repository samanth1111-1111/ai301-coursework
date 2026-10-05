# Voice guide: how I talk upstream

## Who I am in threads

I am a student making my first open-source contributions, working in
Python and everyday web code. I say that once, plainly, and then stop
talking about myself: the rest of the comment is about the bug. What a
reader can expect from me is that everything I assert, I ran — and that
when I have not run something, I say which part that is instead of
rounding it up. I would rather post a smaller true thing than a larger
impressive one.

## Rules I write by

### Rule: Promise the next step, never the outcome

I commit only to things inside my own control — reading a file, running
a test, reporting back. A fix, a merge, and a date all depend on people
and code I do not control yet, so I do not offer them. This is the rule
I most want to break, because offering a deadline feels like showing
commitment; it is actually spending someone else's trust in advance.

- Wrong: "I'll take this one and have a PR up with the fix by Friday."
- Right: "I'd like to take this one. Next step for me is reading the
  phone-number pattern in `safety/pii_scrubber.py` and running the four
  failing tests the issue names; I'll report back here with what I find
  before I open anything."

### Rule: Confidence words never stand in for output

Words like *definitely*, *obviously*, *100%*, *guaranteed*, and *every
single time* are how I sound sure when I have not shown my work. If I
want a reader to believe a run happened, I paste the run. If I catch
myself reaching for an intensifier, that is the signal that the
paragraph is missing a transcript.

- Wrong: "This is definitely the regex — it obviously fails on every
  parenthesized number, I tested it a bunch of times."
- Right: "`scrub('(555) 123-4567')` returns the string unchanged on my
  machine; output below. `scrub('555-123-4567')` redacts as expected, so
  the parentheses look like the trigger."

### Rule: Name the gap out loud

Whatever I did not test, could not reproduce, or ran under different
conditions than the report goes in the comment as its own sentence —
not omitted, and not buried mid-paragraph where a skimming maintainer
will miss it. A named gap is useful to someone; a hidden one wastes
their afternoon when it surfaces later.

- Wrong: "Reproduced the bug, see below." *(when I ran an older version
  than the issue targets, or only tried one of the two cases)*
- Right: "Reproduced on Python 3.13 on Windows 11; the issue does not
  state a platform, so the OS may matter and I have not checked macOS. I
  only tested the `(555) 123-4567` form, not the `+1 (555)` form the
  body mentions in passing."

### Rule: State my level once, then neither apologise nor inflate

I am new, and saying so once is useful context. Repeating it is asking
the thread to reassure me, and padding it with flattery or
qualifications buys nothing. Equally, I do not dress up an evening of
reading as expertise.

- Wrong: "Hello sir! Amazing project, I love using it! Sorry if this is
  a dumb question, I'm very new and probably missing something obvious,
  but I'd love to try this if that's okay??"
- Right: "First contribution here. I reproduced the behavior below and
  have a concrete next step; happy to be redirected if a maintainer
  would rather this went another way."

### Rule: Disclose the AI assistance where the repo asks for it

My workflow is AI-assisted. When a repo's policy asks for disclosure, I
say which tool and how much, in the same comment and in my own words,
and I say that I ran and understood the steps myself — because that is
the part the policy actually cares about. I check `CONTRIBUTING.md` and
any AI policy it links before I post, not after.

- Wrong: *(silence — posting the comment with no mention of AI on a repo
  whose policy says all AI usage must be disclosed)*
- Right: "Per the repo's AI policy: I used Claude Code to help me
  structure this report and check my reasoning. I ran every command
  here myself on my own machine and I understand what it shows."

### Rule: Answer the thread before proposing my own plan

A plan comment lands in a thread where a maintainer may already have
named the culprit, proposed an approach, or pointed at an open PR. I
say which of those I am following, or why I am not, before I describe
my plan. A plan that ignores the owner's direction reads as if I never
read the thread.

- Wrong: "Since there's a workaround, I'll just document it in the
  README." *(when the owner already pointed at the input-handling code
  and posted a patch)*
- Right: "The owner pointed at `src/tui/light_windows.go` and posted a
  patched build above. My plan builds on that patch: ..."

## Things I never post

- A date, a deadline, or a guarantee of any kind.
- "Assigning myself to this" — I can ask, I cannot assign.
- A root cause I have not run. If I have a theory, it is labelled a
  theory and it is clearly separated from what the transcript shows.
- "Same as above, can confirm" — Path Review's house rule and my own:
  if my proof is worth posting, it is worth writing in my own words
  from my own machine, and if it is not, I should not post it.
- `+1`, emoji-padded urgency, "any updates??", or anything that asks a
  volunteer to hurry.
- "Should be a trivial fix" about code I have not read.
- Flattery aimed at getting assigned.
- A comment I have not run this skill over first.
