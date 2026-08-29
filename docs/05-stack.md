# 05 · The stack

Each choice, and the reason for it.

---

| Component | Why this one |
| :--- | :--- |
| **n8n** | Six workflows covering the deal lifecycle |
| **PostgreSQL** | Versioned schema, state machine, audit log and dead-letter tables |
| **DocuSign API** | The NDA send, retried rather than assumed |
| **OpenAI GPT-4** | Document handling inside the deal workflows |
| **Custom buyer portal (web)** | Where the advisory team works the pipeline |
| **Telegram + Email** | Two alert channels, because one channel is a single point of failure |

## The decisions behind that table

### Why a state machine instead of a status field

**What it does.** A versioned schema where a deal can only move along transitions that are permitted, with the audit log written as it moves.

**What was turned down.** A status column any part of the system can set. Flexible, and every team asks for it — and it lets a deal arrive at signature with required fields still empty, which is the failure that costs a firm a transaction.

**What that costs.** Strict by design. A firm that wants to advance a deal with fields missing will find it obstructive, which is the point. The schema is at v2, so migrations are a real operational step rather than an afterthought.

### Why a failed NDA send retries five times and then dead-letters

**What it does.** Exponential backoff, a correlation ID threaded through every step, and a dead-letter table when it still will not go.

**What was turned down.** Sending and assuming it worked. The common case is fine — and the failure is silent, and a silent failure here means a signature nobody ever chased.

**What that costs.** Dead-lettered items need a person to work the queue. The system guarantees nothing is lost; it does not guarantee that nothing needs attention.

### Why there are two alert channels

**What it does.** Anything that needs a human is announced on Telegram and by email, both, every time — not one with the other as a fallback.

**What was turned down.** One channel. Less noise and one integration to keep credentialed — and one channel is a single point of failure on the one day it matters.

**What that costs.** Duplicate alerts to live with, and two integrations to keep credentialed.

## The rule that applies to all of them

**Nothing that only one person can operate.** A system that depends on the engineer who built it is a liability for the client, however well it runs on the day it is handed over. Every choice above had to survive that test before the technical merits mattered at all.

---

[← 04 · Failure handling](04-failure-handling.md) · [06 · Results →](06-results.md)
