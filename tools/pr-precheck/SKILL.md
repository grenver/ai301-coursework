---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer one question about one PR package: is this ready to submit?
A PR package is a candidate pull request (its title, description,
commits, diff, and test evidence), read against the plan it claims to
implement (including that plan's deviation notes and test plan) and
against the issue the plan belongs to (its thread and the repo's
stated PR template and contribution policy).

Do not answer any other question. Do not review code style, suggest a
better fix, re-grade the plan's diagnosis, or grade more than one
package per run. The plan is the fixed reference here: you check
whether the PR matches it, proves it, stays clean, and meets the
repo's asks. Never answer from gut feel; execute the files in this
directory.

## Inputs and modes

Decide the mode first. If you were handed a package bundle (a markdown
file with "Repo facts", "Issue", "Plan context", and "Candidate PR"
sections), you are in eval mode. Otherwise you are in live mode.

**Live mode** (the student's own PR, before it is opened). Run from
the top folder of the student's clone of their fork, on their branch.
The inputs are:

- The plan: `plan.md` in that folder, including its `## Deviations`
  section. Its scope, files, approach, and test plan are what the diff
  and evidence are read against.
- The diff: run `git diff main...HEAD` (three dots) in that folder.
  That is every committed change the branch makes against `main`. Also
  run `git diff main...HEAD --stat` for the file list and
  `git log main..HEAD --oneline` for the commits. Uncommitted or
  untracked files are not part of the PR; ignore them, and never treat
  `plan.md`, `comment.md`, `pr_draft.md`, or `test_evidence.md` as
  part of the diff.
- The title and description: `pr_draft.md`. The first line is the
  title; everything under it is the description.
- The test evidence: `test_evidence.md`, plus whatever the description
  itself pastes.
- The issue: the URL the student gives. Fetch the issue body and its
  full thread with `gh issue view <n> --repo <repo> --comments` (or
  the GitHub web page if `gh` is unavailable).
- The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and
  `docs/CONTRIBUTING.md` in the working copy (read them from `main`
  with `git show main:<path>` if the branch changed them). The course
  also requires an AI-use disclosure in every PR description.

A house-chain student (no plan of their own) reads the house plan and
the house repro pack where these steps say `plan.md` and the repro;
everything else is the same. If any live input is missing (no
`pr_draft.md`, no `test_evidence.md`, no commits ahead of `main`),
grade the checks that need it as `fail` and say which file was
missing; do not invent its content.

**Eval mode** (a frozen practice submission). The bundle is the whole
world. Every fact comes from the bundle text. Do not fetch anything,
do not run git or gh, do not open other files, and do not use what you
know about the real project. Always grade the complete package: every
check in `rubric.md`, then the full verdict rule. Treat every package
as AI-assisted work when applying a disclosure rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else.

- If its `Repo:` line still holds the placeholder
  (`<ORG>/<PATH-REVIEW-REPO>` or anything in angle brackets), stop
  without grading. Tell the student to replace the `Repo:` line in
  `~/.claude/skills/pr-precheck/scope.md` with their section's Path
  Review repo, and emit nothing else.
- If the issue URL, or the repo the branch's fork comes from, is not
  the scoped repo, refuse to grade and say which repo is in scope.
- Apply the house rules it lists while gathering evidence: the branch
  name must look like `<type>/<issue-number>-<slug>` with the issue's
  own number, the PR uses the repo's template with every section
  filled, and one PR covers one issue. Report a broken house rule in
  the summary; it changes the verdict only through a rubric check that
  reads it.

Never guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after grading the checks, read `voice-guide.md` and hold
the PR title and description in `pr_draft.md` against each of its
rules and its "Things I never post" list. For every rule the draft
breaks, add a line to the summary that quotes the rule's heading and
the offending phrase from the draft, and suggests the fix. If no rule
is broken, say so in one line.

The voice guide never changes a check grade or the verdict, because
no rubric check reads it; it is advice for the student's own writing.
In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Read these files from this skill directory, in this order, before
grading:

1. `rubric.md`: the checks (name, evidence, pass condition, weight)
   and the verdict rule. It decides what passes.
2. `references/evidence-guide.md`: where each evidence family lives in
   a bundle and in live mode, and what good looks like. It decides
   where to look.
3. `procedure.md`: the read order, evidence gathering, check
   execution, and verdict assembly. Execute it as written, step by
   step. It decides how.

If `rubric.md` has no filled checks table or no verdict rule, or
`procedure.md` has no steps under its headings, stop and say the tool
cannot grade without them. Do not make up checks or steps. Comments
in the templates do not count as content.

If the procedure is silent on something you need to do, do the
smallest thing the rubric's evidence column implies, and name the gap
in the summary ("procedure gap: ...") so the student can fix it. Never
quietly add a check or change a pass condition.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit;
`reject` means hold it and fix what failed. There is no third verdict
and no score. Reservations go in a check's evidence line, not in the
verdict.

Write a short readable summary first: one line per check with its
grade and the deciding fact, the deciding check for a reject, then (in
live mode only) the voice-guide notes. Then end the reply with this
fenced JSON block, filled in, valid, with every rubric check listed in
rubric order and nothing after it. `item` is the PR or issue URL in
live mode, or the bundle id (such as `pkg-07`) in eval mode.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first. Never grade a check without naming the quote, file,
  hunk, or line that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish. A terse PR that matches its plan,
  proves the fix, and meets the repo's asks is ready. A confident,
  well-formatted one can still hide drift or debris. Read the diff
  itself; never take the description's word for what the diff does.
- The rubric decides, not you. If a check passes by its stated
  condition but feels wrong, it passes. Note the tension in the
  summary; the fix belongs in the rubric.
- The procedure decides how, not you. Follow `procedure.md` as
  written and report its gaps instead of working around them.
- An honest shortfall is not a failure. Work the plan defers, or a
  limit the plan's deviation notes and the description both state
  openly, does not fail a check for being missing.
- Unclear defaults to fail. Treat `unclear` the way the rubric's
  verdict rule says. Where it is silent, an unclear grade on a
  required check counts as a fail: a PR you cannot verify from the
  package is not ready to submit.
