# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

grenver

**Plan comment**

TODO-PASTE-COMMENT-PERMALINK-HERE

Plan for #56, from my repro above (`plain doc chunks: 0`, `headed doc chunks: 1`, `test_document_with_no_headings` XFAIL).

Cause: `_extract_sections()` in `ingestion/chunking/structural_chunker.py` only collects content lines once a heading has been seen (line 120) and only emits a section when `heading_stack` is non-empty (lines 93-94, 124). With no headings nothing is collected, so `chunk()` returns `[]`. That's why adding one `# Heading` gets you 1 chunk.

Fix: in `_extract_sections()` only, collect lines before the first heading and emit them as a section with an empty heading path and level 0 (using `current_level`, which is currently set but never read) when the text isn't blank. Remove the xfail marker on the test. Since `current_level` gets used now, I'll also drop its `# noqa: F841` and the "Leave as-is" comment.

Not changing: the heading regex, how headed docs are split, `SemanticChunker`, `StrategySelector`, or the mypy override in `pyproject.toml`.

Test: re-run my repro. Expect `test_document_with_no_headings PASSED` and `plain doc chunks: 1`, with `headed doc chunks: 1` unchanged. New tests cover the empty `heading_path`/level 0, a headingless doc over 800 tokens, and blank lines before a heading not creating an empty chunk. Lint, typecheck and unit tests before pushing.

Side effect: text above a doc's first heading will now become its own chunk instead of being dropped (same cause). I haven't checked whether anything on the RAG side expects a non-empty `heading_path`.

Branch: `fix/56-headingless-chunks`.

---

## Your branch

**Branch**

fix/56-headingless-chunks

**Evidence**

Fork `grenver/pathreview-ai301-fa26-s3`, Windows 11, Git Bash, Python 3.14.7 venv,
`pip install -e ".[dev]"`. The snippet is the same one from my unit 2 repro (step 5),
saved as `repro56.py`:

```python
from ingestion.chunking.structural_chunker import StructuralChunker

chunker = StructuralChunker()
plain = "This is a plain document with no headings at all. " * 20
headed = "# Heading\nThis is content."

print("plain doc chars:", len(plain))
print("plain doc chunks:", len(chunker.chunk(plain, {})))
print("headed doc chunks:", len(chunker.chunk(headed, {})))
```

Before (branch `main`, commit `2f4e82f`, unmodified):

```
$ .venv/Scripts/python.exe -m pytest tests/unit/test_structural_chunker.py -v -rxX
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings XFAIL [ 20%]
XFAIL tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings - issue #56: structural chunker drops documents with no headings
======================== 14 passed, 1 xfailed in 0.27s ========================

$ PYTHONPATH=. .venv/Scripts/python.exe repro56.py
plain doc chars: 1000
plain doc chunks: 0
headed doc chunks: 1
```

After (branch `fix/56-headingless-chunks`, xfail marker removed):

```
$ .venv/Scripts/python.exe -m pytest tests/unit/test_structural_chunker.py -v -rxX
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_empty_input_returns_empty_list PASSED [  5%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_whitespace_only_input PASSED [ 11%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings PASSED [ 16%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings_keeps_text_and_metadata PASSED [ 22%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_long_document_with_no_headings_is_sub_chunked PASSED [ 27%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_blank_lines_before_first_heading_add_no_chunk PASSED [ 33%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_nested_headings PASSED [ 38%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_path_format PASSED [ 44%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_large_section_sub_chunked PASSED [ 50%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunk_metadata_includes_heading_level PASSED [ 55%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunk_metadata_structure PASSED [ 61%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_preserve_source_metadata PASSED [ 66%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_multiple_h1_headings PASSED [ 72%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_path_breadcrumb PASSED [ 77%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunks_have_text_content PASSED [ 83%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_section_extraction_with_multiple_levels PASSED [ 88%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_not_in_middle_of_content PASSED [ 94%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_empty_sections_handled PASSED [100%]
============================= 18 passed in 0.24s ==============================

$ PYTHONPATH=. .venv/Scripts/python.exe repro56.py
plain doc chars: 1000
plain doc chunks: 1
headed doc chunks: 1

$ .venv/Scripts/python.exe -m pytest tests/unit -q
379 passed, 52 xfailed, 1 warning in 40.26s

$ .venv/Scripts/python.exe -m ruff check .
All checks passed!

$ .venv/Scripts/python.exe -m mypy api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. `20/20 scored items` (full run, saved to `eval-run.txt`). Categories: `clear-accept 7/7
   scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`, bar `18/20:
   PASS`. I set `PYTHONUTF8=1` up front because of the Windows encoding crash from unit 2.
   Since it passed first time, I didn't do any `--only` re-runs or change the rubric,
   procedure, or evidence guide afterwards.

**Package analysis**

`pkg-14` (`zellij-org/zellij#5174`, clear-accept). My verdict: `accept`. Gold: `accept`.
I expected this one to be close because it leaves two things open: "exact functions to be
pinned in the PR after tracing the query issuance with debug logs", and it defers the
Windows session-switch variant from the thread. It still passes `executable` because it
picks one approach (drain pending OSC query responses in the reattach path before pane
input is wired) and names the area (`zellij-server` attach handling, `zellij-client` query
issuance). My rubric allows "Pinning exact functions later is fine if the file/area and the
approach are already chosen." The Windows deferral is marked out of scope with a reason,
so `bounded-scope` passes. The cause fits every control (fresh attach clean, 0.44.1 clean,
cache-clear clean once) and the test is "5 consecutive SSH reattach cycles with no rgb
strings in any pane". Compare pkg-18 (gold `reject`): "recover() 'somewhere'", "upstream or
vendored, whichever is easier". The difference is whether the main decision has been made.

**Check rationale**

From `rubric.md`, `executable`: "Pass if a stranger could start the work today: the plan
has chosen one approach and names where the change goes (a file, module, or code path).
Pinning exact functions later is fine if the file/area and the approach are already
chosen. Fail if the plan leaves the main decision open ("X or Y, whichever is easier", "not
sure which layer", "somewhere"), or if its only action is to investigate, profile, or poke
around without a chosen change."

I first thought of it as "names the exact files and functions", but that would reject
pkg-14 (gold accept), which defers function-level detail on purpose. Looking at the three
unbuildable rejects (pkg-10, pkg-17, pkg-18), what they share isn't missing detail.
They haven't picked an approach ("gocui? tcell? not sure", "whichever is easier",
profile-and-see). So the check asks whether the decision is made, and the quoted phrases
give the grader examples of what an open decision looks like.

**Trade-offs**

It takes the plan's word that an approach is chosen. A plan that names a file and says
something vague like "refactor the attach path to handle it" would pass, since none of
the open-decision phrases appear. I'm accepting that miss, because judging how specific an
approach is gets subjective fast. `decisive-test` catches some of it, since a vague
approach usually comes with a vague test plan. I didn't re-run a canary because the
wording didn't change after the full run. The only file I edited afterwards is
`voice-guide.md`, which eval mode doesn't read.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
