# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

- Where it lives: eval: the cause is the "Diagnosis:" or "Cause:" line
  at the top of "## Candidate plan". The behavior it must explain is
  in "## Repro evidence": the numbered steps, the artifact (output or
  stack trace), the "Control:" / "Control runs:" lines, and the
  "Expected:" / "Actual:" lines. Live: the cause is in the
  diagnosis section of `plan.md`; the evidence is the student's posted
  repro comment as quoted in the drafts.
- What good looks like: the stated cause explains every step and every
  control. Controls matter most: if a control shows the same input
  working with the blamed part in play, or the failure already present
  before the blamed part runs, the cause is ruled out. A cause the
  issue or thread asserts is not grounded if the package's own
  evidence contradicts it.

## Scope

- Where it lives: eval: the plan's "Scope" / "Change" / "Proposed
  changes" paragraph with its "In scope" and "Not in scope" / "Out"
  lines, plus the "Files and areas" list; the comment sometimes adds
  scope ("PR series", "starting with the migration"). Live: the scope
  section of `plan.md` and the draft comment.
- What good looks like: one change aimed at the reproduced behavior,
  plus regression tests, and a line naming what is left out. A
  drive-by rewrite adds migrations, dependency swaps, new options or
  settings, module restructures, or "while in the area" fixes. Work
  marked as deferred or out of scope is fine.

## Executability

- Where it lives: eval: the plan's "Approach" steps and "Files" /
  "Files and areas" list. Live: the approach and files sections of
  `plan.md`.
- What good looks like: one chosen approach and a named file, module,
  or code path where the change goes, so a stranger could open that
  file and start. Saying exact functions will be pinned later is fine
  when the area and approach are chosen. Not good: "X or Y, whichever
  is easier", "somewhere", "not sure which layer", or a plan that is
  only "investigate" or "profile".

## Test plan

- Where it lives: eval: the plan's "Test" / "Test plan" paragraph,
  read against the repro steps and the "Expected:" line. Live: the
  test plan section of `plan.md`, read against the student's repro
  steps.
- What good looks like: the repro (or the issue's test case) re-run
  with a stated observable result: an exit code, an output value, a
  color change, a test passing that fails today. Not enough on its
  own: "the full suite passes", "nothing breaks", "feels fast".

## Honesty

- Where it lives: eval: a "Risk" / "Risks" / "Unknowns" line in the
  plan, or a deferral with reasons in the scope paragraph. Live: the
  risks/unknowns section of `plan.md` and its `## Deviations` section.
- What good looks like: what the evidence does not settle (an
  untested platform, an unmeasured cost, a function not yet located)
  is called an unknown, with what will be done about it. False
  confidence states those things as settled. A mid-build deviation is
  recorded under `## Deviations` in `plan.md` with what changed and
  why.

## Comms

- Where it lives: eval: "## Candidate plan comment", read against
  "## Thread highlights" (look for OWNER, MEMBER, COLLABORATOR, or
  CONTRIBUTOR-maintainer comments) and the "contribution policy" line
  in "## Repo facts". Live: the draft comment, read against the issue
  thread fetched from GitHub and the repo's CONTRIBUTING / AI policy.
- What good looks like: when a maintainer has named the culprit,
  proposed or rejected an approach, posted a patch or test build, or
  linked an open PR, the plan follows it or the comment names it and
  says why it differs. When the policy requires AI disclosure for
  comments or "all AI usage", the comment says which tool was used
  and how much. A policy that asks for disclosure only in pull
  requests does not apply to the plan comment. Boilerplate ignores
  the thread: it re-proposes what a maintainer already rejected, or
  skips past a test build that was posted.
