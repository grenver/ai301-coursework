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

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/144

**Branch**

fix/56-headingless-chunks

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. `agreement: 20/20 scored items  (bar: 18/20: PASS)` (full run, saved to `eval-run.txt`).
   Categories: `clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2
   unreviewable 3/3`. I ran with `PYTHONUTF8=1` again because of the Windows encoding
   crash from unit 2. It passed first time, so I didn't do any `--only` re-runs, and I
   haven't changed `SKILL.md`, `rubric.md`, `procedure.md`, or `evidence-guide.md` since.

**Package analysis**

`pkg-01` (`pandas-dev/pandas#66657`, standards-wall). My verdict: `reject`. Gold: `reject`.

This one has nothing wrong with the change itself. My tool passed `plan-fidelity` ("Diff
touches only tokenizer.c (capacity check in tokenize_bytes) and one regression test,
matching the plan's files and scope"), `decisive-evidence` ("100000 after vs 99999 before;
ASAN clean on branch vs overflow on main; parser suite '2841 passed, 40 skipped'") and
`clean-diff`. It only failed `repo-standards`: "Template requires 'closes #xxxx'
(description never links #66657) and a whatsnew entry for bug fixes (diff has no
doc/source/whatsnew change)."

It reads it that way because the repo facts say "the PR template is a required checklist:
closes #xxxx with the issue number, ... and an entry in the latest
doc/source/whatsnew/vX.X.X.rst when fixing a bug", and my check fails when "a required
section, checklist, or issue link is missing, a required changelog/whatsnew entry is
absent from the diff". The whatsnew part is why the check also reads the diff and not just
the description. pkg-12 shows the same thing from the other side: its description says
"CHANGELOG entry included" but the diff has no CHANGELOG.md change. Without a check like
this, pkg-01 would be a clean accept, which is the point of the standards-wall category.

**Check rationale**

From `rubric.md`, `clean-diff`:

"Pass if every hunk is part of the fix, its tests, or a file the repo or plan asks for, and
the diff carries none of the debris listed under Fail. Comments that explain the fix, and
unchanged context lines shown inside a hunk that also changes the same statement, are
fine. Fail if the diff contains any of: a debug print or log line (including a
commented-out one), commented-out code or an earlier attempt left in, a dead or unused
function or variable (including ones hidden behind `allow(dead_code)` or similar), a
leftover TODO/FIXME, a hunk or block that only re-indents, reformats, reorders imports, or
removes and re-adds identical lines, or an edit to code, text, or files unrelated to the
fix."

The Fail list comes from the debris in the three unreviewable packages: pkg-12's
`// eprintln!("DBG ...")` and `_unused_buffer_probe`, pkg-15's `#[allow(dead_code)]
count_open_fds`, and pkg-18's import reshuffle and re-printed `println!` blocks. The
sentence about "unchanged context lines shown inside a hunk that also changes the same
statement" is there because of pkg-02 (gold accept). Its `save_html` hunk removes and
re-adds `html = self.export_html(` and `theme=theme,` unchanged, because `clear=clear`
became `clear=False` in the same call. A plain "no removed-and-re-added identical lines"
rule would reject pkg-02 for the same thing that rejects pkg-18. The difference is
whether the block also contains a real change. I also listed "commented-out" debug lines
on purpose, since pkg-18's only debug line is commented out and a "no debug prints" rule
could read past it.

**Trade-offs**

The context-line exception is a judgment call for the grader, and the run shows it isn't
applied evenly. On pkg-04 the tool failed `clean-diff` because the hunk "removes and
re-adds identical lines (`let flags = match parsed_version {` and the `Some(v) if v >=
530` and `Some(_)` arms)", which is the same shape it let through on pkg-02. pkg-04 is a
reject anyway (it fails `decisive-evidence` first), so the score didn't move, but on a
clean accept that reading could cost a match.

`repo-standards` has a similar risk. On pkg-06 it cited "Description has no AI-use
disclosure" even though that repo states no AI policy. The rubric only asks for a
disclosure "when the policy requires disclosure of all AI usage or of AI use in pull
requests", so this was an over-read. Again it didn't change the verdict (pkg-06 fails
`plan-fidelity` first), but it would wrongly reject an otherwise clean PR in a repo with no
AI policy. pkg-02, pkg-13 and pkg-19 have no AI policy and passed this check, so I left the
wording alone instead of spending another full run on it.
