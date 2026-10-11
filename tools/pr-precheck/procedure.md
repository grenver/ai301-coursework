# Procedure: how this tool grades a PR package

## Read order

1. Repo facts first (eval: the "Repo facts" block; live:
   `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` from
   `main`, plus the course rule that every PR description discloses
   AI use). Write down, as a numbered list, every ask a PR must meet:
   each template section or checklist item, each file the repo asks
   a bug fix to add (changelog, whatsnew), and the AI-disclosure rule
   word for word with who it applies to. If there are none, write
   "no PR asks".
2. The issue (title and body). Write one line: the reported trigger
   and the failure.
3. The thread highlights. For each OWNER, MEMBER, COLLABORATOR, or
   maintainer-role CONTRIBUTOR comment, write down any explicit
   direction or open question. If none, write "no maintainer
   direction".
4. The plan, before any of the PR. Write down, quoting it: its scope
   (the one change), every deliverable it commits to, its files list,
   its not-in-scope list, any deferral or deviation note, its repro
   trigger input, its expected-after, every failure scenario its test
   plan names, and any test command it names. The plan comes first
   so the PR's wording cannot shape what you think was promised.
5. The diff and the commit list. List every changed file and every
   hunk, one line each, saying what the hunk does. Read the diff
   before the description so you know what changed before you read
   what the PR says changed.
6. The test evidence.
7. The title and description last, because every claim in them is
   checked against everything above.

## Evidence gathering

1. Eval mode: use only the bundle text. Do not fetch, run, or open
   anything else. Live mode: follow the inputs list in `SKILL.md`
   (`scope.md` first, then `plan.md`, `git diff main...HEAD`,
   `git log main..HEAD --oneline`, `test_evidence.md`, `pr_draft.md`,
   the issue thread via `gh`, the template and CONTRIBUTING). Use
   `references/evidence-guide.md` to find each part.
2. plan-fidelity: put the hunk list from Read order step 5 next to
   the plan notes from step 4. Mark each hunk "planned" (it serves a
   change the plan or a deviation note names), or "unplanned" (new
   flag/option/setting, renamed members, rewrite of an unnamed
   function or file, unrelated text change). Then mark each plan
   deliverable "in diff", "disclosed" (plan and description both say
   it is deferred or changed), or "missing". Then list every claim
   the description makes about what changed or about matching the
   plan, and mark each "true" or "contradicted by the diff".
3. decisive-evidence: put the evidence next to the plan's test plan.
   Record: whether the plan's repro trigger input was re-run on the
   branch (quote the command or scenario), whether it is the same
   input or a control or a different path, the after output quoted,
   whether it matches the expected-after, whether a before is shown
   or stated, which named failure scenarios were and were not re-run,
   and the stated suite or check outcome.
4. clean-diff: go through every added line and every hunk and record
   any debug print/log (commented or not), commented-out code, dead
   or unused function or variable, TODO/FIXME, whitespace-only or
   import-reorder or removed-and-re-added-identical block, or edit
   unrelated to the fix. Quote each one with its file.
5. repo-standards: for each ask on the list from Read order step 1,
   find where the title, description, or diff meets it, or record
   "missing". Mark asks that do not apply to this PR "n/a" with the
   reason.
6. thread-engaged: for each maintainer direction from Read order step
   3, record whether the PR follows it or the description names it.
7. If a fact a check needs is not in the package, record "absent".
   Do not fill it from outside knowledge.

## Check execution

1. Run the checks in rubric table order: plan-fidelity,
   decisive-evidence, clean-diff, repo-standards, thread-engaged.
   Grade every check, even after one has already failed.
2. For each check, apply only that check's pass condition to the
   notes gathered for it. Grade `pass` or `fail` and record the one
   quote or fact that decided it (for a fail, the first offending
   item found).
3. A missing thing the check looks for (no before/after, no
   disclosure, no section) is a `fail`, not `unclear`. Grade
   `unclear` only when the package is ambiguous about a fact the
   check needs and the pass condition does not settle it.
4. Grade each check on its own. A correct fix does not rescue debris,
   strong evidence does not rescue drift, and a confident or polished
   description counts for nothing.
5. Re-open the package only when the notes for a check are missing a
   fact. Otherwise grade from the notes.

## Verdict assembly

1. Apply the verdict rule in `rubric.md`: `accept` only if every
   required check is `pass`; otherwise `reject`.
2. Count `unclear` on a required check as `fail`. Report preferred
   checks but leave them out of the verdict.
3. For a reject, the deciding check is the first failing required
   check in table order. Name it in the summary and quote the
   evidence that failed it. Mention any other failing required checks
   after it.
4. Write one summary line per check, then (live mode only) the
   voice-guide notes, then the JSON block exactly as `SKILL.md`
   specifies, with every check in table order, and nothing after it.
