---
name: review-loop
description: >-
  Load before answering any review or validation finding, before dispatching an
  implementation task whose acceptance criteria are not yet written as tests,
  and whenever a fix round is being opened or refused.
user-invocable: false
metadata:
  internal: true
---

# review-loop

The captain's review-loop contract for delegated work: how a finding is judged, how many rounds a
change may take, and what is recorded.
`ask-user-authority` owns whether a finding is firstmate's to decide or escalates; this skill owns
the shape of the round itself and applies at every gate either way.
The per-home copy of the captain's own words is `data/orchestrator-rules.md`; where it exists it wins
for that home, and this skill is the shipped default it refines.

## Before dispatch

Acceptance criteria are written as named tests before implementation is dispatched.
A brief without them is not ready.
The behaviour being pinned belongs in `## Captain's intent` in the captain's terms; the tests that
prove it belong in `## Firstmate spec`.

## Severity, and what a blocker is

Label every finding **BLOCKER**, **SHOULD-FIX**, or **NIT**.
The gate's own `error` / `warning` / `info` severity is the starting point; the label recorded is the
one applied when deciding.

Only a BLOCKER opens a fix round.
SHOULD-FIX and NIT are recorded in the ledger and filed to the backlog as their own items; they are
never fixed inside the change under review.

A BLOCKER carries evidence: a failing test, or an exact reproduction - commands, inputs, and the
observed failure.
A finding that asserts risk without demonstrating a failure is not a blocker: relabel it SHOULD-FIX or
NIT and do not open a round for it.
A regression the fixes introduced is always a BLOCKER and needs its own failing test.

## The ledger

Keep `data/<task-id>/review-ledger.md` for every task that reaches review.
It records each finding, its label, its origin round, and its decision - fixed, won't fix with the
reason, or deferred with the backlog id it was filed to - plus one line per round:
`round N: reason = new finding | incomplete fix | regression | unclear spec`.

The ledger is read before a gate is answered and before a review or fix round acts.
A finding already recorded as fixed, deferred, or won't-fix is not reopened; a reviewer that disagrees
states what has changed since it was decided rather than restating it.

## Scope freeze and round budget

Scope is frozen after the first review.
A re-review answers two questions only: were the listed blockers fixed, and does the suite still pass.
No fresh breadth review, however much else looks improvable.

Two fix rounds is the hard ceiling.
At the second, stop: report what is open, why it is stuck, and a recommendation, and wait for the
captain's go-ahead before any further round.

## What the reason line is for

`new finding` means the round found something not previously recorded.
`incomplete fix` means a listed blocker was not actually closed.
`regression` means the fixes broke something that worked.
`unclear spec` means the round could not judge because the acceptance criteria did not settle the
question - that is a briefing defect, and the fix is upstream in the brief, not another patch round.
