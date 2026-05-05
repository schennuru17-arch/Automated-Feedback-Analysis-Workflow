# Setup Guide

End-to-end steps to deploy this scenario in your own Make.com workspace.

## Prerequisites

- Make.com account (Free tier works for low submission volumes)
- Google account with access to Forms, Sheets, and Drive
- Google AI Studio account with a Gemini API key (or a Make Google Gemini connection)

## 1. Create the Google Form

Build a form with two questions matching `form/customer-feedback-form.md`:

1. **How satisfied are you with our product or service?** — Multiple choice (Very satisfied / Satisfied / Neutral / Unsatisfied / Very unsatisfied)
2. **Please describe your experience or share any feedback in your own words.** — Long answer text

In the form's **Responses** tab, click the Sheets icon to link it to a new spreadsheet. Copy the form's URL — the long string between `/d/` and `/edit` is your **Form ID**.

## 2. Prepare the response sheet

Open the auto-created responses sheet. Add two columns to the right of the existing ones:

| A | B | C | D | E |
|---|---|---|---|---|
| Timestamp | How satisfied… | Please describe… | **Sentiment** | **Summary** |

Copy the spreadsheet URL — the long string between `/d/` and `/edit` is your **Sheet ID**.

## 3. Set up the Make connections

In Make.com, go to **Connections** and create three Google connections (these can use the same Google account):

- One for **Google Forms** (Watch Responses)
- One for **Google Gemini AI** (Generate completion)
- One for **Google Sheets** (Filter Rows + Update Row)

For Gemini, you'll need a Google Cloud project with the Gemini API enabled, or you can use Make's hosted Gemini connector with an API key from [Google AI Studio](https://aistudio.google.com/).

## 4. Import the blueprint

1. Open Make → **Scenarios** → **Create a new scenario**
2. Click the three-dot menu → **Import Blueprint**
3. Upload `blueprint/make-blueprint.json`
4. Open each module and re-bind:
   - **Google Forms — Watch Responses**: connection + select your form
   - **Google Gemini AI**: connection + confirm `gemini-2.5-flash` model
   - **JSON — Parse JSON**: leave as-is (it parses module 3's output)
   - **Google Sheets — Search Rows**: connection + select your spreadsheet and `Form Responses 1` sheet
   - **Google Sheets — Update a Row**: connection + same spreadsheet/sheet

The placeholders (`YOUR_CONNECTION_ID`, `YOUR_GOOGLE_FORM_ID`, `YOUR_GOOGLE_SHEET_ID`, `YOUR_GOOGLE_DRIVE_FOLDER_ID`) get auto-replaced when you select the resources in each module's dropdowns.

## 5. Test

1. Submit a test response through your Google Form
2. In Make, click **Run once** on the scenario
3. Check that the linked sheet has `Sentiment` and `Summary` filled in for that row
4. Check the execution log if anything failed — usually it's a connection scope or a sheet permission

## 6. Schedule

Once the manual run works:

- Toggle the scenario **ON** (top-left of the editor)
- Set the schedule on the Forms trigger (every 15 min is a reasonable default for a Free plan)

## Common issues

| Symptom | Fix |
|---|---|
| `Invalid JSON` at parse step | Gemini sometimes wraps output in markdown fences. Tighten the prompt or add a Tools → Set Variable step to strip ` ```json ` before parsing. |
| Search Rows finds nothing | The free-text answer in the sheet must exactly match what Gemini received. If you change the form question wording, update the filter. |
| Update Row writes to the wrong row | The row number is taken from the Search Rows result; confirm only one row matches. Use the timestamp as a secondary filter if duplicates are possible. |
| Hitting Make operations limit | Reduce trigger frequency, or upgrade to a paid plan. |
