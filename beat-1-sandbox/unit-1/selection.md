# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56

**Verdict output**

```
Scope: repo confirmed in scope (`codepath/pathreview-ai301-fa26-s3` per scope.md).
Path Review house rule applies: other students' claim comments in this repo do not
count as active claims.

Rubric: 5 checks from rubric.md (repo-active, contribution-policy, bounded-scope,
available — all required; task-specified — preferred). Verdict rule: accept only if
every required check passes; a required unclear counts as fail.

Candidates graded:
1. https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56
2. https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62
3. https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73

## Issue #56 — Structural chunker silently drops documents that contain no headings

- repo-active: pass — repo not archived; default branch `main` last pushed
  2026-09-16 (commit 2f4e82f, human author Andrew Burke), well within 180 days of
  today.
- contribution-policy: pass — `docs/CONTRIBUTING.md` gives a full workflow (branch
  naming, commit format, CI requirements) and states no AI-use restriction anywhere;
  silence passes per rubric.
- bounded-scope: pass — one bug in `StructuralChunker.chunk()`; the issue names the
  exact reproduction (chunking a ~1000-char headingless document returns 0 chunks),
  the fix location, and a named failing test (`test_document_with_no_headings`).
  Single symptom, single root cause, no bundled deliverables.
- available: pass — assignees: none; issue untouched since it opened on 2026-09-10
  (updated_at == created_at, 0 comments); a repo-wide `is:pr is:open` search returns
  0 results, so no open PR references it.
- task-specified (preferred): pass — reproduction script with exact observed (0
  chunks) vs. expected (non-zero) behavior, plus the specific failing unit test to
  make pass.
- Verdict: accept

## Issue #62 — Health check references `settings.redis_host`, which does not exist on Settings

- repo-active: pass — same repo facts as above.
- contribution-policy: pass — same CONTRIBUTING.md, same silence.
- bounded-scope: pass — one bug in `api/routes/health.py`'s Redis probe: it reads
  `settings.redis_host`/`settings.redis_port`, which do not exist on `Settings`
  (only `redis_url` does), so the probe's broad `except Exception` reports Redis
  down even when it is reachable. Single root cause, single fix, concrete repro
  (`GET /health` with Redis running yields a 503 and an `AttributeError` for
  `redis_host` in the log).
- available: pass — assignees: none; 0 open PRs repo-wide. One comment: skonda29
  (author association NONE), "Hello I am a student of codepath, and would like to
  work on this issue" (2026-09-21). Under the Path Review house rule in scope.md,
  classmates' claim comments do not count as active claims, so this does not fail
  availability.
- task-specified (preferred): pass — exact broken attribute named, the correct field
  to use instead named, concrete repro with expected status code and log line.
- Verdict: accept

## Issue #73 — README and `.env.example` disagree about which LLM API key to set

- repo-active: pass — same repo facts as above.
- contribution-policy: pass — same CONTRIBUTING.md, same silence.
- bounded-scope: pass — one documentation-consistency fix across two named files
  (`README.md`, `.env.example`); explicit success criterion ("make the two files
  agree"), with `core/config.py` named as the source of truth. No open design
  decision.
- available: pass — assignees: none; 0 comments; 0 open PRs repo-wide.
- task-specified (preferred): pass — exact discrepancy named (`OPENROUTER_API_KEY`
  vs. the `mock`/`openai` choices `.env.example` currently documents).
- Verdict: accept

## Ranked summary (fit profile: Python, practical AI/software-engineering work, debugging, small testable changes)

All three candidates are accepted. Ranked by fit:

1. #56 (structural chunker) — a Python bug inside the RAG ingestion pipeline
   itself, the most "practical AI" of the three, with a ready repro script and an
   existing unit test to flip from failing to passing, which makes the fix
   directly verifiable.
2. #62 (health check redis_host) — an equally clear, equally small Python bug with
   a crisp repro, but on an operational health-check endpoint rather than the AI
   pipeline itself.
3. #73 (README/.env.example) — a clean, low-risk documentation fix, but involves no
   code or debugging.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "not archived; default branch last pushed 2026-09-16 by a human author, well within 180 days of today"},
      {"name": "contribution-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states a full workflow with no AI-use restriction; silence passes"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "single bug in StructuralChunker.chunk() with a named repro and a named failing test, no bundled deliverables"},
      {"name": "available", "grade": "pass", "evidence": "assignees: none; 0 comments since 2026-09-10; 0 open PRs repo-wide"},
      {"name": "task-specified", "grade": "pass", "evidence": "repro script shows observed 0 chunks vs. expected non-zero, and names the failing unit test"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "not archived; default branch last pushed 2026-09-16 by a human author, well within 180 days of today"},
      {"name": "contribution-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states a full workflow with no AI-use restriction; silence passes"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "single bug: health probe reads settings.redis_host/redis_port, which do not exist on Settings (only redis_url does); single fix, single symptom"},
      {"name": "available", "grade": "pass", "evidence": "assignees: none; 0 open PRs repo-wide; one comment is a classmate's claim, which the Path Review house rule in scope.md says does not block"},
      {"name": "task-specified", "grade": "pass", "evidence": "exact broken attribute named, correct field (redis_url) named, concrete repro via GET /health with expected log line"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "not archived; default branch last pushed 2026-09-16 by a human author, well within 180 days of today"},
      {"name": "contribution-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states a full workflow with no AI-use restriction; silence passes"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "one documentation consistency fix across two named files, explicit success criterion, core/config.py named as source of truth"},
      {"name": "available", "grade": "pass", "evidence": "assignees: none; 0 comments; 0 open PRs repo-wide"},
      {"name": "task-specified", "grade": "pass", "evidence": "exact discrepancy named between README.md and .env.example, with the field names to reconcile"}
    ],
    "verdict": "accept"
  }
]
```
```

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. `0/1 scored items` — smoke run (`--limit 3`, issue-01..03) on the rubric as I
   found it. Two of the three items errored out with a Windows console encoding
   crash inside `run_eval.py`'s subprocess call (`UnicodeEncodeError` writing to
   the child's stdin under the `cp1252` codepage), not a rubric problem. I fixed
   this by exporting `PYTHONUTF8=1` and `PYTHONIOENCODING=utf-8` before every run
   from then on; only issue-01 finished, and it disagreed (gold `accept`, mine
   `reject`, failed `bounded-scope`).
2. `2/3 scored items` — same three issues, re-run after the encoding fix. issue-01
   still disagreed on `bounded-scope`: it's a docs task touching five files for one
   GA feature, and my rubric's wording read that as an umbrella issue.
3. `3/5 scored items` — `--only issue-01,05,10,15,20`, after loosening
   `bounded-scope` to say a documentation change for one named feature stays
   bounded even across several files. issue-01 fixed, but issue-15 and issue-20
   (both gold `reject`, category `scope`) newly flipped to `accept`.
4. `11/12 scored items` — `--only issue-01,04,05,06,09,10,11,14,15,16,19,20`, after
   adding a clause failing the check when the thread shows repeated claim/unassign
   cycles or closed abandoned PRs (issue-15's real problem), or when a feature
   request has no maintainer engagement and leaves a core choice marked TBD
   (issue-20's real problem). Both fixed, but issue-19 (gold `accept`) newly
   disagreed: my new wording was read as an umbrella issue when a maintainer
   listed several candidate causes and optional follow-on optimizations for one
   UI-freeze bug.
5. `3/3 scored items` — `--only issue-19,05,10`, after clarifying that one bug
   described with multiple candidate causes or fix approaches for the same symptom
   is still bounded. issue-19 fixed; the two umbrella canaries (issue-05, issue-10)
   stayed correctly rejected.
6. `19/20 scored items` — first full run on the settled rubric, confirming the fix
   generalized across the whole set (bar: 18/20 — PASS; all five categories had at
   least one match).
7. `20/20 scored items` — final full run, saved with `--save-run` to
   `eval-run.txt`. This is the score in the committed file.

**Issue analysis**

`issue-19` (source `zxcalc/zxlive#517`, category `clear-accept`). My rubric's
verdict: `accept`. Gold label: `accept`. Reasoning: the issue is maintainer-filed
(`RazinShaikh`, COLLABORATOR) and names one symptom — proof-mode UI freezes when
selecting large subgraphs — with two diagnosed causes (slow matchers, UI blocked on
the matching thread) and three optional follow-on optimizations (multi-processing,
category-scoped matching, threaded rewrite application). `repo-active` passes on
recent human commits; `contribution-policy` passes on silence; `available` passes
on no assignee, no linked PR, and zero comments; `bounded-scope` passes because the
whole list describes ways to fix one bug, not independent deliverables the fixer
would need to split across separate PRs — the fixer investigates the real cause and
adopts as much of the suggested list as the fix needs. `task-specified` (preferred)
also passes on the named causes. Every required check passing gives `accept`,
matching gold.

