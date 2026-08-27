# 07 · Limitations

Written by the person who made the trade-offs.

---

- The state machine is strict by design. A firm that wants to advance a deal with fields missing will find it obstructive — which is the point.

- Dead-lettered items need a human to work the queue. The system guarantees nothing is lost, not that nothing needs attention.

- Schema is versioned at v2, so migrations are a real operational step rather than an afterthought.

## On reading this section

A limitations section is not a disclaimer. It is the fastest way to tell whether a system was designed or assembled. Every one of the constraints above was a decision with a reason behind it, and each one could be lifted — at a cost that was not worth paying for the problem in this brief.

If your situation makes a different trade the right one, that is a conversation worth having.

---

[← 06 · Results](06-results.md) · [README](../README.md)
