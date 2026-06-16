# GTM Orchestrator — Personal Job Search Edition

An automated GTM pipeline built with Python, Claude Code, n8n, HubSpot, HeyReach, and Smartlead. Discovers European B2B SaaS companies hiring GTM Engineers, scores them, writes personalised outreach, and pushes to sequencers automatically.

## What it does

1. **Discovers leads** from LinkedIn, EuroVC portfolio pages (Northzone, Balderton, HV Capital, Earlybird), and GitHub
2. **Scores each company** High/Medium/Low based on location, hiring signal, VC backing, and company size
3. **Writes 4 personalised messages** per contact using Claude API — LinkedIn Message 1, LinkedIn Message 2, Email 1, Email 2
4. **Pushes to HubSpot** via n8n webhook with ICP score and outreach angle
5. **Sends Slack alerts** after every pipeline run with lead counts and push results
6. **Pushes to HeyReach** via n8n webhook for LinkedIn sequences
7. **Pushes to Smartlead** via REST API for email sequences

## Pipeline flow

```
sourcer.py → raw_leads_europe.csv
↓
enricher.py → enriched_leads.csv (scored High/Medium/Low)
↓
outreach_writer.py → outreach_sequences.csv (4 messages per company)
↓
Clay enrichment → clay_enriched.csv (real contact names + emails)
↓
merge_and_push.py → final_outreach.csv + HubSpot push
↓
heyreach_push.py → n8n → HeyReach (LinkedIn sequences)
↓
smartlead_push.py → Smartlead (email sequences)
↓
monitor.py → pipeline_log.txt + Slack notification
```

## Sources

| Source | What it finds |
|--------|--------------|
| LinkedIn jobs-guest API | Companies actively hiring GTM Engineers in Europe |
| Northzone portfolio | VC-backed European SaaS companies |
| Balderton portfolio | VC-backed European SaaS companies |
| HV Capital portfolio | VC-backed European SaaS companies |
| Earlybird portfolio | VC-backed European SaaS companies |
| GitHub API | Contributors to GTM/RevOps open source repos |

## Scoring logic

| Signal | Score |
|--------|-------|
| Active GTM/RevOps job posting on LinkedIn | High |
| Netherlands or Amsterdam location | +1 boost |
| VC portfolio company | Medium |
| GitHub EU contributor | Medium |
| Non-EU location or no signal | Low |

### 2. Configure environment
Copy `.env.example` to `.env` and fill in your keys:
```
SLACK_WEBHOOK_URL=your_slack_webhook_url
HEYREACH_API_KEY=your_heyreach_api_key
N8N_HEYREACH_WEBHOOK_URL=your_n8n_heyreach_webhook_url
HUBSPOT_TOKEN=your_hubspot_private_app_token
ANTHROPIC_API_KEY=your_anthropic_api_key
SMARTLEAD_API_KEY=your_smartlead_admin_api_key

Rebecca Akparanta — GTM Engineer
Portfolio: rebeccaakparanta.vercel.app
