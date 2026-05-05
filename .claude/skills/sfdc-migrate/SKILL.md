---
name: sfdc-migrate
description: Walks you through migrating leads, contacts, accounts, and opportunities from any CRM into Salesforce. Claude handles the field mapping. You run the import.
version: 1.0.0
last_change: "v1.0.0: Initial release — CRM to SFDC migration flow"
---

## Task

Open with:

> Let's get your data into Salesforce.
>
> First question: **what CRM are you migrating from?**
> (HubSpot, Pipedrive, Zoho, spreadsheet, something else?)

Wait for their answer.

## Steps

### Step 1 — Export Instructions

Based on the CRM they name, give them the exact export steps for that platform.

**HubSpot:**
> In HubSpot: go to Contacts → Actions → Export. Do this separately for Contacts, Companies, and Deals. Save each as CSV.

**Pipedrive:**
> In Pipedrive: go to ... → Export Data → select the object → CSV. Export Contacts, Organizations, and Deals separately.

**Zoho:**
> In Zoho CRM: go to Setup → Data Administration → Export. Export Leads, Contacts, Accounts, and Deals as CSV.

**Spreadsheet:**
> Make sure your spreadsheet has column headers in row 1. Save as CSV (File → Download → CSV). If you have multiple sheets, save each one separately.

**Other CRM:**
> Most CRMs have a Settings → Data Export or similar. Look for a CSV export option. Export each object type separately — accounts/companies, contacts, leads, and deals/opportunities.

Then:
> Once you have the CSV files, come back. We'll map the fields next.

---

### Step 2 — Field Mapping

When they're back with their CSVs:

> Paste your column headers from the **Accounts/Companies** export here — just the header row, not the data.

Wait for them to paste headers. Then generate a mapping table:

```
Your Column Name → Salesforce Field (API Name) → Required?
─────────────────────────────────────────────────────────
Company Name    → Name                          → YES
Website         → Website                       → No
Industry        → Industry                      → No
Phone           → Phone                         → No
[etc.]
```

Flag:
- Any columns with no SFDC equivalent → "skip this one or we create a custom field"
- Any required SFDC fields missing from their export → "you'll need to add a default value"

Repeat for Contacts, Leads, and Opportunities.

---

### Step 3 — Load Sequence

After mapping is done:

> Here's the order you need to import — this is critical. Contacts reference Accounts, so Accounts must exist first.
>
> **Import in this exact order:**
> 1. Accounts
> 2. Contacts
> 3. Leads
> 4. Opportunities
> 5. Tasks / Activities (if any)
>
> Never import Contacts before Accounts or you'll get orphaned records.

---

### Step 4 — Import Method

> How many records are you importing per object (roughly)?

- **Under 50,000**: use SFDC Data Import Wizard — no code needed
  > Go to Setup → Data → Data Import Wizard → Launch Wizard. Select the object, upload your CSV, use the field mapping we just built, and import.

- **Over 50,000**: offer to write a Bulk API 2.0 script
  > That's a large dataset — I'll write a Python script that loads it via Bulk API. Do you have Python installed?

---

### Step 5 — Validation

After each object is imported:

> Let's validate the import. 

If Agentforce DX is connected — run validation queries directly:
```
SELECT COUNT(Id) FROM Account
SELECT COUNT(Id) FROM Contact WHERE AccountId = null
SELECT Email, COUNT(Id) FROM Contact GROUP BY Email HAVING COUNT(Id) > 1
```
Report results back.

If not connected — give them the queries to paste into the SFDC Developer Console.

Flag anything off. Diagnose and offer a fix before they move to the next object.

---

### Step 6 — Wrap

After all objects are validated:

> Migration complete. Here's what's now in your org:
> - Accounts: [count]
> - Contacts: [count]
> - Leads: [count]
> - Opportunities: [count]
>
> Frontspin and TitanX read from SFDC natively — they'll pick up the new records automatically.
>
> Ready to build your reports? Run `/sfdc-reports` and tell me what you want to track.
