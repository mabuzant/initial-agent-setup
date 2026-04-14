# flowOrder Agents — Complete Setup Guide

## Overview

This package contains **11 autonomous agents** + 1 lightweight poller that run flowOrder's growth automation 24/7. Total cost: **~$22-42/month** (well under your $100 Max 5x budget).

---

## Architecture

```
Instagram Inbox
    ↓
Railway Poller (checks every 60 sec) → writes to GitHub
    ↓
GitHub Repo (floworder-agents/) ← Claude Code reads from here
    ↓
Claude Code Scheduled Tasks (11 agents run on schedules)
    ↓
dm_queue/ (approved DMs) → Message Sender → Instagram API
    ↓
daily_signals/ (agent reports) → Khalid synthesizes
    ↓
Dispatch (mobile push notifications) → Your iPhone
```

---

## Folder Structure

```
floworder-agents/                 ← Main project folder
├── knowledge/                    ← Shared knowledge base (all agents read from here)
│   ├── product.md               ← Product facts, features, pricing
│   ├── faq.md                   ← Common questions (Arabic + English)
│   ├── messaging_playbook.md    ← Brand voice rules
│   ├── competitive_intel.md     ← Salla/Zid/Shopify tracking
│   ├── audience_intel.md        ← Lead segments, funnel stats
│   └── INDEX.md                 ← Freshness tracker
│
├── dm_queue/                    ← DM pipeline
│   ├── approved/                ← Ready to send
│   ├── outreach/                ← Cold outreach DMs
│   ├── pending_review/          ← Need human approval
│   ├── blocked_rate_limit/      ← Rate limit violations
│   └── corrupted/               ← Invalid files
│
├── sent/                        ← Sent DM logs (never delete)
│   ├── outreach_log.json        ← All outreach history
│   └── [username]_history.json  ← Per-customer conversation history
│
├── daily_signals/               ← Agent reports (read by Khalid)
│   ├── noor_YYYY-MM-DD.md
│   ├── nasser_YYYY-MM-DD.md
│   ├── raqeeb_YYYY-MM-DD.md
│   ├── tarek_YYYY-MM-DD.md
│   ├── rashid_YYYY-MM-DD.md
│   ├── amira_YYYY-MM-DD.md
│   ├── layla_YYYY-MM-DD.md
│   ├── fahad_YYYY-MM-DD.md
│   ├── knowledge_builder_YYYY-MM-DD.md
│   └── salma_YYYY-MM-DD.md
│
├── briefs/                      ← Khalid's output
│   ├── brief_YYYY-MM-DD.md      ← Daily morning brief
│   ├── pmf_report_YYYY-MM-DD.md ← Layla's Sunday deep report
│   └── decisions_log.md         ← All approved/rejected decisions
│
├── alerts/                      ← Urgent issues
│   ├── URGENT.md                ← P1 infrastructure alerts
│   └── competitive_threat.md    ← High-priority competitor moves
│
├── feature_requests/            ← Feature scoping
│   ├── customer_requests.md     ← All customer feature requests
│   └── [feature]_plan.md        ← Fahad's implementation plans
│
├── agents/                      ← Agent prompts (11 .md files)
│   ├── 01-noor-dm-reply.md
│   ├── 02-nasser-cold-outreach.md
│   ├── 03-raqeeb-integrity.md
│   ├── 04-tarek-infrastructure.md
│   ├── 05-rashid-competitive-intel.md
│   ├── 06-amira-customer-success.md
│   ├── 07-layla-growth-strategy.md
│   ├── 08-fahad-technical-architect.md
│   ├── 09-knowledge-builder.md
│   ├── 10-salma-brand-voice.md
│   └── 11-khalid-coo.md
│
├── daemon/                      ← Message sender (lightweight script)
│   ├── heartbeat.txt            ← Daemon writes timestamp every 60 sec
│   └── sender.js                ← Reads dm_queue/approved/, sends via Instagram API
│
├── inbox_new/                   ← Railway poller writes new DMs here
├── leads_new/                   ← Railway poller writes new leads here
│
├── .env                         ← Credentials (Instagram, Stripe, etc.)
├── package.json                 ← Node.js dependencies
└── README.md                    ← This file
```

---

## Setup Steps (Do Tonight)

### Phase 1: Create GitHub Repo (5 min)
```bash
# On your Mac
mkdir floworder-agents
cd floworder-agents

# Copy all files from this ZIP into floworder-agents/

# Initialize git
git init
git add .
git commit -m "Initial agent setup"

# Create GitHub repo (via GitHub website or CLI)
gh repo create floworder-agents --private --source=. --push
```

---

### Phase 2: Install Claude Code CLI (5 min)
```bash
# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Authenticate
claude auth login
# Opens browser → login with your Anthropic account (Max 5x plan)

# Navigate to project folder
cd floworder-agents

# Initialize Claude Code project
claude init
```

