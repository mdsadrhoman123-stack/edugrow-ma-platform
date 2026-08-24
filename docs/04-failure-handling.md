# 04 · Failure handling

The part of the system that took the longest to build and gets written about the least.

---

| What goes wrong | How it is detected | What the system does | Who finds out |
| :--- | :--- | :--- | :--- |
| **Required field missing** | State-machine guard | Deal cannot advance — blocked, not warned | Blocked state is visible and logged |
| **Signature send fails** | API error | Retried up to five times with exponential backoff | Alert only if all five fail |
| **All retries exhausted** | Retry count | Written to a dead-letter table, never dropped | Dual alert: Telegram and email |
| **Same action submitted twice** | Idempotency guarantee | Second submission does not re-run the step | Nobody — by design |
| **Tracing a problem after the fact** | Correlation ID threaded through every step | The whole path for one deal can be reconstructed | Whoever asks |
| **One alert channel is down** | Dual delivery | The other channel still lands | The human still finds out |

## The three rules behind that table

**1 — Fail closed, not open.** When the system cannot establish that an action is safe, it holds. A held item is a visible problem. An item processed on a guess is an invisible one.

**2 — Nothing disappears.** Anything that cannot be completed is recorded where a human can find it later, not dropped from the run.

**3 — Silence is a fault.** An empty result where results were expected is treated as a possible failure of the source, not as an absence of work. This is the check most automations skip.

---

[← 03 · Architecture](03-architecture.md) · [05 · The stack →](05-stack.md)
