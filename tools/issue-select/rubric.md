# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo-active | The `archived` flag and the latest default-branch commit date in Repo facts; in live mode, the archive banner and newest default-branch commit. | The repository is not archived and has at least one human-authored default-branch commit within 180 days of the bundle capture date (or today in live mode). | required |
| contribution-policy | The `contribution policy` line in Repo facts; in live mode, CONTRIBUTING, linked contributor-policy files, and PR templates. | The stated policy does not prohibit AI-assisted code or documentation contributions. Silence and conditions such as disclosure, testing, or personal understanding pass. | required |
| bounded-scope | The issue body and comment thread. | The issue asks for one implementable change, documentation update, or reproducible bug fix, with a settled design; it is not a support request, an umbrella/tracking issue, a codebase-wide migration, or an unresolved design/product decision. A change to one named feature stays bounded even when it touches several existing files or pages, as long as the content and the affected locations are named. One bug or one performance problem described with multiple candidate causes, fix approaches, or optional follow-on optimizations for that same symptom is still bounded: the fixer investigates, addresses the real cause, and may adopt only as much of the suggested list as needed; a long list of ideas for solving one problem is not the same as bundling independent deliverables. Fail as an umbrella/tracking issue only when the body bundles multiple independent features or unrelated fixes meant to be split across separate contributors or PRs, or explicitly reads as a tracker. Also fail as unsettled when the thread shows a repeated pattern (two or more cycles) of contributors claiming the issue and later being unassigned for inactivity, or closed/abandoned PRs against it that never merged, or when a feature request has drawn no maintainer engagement at all and still leaves a core implementation or product choice marked open or TBD. | required |
| available | Repo facts for assignees and linked PRs, plus comments for current work-in-progress statements. | There is no assignee and no open linked PR, and no comment from the last 90 days says someone is actively working on the issue. In Path Review live mode, ignore classmates' claim comments per `scope.md`. | required |
| task-specified | The issue body and maintainer comments. | The requested outcome or failing behavior is stated well enough to identify what to change and how to tell the work is complete; a maintainer-filed bug or an issue with explicit acceptance criteria passes. | preferred |

## Verdict rule

Accept only if every required check passes. A required `unclear` counts as fail. Preferred checks never change the verdict; they rank accepted issues.
