---
name: sfdc-migrate
description: Walks Elijah through migrating his old CRM data into his HTC Salesforce org. Pre-configured for his stack — handles field mapping, load sequence, and validation against his SFDC + Frontspin + TitanX setup.
version: 1.0.0
last_change: "v1.0.0: HTC Consulting build — Elijah's objects and stack pre-loaded"
---

## Context

**Client:** Elijah — HTC Sales Consulting  
**Stack:** Salesforce + Frontspin + TitanX.io  
**Objects to migrate:** Accounts, Contacts, Leads, Opportunities  
**Note:** Frontspin and TitanX read from SFDC natively — once the base objects are loaded, both tools pick up the records automatically. No extra integration steps.

---

## Task

Open with:

> Let's get your data into Salesforce, Elijah.
>
> What CRM are you migrating from?

Wait for their answer.

## Steps

### Step 1 — Export Instructions

Give them the exact export steps for their CRM (see generic sfdc-migrate skill for full instructions per platform).

Close with:
> Export Accounts, Contacts, Leads, and Opportunities as separate CSV files. Come back when you have them and we'll do the field mapping.

---

### Step 2 — Field Mapping

When they're back:

> Paste your **Accounts** column headers here — just the top row.

Generate mapping table. Flag:
- Missing required SFDC fields
- Columns with no SFDC equivalent
- Any fields that look like they may have been added by their old CRM vs. standard fields

Repeat for Contacts, Leads, Opportunities.

**HTC-specific note:** If any columns look like they were added by a sales engagement tool (call counts, email counts, sequence names), flag them:
> This field looks like it came from a sales tool — Frontspin will recreate this data once it's connected to SFDC. You can skip importing it.

---

### Step 3 — Load Sequence

> Here's the import order for your HTC org:
>
> 1. **Accounts** — no dependencies
> 2. **Contacts** — references AccountId
> 3. **Leads** — standalone
> 4. **Opportunities** — references AccountId
> 5. **Tasks** — last, if you have historical activity
>
> Import in this order exactly. Do not skip ahead.

---

### Step 4 — Import Method

Ask:
> Roughly how many records per object?

- Under 50K: Data Import Wizard (`Setup → Data → Data Import Wizard`)
- Over 50K: offer Bulk API 2.0 Python script

---

### Step 5 — Validation

After each object imports, validate:

If Agentforce DX connected — run directly against `htc-sandbox`:
```sql
SELECT COUNT(Id) FROM Account
SELECT COUNT(Id) FROM Contact WHERE AccountId = null
SELECT Email, COUNT(Id) FROM Contact GROUP BY Email HAVING COUNT(Id) > 1
SELECT COUNT(Id) FROM Opportunity WHERE AccountId = null
```

If not connected — provide queries for SFDC Developer Console.

Flag and fix any issues before moving to the next object.

---

### Step 6 — Frontspin + TitanX Confirmation

After all objects are loaded:

> Your base data is in. Quick note on your tools:
>
> **Frontspin** syncs with SFDC natively — your new Contacts and Accounts will appear in Frontspin automatically. Any sequences you run will log back to SFDC as Tasks.
>
> **TitanX** reads from your SFDC Leads and Contacts — your imported records will be available for enrichment immediately.
>
> No extra steps needed for either tool.

---

### Step 7 — Hand Off to Reports

> Migration is done. Here's what's in your HTC org:
> - Accounts: [count]
> - Contacts: [count]
> - Leads: [count]
> - Opportunities: [count]
>
> Ready to build your pipeline dashboard? Run `/sfdc-reports` and say "start with the basics."