**Check rationale**

From `rubric.md`, the `bounded-scope` pass condition (as currently written)
includes: "One bug or one performance problem described with multiple candidate
causes, fix approaches, or optional follow-on optimizations for that same symptom
is still bounded: the fixer investigates, addresses the real cause, and may adopt
only as much of the suggested list as needed; a long list of ideas for solving one
problem is not the same as bundling independent deliverables." This exists because
my first pass at `bounded-scope` only excluded issues that "bundle multiple
independent features, fixes, or checklist items" — worded broadly enough that the
grading model read a maintainer's diagnostic list (causes plus optional
optimizations for one bug) the same way it would read an actual umbrella issue,
and wrongly rejected `issue-19`, a clean gold `accept`. The added sentence tells
the grader what distinguishes "several ideas for fixing one problem" from "several
unrelated things bundled into one issue."

**Trade-offs**

The trade-off is that `bounded-scope` is now more permissive toward issues that
list several fix ideas, which could let through an issue where those "candidate
approaches" are actually independent features in disguise. To check this, I
re-ran the two umbrella-category canaries, `issue-05` and `issue-10`, with `--only`
immediately after adding the clause (run 5 above): both stayed correctly rejected,
because the check still requires the umbrella exclusion to fire whenever the body
bundles "multiple independent features or unrelated fixes meant to be split across
separate contributors or PRs" — a maintainer's list of causes for one symptom
doesn't meet that bar, but a checklist of unrelated feature asks still does. The
case I accept this could still miss: a feature request that lists several
implementation options for what is actually more than one deliverable, phrased
casually enough that neither clause clearly fires; I have not found such a case in
the 20-item set, and category floors were satisfied without a false accept.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. This bug sits inside PathReview's ingestion pipeline, which is the part of the
   project I actually want to get better at — practical AI/RAG code, not just
   glue. The fix looks like it's contained to one method (`StructuralChunker.chunk()`),
   there's already a repro script and a named failing test, so I can size the work
   before I even start, and it fits comfortably in the time I have this week.
2. The rubric correctly told me the repo is alive, that the project's contribution
   docs say nothing against AI-assisted work, and that nobody has claimed or opened
   a PR against this one. What it couldn't weigh is the thing that actually made me
   rank it #1 over the very similarly-shaped `#62`: this issue's fix and its
   existing `xfail`-marked test both live in the RAG ingestion code the course is
   about, so it teaches me more of the codebase I care about, and flipping that
   test from failing to passing gives me an unambiguous "I'm done" signal that no
   rubric check captures.
3. I expect the fix itself to be small — probably a fallback branch in `chunk()`
   for documents with no headings — but I haven't set up the project locally yet,
   so I'll need to get through `docs/SETUP.md` (Docker services, migrations, seed
   data) before I can even run the existing unit test. `docs/CONTRIBUTING.md` also
   warns that a first PR from a new GitHub account sits queued for a maintainer to
   approve CI, so I'm treating that as an expected wait, not a sign something's
   wrong with my branch.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
