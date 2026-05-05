---
name: sfdc-reports
description: Build a Salesforce report or dashboard by describing what you want in plain English. If Agentforce DX is connected, Claude deploys it directly. If not, Claude gives you the exact build spec for SFDC Report Builder.
version: 1.0.0
last_change: "v1.0.0: Initial release — conversational report builder"
---

## Task

Open by asking:

> What do you want to measure or track in Salesforce?
>
> Describe it in plain English — don't worry about report types or field names. Examples:
> - "I want to see my open deals by stage for this quarter"
> - "Show me which leads came in this week and where they're from"
> - "I need to know which reps logged the most calls this month"
>
> What do you want to see?

Wait for their answer.

## Steps

### Step 1 — Clarify Intent

Based on their description, ask one clarifying question if needed:

- Time range (this week, this quarter, this year, all time)?
- Filtered to their records only, or the whole team?
- Any specific fields they definitely want to see?

Don't ask more than one question. If the intent is clear, skip this step entirely.

---

### Step 2 — Check Connection Mode

Silently determine: is Agentforce DX connected in this session?

Test by attempting a simple org query. If it works — **deploy mode**. If not — **spec mode**.

---

### Step 3a — Deploy Mode (Agentforce DX connected)

Tell the user:

> Great — I'm connected to your org. I'll build and deploy this directly.
>
> I'm going to:
> 1. Check your org's available fields for this object
> 2. Generate the report definition
> 3. Deploy it to your sandbox
>
> Give me a moment.

Execute:
1. `sobject-describe` on the relevant object to confirm field names
2. Generate the Report metadata XML based on their request
3. `metadata-deploy` to sandbox org
4. Confirm it's live

Then:
> Done — your report is live in your sandbox. Go to Reports in SFDC and look for **[report name]**.
>
> Check it and let me know if you want to adjust anything — different columns, different filters, different time range. Or if it looks good, I can deploy the same thing to production.

---

### Step 3b — Spec Mode (no Agentforce DX connection)

Tell the user:

> Here's exactly how to build this in SFDC Report Builder:

Output a clean build spec:

```
Report Type: [e.g. Opportunities]
Report Name: [suggested name]

Columns:
- [Field 1]
- [Field 2]
- [Field 3]

Group By: [field]

Filters:
- [Filter 1: field / operator / value]
- [Filter 2: field / operator / value]

Sort: [field] [ascending/descending]
```

Then:
> Take this to SFDC → Reports → New Report. Follow this spec and it'll be built in about 5 minutes.
>
> Want to build another one? Tell me the next thing you want to track.

---

### Step 4 — Dashboard Offer

After one or more reports are built, offer:

> Want me to pull these into a dashboard? Tell me which reports you want on it and I'll either build the dashboard spec or deploy it directly — same as the reports.
