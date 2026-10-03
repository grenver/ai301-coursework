# Procedure: how this skill grades a plan package

## Read order

1. Read the repo facts first (eval: the "Repo facts" block; live: the
   repo's README/CONTRIBUTING per the evidence guide). Write down,
   word for word, any AI-use or disclosure requirement and who it
   applies to (pull requests only, issue comments, or "all AI usage").
2. Read the issue (title and body). Write down, in one line, the exact
   behavior reported: the trigger and the failure.
3. Read the thread highlights. For every comment by a maintainer,
   OWNER, MEMBER, or COLLABORATOR, write down any explicit direction:
   a named culprit file or line, a proposed or rejected approach, a
   posted patch or test build, a linked open PR. If there is none,
   write "no maintainer direction".
4. Read the repro evidence before the plan. Write down: what each step
   showed, what each control run showed, and what the control rules
   in or out. Do this before reading the plan so the plan's wording
   cannot shape what you think the evidence says.
5. Read the candidate plan. Write down its stated cause, its in-scope
   list, its out-of-scope list, its files/areas, its test plan, and
   its stated risks/unknowns, quoting each.
6. Read the candidate plan comment last, since it is graded against
   everything above it.

## Evidence gathering

1. Eval mode: use only the bundle text. Do not fetch anything.
2. Live mode: read `scope.md` first and stop if the issue is outside
   its Repo line. Then fetch the issue body and all comments (with
   `gh issue view <n> --repo <repo> --comments`, or the GitHub API /
   web page if `gh` is not available). Take the reproduction evidence
   from the student's posted repro comment on that issue as quoted in
   their drafts. Take repo facts from the repo's README, CONTRIBUTING,
   and any AI policy file. The plan is `plan.md`; the comment is the
   draft comment file. Ignore other files in the working directory.
3. For each check in `rubric.md`, collect the facts named in its
   Evidence column from the notes made in Read order:
   - grounded-diagnosis: the stated cause plus every repro step and
     control result.
   - bounded-scope: every change the plan commits to, listed one per
     line, each marked "needed for the reproduced bug" or "extra".
   - executable: the chosen approach and the named file/module/path;
     any phrase that leaves the main decision open ("or", "whichever",
     "not sure", "somewhere", "investigate").
   - decisive-test: each success signal in the test plan, each marked
     "observable for this bug" or "generic".
   - thread-and-convention: the maintainer-direction notes from step 3
     of Read order, whether the plan/comment follows or names each
     one, the disclosure rule from step 1, and whether the comment
     contains a disclosure naming a tool and extent.
   - honest-unknowns: the plan's risk/unknown statements and any
     claim of certainty the evidence does not back.
4. If a fact a check needs is not in the package, record "absent" for
   it. Do not infer it from outside knowledge.

## Check execution

1. Run the checks in table order: grounded-diagnosis, bounded-scope,
   executable, decisive-test, thread-and-convention, honest-unknowns.
2. For each check, apply only that check's pass condition to the facts
   gathered for it. Grade `pass` or `fail`, and record the one quote
   or fact that decided it.
3. Grade `unclear` only when the evidence the check needs is truly
   absent from the package and its pass condition does not say what
   absence means. A plan that states no cause, no files, or no test
   plan is a `fail` for that check, not `unclear`, because the
   absence is itself what the check grades.
4. Grade each check on its own. A strong result on one check (a
   correct core fix, a careful test plan) never rescues a failing
   result on another, and a polished or confident tone counts for
   nothing.
5. Re-read a part of the package only if the gathered notes for that
   check are missing a fact. Otherwise grade from the notes.

## Verdict assembly

1. Apply the verdict rule in `rubric.md`: `accept` only if every
   required check is `pass`; otherwise `reject`.
2. Count `unclear` on a required check as `fail`. Ignore preferred
   checks for the verdict, but still report them.
3. For a reject, name the first failing required check in table order
   as the deciding check and quote the evidence that failed it in the
   summary.
4. Write a short summary line per check, then the JSON block exactly
   as `SKILL.md` specifies, with every check listed, and nothing after
   it.
