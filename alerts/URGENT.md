🚨 CRITICAL: Raqeeb detected major integrity issue

Issue: message-sender daemon is NOT running — no process found in ps aux
Severity: P1
Time detected: 2026-04-14T21:07:59Z
Auto-fix attempted: No — daemon restart requires infrastructure access not available to Raqeeb
Manual intervention needed: YES

---

## Details

### What was found
Running `ps aux | grep "message-sender"` returned zero results. The message-sender
daemon responsible for dispatching approved DMs from dm_queue/approved/ is completely
absent from the process list.

Additionally, the following expected runtime directories do not exist:
- sent/                           (no sent log — rate limit checks cannot run)
- dm_queue/approved/              (no approved queue — may be pre-deployment)
- dm_queue/outreach/              (no outreach queue)
- dm_queue/blocked_rate_limit/    (no rate limit block folder)
- dm_queue/archived/              (no archive folder)
- dm_queue/corrupted/             (no quarantine folder)

### What this means
- No DMs are being dispatched. The queue pipeline is broken or not yet deployed.
- Raqeeb cannot determine how long the daemon has been down (no PID file, no daemon logs).
- 6 DMs are sitting in dm_queue/pending_review/ awaiting approval — once approved,
  they will not be sent until the daemon is restored.
- Rate limit check is BLIND: no sent/ directory means Raqeeb cannot detect if
  the same user is being over-messaged.

### What still needs attention
1. Investigate why message-sender is not running (crash? never started? deployment issue?)
2. Check if this is a fresh deployment that hasn't been initialized yet
3. Start the daemon and verify it can read from dm_queue/approved/
4. Create the sent/ directory and initialise outreach_log.json
5. Create missing queue subdirectories: approved/, outreach/, blocked_rate_limit/,
   archived/pending_expired/, corrupted/

### Dispatch Notification
🚨 Integrity issue detected
Daemon (message-sender) is NOT running — queue is dead
Raqeeb auto-fixed: nothing (restart requires manual intervention)
Check alerts/URGENT.md for details
