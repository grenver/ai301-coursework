# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: in an eval bundle, the repro report's own environment
line(s) (OS, tool/library version, relevant config), read against the
`Repo facts` block's "latest release" and the issue's own stated
version/platform. In live mode: the student's draft repro report, read
against the issue thread's stated version/OS and the repo's release
page if the issue is silent.

What good looks like: OS and the specific tool/library version(s) are
named, and either they match what the issue targets, or the report
says in its own words how they differ ("tested on 1.5.3, issue
confirmed on latest/main") instead of letting the difference pass
unremarked. A report naming an environment nobody could place ("my
machine", no version at all) fails even if everything else is
detailed.

## Steps

Where it lives: the repro report's numbered or narrated steps, read
against the issue's description of the trigger (the exact input,
flag, or sequence that produces the bug) and, in eval bundles, the
`Repo facts`/issue text for anything the trigger depends on (a config
file, a specific argument shape, a data size).

What good looks like: a stranger with only a terminal and the named
tool/library version could start from the stated point and reach the
same trigger the issue names, using inputs that are either given
verbatim (a command, a config snippet) or fully reconstructable (a
public playground link re-created from scratch, not just re-opened).
Steps that route through a private repo, an unshared config, or a
local file the report never shows fail here even if the narration
around them is confident.

## Behavior shown

Where it lives: the repro report's artifacts — a captured command
output, a log excerpt, a screenshot description — read side by side
with the issue's own description of the reported behavior (the exact
error type, exit behavior, or symptom named in the issue, not a
paraphrase of it).

What good looks like: the artifact's content matches the issue's
named behavior at the level of specifics that would distinguish it
from something adjacent — same error class, same crash vs.
graceful-failure distinction, same code path — not just "something
went wrong here too." An honest, well-evidenced cannot-reproduce
(the artifact shows the attempt ran cleanly, and the report says so)
counts as behavior faithfully shown; it is a different, valid
outcome, not a lesser one. A polished artifact that shows a different
failure mode than the one reported, narrated as if it confirmed the
issue, is the trap this check exists to catch.

## Honesty

Where it lives: every claim sentence in the claim comment and the
repro report (a diagnosis, a "this reproduces," a "this does not
reproduce," a root-cause guess), read against whatever artifact or
concrete observation sits next to it in the same package.

What good looks like: a claim only says as much as its evidence
shows. "I reproduced X" needs the artifact for X right there. "I
believe the cause is Y" without a trace, profiler output, or code
citation showing Y is a guess wearing a claim's clothes. An honest
cannot-reproduce that names what was tried and what differed from the
report's conditions is fully evidenced even though it found nothing;
a confident claim backed only by enthusiasm, repetition, or "everyone
I know has this problem" is not evidenced no matter how detailed the
surrounding prose reads.

## Comms

Where it lives: the claim comment's own text, read against the
issue's specific content (does it name what the issue is actually
about, or could it be pasted onto any issue in this repo?); and the
`Repo facts` block's stated AI-use/contribution policy and bug-report
template, read against whether the comments disclose AI assistance
and cover the template's asked-for fields. In live mode: the repo's
`CONTRIBUTING.md`/`AI_POLICY.md`-equivalent docs and issue template.

What good looks like: the claim comment states a specific intent tied
to this issue's actual content and a concrete next step, not
flattery-plus-guaranteed-timeline boilerplate. Disclosure is a hard
gate, not a style note: silence in the repo's policy means no
disclosure is owed, but a policy that requires stating the tool and
extent of AI assistance is not satisfied by a human-sounding voice
alone — the comment must actually disclose. Template conformance is a
softer signal: covering the asked-for fields is good practice but a
terse report that answers everything the template wants, just without
its headings, still satisfies it.
