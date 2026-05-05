---
name: sfdc-setup
description: Walks Elijah through connecting Claude to his HTC Salesforce org via Agentforce DX. Pre-configured for his stack — SFDC + Frontspin + TitanX.io. Run once. After setup, Claude can query his org, build reports, and deploy dashboards directly.
version: 1.0.0
last_change: "v1.0.0: HTC Consulting build — Elijah's stack pre-loaded"
---

## Context

**Client:** Elijah — HTC Sales Consulting  
**Stack:** Salesforce + Frontspin + TitanX.io  
**Org aliases:** `htc-sandbox` and `htc-prod`  
**Goal:** Connect Claude to his SFDC org so he can build reports and dashboards by describing them in plain English — no clicking through Report Builder.

---

## Task

Open with:

> Hey Elijah! We're going to connect Claude directly to your Salesforce org — this is Agentforce DX, Salesforce's official tool for this.
>
> Once it's done, you tell me what you want to track, and I'll build and deploy the report straight into your org. No Report Builder. No clicking.
>
> This takes about 10 minutes. You only do it once.
>
> First — do you have Node.js installed? Run `node -v` in your terminal and tell me what it says.

Wait for response, then continue step by step.

## Steps

### Step 1 — Check Node.js

If they have Node (any version): proceed to Step 2.

If not:
> No problem. Go to nodejs.org, download the LTS version, install it, and come back. We'll keep going from here.

---

### Step 2 — Install Salesforce CLI

> Let's install the Salesforce CLI — this is what lets Claude talk to your org.
>
> In your terminal:
> ```
> npm install -g @salesforce/cli
> ```
>
> Takes about a minute. Let me know when it's done.

After confirmation:
> Quick check — run:
> ```
> sf --version
> ```
> What did it show?

---

### Step 3 — Authenticate Sandbox

> Now let's connect your HTC sandbox. Run:
> ```
> sf org login web -a htc-sandbox
> ```
>
> A browser opens — log into your **sandbox** org. Come back when it's done.

Confirm: "Did it say Successfully authorized?"

---

### Step 4 — Authenticate Production

> Good. Now production:
> ```
> sf org login web -a htc-prod
> ```
>
> Log into your **production** HTC org this time. Come back when done.

---

### Step 5 — Add Agentforce DX to Claude Desktop

> Last step. Open this file:
> ```
> ~/Library/Application Support/Claude/claude_desktop_config.json
> ```
>
> If it doesn't exist, create it. Paste this in:
> ```json
> {
>   "mcpServers": {
>     "salesforce": {
>       "command": "npx",
>       "args": ["-y", "@salesforce/mcp", "--target-org", "htc-sandbox"]
>     }
>   }
> }
> ```
>
> Save it. Quit Claude Desktop completely and reopen it.

---

### Step 6 — Test Connection

> In a new Claude Desktop conversation, type:
> *"List my authenticated Salesforce orgs"*
>
> What did it say?

If it shows `htc-sandbox` and `htc-prod` — connected.

If it errors — check the JSON format (missing comma, bracket?) and that Claude Desktop fully restarted.

---

### Step 7 — First Real Test with Your Stack

> Let's make sure your Frontspin and TitanX fields came through. In Claude Desktop:
>
> *"Describe the Opportunity object in htc-sandbox and list all custom fields."*
>
> You should see your standard SFDC fields plus anything Frontspin or TitanX added. Tell me what you see.

Once confirmed:
> You're connected. Claude can now see your full org — including your Frontspin activity data and TitanX enrichment fields.
>
> Run `/sfdc-reports` and tell me the first thing you want to track. I'll build it.
