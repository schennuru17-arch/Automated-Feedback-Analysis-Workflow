# Automated Feedback Analysis Workflow

An end-to-end no-code automation that collects customer feedback through a Google Form, classifies sentiment and generates summaries with Google Gemini AI, and writes structured results back to Google Sheets — without any manual intervention.

Built with **Make.com**, **Google Forms**, **Google Gemini 2.5 Flash**, and **Google Sheets**.

> Capstone-style project from the TripleTen AI Automation program.

---

## Why this project

Customer feedback is high-signal but expensive to triage by hand: someone reads each response, decides whether it's positive or negative, and writes a one-line summary so the team can act on it. This workflow replaces that loop with an event-driven pipeline that runs the moment a form is submitted, so insights land in a structured sheet ready for review or downstream reporting.

---

## Architecture

```
Customer  ──▶  Google Form  ──▶  Make scenario  ──▶  Gemini AI  ──▶  JSON Parse  ──▶  Google Sheets
                                  (Watch Responses)   (Sentiment +    (Validate +    (Search row +
                                                       Summary)        extract)       Update row)
```

| Stage | Module | Purpose |
|---|---|---|
| 1 | `google-forms:watchResponses` | Polls the form for new submissions |
| 2 | `gemini-ai:createACompletionGeminiPro` | Classifies sentiment and writes a one-sentence summary, returning a strict JSON object |
| 3 | `json:ParseJSON` | Validates and parses the model output into typed fields |
| 4 | `google-sheets:filterRows` | Locates the original response row in the sheet by free-text answer |
| 5 | `google-sheets:updateRow` | Writes `Sentiment` and `Summary` back to that row |

The full flowchart (including the planned negative-sentiment Gmail alert branch) is in [`presentation.pdf`](presentation.pdf).

---

## Files in this repo

| File | Purpose |
|---|---|
| [`README.md`](README.md) | This file |
| [`make-blueprint.json`](make-blueprint.json) | Importable Make scenario (sanitized — placeholders replace personal IDs) |
| [`setup.md`](setup.md) | Step-by-step deployment guide |
| [`gemini-sentiment-prompt.md`](gemini-sentiment-prompt.md) | The prompt and output contract sent to Gemini |
| [`customer-feedback-form.md`](customer-feedback-form.md) | Google Form fields and structure |
| [`google-sheets-schema.md`](google-sheets-schema.md) | Column schema for the responses sheet |
| [`presentation.pdf`](presentation.pdf) | Project deck |
| [`LICENSE`](LICENSE) | MIT |

---

## Quick start

1. **Clone this repo** and open `make-blueprint.json`.
2. **Create the Google Form** described in [`customer-feedback-form.md`](customer-feedback-form.md). Note its Form ID.
3. **Link a responses sheet** and add the columns from [`google-sheets-schema.md`](google-sheets-schema.md).
4. **Import the blueprint** into Make.com (Scenarios → Create new → Import Blueprint).
5. **Replace the placeholders** (`YOUR_CONNECTION_ID`, `YOUR_GOOGLE_FORM_ID`, `YOUR_GOOGLE_SHEET_ID`, `YOUR_GOOGLE_DRIVE_FOLDER_ID`) by reconnecting your Google account and selecting your form / sheet from the dropdowns.
6. **Run once manually** to verify, then turn the scenario on.

Detailed walkthrough: [`setup.md`](setup.md).

---

## What the AI sees and returns

The Gemini call uses a tightly-scoped prompt with a fixed output contract — see [`gemini-sentiment-prompt.md`](gemini-sentiment-prompt.md). The model returns:

```json
{
  "sentiment": "Positive | Neutral | Negative",
  "summary": "One-sentence summary of the feedback."
}
```

Anything else fails the JSON parse step and is caught before it reaches the sheet — that's the design choice that makes this workflow safe to leave running unattended.

---

## Sample results

Real submissions processed through the live scenario:

| Satisfaction | Free-text feedback | Sentiment | Summary |
|---|---|---|---|
| Very satisfied | "Overall, I had a good experience. The service met my needs…" | Positive | The user was very satisfied with the experience. |
| Unsatisfied | "Not as expected" | Negative | The user is unsatisfied and the product did not meet expectations. |
| Satisfied | "Had a hands-on experience" | Positive | The user is satisfied with the hands-on experience. |

---

## Roadmap

Implemented in this version:

- Form-triggered ingestion
- Gemini-based sentiment + summary
- Strict JSON contract with parse-time validation
- Row-level write-back to Google Sheets

Planned next:

- Conditional Gmail alert when sentiment is `Negative` (branch shown in the architecture diagram)
- Slack notification fan-out
- Weekly / monthly trend reporting in a dashboard tab
- Retry + dead-letter handling for transient API errors

---

## Tech

- [Make.com](https://www.make.com/) — orchestration
- [Google Forms](https://forms.google.com/) — capture
- [Google Gemini 2.5 Flash](https://ai.google.dev/) — sentiment + summary
- [Google Sheets](https://sheets.google.com/) — structured output

---

## License

MIT — see [`LICENSE`](LICENSE).

## Author

**Swetha Chennuru** · [github.com/schennuru17-arch](https://github.com/schennuru17-arch)
