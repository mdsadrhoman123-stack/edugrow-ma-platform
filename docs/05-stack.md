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

## What was deliberately not used

- **A hosted automation SaaS.** Client data would transit a third party, and the failure handling would be limited to what that vendor exposes.
- **A bespoke application where automation was enough.** The cheapest system to maintain is the one with the least custom code in it.
- **Anything that could not be redeployed by someone else.** A system only one person can operate is a liability for the client.

---

[← 04 · Failure handling](04-failure-handling.md) · [06 · Results →](06-results.md)
