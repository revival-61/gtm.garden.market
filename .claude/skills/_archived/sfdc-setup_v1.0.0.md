---
name: sfdc-setup
description: Walks you through connecting Claude to your Salesforce org via Agentforce DX — conversational, step by step. Run this once. After setup, Claude can query your org, build reports, and deploy dashboards directly.
version: 1.0.0
last_change: "v1.0.0: Initial release — Agentforce DX setup flow"
---

## Task

Welcome Elijah and orient to what we're doing:

> Hey! This skill is going to walk you through connecting Claude to your Salesforce org using Agentforce DX — Salesforce's official tool for this.
>
> Once it's set up, you'll be able to describe a report or dashboard in plain English and Claude will build and deploy it directly to your org. No clicking through Report Builder.
>
> This takes about 10 minutes and you only do it once.
>
> Before we start — do you have Node.js installed? You can check by typing `node -v` in your terminal. What did it say?

Wait for their response, then continue step by step.

## Steps

### Step 1 — Check Node.js

If they have Node (any version): proceed to Step 2.

If they don't have Node, guide them:
> No problem. Go to nodejs.org, download the LTS version, and install it. Come back here when it's done and we'll keep going.

Wait for confirmation before continuing.

---

### Step 2 — Install Salesforce CLI

> Great. Now let's install the Salesforce CLI — this is what lets Claude talk to your org.
>
> In your terminal, run:
> ```
> npm install -g @salesforce/cli
> ```
>
> This takes about a minute. Let me know when it finishes.

After they confirm:
> Let's verify it worked. Run:
> ```
> sf --version
> ```
> What did it show?

If it shows a version number — proceed. If it errors — troubleshoot before continuing.

---

### Step 3 — Authenticate Your Sandbox

> Perfect. Now we're going to connect to your Salesforce sandbox first — always test there before production.
>
> Run this command:
> ```
> sf org login web -a sfdc-sandbox
> ```
>
> A browser window will open. Log into your **sandbox** org (not production). Once you're logged in, come back here.

Wait for confirmation, then:
> Did it show "Successfully authorized" in the terminal? What org username did it show?

---

### Step 4 — Authenticate Production

> Great. Now let's connect your production org too:
> ```
> sf org login web -a sfdc-prod
> ```
>
> This time log into your **production** org. Come back when it's done.

Confirm they're authenticated to both.

---

### Step 5 — Add Agentforce DX to Claude Desktop

> Last step. We need to tell Claude Desktop about your Salesforce connection.
>
> Open this file on your Mac:
> ```
> ~/Library/Application Support/Claude/claude_desktop_config.json
> ```
>
> If it doesn't exist yet, create it. Add this inside (or merge it in if the file already has content):
> ```json
> {
>   "mcpServers": {
>     "salesforce": {
>       "command": "npx",
>       "args": ["-y", "@salesforce/mcp", "--target-org", "sfdc-sandbox"]
>     }
>   }
> }
> ```
>
> Save the file, then **quit and restart Claude Desktop completely**.
>
> Done? Tell me when it's back open.

---

### Step 6 — Test the Connection

Once they're back in Claude Desktop:

> Let's make sure everything is connected. In a new Claude Desktop conversation, type:
>
> *"List my authenticated Salesforce orgs"*
>
> What did Claude respond with?

If it returns their org names — setup is complete.

If it errors — troubleshoot: check the config file format (valid JSON?), confirm Claude Desktop fully restarted, confirm sf CLI is in their PATH.

---

### Step 7 — First Real Test

> You're connected. Let's do something real.
>
> In Claude Desktop, type:
>
> *"Describe the Opportunity object in my sfdc-sandbox org and list all field names."*
>
> Claude should return a list of all your Opportunity fields — including any custom ones from Frontspin or TitanX.

Once they confirm it works:

> Setup is complete. Claude can now query your org, build reports, and deploy config directly.
>
> You're ready to build your first report. Run `/sfdc-reports` to get started — or just tell me what you want to measure and we'll build it right now.
