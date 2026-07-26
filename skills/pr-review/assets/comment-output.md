# PR review output

Fill this in and return it to the user. Post nothing. Drop any priority section that has no findings rather than writing "none" under it, and drop the held-back line when nothing was held back.

```text
## <PR title> — <link>

**Verdict:** <Request changes | Comment | Approve> — <one line on why>
**Coverage:** reviewed <n> files (<list or areas>) · skipped <generated, lockfiles, formatting> · could not verify <what and why>

### Critical

**C1 — <short title>**

- **Where:** `<path>:<line>` (<new | old> side) — `<quoted line from the diff>`
- **Why it matters:** <2-3 sentences for the user: what actually goes wrong, when, and what it costs. Not the comment text.>
- **Confidence:** <Confirmed | Supported>
- **Comment to post:**

  > <paste-ready text addressed to the PR author>

### High

**H1 — <short title>**

<same shape as above>

### Medium

**M1 — <short title>**

<same shape as above>

### Low

**L1 — <short title>**

<same shape as above>

### Optional

**O1 — <short title>**

<same shape as above>

Held back: <n> further Medium/Low findings beyond the cap — <one line on what kind>.
```

When nothing clears the evidence gate, the whole report is the title line, an `Approve` verdict, the coverage line, and one sentence on what was checked and found sound. That is a complete and correct output, not an empty one.
