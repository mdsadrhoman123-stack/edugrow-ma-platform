# 01 · The problem

**M&A Deal-Flow Platform** — the problem, as Boutique M&A advisory firms describe it

---

A boutique M&A advisory firm needed its NDA and deal-flow process to run without a human watching every step.

But a dropped signature request or a silent failure in a live deal costs a client's trust, or the deal itself. Off-the-shelf automation tools do not give this kind of visibility or recovery.

So the build started from the failure cases rather than the happy path.

## Why it was not solved already

Every business in this position has already tried the obvious answers: a shared inbox, a spreadsheet, a rule in an off-the-shelf tool, a reminder to be more careful. Those work until volume grows or someone is on holiday.

The gap is not effort. It is that the process lives in people's habits rather than in a system, so it degrades quietly and nobody can measure by how much.

## What the requirement actually was

A versioned PostgreSQL schema with state-machine enforcement, full audit logging, dead-letter tables and idempotency guarantees. The NDA workflow retries a failed send up to five times with exponential backoff, threads a correlation ID through every step, and raises dual alerts the moment a human is needed.

---

[← README](../README.md) · [02 · The journey →](02-journey.md)
