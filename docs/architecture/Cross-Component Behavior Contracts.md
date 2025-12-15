# Cross-Component Behavior Contracts

Rules that must be enforced consistently by Laravel, FastAPI, agent, and kernel.

## Time Synchronization
- Acceptable drift ±5s. If exceeded, FastAPI tags device `unsynchronized` and limits to low-risk commands.
- Agent must attempt NTP sync and retry every 2 minutes until within bounds.
- Signed payloads include `issued_at`/`expires_at`; reject if outside skew window.

## Sequence Numbers
- Sequence persists across restarts; stored in agent state and FastAPI cache.
- On agent restart, resume with last persisted `sequence+1`; FastAPI rejects replays and stale sequences.
- Kernel operations use independent monotonic counters stored in driver registry key; reset only on explicit reset event logged to audit chain.

## Offline Queue
- Max queued commands offline: 200; FIFO order preserved; commands older than 30 minutes dropped.
- Queue corruption triggers flush and re-fetch of pending commands; event emitted for audit.
- Upon reconnect, agent sends `resume_token` with last applied sequence; FastAPI resends missing commands.

## Policy Hash Mismatch
- If agent `policy_hash` ≠ server active hash, deny high-risk commands and request bundle refresh.
- FastAPI returns `policy_out_of_sync` error; Laravel invalidates cached decisions and pushes latest bundle.
- Persistent mismatch >15 minutes escalates to compliance `non_compliant`.
