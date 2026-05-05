# Customer Feedback Form

The Google Form that triggers the workflow.

## Title

**Customer Feedback Form**

## Questions

### 1. How satisfied are you with our product or service?

- **Type:** Multiple choice (single select)
- **Required:** Yes
- **Options:**
  - Very satisfied
  - Satisfied
  - Neutral
  - Unsatisfied
  - Very unsatisfied

### 2. Please describe your experience or share any feedback in your own words.

- **Type:** Long answer text (paragraph)
- **Required:** Yes (recommended)

## Settings

- **Collect email addresses:** Off (turn on if you want to attribute feedback to known users; the schema and workflow will need a `Respondent Email` column added)
- **Limit to 1 response:** Off
- **Edit after submit:** Off
- **Response receipts:** Off

## Linking to Sheets

In the form's **Responses** tab, click the green Sheets icon and choose **Create a new spreadsheet**. Google will name it `<Form Title> (Responses)` by default. The Make scenario expects the worksheet tab to be named `Form Responses 1`, which is the default Google assigns.

## Notes for the workflow

- Each question's `questionId` is referenced inside the Make blueprint as a key like `7721141b`. When you import the blueprint into your own workspace and rebind the form, Make will resolve the question IDs from your form automatically — you do **not** need to edit the JSON by hand.
- If you reword either question after deploying, re-test the scenario: the Sheets `Search Rows` step matches on the free-text answer column, so a wording change will not break it, but if you change the *order* of columns in the sheet, the Update Row mapping must be updated.
