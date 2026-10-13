# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/samanth1111-1111/pathreview-ai301-fa26-s1/pull/1

**Branch**

`fix/53-pii-parenthesized-phone`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run 1: crashed before it graded anything, so it has no score. On
   Windows, Python sent the prompt to `claude` as cp1252 text and stopped with
   `UnicodeEncodeError: 'charmap' codec can't encode characters`. I changed no
   skill files and re-ran with `PYTHONUTF8=1`.
2. Full run 2: `agreement: 19/20 scored items  (bar: 18/20: PASS)`, with
   `categories: clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.
   This is the run in `eval-run.txt`.

I did no `--only` re-runs, because the first scored run already cleared the
bar and every category had a match.

**Package analysis**

**pkg-05** (nushell/nushell#18848, category clear-accept). My rubric decided
**reject**. The gold label is **accept**.

My tool passed plan-scope, plan-complete, description-fidelity, repo-checks,
clean-diff, template-asks, and ai-disclosure. It failed one check,
repro-evidence, with this evidence line: "The same-name same-key case is not
re-run; only the unsupported claim 'Same-key redefine prints the one-time
warning.' appears, with no command or output."

It read the package that way because of this sentence in my repro-evidence
pass condition: "Every distinct failure case the plan's test plan names must
be re-run, except a case the plan or description explicitly defers." I wrote
that sentence for calib-04, where the plan names two crashes and the evidence
re-runs only one. In pkg-05, though, the same-key warning isn't a failure
case from the issue. It's new behavior the fix adds. The issue's actual
failing case (atuin's two bindings collapsing into one, so ctrl-r reverts) is
shown before and after in the keybinding table, which goes from one `atuin`
row to two. So my rubric treated "any case the test plan names" as if it
meant "a failing case the repro reproduced". To fix it, I would limit that
clause to failure cases from the repro evidence, and run tests.

**Check rationale**

From `tools/pr-precheck/rubric.md`, as it reads now:

> | ai-disclosure | The repo facts' contribution policy (AI policy) read against the description; in live mode, the course's standing disclosure ask | Treat every package as AI-assisted work. Passes if the repo states no AI policy that requires disclosure (eval), OR the description discloses AI use in the author's own words, naming the tool or the extent of help, where the policy (or, live, the course) requires it. Fails if the policy requires disclosure of AI use and the description contains none. | required |

Why it reads that way: the check is based on what the repo states, not on my
own preference. Most packages say "no stated AI policy". If I failed those
for having no disclosure, I would reject clear accepts like pkg-19. The rule
"Treat every package as AI-assisted work" is what makes a good fix with no
disclosure still fail when the policy demands one, as in pkg-20, where
ghostty's policy says "All AI usage in any form must be disclosed". I
rejected the idea of folding this into a general "template" check, because
a rubric that only looks at template sections never reads the AI policy line
and would miss pkg-20. With only two standards-wall packages, the category
floor depends on catching it. The live-mode clause is there because the
course always asks for disclosure, even though Path Review's template has no
section for it.

**Trade-offs**

My repro-evidence check gives up strictness. My group's worksheet said a
note after `#` is not proof, but pkg-11 (a gold accept) shows its after-run
exactly that way: "`# after: searches normally, no error`". The clear
accepts also state suite results as a sentence ("`go test ./...` passes
(4108 tests)"). So I wrote the check to accept an annotated transcript "when
it names the observable result", and a suite outcome with its command. The
case I accept it will miss is a dishonest `# after:` comment that names an
outcome nobody actually observed. In the other direction, the "every distinct
failure case" clause changed pkg-05's result to a disagreeing reject. I kept
it anyway, because letting a skipped failure mode through (calib-04's second
crash) seemed worse than holding a PR a reviewer would ask for one more
transcript on. I did not grade calibration packages, so I haven't confirmed
that calib-04 is caught.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
