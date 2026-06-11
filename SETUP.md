# Setup Guide

A 10-minute walkthrough to get the ESS Insights dashboard running on your own Copilot Studio agent data.

---

## Prerequisites

- **Power BI Desktop** (free) — [download here](https://powerbi.microsoft.com/desktop/)
- **Access to your Copilot Studio agent's Dataverse environment** with read permission on the `ConversationTranscript` table
- *(Optional)* Your organization's HR roster as a CSV — for Organization / Country breakouts
- *(Optional)* The `ProductFeedback` Dataverse table — for thumbs and CSAT
- *(Optional)* Copilot Studio Message Consumption export — for credit-consumption metrics

---

## Step 1 — Export your conversation transcripts

This is the **only required** file. The transcripts live in a Dataverse table called `ConversationTranscript` that Copilot Studio writes to automatically. You export them through the Power Apps maker portal.

> **Heads up — you need the right security role.** Environment Maker alone is *not* enough. You (or someone) need the **Bot Transcript Viewer** security role assigned in the environment that hosts your ESS agent. An admin can grant this via *Admin center → Environment → Settings → Users + permissions → Security roles*. See [Microsoft's docs](https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-share-bots#assign-the-bot-transcript-viewer-security-role-during-agent-sharing) for the official walkthrough.

### Steps

1. Sign in to [https://make.powerapps.com](https://make.powerapps.com/) and use the environment selector (top-right) to switch to the environment that hosts your ESS agent.
2. In the left navigation pane, select **Tables**, then **All** at the top of the table list.
3. In the **Search** box, type `conversation`.
4. Select the **ConversationTranscript** table to open it.
5. On the top menu bar, select **Export** → **Export data**.
6. Wait a few minutes while Power Apps compiles the data (you'll see a status indicator at the top).
7. When the status changes to ready, select **Download exported data**. Your browser downloads a `.zip` archive to its default downloads folder.
8. Unzip the archive. Inside you'll find the transcripts as a `.csv` file (it may have a long auto-generated name like `ConversationTranscript_2026-06-11.csv`).
9. Move/rename the CSV to a stable location you'll point Power BI at, e.g.:
   - **Windows:** `C:\Users\<you>\Documents\AgentData\ConversationTranscripts.csv`
   - **Mac:** `/Users/<you>/Documents/AgentData/ConversationTranscripts.csv`

### What you're getting

- **Default window:** the last 30 days of conversations (configurable in your environment's retention settings).
- **One row per conversation segment.** The system saves a transcript record after 30 minutes of inactivity. Very long conversations (>1 MB of activity) are split across multiple rows sharing the same `Name` and `ConversationStartTime`. The template handles this re-assembly for you.
- **Key columns**: `Content` (the full JSON activity log — message, event, trace), `ConversationStartTime`, `Metadata` (agent/tenant IDs), and `Name`. The template parses `Content` to extract messages, user identity, topics, outcomes, response times, and feedback.

### What's NOT in the export

Per Microsoft's documentation, transcripts are **not** written for:
- Microsoft Dataverse for Teams environments
- Dataverse developer environments
- Microsoft 365 Copilot agents

If your ESS agent runs in one of these environments, this template won't have data to load. Confirm your agent runs in a standard Dataverse production or sandbox environment.

> ⚠️ **CRITICAL — do NOT open this CSV in Excel before loading it into Power BI.** Excel silently corrupts the embedded JSON in the `Content` column when it re-saves the file. You'll get an `M Engine error: Token Identifier expected` when Power BI tries to parse it. If you accidentally opened and saved it, just re-download from Dataverse — don't try to repair it manually.

---

## Step 2 — Download the template

1. From this repo, click **[`ESS Dashboard Template (CSV Upload).pbit`](./ESS%20Dashboard%20Template%20(CSV%20Upload).pbit)**.
2. Click **Download raw file** (top right of the file preview).
3. Double-click the downloaded `.pbit` to open it in Power BI Desktop.

---

## Step 3 — Provide the file paths

When the template opens you'll see a parameter prompt with up to four fields:

| Parameter | Required? | Paste this |
|---|---|---|
| **Transcript File** | ✅ Yes | Full path to your ConversationTranscripts CSV from Step 1 |
| **Org Data File** | ⭐ Recommended | Full path to your HR roster CSV (see Step 4) |
| **Product Feedback File** | Optional | Full path to your ProductFeedback CSV |
| **Agent Credits File** | Optional | Full path to your Message Consumption export |

Click **Load**. Leave any optional fields blank if you don't have those files yet — the report will still open and the relevant pages will show as blank.

> **Path format tip:** Use the full absolute path. Example: `C:\Users\stephanie\Documents\AgentData\transcripts.csv`

---

## Step 4 — (Recommended) Add Org Data for richer breakouts

Without Org Data, the "Users by Organization" and "Users by Country" charts on the Adoption page will be empty. Adding a simple HR roster CSV unlocks them across every page.

**Required columns** (case-insensitive; extras are ignored):

| Column | Example | Notes |
|---|---|---|
| `UserPrincipalName` | `jane.doe@contoso.com` | Must match the UPN your agent sees |
| `Department` | `Finance` | Becomes "Organization" in the report |
| `JobTitle` | `Senior Analyst` | Optional |
| `Country` | `USA` | Optional, but unlocks the Country chart |
| `DisplayName` | `Jane Doe` | Optional |
| `Email` | `jane.doe@contoso.com` | Optional |

**Where to get it:**
- From your HR system as a CSV export, or
- From Entra ID / Microsoft Graph via a quick PowerShell export

**To load it after first open:**
1. **Home** → **Transform data** → **Edit Parameters**.
2. Paste the file path into **Org Data File**.
3. Click **OK** → **Close & Apply**.

---

## Step 5 — Validation

Before sharing the report, sanity-check these on the **Adoption** page:

- ✅ **Total Conversations** is non-zero and roughly matches the row count of your transcripts CSV.
- ✅ **Total Users** is plausible (not 1, not equal to Total Conversations — somewhere in between).
- ✅ The **Conversations & Users by Week** line chart shows a sensible date range matching your export window.
- ✅ If you loaded Org Data: **Users by Organization** and **Users by Country** show real labels (not just `(Blank)`).

Then check the **Metric Glossary** page (📖) — every metric on every page is defined there. Use it to answer "where does this number come from?" questions before they get asked.

---

## Step 6 — Refresh & publish

- **Manual refresh:** **Home** → **Refresh** after dropping a fresh CSV in the same path.
- **Publish to Power BI Service:** **Home** → **Publish** → pick a workspace. For scheduled refresh, set up a Gateway pointing at the folder containing your CSVs.
- **Export to PDF:** **File** → **Export** → **Export to PDF** for monthly recap decks.

---

## Common issues

### "M Engine error: Token Identifier expected"
You (or someone) opened the transcripts CSV in Excel before loading. Excel re-saves the file with broken JSON escaping. Re-export from Dataverse and load directly.

### Power BI hangs or runs out of memory on load
You may have an extremely large transcript file (>500 MB). Apply a date filter at the Dataverse export step to narrow the window before downloading.

### "Users by Organization" shows everyone as `(Blank)`
The Org Data file's UPN column doesn't match the UPNs in your transcripts. Check:
- The Org Data column is named `UserPrincipalName` (or `UPN` or `PersonId`).
- The values are full UPNs (`user@contoso.com`), not short usernames.
- Email casing matches — the model normalizes to lowercase, but typos won't be caught.

### Credit Consumption page is blank
You didn't load the Agent Credits file. This file comes from Copilot Studio's **Analytics → Message Consumption** export, not from Dataverse.

### A specific page errors out with "Something's wrong with one or more fields"
You're likely on an older version of the template. Download the latest `.pbit` from this repo — recent fixes make every visual resilient to missing optional data.

---

## Need more help?

- Open an issue: https://github.com/downeysteph/ESS-Insights/issues
- Check the **Load Diagnostics** page (hidden in the page selector) for row counts and parser warnings.
