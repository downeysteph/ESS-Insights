# ESS Insights — Employee Self-Service Agent Analytics

> **Measure the real-world impact of your Microsoft Employee Self-Service (ESS) Copilot Studio agent — adoption, outcomes, deflection, and user feedback — using only the data your agent already produces.**

A drop-in Power BI template purpose-built for the **Microsoft ESS agent**, with a 7-page executive dashboard that answers the questions HR, IT, and the executive sponsor will actually ask after launch.

> 💡 Built for ESS, but works for **any Copilot Studio agent** — the same template will load and analyze transcripts from any agent (HR, IT, sales enablement, custom). See [Customize for your agent](#customize-for-your-agent).

![ESS Insights — animated preview of all 7 dashboard pages](images/dashboard-preview.gif)

---

## Why use this template for your ESS agent

The Microsoft ESS agent gives your employees a single, conversational front door to HR, IT, payroll, benefits, and travel self-service. But the platform gives you *nested data*, not insights. This template answers the questions an ESS program owner needs to answer every month:

- **Are employees adopting it?** Distinct users, repeat usage, DAU/WAU/MAU trend
- **Is it actually resolving their requests?** Resolution vs. escalation vs. abandonment rates, by topic
- **How fast does it answer?** Avg duration, response time, turns to resolve
- **What is it saving the business?** Tickets deflected, hours saved, dollar value, credit cost
- **Are employees happy with it?** In-conversation thumbs, CSAT, verbatim comments
- **Which intents need authoring help?** Per-topic deflection, abandonment, and outcomes

All seven pages light up from a single Power Platform export. Add optional companion files to unlock organization/country breakouts, satisfaction scores, and credit cost analysis.

---

## What you get

| # | Page | What it answers |
|---|---|---|
| 1 | **Conversation Outcomes** | Resolution / escalation / abandonment trend, topic outcomes, top deflected topics |
| 2 | **Time to Knowledge** | Avg duration, response time, turns to resolve, abandonment & unengaged rate |
| 3 | **Organization Adoption** | Volume, distinct users, repeat-usage rate, DAU/WAU/MAU, breakdown by Org & Country |
| 4 | **Conversation Details** | Per-topic drill-through with full transcripts and a first-message word cloud |
| 5 | **Agent Feedback** | In-conversation thumbs, CSAT, verbatim comments, satisfaction trend |
| 6 | **Business Impact** | Tickets deflected, hours saved, $ saved, credit-consumption leaderboard |
| 7 | **Metric Glossary** | Every metric defined, calculated, and sourced — no black boxes |

---

## Quick start

1. **Download** [`ESS Dashboard Template (CSV Upload).pbit`](./ESS%20Dashboard%20Template%20(CSV%20Upload).pbit) from this repo.
2. **Export your conversation transcripts** from the Dataverse environment that hosts your ESS agent (the only required file).
3. **Open the .pbit** in Power BI Desktop. When prompted, paste the full path to your transcripts CSV and click **Load**.
4. **Done.** All 7 pages populate from that one file. Optional companion files unlock breakouts (see below).

<details>
<summary><h3 style="display:inline">📘 Written Setup Guide (click to expand)</h3></summary>

Get the **ESS Insights dashboard** running on your own Copilot Studio agent data in about **10 minutes**.

---

## Before you start

✅ **Power BI Desktop** installed — [download free](https://powerbi.microsoft.com/desktop/)
✅ **Bot Transcript Viewer** security role on the Dataverse environment that hosts your ESS agent — an admin must grant this. [Microsoft's how-to](https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-share-bots#assign-the-bot-transcript-viewer-security-role-during-agent-sharing)
✅ A folder you'll use to store the CSVs (e.g. `Documents/AgentData`)

> ⚠️ **Environment Maker is NOT enough.** Without the Bot Transcript Viewer role, you won't see the ConversationTranscript table in Step 1.

---

## Step 1 — Export your conversation transcripts ✅ Required

**Outcome:** a CSV file containing the last 30 days of agent conversations.

1. **Sign in to Power Apps**
   - Go to [https://make.powerapps.com](https://make.powerapps.com/)
   - Use the environment selector (top-right) to switch to the environment that hosts your **ESS agent**

2. **Open the ConversationTranscript table**
   - In the left sidebar, select **Tables**
   - Click **All** at the top of the table list
   - In the search box, type `conversation`
   - Click the **ConversationTranscript** table to open it

3. **Export the data**
   - In the top menu bar, select **Export** → **Export data**
   - Wait a few minutes for the export to compile (status banner at the top)

4. **Download the file**
   - When the status shows ready, click **Download exported data**
   - A `.zip` archive downloads to your browser's default downloads folder

5. **Unzip and rename**
   - Unzip the archive — inside you'll find a CSV with an auto-generated name like `ConversationTranscript_2026-06-11.csv`
   - Move it to your data folder and rename it to something simple:
     - **Windows:** `C:\Users\<you>\Documents\AgentData\ConversationTranscripts.csv`
     - **Mac:** `/Users/<you>/Documents/AgentData/ConversationTranscripts.csv`

> ⚠️ **Do NOT open this CSV in Excel.** Excel will silently corrupt the JSON in the `Content` column and you'll get an `M Engine error: Token Identifier expected` on load. If you accidentally opened and saved it, re-download from Dataverse — don't try to repair it.

> 💡 **Default window is last 30 days.** Want more? Your admin can change the retention period in the environment settings before you export.

❌ **Transcripts aren't written for these environments:** Dataverse for Teams, Dataverse developer environments, or Microsoft 365 Copilot agents. Confirm your ESS agent runs in a standard Dataverse production/sandbox environment, otherwise the export will be empty.

---

## Step 2 — (Recommended) Export your Org Data ⭐

**Outcome:** a CSV that maps each user's UPN to their Department, Country, and JobTitle. Unlocks "Users by Organization" and "Users by Country" charts on every page.

**Required column** (only one):

| Column | Example | Required? |
|---|---|---|
| `UserPrincipalName` | `jane.doe@contoso.com` | ✅ Yes — must match your agent's UPNs |
| `Department` | `Finance` | ⭐ Recommended — becomes "Organization" |
| `Country` | `USA` | ⭐ Recommended — unlocks Country chart |
| `JobTitle` | `Senior Analyst` | Optional |
| `DisplayName` | `Jane Doe` | Optional |
| `Email` | `jane.doe@contoso.com` | Optional |

**Where to get it** (pick one):

- 🅰️ **From HR** — most HR systems can export a roster CSV with the columns above
- 🅱️ **From Entra ID** — a 30-second PowerShell command:
  ```powershell
  Connect-MgGraph -Scopes "User.Read.All"
  Get-MgUser -All -Property UserPrincipalName,Department,JobTitle,Country,DisplayName,Mail |
    Select-Object UserPrincipalName,Department,JobTitle,Country,DisplayName,@{N='Email';E={$_.Mail}} |
    Export-Csv -Path "C:\Users\<you>\Documents\AgentData\OrgData.csv" -NoTypeInformation
  ```
- 🅲 **Skip for now** — the template still loads. You can add Org Data later via *Transform data → Edit Parameters*.

---

## Step 3 — (Optional) Export Product Feedback & Agent Credits

**Outcome:** unlocks the Agent Feedback and Business Impact pages.

| File | Source | Unlocks |
|---|---|---|
| **Product Feedback** | Power Apps → Tables → `ProductFeedback` → Export → Export data (same flow as Step 1) | Thumbs up/down, CSAT scores, verbatim comments page |
| **Agent Credits** | Copilot Studio → **Analytics → Message Consumption** → Export | Credit-consumption leaderboard, Business Impact metrics |

Save each as a CSV in your data folder. You can skip both and add them later.

---

## Step 4 — Download & open the template

1. **Download the .pbit**
   - In this repo, click **[`ESS Dashboard Template (CSV Upload).pbit`](./ESS%20Dashboard%20Template%20(CSV%20Upload).pbit)**
   - Click **Download raw file** (top-right of the file preview)

2. **Open it**
   - Double-click the downloaded `.pbit` — it opens in Power BI Desktop and shows a parameter prompt

---

## Step 5 — Provide the file paths

In the parameter prompt, paste the **full absolute path** to each CSV from Steps 1–3:

| Parameter | Required? | Example value |
|---|---|---|
| **Transcript File** | ✅ Yes | `/Users/<you>/Documents/AgentData/ConversationTranscripts.csv` |
| **Org Data File** | ⭐ Recommended | `/Users/<you>/Documents/AgentData/OrgData.csv` |
| **Product Feedback File** | Optional — leave blank | `…/ProductFeedback.csv` |
| **Agent Credits File** | Optional — leave blank | `…/AgentCredits.csv` |

Click **Load**.

> 💡 **Leaving an optional field blank is fine.** The template loads cleanly and the relevant pages just stay empty until you add the data.

> ⚠️ **Use forward slashes on Mac, backslashes on Windows.** Wrap paths in nothing — just paste the raw path.

---

## Step 6 — Validate before sharing

When the model finishes loading, go to the **Organization Adoption** page and sanity-check:

- [ ] **Total Conversations** is non-zero and roughly matches the row count of your transcripts CSV
- [ ] **Total Users** is plausible — not `1`, not equal to Total Conversations (somewhere in between)
- [ ] **Conversations & Users by Week** line chart shows a sensible date range matching your export window
- [ ] If you loaded Org Data: **Users by Organization** and **Users by Country** show real labels, not just `(Blank)`

Then check the **Metric Glossary** page (📖) — every metric on every page is defined there. Use it to answer "where does this number come from?" before stakeholders ask.

---

## Step 7 — Refresh, publish, share

| Goal | How |
|---|---|
| **Refresh after new export** | Drop the fresh CSV at the same path → **Home → Refresh** |
| **Publish to Power BI Service** | **Home → Publish** → pick workspace. Schedule refresh needs a Gateway pointing at your CSV folder |
| **Export to PDF for monthly recap** | **File → Export → Export to PDF** |
| **Re-brand for a different agent** | Edit the "ESS Agent" title text on each page header → save as new `.pbit` |

---

## Quick reference card

Save this as a sticky note:

| Step | What | Where | Time |
|---|---|---|---|
| 1 | Export `ConversationTranscript` table | Power Apps → Tables | 5 min |
| 2 | Export HR roster (optional) | HR system or Entra/Graph | 2 min |
| 3 | Export `ProductFeedback` & Message Consumption (optional) | Power Apps + Copilot Studio | 3 min |
| 4 | Download & open `.pbit` | This repo | 1 min |
| 5 | Paste file paths into parameter prompt | Power BI Desktop | 1 min |
| 6 | Validate Total Users + Total Conversations | Adoption page | 1 min |
| 7 | Publish to workspace or export PDF | Power BI Service | 2 min |

---

## Common issues & fixes

| Symptom | Cause | Fix |
|---|---|---|
| `M Engine error: Token Identifier expected` on open | CSV opened in Excel before loading; Excel corrupted the JSON | Re-export from Dataverse, do **not** open in Excel |
| Can't find `ConversationTranscript` table in Power Apps | Missing **Bot Transcript Viewer** security role | Ask your admin to grant it — Environment Maker isn't enough |
| Empty CSV after export | Agent runs in Teams/Developer/M365 Copilot env (transcripts aren't written) | Move agent to standard Dataverse production/sandbox env |
| Power BI hangs or runs out of memory on load | Very large transcript file (>500 MB) | Apply a date filter at the Dataverse export step to narrow the window |
| `Users by Organization` shows everyone as `(Blank)` | Org Data UPN column doesn't match transcripts | Confirm column is named `UserPrincipalName` and values are full UPNs (`user@contoso.com`) |
| `Users by Country` chart shows "Something's wrong with one or more fields" | Org Data CSV missing the `Country` column | Add a `Country` column (can be empty), or download the latest `.pbit` from this repo |
| Credit Consumption page is blank | Agent Credits file not loaded | Export from Copilot Studio **Analytics → Message Consumption**, not Dataverse |
| Total Users count seems too low | Same employee shows as both UPN and Entra Object ID in transcripts | Already handled — the model cross-walks both identities automatically |
| Repeat-usage rate is 0% | Period too short — everyone is a first-time user | Widen the date filter or wait for more data |

---

## Need more help?

- 🐛 [Open an issue](https://github.com/downeysteph/ESS-Insights/issues)
- 🩺 Check the **Load Diagnostics** page (in the page selector) for row counts and parser warnings
- 📖 The **Metric Glossary** page has every measure's definition and source


</details>

> Prefer a standalone page? See [SETUP.md](./SETUP.md).

---

## Data inputs

| File | Required? | Unlocks |
|---|---|---|
| **Conversation Transcripts** (Dataverse export from your ESS environment) | ✅ Required | All adoption, outcomes, time-to-knowledge, and conversation-detail metrics |
| **Org Data** (HR roster CSV: UPN, Department, JobTitle, Country) | ⭐ Recommended | "Users by Organization" and "Users by Country" breakouts on every page |
| **Product Feedback** (Dataverse export) | Optional | Thumbs up/down, CSAT, verbatim comments page |
| **Agent Credits** (Copilot Studio usage export) | Optional | Credit consumption leaderboard and Business Impact page |

> The template **will load and render every page without errors** even if you provide only the required transcripts file. Optional pages and breakouts will show blank where data is missing — by design, so you can start with the minimum and add more later.

---

## Validation & troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| "M Engine error: Token Identifier expected" on open | CSV opened in Excel before loading; Excel corrupted the JSON columns | Re-export from Dataverse, do **not** open in Excel, load directly into Power BI |
| Total Users count looks wrong | Same employee appears under both UPN and Entra Object ID in your transcripts | This is handled automatically — both identities are cross-walked in the model |
| "Users by Organization" chart shows only (Blank) | Org Data file not loaded, or UPN format doesn't match | Set the **Org Data File** parameter (Transform data → Edit Parameters); ensure the column is named `UserPrincipalName` |
| "Users by Country" chart shows "Something's wrong with one or more fields" | Your Org Data CSV is missing the Country column | Add a `Country` column to your Org Data file (it can be empty), or re-download the latest template |
| Repeat-usage rate is 0% | Period is too short (everyone is a first-time user) | Widen the date filter, or wait for more data |

Full validation checklist: [SETUP.md § Validation](./SETUP.md#step-5--validation)

---

## Customize for your agent

While this template is purpose-built for the Microsoft ESS agent, the underlying data model works against **any Copilot Studio agent's transcripts**. To use it for a different agent (HR-only, IT helpdesk, sales enablement, custom internal agent):

1. Point the **Transcript File** parameter at that agent's Dataverse export.
2. Open the **Adoption** page → click the title text box → replace "ESS Agent" with your agent name.
3. (Optional) Open the **Metric Glossary** page → update the "About this report" callout.
4. Save as a new `.pbit` and re-distribute to your stakeholders.

No semantic-model edits required.

---

## Distribute to your stakeholders

- **Publish to Power BI Service** for a live, refreshable workspace report.
- **Export to PDF** for monthly executive updates.
- **Pin individual visuals** to a Teams dashboard for daily monitoring.

---

## Storytelling tips

When presenting these numbers to a non-technical audience:

- **Lead with Business Impact, not Adoption.** Hours saved and tickets deflected resonate more than DAU with an exec sponsor.
- **Pair Outcomes with Topic Outcomes.** A 79% engagement rate means nothing without knowing *which* topics drove it.
- **Use Verbatim Feedback as proof.** Two quotes from real employees beats a 4.1/5 CSAT score every time.
- **Show the trend, not the snapshot.** The weekly trend visuals are your story arc — start there.

---

## Contributing & feedback

Found a bug? Have a feature request? [Open an issue](https://github.com/downeysteph/ESS-Insights/issues) — feedback from real ESS customers makes this template better for everyone.

---

## License

[MIT](./LICENSE) — use it, modify it, ship it, share it.
