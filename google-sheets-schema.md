# Google Sheets Schema

The destination spreadsheet linked to the Customer Feedback Form.

## Worksheet

- **Tab name:** `Form Responses 1` *(default — do not rename)*
- **Header row:** Row 1

## Columns

| Col | Header | Source | Type | Notes |
|---|---|---|---|---|
| A | Timestamp | Google Forms | Datetime | Auto-populated on submission |
| B | How satisfied are you with our product or service? | Google Forms | String | One of the 5 satisfaction levels |
| C | Please describe your experience or share any feedback in your own words. | Google Forms | Long string | Free-text feedback |
| D | Sentiment | Make → Gemini | Enum | `Positive` / `Neutral` / `Negative` |
| E | Summary | Make → Gemini | String | One-sentence summary |

Columns A–C are written by Google Forms automatically. Columns D and E are written by the Make scenario's `Update a Row` module after the Gemini response is parsed.

## Sample rows

| Timestamp | Satisfaction | Feedback | Sentiment | Summary |
|---|---|---|---|---|
| 1/24/2026 13:18:20 | Unsatisfied | Not as expected | Negative | The user is unsatisfied and the product did not meet expectations. |
| 1/24/2026 13:21:36 | Satisfied | Had a hands-on experience | Positive | The user is satisfied with the hands-on experience. |
| 1/25/2026 15:18:49 | Very satisfied | Overall, I had a good experience. The service met my needs… | Positive | The user was very satisfied with the experience. |

## Optional extensions

- **Column F – Respondent Email** (if you enable email collection on the form)
- **Column G – Confidence** (if you extend the Gemini prompt to return a confidence score)
- **Column H – Alert Sent** (boolean flag set when a Gmail/Slack alert fires for negative sentiment)

If you add columns, update the **Update a Row** module's value mapping in the Make blueprint to include the new column letters.
