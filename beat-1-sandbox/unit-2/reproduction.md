# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

grenver

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5869546963

Claiming issue #56. Plan: reproduce the reported drop using the repro
script referenced in the issue (a ~1000-char document with no headings
returning 0 chunks) and run the existing `test_document_with_no_headings`
test locally, then report back here with what I find before starting on
a fix.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5869685454

Repro report for #56: I reproduced it.

**Environment**
- Repo: my fork (`grenver/pathreview-ai301-fa26-s3`), fresh clone, branch `main`, commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088` (2026-09-16)
- OS: Windows 11 Pro, commands run in Git Bash
- Python 3.14.7 in a fresh venv; pytest 9.1.1; tiktoken 0.14.0
- Setup: `pip install -e ".[dev]"` only. Did not start Docker/Postgres/Redis — this bug lives entirely in a unit-tested module, and `pyproject.toml` marks unit tests as having no external dependencies.

**Steps**
1. `git clone https://github.com/grenver/pathreview-ai301-fa26-s3.git && cd pathreview-ai301-fa26-s3`
2. `python -m venv .venv`
3. `.venv/Scripts/python.exe -m pip install -e ".[dev]"`
4. `.venv/Scripts/python.exe -m pytest tests/unit/test_structural_chunker.py -v -rxX`
5. Ran the issue's own snippet plus a heading control:

```python
from ingestion.chunking.structural_chunker import StructuralChunker

chunker = StructuralChunker()
plain = "This is a plain document with no headings at all. " * 20
headed = "# Heading\nThis is content."

print("plain doc chars:", len(plain))
print("plain doc chunks:", len(chunker.chunk(plain, {})))
print("headed doc chunks:", len(chunker.chunk(headed, {})))
```

**Output**

Step 4:
```
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings XFAIL
======================== 14 passed, 1 xfailed in 3.93s ========================
```

Step 5:
```
plain doc chars: 1000
plain doc chunks: 0
headed doc chunks: 1
```

**Result**

`StructuralChunker.chunk()` returns an empty list for the ~1000-char headingless document, exactly matching the issue. The same call with a single `# Heading` line returns one chunk.

Reading `_extract_sections()` in `ingestion/chunking/structural_chunker.py`, the source itself names the defect at lines 113-116: a comment on the heading-stack update states "the tracked heading level is never read back, so heading-less documents get dropped." Concretely: content lines are only appended to `current_section_lines` when `heading_stack` is non-empty (line 120), and a section is only emitted when both `current_section_lines` and `heading_stack` are non-empty (lines 93-101 and 124) — so a document with zero headings never populates `heading_stack`, and therefore never emits a section. I haven't changed any code to confirm a fix yet.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. `2/3 scored items` — smoke run (`--limit 3`, pkg-01..03) on the rubric as first drafted
   from studying the eval set and gold labels. pkg-03 (gold `accept`) disagreed, failing
   `evidence-backed`. Re-running pkg-03 alone (`--only pkg-03`) immediately after gave
   `accept` with `evidence-backed` passing on the same package text — a sign of grading
   variance in the judge model on that run, not a rubric defect.
2. `16/17 scored items` (3 errored) — first full run. Three packages (`pkg-04`, `pkg-07`,
   `pkg-11`) crashed with `UnicodeEncodeError: 'charmap' codec can't encode character
   ...: character maps to <undefined>` writing emoji-containing package text to the
   child process's stdin under Windows' `cp1252` codepage — a harness/platform issue, not
   a rubric problem. Of the 17 that did grade, one disagreed: `pkg-09` (gold `accept`)
   came back `reject`, failing `steps-followable`.
3. `4/4 scored items` — `--only pkg-04,pkg-07,pkg-09,pkg-11`, after exporting
   `PYTHONUTF8=1` and `PYTHONIOENCODING=utf-8` before the run to fix the encoding crash.
   All four graded and all four agreed, including `pkg-09` (`accept`), confirming the
   `pkg-09` and `pkg-03` disagreements above were judge-model noise on a specific run
   rather than a wording problem in the rubric — no check text was changed between
   attempts 2 and 3.
