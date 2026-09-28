# Voice guide: how I talk upstream

## Who I am in threads

I'm a first-time contributor to this project, comfortable in Python
but new to this specific codebase. I'm here to investigate one named
bug (`StructuralChunker` dropping headingless documents), not to
pitch myself or promise a delivery date. Readers on the thread should
expect a plain account of what I ran and what I saw, not a sales
pitch for why they should pick me.

## Rules I write by

### Rule: promise the next step, not the outcome

I say what I am going to do next, never that I will fix it or by
when. I don't control how long a real fix takes, and a broken promise
costs more trust than a modest one.

- Wrong: "I'll have a PR up fixing this by tomorrow, guaranteed!"
- Right: "I'm going to reproduce this locally and report back with
  what I find before I touch the fix."

### Rule: every claim carries its receipt

If I say something reproduced, didn't reproduce, or has a particular
cause, the sentence right next to it points at the exact output, test
result, or line of code that shows it. A claim with no receipt gets
cut or turned into a question.

- Wrong: "This is definitely the chunker's heading-detection logic,
  it's flaky like that."
- Right: "`test_document_with_no_headings` fails with 0 chunks
  returned; the loop in `chunk()` only appends a chunk on a heading
  match, so a headingless document never appends one."

### Rule: name the gap, don't paper over it

When my setup differs from the issue's (a version, an OS, a config I
couldn't match), I say so in the same comment instead of letting a
reader assume I matched it exactly.

- Wrong: "Reproduced the bug, here's the output." (when actually
  running against a slightly different commit or config than the
  issue names)
- Right: "Reproduced against `main` at commit `2f4e82f` (the issue
  doesn't pin a commit); output below."

### Rule: no flattery, no padding

I don't open with "great project, love this repo!" or close with
"thank you so much, looking forward to contributing!" It reads as
filler around thin content and wastes a maintainer's read time.

- Wrong: "Hi! Huge fan of this project, it's awesome. Excited to dig
  into this one!"
- Right: "Claiming issue #56. Plan: reproduce the reported drop with
  the repo's existing repro script, then report back."

### Rule: an honest "I couldn't reproduce it" is a complete report

If my attempt doesn't trigger the bug, I say that plainly, show what I
tried, and name what might differ — I don't stretch a partial result
into a confident confirmation to seem more useful.

- Wrong: "Can confirm, this is broken." (when the artifact actually
  shows a successful run)
- Right: "I could not trigger the drop with a 1000-char headingless
  doc; output below shows N chunks returned, not 0. I haven't tried
  documents over the chunker's size threshold yet."

## Things I never post

- A fix ETA or a "guaranteed" anything — I don't control the real
  timeline.
- "Same as above, can confirm" on a classmate's or another
  contributor's comment — my proof goes up in my own words, from my
  own run, every time.
- A claim of certainty about root cause before I've actually read the
  code path, even if the issue's own description makes the cause seem
  obvious.
- Flattery or thank-you padding that isn't carrying information.
