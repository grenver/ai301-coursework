---
name: issue-select
description: Grade a candidate open-source issue against a written rubric and decide whether it is worth taking as a first contribution. Use when evaluating a GitHub issue URL or an eval snapshot file as a potential first issue.
---

# issue-select: rubric-driven first-issue grading

Grade one candidate issue by executing every check in `rubric.md` against evidence. Do not use gut feel.

## Inputs

- **Live mode:** One or more GitHub issue URLs. Read `scope.md`, gather evidence only from the scoped Path Review repository, and use `references/evidence-guide.md` for evidence locations.
- **Eval mode:** One snapshot bundle. Use only its text; do not fetch live GitHub data.

## Workflow

1. In live mode, read `scope.md`, confirm the repository is in scope, and apply its house rule. In both modes, read `rubric.md`.
2. Execute every rubric row. For each, report `pass`, `fail`, or `unclear` with the specific deciding evidence.
3. Apply the written verdict rule exactly. Preferred checks never affect the verdict.
4. For several live candidates, rank only accepted issues using the fit profile. Then list rejects and the required check that rejected each.

## Output format

End with a fenced JSON block and nothing after it:

```json
{
  "item": "<issue URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear", "evidence": "<deciding fact or quote>"}
  ],
  "verdict": "accept|reject"
}
```

In multi-issue live mode, the final JSON block is an array of these objects, with accepted candidates first in fit order.

## Grading discipline

- Name evidence before assigning every grade.
- Let the rubric decide. Record tensions as notes; revise the rubric later rather than changing a grade ad hoc.
- Treat unclear as the verdict rule says. Never silently skip a check.
