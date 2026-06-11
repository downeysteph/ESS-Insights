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

This is the **only required** file.

1. Sign in to [Power Apps](https://make.powerapps.com/) and switch to the environment that hosts your Copilot Studio agent.
2. In the left nav, open **Tables** → search for **ConversationTranscript**.
3. Click **Export** → **Export data** → **CSV**.
4. Wait for the export job to finish, then download the resulting CSV.
5. Save it somewhere you'll remember, e.g.:
   - **Windows:** `C:\Users\<you>\Documents\AgentData\ConversationTranscripts.csv`
   - **Mac:** `/Users/<you>/Documents/AgentData/ConversationTranscripts.csv`

> ⚠️ **Do NOT open this CSV in Excel before loading it into Power BI.** Excel will silently corrupt the embedded JSON columns and you'll get an `M Engine error: Token Identifier expected` when Power BI tries to parse it. If you accidentally opened it, just re-download from Dataverse.

---

## Step 2 — Download the template

1. From this repo, click **[`ESS Dashboard Template.pbit`](./ESS%20Dashboard%20Template.pbit)**.
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
