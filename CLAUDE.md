# Memory

## Memory Rules — `./memory/` (CRITICAL — Non-Negotiable)

- **ALWAYS read `memory/README.md` before doing ANYTHING in this project**, then
  open the file it routes you to for the task at hand. The README is an index by
  activity; reading the whole directory every session is waste.
- **NEVER store project memory in global memory (`~/.claude/` or anywhere outside
  this project root).** All authored memory lives in `./memory/` only.
- After finishing any implementation, **update `./memory/` immediately** — record
  decisions made, conventions discovered, and anything a future session would
  waste time re-deriving.
- If we are working on a task from `TODO.md`, update that file when you are done.
- A session that skips this re-derives decisions already settled in
  `decisions.md`, re-finds bugs already explained in `debugging.md`, and breaks
  rules already written down in `invariants.md`. That is the cost, and it is paid
  every time.

---

# Guidelines

## 1. Test-Driven Development

**Tests are the only thing I can rely on to verify this code works. Write them
first, where writing them first is honest.**

The cycle, for the logic that decides anything:

1. **Red** — write the failing test, run it, and show me the failure output.
   A compile error is not red. The test must build and fail on its assertion.
2. **Green** — the minimum code that passes. No extra branches, no speculation.
3. **Refactor** — only the code you just wrote. This does not license touching
   adjacent code (see Guideline 4).

`cargo test` must be green before any commit.

**Where test-first is mandatory:** anything that decides size, price, or whether
we act — the planner, gates, reservations, allocations, decimal arithmetic,
normalization. A wrong number here costs real money. These are also the tests
that catch the failure mode of an agent writing both sides: a test written after
the implementation asserts what the code _does_, bugs included.

**Where it is not** — say which case applies, then proceed:

- Venue adapters and wire formats. You cannot assert a payload shape you have not
  seen. The honest order is: call it, record the real payload, write the test
  against the recording, then write the parser.
- Exploratory or measurement work (e.g. a first live run).
- Schema migrations, renames, and prose in `memory/` or `TODO.md`.

When you take an exemption, say what you could not cover and why.

**What may be faked:** clock, HTTP transport, and venue responses — from real
recorded payloads only, never invented shapes. **What may not:** decimal
arithmetic, the planner, reservation and gate logic. Those run against real
values.

**Where tests live.** Unit tests in `#[cfg(test)] mod tests` beside the code, as
they are today. Anything crossing a boundary — a store against a real Postgres
via `testcontainers`, a collector against a recorded venue payload — belongs in
`tests/`. There are none yet and `testcontainers` is declared but unused; adding
the first one is real work to be asked for, not a side effect of another task.

Coverage is not a target. The bar: **every decision that moves money has a test
that fails when the decision changes.**

## 2. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:

- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 3. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios — and no tests for them either.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes,
simplify.

## 4. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken. The refactor step in Guideline 1 is
  scoped to code you wrote in that cycle — it is not a licence to tidy.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.
- **Before editing shared/widely-called code, run an impact/call-path query via
  `codebase-memory-mcp`** so you know the blast radius before you touch it.

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 5. Goal-Driven Execution

**Define success criteria. Loop until verified.**

For multi-step tasks, state a brief plan, with the check that closes each step:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

A check is something that can fail: a named test, a query result, a page that
renders. "It works" is not a check. Strong criteria let you loop independently;
weak criteria require constant clarification.

## 6. Signal Uncertainty

**Don't state guesses as facts. When confidence is low, say so.**

When your knowledge is incomplete, inferred, or unverified:

- Preface responses with "possibly", "likely", "I'm not certain", or "you should
  verify this" — don't omit them.
- Distinguish between what you know and what you're inferring.
- If a claim requires external verification before acting on it, flag that
  explicitly.
- Never let confident tone substitute for confident knowledge.

When you notice you're filling a gap with an assumption:

- Name the gap: "I don't have visibility into X, so I'm assuming Y."
- Offer to stop rather than guess: "I can proceed on that assumption, or you can
  verify first."
- Don't bury uncertainty at the end of a long confident response.

The test: Could a developer act on this response and only discover it was wrong
after the damage is done? If yes, the uncertainty wasn't signalled clearly
enough.

---

**These guidelines are working if:** money-moving logic has a test that was
written before it and fails when it changes, exemptions from test-first are named
rather than quietly taken, diffs contain fewer unnecessary changes, structural
questions are answered from the MCP instead of costly file exploration, and wrong
information is flagged before it causes damage.
