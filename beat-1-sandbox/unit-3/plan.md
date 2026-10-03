# Plan for #56: structural chunker drops documents with no headings

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56
Built from my repro comment:
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5869685454

## Repro evidence this plan relies on

From my repro on my fork at `main`, commit `2f4e82f` (Windows 11, Git Bash,
Python 3.14.7, pytest 9.1.1, tiktoken 0.14.0, `pip install -e ".[dev]"`):

Step 4, `python -m pytest tests/unit/test_structural_chunker.py -v -rxX`:

```
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings XFAIL
======================== 14 passed, 1 xfailed in 3.93s ========================
```

Step 5, the issue's snippet plus a heading control:

```
plain doc chars: 1000
plain doc chunks: 0
headed doc chunks: 1
```

The control matters: the same `chunk()` call returns one chunk as soon as the
document has a single `# Heading` line, so the drop depends only on whether a
heading has been seen, not on document length or the tokenizer.

## Diagnosis

The cause is in `_extract_sections()` in
`ingestion/chunking/structural_chunker.py`, not in `chunk()`:

- Line 120: a content line is only collected `if heading_stack or
  current_section_lines`. With no heading yet, both are empty, so every line
  of a headingless document is thrown away.
- Lines 93-94 and 124: a section is only emitted when `heading_stack` is
  non-empty, so even collected lines could never become a section without a
  heading.
- Lines 113-116: the seeded comment says the defect directly: "the tracked
  heading level is never read back, so heading-less documents get dropped."
  `current_level` is assigned (with `# noqa: F841` silencing the unused
  variable) but never used.

`_extract_sections()` returns `[]`, so `chunk()`'s loop never runs and it
returns `[]`. That matches step 5 (`plain doc chunks: 0`), and the heading
control (`headed doc chunks: 1`) shows the same function works once
`heading_stack` is non-empty.

## Scope

In scope, one change: make `_extract_sections()` keep content that appears
before any heading, so a headingless document becomes one section with an
empty heading path and level 0, and then flows through `chunk()`'s existing
size logic (single chunk under 800 tokens, semantic sub-chunks above).

Not in scope:

- The heading regex, the `heading_stack` pop/push logic, or how headed
  sections are split. Headed documents must chunk exactly as they do today.
- `SemanticChunker`, `StrategySelector`, or any change to which chunker a
  `source_type` gets.
- The `var-annotated` mypy override for `ingestion.chunking.structural_chunker`
  in `pyproject.toml`. It is about `heading_stack = []`, not issue #56.
- Any other lint or type debt in the file.

## Files

- `ingestion/chunking/structural_chunker.py`: `_extract_sections()` only.
- `tests/unit/test_structural_chunker.py`: remove the xfail marker on
  `test_document_with_no_headings` and add regression tests.

## Approach

1. In `_extract_sections()`, collect every non-heading line into
   `current_section_lines` (drop the `heading_stack or current_section_lines`
   gate on line 120).
2. When a section is saved (at the next heading and at the end), emit it when
   `heading_stack` is non-empty, as today, or when there is no heading yet but
   the collected text is non-blank. The headingless section gets
   `path = []` (so `heading_path == ""`) and `level = current_level`, which is
   0 before any heading. Using `current_level` for the level is the read-back
   the seeded comment says is missing; for headed sections it equals
   `heading_stack[-1][0]`, so their level is unchanged.
3. Since `current_level` is now read, its `# noqa: F841` no longer
   suppresses anything, and the seeded-defect comment on lines 113-115
   ("Leave as-is") describes a bug that is gone. I'll remove both. This is my
   own call, not a CONTRIBUTING rule: CONTRIBUTING's "remove its suppression
   too" is about the `pyproject.toml` entries, and it separately says not to
   "clean up" deliberate `# noqa` lines. I'm removing this one only because
   the fix makes it untrue, and I'll say so in the PR.
4. Remove `@pytest.mark.xfail(strict=True, ...)` from
   `test_document_with_no_headings` (CONTRIBUTING: the strict marker turns a
   fixed test into an `XPASS(strict)` failure).
5. Add regression tests: a headingless doc gives one chunk whose text is the
   whole document, `heading_path == ""`, `heading_level == 0`; a headingless
   doc over 800 tokens gives more than one chunk; whitespace-only content
   before the first heading does not create an empty chunk.
6. Run `make lint`, `make typecheck`, `make test-unit` before committing.
   Commit as `fix(ingestion): ...` with `Fixes #56`, on branch
   `fix/56-headingless-chunks`.

## Test plan

Re-run my unit 2 repro steps against the change, same environment:

- Step 4, `python -m pytest tests/unit/test_structural_chunker.py -v -rxX`:
  before was `14 passed, 1 xfailed`. Expected after:
  `test_document_with_no_headings PASSED`, no `XFAIL` or `XPASS` lines, and
  every other test still passing (the 14 old ones plus my new ones).
- Step 5, the issue's snippet with the heading control. Before:
  `plain doc chunks: 0`, `headed doc chunks: 1`. Expected after:
  `plain doc chars: 1000`, `plain doc chunks: 1`, `headed doc chunks: 1`.
  The headed count staying at 1 shows headed documents are unchanged.
- `make lint` and `make typecheck` pass, so removing the `noqa` adds no new
  finding.

## Risks and unknowns

- Behavior change beyond the issue's exact case: a document with text before
  its first heading (an intro paragraph above `# Title`) will now produce an
  extra chunk for that text, where today the text is silently dropped. It is
  the same root cause, so I'm keeping it, and I'll say so in the PR. I have
  not checked whether any README fixture in `tests/` relies on that preamble
  being dropped; the full `make test-unit` run will show it.
- Downstream code may assume `heading_path` is non-empty. In this repo the
  only reader I found is `StrategySelector`, which passes chunks through
  without reading it, but I haven't traced the RAG retrieval side.
- I don't know yet if the semantic sub-chunker keeps the empty `heading_path`
  it is given for a long headingless doc. The over-800-token test in step 5
  of the approach will show it.

## Deviations

Nothing in the change differs from the plan. The build on
`fix/56-headingless-chunks` touches only `_extract_sections()` and the
test file, in the order of the approach steps above, and the posted plan
is still accurate.

What the build settled that the plan left open:

- The semantic sub-chunker does keep the empty path: a 300-sentence
  headingless doc gave 7 chunks, all with `heading_path == ""` and
  `heading_level == 0`.
- No existing test relied on text before the first heading being dropped:
  `pytest tests/unit` gives `379 passed, 52 xfailed` (the other xfails are
  other seeded issues).
- `make lint` and `make typecheck` (mypy on `api/ core/ ingestion/ rag/
  agent/ safety/`) are clean. Running `mypy .` over the whole repo reports 3
  `var-annotated` errors in `tests/unit/test_relevance_scorer.py` and
  `tests/unit/test_faithfulness_checker.py`; the same 3 appear on `main`
  without my change, so I'm leaving them out of scope.
- Still open: I have not traced the RAG retrieval side for code that
  expects a non-empty `heading_path`.
