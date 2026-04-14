🚨 CRITICAL: Raqeeb detected major integrity issue

Issue: message-sender daemon is NOT running — confirmed dead 68+ minutes
Severity: P1
First detected: 2026-04-14T21:07:59Z
Re-confirmed: 2026-04-14T22:15:47Z
Confirmed minimum downtime: 68 minutes
Auto-fix attempted: No — daemon restart requires infrastructure access not available to Raqeeb
Manual intervention needed: YES

---

## Details

### What was found
Running `ps aux | grep "message-sender"` returned zero results at both 21:07Z and 22:15Z.
The message-sender daemon responsible for dispatching approved DMs is absent from the
process list. Downtime is now confirmed at a minimum of 68 minutes.

Additionally, the following expected runtime directories do not exist:
- sent/                           (no sent log — rate limit checks cannot run)
- dm_queue/approved/              (no approved queue — may be pre-deployment)
- dm_queue/outreach/              (no outreach queue)
- dm_queue/blocked_rate_limit/    (no rate limit block folder)
- dm_queue/archived/              (no archive folder)
- dm_queue/corrupted/             (no quarantine folder)

### 🆕 Escalation: Urgency Window Breach Incoming
Two critical DMs in dm_queue/pending_review/ have respond_within_24h urgency.
Their approval + send windows close at 2026-04-15T08:00:00Z (~9h 44m from 22:15Z check).

| Username | Category | Risk | MRR | Signal |
|----------|----------|------|-----|--------|
| @luqaimat_aisha | reengagement | critical | 149/mo | Asked "كيف أوقف الاشتراك؟" (cancel intent) on 2026-04-11 |
| @mashawi_express | reengagement | critical | 299/mo | Said "thinking of switching to something else" on 2026-04-09 |

Both have `escalateToKhalid: true` and `callRecommended: true`.
With the daemon down, even if approved, these DMs CANNOT be sent automatically.
Manual dispatch or daemon restoration needed before 08:00Z 2026-04-15.

### What this means
- No DMs are being dispatched. The queue pipeline is broken or not yet deployed.
- 6 DMs are sitting in dm_queue/pending_review/ awaiting approval.
- Rate limit check is BLIND: no sent/ directory means Raqeeb cannot detect
  if the same user is being over-messaged.
- Two high-value customers with active churn intent will miss their response window
  if the daemon is not restored and DMs are not sent before 08:00Z 2026-04-15.

### What still needs attention
1. Investigate why message-sender is not running (crash? never started? deployment issue?)
2. Check if this is a fresh deployment that hasn't been initialized yet
3. Start the daemon and verify it can read from dm_queue/approved/
4. Create the sent/ directory and initialise outreach_log.json
5. Create missing queue subdirectories: approved/, outreach/, blocked_rate_limit/,
   archived/pending_expired/, corrupted/
6. PRIORITY: Manually review and send DMs to @luqaimat_aisha and @mashawi_express
   before 2026-04-15T08:00:00Z — both are critical churn risk

---

## Dispatch Notification

🚨 Integrity issue detected
Daemon (message-sender) STILL NOT running — now confirmed dead 68+ minutes
2 critical churn-risk DMs will miss 24h response window by ~08:00Z tomorrow if not acted on:
  • @luqaimat_aisha — asked to cancel
  • @mashawi_express (299 AED/mo) — switching intent
Raqeeb auto-fixed: nothing (restart requires manual intervention)
Check alerts/URGENT.md for full details
