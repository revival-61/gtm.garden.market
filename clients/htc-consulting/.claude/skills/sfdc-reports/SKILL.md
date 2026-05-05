---
name: sfdc-reports
description: Build Salesforce reports and dashboards for Elijah at HTC Consulting. Pre-configured for his stack — SFDC + Frontspin + TitanX.io. Describe what you want to track and Claude builds it.
version: 1.0.0
last_change: "v1.0.0: HTC Consulting build — Elijah's stack and starter reports pre-loaded"
---

## Context

**Client:** Elijah — HTC Sales Consulting  
**Stack:** Salesforce + Frontspin (sales engagement, call/email logging) + TitanX.io (lead enrichment and forms)  
**Org aliases:** `htc-sandbox` (test) → `htc-prod` (production)  
**Starter dashboard:** 5 core pipeline reports (see Step 3)

---

## Task

Open with:

> What do you want to track or measure?
>
> Describe it in plain English. Or if you want to start with your core pipeline dashboard, just say "start with the basics" and I'll build your 5 starter reports right now.

Wait for their response.

## Steps

### Step 1 — Clarify (if needed)

If their request is clear — skip this. If not, ask one question:
- Time range?
- Just your records or the whole team?
- Any specific fields you need to see?

One question max.

---

### Step 2 — Check Connection Mode

Silently test org connectivity. If Agentforce DX is connected — **deploy mode**. If not — **spec mode**.

---

### Step 3 — Starter Reports (if they said "start with the basics")

Build these 5 in sequence, deploying each to `htc-sandbox`:

**Report 1 — Open Pipeline by Stage**
- Object: Opportunities
- Columns: Opportunity Name, Account Name, Amount, Close Date, Stage
- Group by: Stage
- Filter: IsClosed = false, Close Date = THIS_QUARTER
- Sort: Amount DESC

**Report 2 — Frontspin Activity This Week**
- Object: Tasks
- Columns: Subject, Type, Activity Date, Who (Contact), What (Account), Owner
- Filter: ActivityDate = THIS_WEEK
- Note: Frontspin logs calls and emails as Tasks — this validates the sync is working

**Report 3 — Forecast: Closing This Month**
- Object: Opportunities
- Columns: Opportunity Name, Account Name, Amount, Stage, Probability, Close Date
- Filter: Close Date = THIS_MONTH, IsClosed = false
- Sort: Amount DESC

**Report 4 — Leads by Source (TitanX)**
- Object: Leads
- Columns: First Name, Last Name, Company, Lead Source, Status, Created Date
- Group by: Lead Source
- Filter: CreatedDate = LAST_N_DAYS:90

**Report 5 — Win/Loss by Stage**
- Object: Opportunities
- Columns: Stage, COUNT(Id), SUM(Amount)
- Group by: Stage
- Filter: IsClosed = true, CloseDate = THIS_YEAR

After all 5 deploy:
> All 5 reports are live in your sandbox. Check them in SFDC → Reports.
>
> Want me to pull them into a single pipeline dashboard? Say yes and I'll build and deploy it.

---

### Step 4a — Deploy Mode (Agentforce DX connected)

For any custom report request:

1. `sobject-describe` to confirm field API names (especially Frontspin/TitanX custom fields)
2. Generate Report metadata
3. Deploy to `htc-sandbox`
4. Confirm live, give report name and location
5. Offer to deploy to `htc-prod` once they've reviewed it

---

### Step 4b — Spec Mode (no connection)

Output a clean build spec in this format:

```
Report Type: [object]
Report Name: [suggested name]

Columns:
- [Field label (API name)]

Group By: [field]

Filters:
- [field] [operator] [value]

Sort: [field] [direction]
```

Then:
> Take this to SFDC → Reports → New Report and follow the spec. About 5 minutes to build.
>
> When you're ready for Agentforce DX so Claude can deploy directly, run `/sfdc-setup`.

---

### Step 5 — Production Deploy

After sandbox is validated:
> Everything look right in sandbox? Say "deploy to prod" and I'll push it to `htc-prod`.
