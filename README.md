# ESS Insights — Copilot Studio Agent Analytics

> **Understand, track, and improve outcomes for any Microsoft Copilot Studio agent — using only the data your agent already produces.**

A drop-in Power BI template that turns raw Copilot Studio conversation transcripts (and a few optional companion exports) into a 7-page executive dashboard covering adoption, outcomes, time-to-knowledge, business impact, and user feedback.

![ESS Insights — Organization Adoption page](images/01-organization-adoption.png)

---

## Why use this template

Copilot Studio gives you transcripts. It does **not** give you the story those transcripts tell. This template answers the questions a stakeholder will actually ask the day after launch:

- **Adoption** — How many users? How many conversations? Are they coming back?
- **Outcomes** — What % of conversations resolve vs. escalate vs. get abandoned?
- **Time to Knowledge** — How fast does the agent answer? How many turns does it take?
- **Business Impact** — How many support tickets did we deflect? What's the dollar value?
- **Feedback** — Where are users happy, and where are they frustrated?
- **Topic-level diagnosis** — Which intents are working and which need authoring help?

It works for **any Copilot Studio agent** — HR, IT, ESS, sales enablement, custom — not just Microsoft Employee Self-Serve (ESS).

---

## What you get

| Page | What it answers |
|---|---|
| 📈 **Organization Adoption** | Volume, distinct users, repeat-usage rate, DAU/WAU/MAU, breakdown by Org & Country |
| 🎯 **Conversation Outcomes** | Resolution / escalation / abandonment trend, topic outcomes, top deflected topics |
| ⏱ **Time to Knowledge** | Avg duration, response time, turns to resolve, abandonment & unengaged rate |
| 💼 **Business Impact** | Tickets deflected, hours saved, $ saved, credit-consumption leaderboard |
| 👍 **Agent Feedback** | In-conversation thumbs, CSAT, verbatim comments, satisfaction trend |
| 💬 **Conversation Details** | Per-topic drill-through with full transcripts and a first-message word cloud |
| 📖 **Metric Glossary** | Every metric defined, calculated, and sourced — no black boxes |

<details>
<summary>📸 Preview all 7 pages</summary>

![Conversation Outcomes](images/04-conversation-outcomes.png)
![Time to Knowledge](images/02-time-to-knowledge.png)
![Business Impact](images/03-business-impact.png)
![Agent Feedback](images/05-agent-feedback.png)
![Conversation Details](images/06-conversation-details.png)
![Metric Glossary](images/07-metric-glossary.png)

</details>

---

## Quick start

1. **Download** [`ESS Dashboard Template.pbit`](./ESS%20Dashboard%20Template.pbit) from this repo.
2. **Export your conversation transcripts** from Dataverse (the only required file).
3. **Open the .pbit** in Power BI Desktop. When prompted, paste the full path to your transcripts CSV and click **Load**.
4. **Done.** All 7 pages populate from that one file. Optional companion files unlock breakouts (see below).

📘 **Full step-by-step instructions:** [SETUP.md](./SETUP.md)

---

## Data inputs

| File | Required? | Unlocks |
|---|---|---|
| **Conversation Transcripts** (Dataverse export) | ✅ Required | All adoption, outcomes, time-to-knowledge, and conversation-detail metrics |
| **Org Data** (HR roster CSV: UPN, Department, JobTitle, Country) | ⭐ Recommended | "Users by Organization" and "Users by Country" breakouts on every page |
| **Product Feedback** (Dataverse export) | Optional | Thumbs up/down, CSAT, verbatim comments page |
| **Agent Credits** (Copilot Studio usage export) | Optional | Credit consumption leaderboard and Business Impact page |

> The template **will load and render every page without errors** even if you provide only the required transcripts file. Optional pages and breakouts will show blank where data is missing — by design, so you can start with the minimum and add more later.

---

## Validation & troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| "M Engine error: Token Identifier expected" on open | CSV opened in Excel before loading; Excel corrupted the JSON columns | Re-export from Dataverse, do **not** open in Excel, load directly into Power BI |
| Total Users count looks wrong | Same user appears under both UPN and Entra Object ID in your transcripts | This is handled automatically — both identities are cross-walked in the model |
| "Users by Organization" chart shows only (Blank) | Org Data file not loaded, or UPN format doesn't match | Set the **Org Data File** parameter (Transform data → Edit Parameters); ensure the column is named `UserPrincipalName` |
| "Users by Country" chart shows "Something's wrong with one or more fields" | Your Org Data CSV is missing the Country column | Add a `Country` column to your Org Data file (it can be empty), or re-download the latest template |
| Repeat-usage rate is 0% | Period is too short (everyone is a first-time user) | Widen the date filter, or wait for more data |

Full validation checklist: [SETUP.md § Validation](./SETUP.md#step-5--validation)

---

## Customize for your agent

The template ships branded as "ESS Agent" but works for any Copilot Studio agent. To re-brand:

1. Open the **Adoption** page → click the title text box → replace "ESS Agent" with your agent name.
2. (Optional) Open the **Metric Glossary** page → update the "About this report" callout.
3. Save as a new `.pbit` and re-distribute to your stakeholders.

No semantic-model edits required.

---

## Distribute to your stakeholders

- **Publish to Power BI Service** for a live, refreshable workspace report.
- **Export to PDF** for monthly executive updates.
- **Pin individual visuals** to a Teams dashboard for daily monitoring.

---

## Storytelling tips

When presenting these numbers to a non-technical audience:

- **Lead with Business Impact, not Adoption.** Hours saved and tickets deflected resonate more than DAU.
- **Pair Outcomes with Topic Outcomes.** A 79% engagement rate means nothing without knowing *which* topics drove it.
- **Use Verbatim Feedback as proof.** Two quotes from real users beats a 4.1/5 CSAT score every time.
- **Show the trend, not the snapshot.** The weekly trend visuals are your story arc — start there.

---

## Contributing & feedback

Found a bug? Have a feature request? [Open an issue](https://github.com/downeysteph/ESS-Insights/issues) — feedback from real customers makes this template better for everyone.

---

## License

[MIT](./LICENSE) — use it, modify it, ship it, share it.