4. `20/20 scored items` — confirming full run, saved with `--save-run` to `eval-run.txt`.
   All five categories matched (`clear-accept 8/8 disclosure 1/1 no-evidence 4/4
   unfollowable-comms 3/3 wrong-target 4/4`), bar 18/20: PASS. This is the score in the
   committed file.

**Package analysis**

`pkg-09` (source `sharkdp/fd#2033`, category `clear-accept`). My rubric's verdict:
`accept`. Gold label: `accept`. Reasoning: the report is an honest cannot-reproduce, not a
positive confirmation. The candidate tried to trigger the argument-size-limit reordering
the issue describes, ran it five times plus a padded-argument variant, and in every run
observed the opposite of what would confirm the bug (`ONE` batches always land before
`TWO`). `environment-recorded` passes (fd version, distro, kernel, and `ARG_MAX` all
named). `steps-followable` passes because a stranger can run the exact commands shown
(the file-generation loop, the two `--exec-batch` commands, the log inspection) and reach
the same attempt, even though that attempt does not trigger the bug — followability is
about whether the steps are concrete and reproducible, not about whether they produce a
positive result. `faithful-target` passes under the rubric's explicit clause for a report
that "honestly states it could not trigger that behavior and shows what happened
instead." `evidence-backed` passes because the negative claim is backed by the actual log
output across multiple runs, and the report names concretely what might differ (uniform
file-name lengths, a 2 MiB `ARG_MAX` versus a possibly-needed smaller or uneven limit)
rather than just asserting failure. `claim-specificity` passes on the comment's tie to the
thread's own suggested acceptance test. `disclosure-conventions` passes because fd's
stated policy asks for disclosure in pull requests but states no disclosure ask for issue
comments, and none is given. Every required check passing gives `accept`, matching gold —
this package is also the one that flickered to a false `reject` in run 2 above, which is
why I re-ran it in isolation before trusting the rubric wording rather than editing it.

**Check rationale**

From `rubric.md`, the `faithful-target` pass condition (as currently written): "Pass if
the shown artifact demonstrates the issue's exact reported behavior (same trigger, same
failure mode/exit condition), or if the report honestly states it could not trigger that
behavior and shows what happened instead. Fail if the artifact shows a different behavior
(a different error type, a graceful failure instead of the reported crash, a different
code path) while the report narrates it as confirming the issue." I wrote it with the
"or" clause from the start, after reading `pkg-09` and `pkg-10` (both gold `accept`,
honest cannot-reproduce) side by side with `pkg-02`, `pkg-08`, `pkg-16`, and `pkg-17` (all
gold `reject`, `wrong-target`): the difference between them is never whether the artifact
shows the reported bug, it is whether the report is honest about what the artifact
actually shows. A check that only accepted artifacts matching the issue's behavior would
have wrongly failed both honest-negative packages; a check with no target-fidelity
condition at all would have let the four wrong-target packages through on the strength of
their confident narration. The clause exists to hold those two apart without conflating
"did not happen" with "misrepresented."

**Trade-offs**

The trade-off is that `faithful-target` alone would be gameable: a report could claim an
honest cannot-reproduce while barely attempting the trigger, and this check's wording
would still pass it, since it only asks whether the *stated* outcome matches what the
artifact shows, not how hard the attempt was. The `evidence-backed` check is what
actually closes that gap — it requires the negative claim to be backed by a concrete
observation of what was tried and what differed (which is exactly what separates `pkg-09`
and `pkg-10` from a package that just asserted "couldn't repro, moving on"). I did not
have to re-run a canary for this, because no wording in either check changed between the
first full run and the confirming run (only the harness's stdin encoding was fixed, and
the two flickers were re-confirmed rather than patched around); the case I accept this
combination could still miss is a cannot-reproduce report that fabricates the appearance
of a real attempt in prose without any inspectable output at all — a failure mode the
20-package set does not contain an example of, so I have not verified the pair catches
it.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