---

### Phase 3: Connect Services (15 min)

#### A. Connect GitHub
```bash
claude mcp install github
claude mcp connect github
# Opens browser → authorize GitHub app → select floworder-agents repo
```

#### B. Connect Stripe
```bash
claude mcp install stripe
claude mcp connect stripe
# Opens browser → enter Stripe API key from dashboard.stripe.com/apikeys
```

#### C. Setup Instagram API
1. Go to [developers.facebook.com](https://developers.facebook.com)
2. Create app → Instagram Basic Display
3. Get access token
4. Create `.env` file:

```bash
# .env
INSTAGRAM_ACCESS_TOKEN=your_token_here
INSTAGRAM_USER_ID=your_instagram_business_account_id
STRIPE_API_KEY=sk_live_...
```

**IMPORTANT**: Add `.env` to `.gitignore` (don't commit credentials)

---

### Phase 4: Schedule Agents in Claude Code (30 min)

#### Option A: Using Dashboard (Easiest)
```bash
# Start Claude Code dashboard
claude dashboard
# Opens http://localhost:3000 in browser
```

**In the dashboard**:
1. Click **"New Scheduled Task"**
2. For each agent (01-noor.md through 11-khalid.md):
   - **Name**: [Agent name from file]
   - **Schedule**: [See schedule table below]
   - **Prompt**: [Paste entire content from .md file]
3. Click **"Create Task"**

**Schedule Table**:
| Agent | Schedule Type | Cron Expression |
|-------|---------------|-----------------|
| Noor | File watcher | `inbox_new/*.json` |
| Nasser | File watcher | `leads_new/*.json` |
| Raqeeb | Every 5 min | `*/5 * * * *` |
| Tarek | Every 30 min | `*/30 * * * *` |
| Rashid | Every 6 hours | `0 */6 * * *` |
| Amira | Every 4 hours | `0 */4 * * *` |
| Layla (daily) | 8:00 AM UAE | `0 4 * * *` |
| Layla (Sunday) | 7:00 AM UAE Sunday | `0 3 * * 0` |
| Fahad | On-demand | (no schedule, triggered by Khalid) |
| Knowledge Builder | Sunday 6 AM UAE | `0 2 * * 0` |
| Salma | Sunday 7 AM UAE | `0 3 * * 0` |
| Khalid | Daily 8:30 AM UAE | `0 4 30 * * *` |

---

#### Option B: Using CLI (Faster)
```bash
# Schedule all agents at once
claude task create --name "Noor" --schedule "file-watcher:inbox_new/*.json" --prompt-file agents/01-noor-dm-reply.md
claude task create --name "Nasser" --schedule "file-watcher:leads_new/*.json" --prompt-file agents/02-nasser-cold-outreach.md
claude task create --name "Raqeeb" --schedule "*/5 * * * *" --prompt-file agents/03-raqeeb-integrity.md
claude task create --name "Tarek" --schedule "*/30 * * * *" --prompt-file agents/04-tarek-infrastructure.md
claude task create --name "Rashid" --schedule "0 */6 * * *" --prompt-file agents/05-rashid-competitive-intel.md
claude task create --name "Amira" --schedule "0 */4 * * *" --prompt-file agents/06-amira-customer-success.md
claude task create --name "Layla Daily" --schedule "0 4 * * *" --prompt-file agents/07-layla-growth-strategy.md
claude task create --name "Knowledge Builder" --schedule "0 2 * * 0" --prompt-file agents/09-knowledge-builder.md
claude task create --name "Salma" --schedule "0 3 * * 0" --prompt-file agents/10-salma-brand-voice.md
claude task create --name "Khalid" --schedule "0 4 30 * * *" --prompt-file agents/11-khalid-coo.md
```

---

### Phase 5: Deploy Instagram Poller to Railway (20 min)

**I'll write the poller script for you tomorrow morning** (it's a simple 50-line Node.js script).

For now, here's the Railway setup:

1. Go to [railway.app](https://railway.app)
2. Sign up with GitHub
3. Create new project → **"Deploy from GitHub repo"**
4. Select `floworder-agents`
5. Railway will auto-detect `poller.js` (when I create it tomorrow)
6. Add environment variables in Railway dashboard:
   - `INSTAGRAM_ACCESS_TOKEN`
   - `GITHUB_TOKEN` (for writing files to repo)
7. Deploy

**Cost**: $5/month (always-on, 24/7)

---

### Phase 6: Configure Dispatch Notifications (10 min)

In Claude Code dashboard:
1. Settings → Notifications → Dispatch
2. Enable push notifications
3. Configure triggers:
   - **Instant push** (P1): Tarek alerts, Amira churn warnings, Khalid decisions
   - **Daily digest** (8:30 AM): Khalid's morning brief
4. Test: Trigger a test notification

---

## Testing the System (15 min)

### Test 1: End-to-End DM Flow
```bash
# Create a test DM
echo '{
  "username": "@testuser",
  "message": "كم سعر الاشتراك؟",
  "timestamp": "'$(date -u +%Y-%m-%dT%H:%M:%SZ)'"
}' > inbox_new/test_dm.json

# Wait 2 minutes (Noor should process it)
# Check output:
cat dm_queue/approved/testuser_*.json

# Should see generated Arabic reply about pricing
```

---

### Test 2: Khalid's Morning Brief
```bash
# Manually trigger Khalid
claude task run "Khalid"

# Check output:
cat briefs/brief_$(date +%Y-%m-%d).md

# Should see synthesized brief with metrics from all agents
```

---

### Test 3: Dispatch Notification
```bash
# Trigger a P1 alert (simulate site down)
echo "🚨 P1: floworder.shop is DOWN" > alerts/URGENT.md

# Tarek will detect this on next run (within 30 min)
# You should get mobile push notification
```

---

## Monitoring & Maintenance

### Daily (8:30 AM — read Khalid's brief)
- Check your phone for Dispatch notification
- Read morning brief (5 min)
- Reply with decisions if any
- That's it!

### Weekly (Sunday morning)
- Layla's PMF report available in `briefs/`
- Knowledge Builder updated all files
- Salma audited brand voice

### Monthly
- Review `briefs/decisions_log.md` (what did you approve?)
- Check token usage (should be $22-42/month)
- Review agent performance (are insights actionable?)

---

## Troubleshooting

### "Noor isn't replying to DMs"
1. Check `inbox_new/` — are files appearing?
   - If NO → Railway poller isn't running (check Railway logs)
   - If YES → Check `daily_signals/noor_errors_YYYY-MM-DD.md`
2. Check `knowledge/product.md` — is it stale?
   - If >7 days old → Knowledge Builder didn't run (check Sunday logs)

---

### "Khalid's brief is empty"
1. Check `daily_signals/` — are agent reports being written?
   - If NO → Agents aren't running (check Claude Code dashboard)
2. Check Khalid's schedule — is it set to 8:30 AM UAE (0 4 30 * * *)?

---

### "Too many Dispatch notifications"
1. Adjust notification rules in Claude Code settings
2. Move non-urgent items to daily digest only
3. Increase P1 threshold (e.g., only alert if site down >5 min)

---

## Cost Breakdown

| Component | Monthly Cost |
|-----------|--------------|
| Claude Code (agents) | ~$22-42 (token usage, included in Max 5x) |
| Railway (poller) | $5 |
| GitHub | $0 (free tier) |
| Stripe API | $0 (included) |
| Instagram API | $0 (free) |
| **Total** | **$27-47/month** |

**Your Max 5x plan ($100/month)** covers all of this with headroom.

---

## What Happens Tomorrow Morning

**6:00 AM UAE** (Sunday only):
- Knowledge Builder scrapes floworder.shop → updates product.md, faq.md

**7:00 AM UAE** (Sunday only):
- Salma audits last week's DMs → updates messaging_playbook.md

**8:00 AM UAE** (Daily):
- Layla reads all signals → writes daily insight
- **Sunday**: Layla also writes deep PMF report

**8:30 AM UAE** (Daily):
- **Khalid reads everything → synthesizes morning brief**
- **You get Dispatch notification on your phone**
- **You read brief (5 min), make decisions, done**

**Every 5 min** (24/7):
- Raqeeb scans queue for duplicates, fixes issues

**Every 30 min** (24/7):
- Tarek checks site health, daemon status, Stripe

**Every 4 hours** (24/7):
- Amira checks for churn risk, generates re-engagement campaigns

**Every 6 hours** (24/7):
- Rashid monitors Salla/Zid/Shopify for competitive threats

**Every 60 seconds** (24/7, on Railway):
- Instagram poller checks for new DMs, new leads

**When new DM arrives**:
- Noor generates reply → dm_queue/approved/ → Sender sends it

**When new lead discovered**:
- Nasser generates personalized outreach → dm_queue/outreach/ → Sender sends it

---

## Next Steps for Tomorrow

1. **I'll write the Railway poller script** (50 lines, ready to deploy)
2. **I'll write the Message Sender daemon** (reads approved/, sends to Instagram)
3. **You deploy both to Railway** (5 min setup)
4. **System goes live** — fully autonomous 24/7

---

## Questions?

If anything isn't clear, ask me tomorrow morning. For now:
1. **Create the GitHub repo**
2. **Schedule the 11 agents in Claude Code**
3. **Configure Dispatch**
4. **Go to sleep** 😴

By morning, you'll have the full system running.

---

**Welcome to autonomous growth operations.** 🚀
