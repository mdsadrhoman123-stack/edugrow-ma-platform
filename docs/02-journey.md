# 02 · The client journey

What this looks like from the outside, for **Boutique M&A advisory firm**.

---

### 01 — A deal is entered

Intake writes to a versioned schema, not a spreadsheet.

### 02 — The state machine decides

A deal cannot move to the next stage without its required fields. Blocked, not warned — that distinction is the product.

### 03 — The NDA goes out

And if the send fails it is retried five times with exponential backoff, not assumed successful.

### 04 — Failure has somewhere to go

After five attempts the item lands in a dead-letter table and two alert channels fire. Nothing evaporates.

### 05 — Everything is traceable

A correlation ID runs through every step, so one deal's whole path can be reconstructed months later.

### 06 — Re-running is safe

Idempotency guarantees mean nothing runs twice.

---

## The one decision that shaped everything else

A versioned PostgreSQL schema with state-machine enforcement, full audit logging, dead-letter tables and idempotency guarantees. The NDA workflow retries a failed send up to five times with exponential backoff, threads a correlation ID through every step, and raises dual alerts the moment a human is needed.

---

[← 01 · The problem](01-problem.md) · [03 · Architecture →](03-architecture.md)
