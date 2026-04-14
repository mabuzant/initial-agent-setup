# flowOrder Autonomous Agents

**11 AI agents running your Instagram DM sales, outreach, customer success, and growth strategy 24/7.**

---

## Quick Start

1. **Read**: `00-SETUP-GUIDE.md` (complete deployment instructions)
2. **Schedule**: 11 agents in Claude Code (paste prompts from `agents/` folder)
3. **Deploy**: Instagram poller to Railway (I'll write the script tomorrow)
4. **Monitor**: Khalid sends you a morning brief via Dispatch every day at 8:30 AM

---

## The Agents

### Real-Time Layer (24/7)
1. **Noor** (DM Reply Engine) — Replies to every Instagram DM in Emirati Arabic or English
2. **Nasser** (Cold Outreach) — Finds new leads, sends personalized first-touch DMs
3. **Raqeeb** (Integrity Watchdog) — Prevents duplicate sends, enforces rate limits

### Monitoring Layer (Every 30min - 6 hours)
4. **Tarek** (Infrastructure) — Checks site health, daemon status, Stripe disputes
5. **Rashid** (Competitive Intel) — Monitors Salla/Zid/Shopify for threats
6. **Amira** (Customer Success) — Detects churn risk, generates re-engagement campaigns

### Strategic Layer (Daily/Weekly)
7. **Layla** (Growth Strategy) — Daily insight + Sunday PMF report
8. **Fahad** (Technical Architect) — Scopes features, estimates effort
9. **Knowledge Builder** (Sunday 6 AM) — Updates product.md, faq.md, competitive_intel.md
10. **Salma** (Sunday 7 AM) — Audits brand voice, updates messaging playbook

### Command Layer (Daily 8:30 AM)
11. **Khalid** (COO) — Synthesizes all signals → morning brief → mobile push notification

---

## Folder Structure

```
floworder-agents/
├── agents/           # 11 agent prompt files (.md)
├── knowledge/        # Shared knowledge base (product.md, faq.md, etc.)
├── dm_queue/         # DM pipeline (approved, outreach, pending_review)
├── sent/             # Sent DM logs
├── daily_signals/    # Agent reports (read by Khalid)
├── briefs/           # Khalid's morning brief + PMF reports
├── alerts/           # P1 infrastructure + competitive threats
└── inbox_new/        # Railway poller writes new DMs here
```

---

## Cost

**~$27-47/month total**:
- Claude Code agents: $22-42 (token usage, included in Max 5x)
- Railway poller: $5

---

## Mobile Workflow

**8:30 AM daily**: 
- 📱 Dispatch notification: "Morning brief ready"
- 👆 Tap → read Khalid's 5-min brief
- ✅ Reply with decisions (approve features, review DMs)
- 🚀 Agents execute approved actions

**Real-time alerts**:
- 🚨 Site down
- ⚠️ High-value customer churning
- 🔔 DM needs human review

---

## Files in This Package

```
00-SETUP-GUIDE.md           ← Start here (complete setup instructions)
README.md                   ← This file
agents/
  01-noor-dm-reply.md       ← DM reply agent
  02-nasser-cold-outreach.md ← Cold outreach agent
  03-raqeeb-integrity.md    ← Integrity watchdog
  04-tarek-infrastructure.md ← Infrastructure monitoring
  05-rashid-competitive-intel.md ← Competitive intelligence
  06-amira-customer-success.md ← Churn prevention
  07-layla-growth-strategy.md ← Growth insights + PMF
  08-fahad-technical-architect.md ← Feature scoping
  09-knowledge-builder.md   ← Knowledge base updates
  10-salma-brand-voice.md   ← Brand consistency audit
  11-khalid-coo.md          ← Morning brief synthesizer
```

---

## Next Steps

1. **Tonight**: Schedule agents in Claude Code (follow `00-SETUP-GUIDE.md`)
2. **Tomorrow morning**: I'll write the Railway poller + Message Sender scripts
3. **Deploy & test**: End-to-end DM flow
4. **Go live**: Fully autonomous by tomorrow night

---

**Questions?** Ask me in the chat tomorrow morning.

**Let's build.** 🚀
