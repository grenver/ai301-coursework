# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

- Where it lives, eval: the plan is the "## Plan context" block. Its
  "Scope" sentence names the one change; "Not in scope" names the
  boundary; "Files" lists what may change; a "Known limit",
  "Deliberately deferred", or "Deviation" sentence records an honest
  shortfall. What actually changed is "### Diff" (each `--- a/` /
  `+++ b/` pair is a file, each `@@` a hunk) and "### Commits" (a
  commit like "add --time-format" names unplanned work outright). The
  fidelity claims are in "### Description": phrases like "exactly as
  planned", "in full", "no functional changes outside", "now
  documents".
- Where it lives, live: the plan is `plan.md` (its Scope, Files,
  Approach, and `## Deviations` sections). The diff is
  `git diff main...HEAD`, the file list `git diff main...HEAD --stat`,
  the commits `git log main..HEAD --oneline`. The claims are in the
  Summary and Changes sections of `pr_draft.md`.
- What good looks like: every changed file is in the plan's files
  list or a deviation note, every hunk serves the planned change, and
  every planned deliverable is in the diff or named as deferred in
  both the plan and the description. Each bullet under Changes maps
  to a hunk, and each hunk maps to a bullet. Drift in the "more"
  direction is an extra flag, setting, rename, or rewrite; drift in
  the "less" direction is a promised docs update or second fix that
  the description says is done but the diff does not contain.

## Test evidence (harness category: not-tested)

- Where it lives, eval: the plan's "Test plan" sentence (the repro
  input, the expected-after, any second failure scenario, the test
  command) and its "Repro evidence" line (the input that showed the
  bug). The proof is "### Test evidence", sometimes with the before
  in "### Description" instead.
- Where it lives, live: the Test plan section of `plan.md`;
  `test_evidence.md` (before on `main`, after on the branch, plus the
  output of `make test-unit`, `make test-integration`, `make lint`,
  `make typecheck`); and the Testing section of `pr_draft.md`.
- What good looks like: the same command or input that showed the
  bug, run on the branch, with its output pasted and matching the
  plan's expected-after (for #56: `plain doc chunks: 1` where `main`
  gave `0`, and `test_document_with_no_headings PASSED` where `main`
  gave `XFAIL`), plus each check's result. Not enough: "tested
  locally", "works now", "tests pass" alone, a run of only the control
  input, or a run of a different input from the plan's repro. A failed
  or expected-to-fail check (for example "no tests ran" from
  `make test-integration`) is fine when pasted as is with the reason.

## Diff quality (harness category: unreviewable)

- Where it lives, eval: every `+` and `-` line in "### Diff", and the
  wording of "### Commits" ("wip", "misc cleanups while debugging",
  "fmt pass" point at debris). Live: `git diff main...HEAD` and
  `git log main..HEAD --oneline`.
- What good looks like: each hunk is the fix, its regression test, a
  comment explaining the fix, or a file the repo asks for. Debris
  tells: `print`/`eprintln!`/`console.log`/debug log lines (even
  commented out), commented-out code or a first attempt, an unused
  function (often under `#[allow(dead_code)]` or named `_unused...`),
  TODO/FIXME left on a line, an import block reshuffled for no reason,
  and runs of lines removed and re-added identical or only re-indented.
  Lines re-shown inside a hunk because one argument in the same call
  changed are not churn.

## Standards and comms (harness category: standards-wall)

- Where it lives, eval: the "pull requests:" and "contribution
  policy:" lines in "## Repo facts" list the template sections,
  checklist items, required files (CHANGELOG.md, whatsnew), and any AI
  policy. "## Thread highlights" holds maintainer direction. They are
  met (or not) in "### Title", "### Description", and "### Diff".
- Where it lives, live: `.github/PULL_REQUEST_TEMPLATE.md` (Summary,
  Issue with `Closes #`, Changes, Testing checkboxes, Screenshots /
  Demo, Notes for Reviewers), `docs/CONTRIBUTING.md` (branch name
  `<type>/<issue>-<slug>`, conventional commits, fill the template
  completely, remove the seeded bug's xfail marker and its
  `pyproject.toml` suppression), the course's AI-use disclosure rule
  (goes in Notes for Reviewers), and the issue thread from
  `gh issue view <n> --comments`. They are met in `pr_draft.md`.
- What good looks like: every named section or checklist item has
  real content or a plain statement covering it ("N/A, no UI change");
  `Closes #<n>` with the real number; only true boxes ticked; a
  required changelog entry is in the diff; and where the policy asks
  for AI disclosure, the description names the tool and what it did.
  Boilerplate or a missing ask fails: an empty template section, no
  issue link when one is asked for, a missing whatsnew entry, or no
  disclosure under an "all AI usage must be disclosed" policy.
