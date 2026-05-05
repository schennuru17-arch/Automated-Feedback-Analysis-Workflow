# Gemini Sentiment Prompt

The prompt sent to `gemini-2.5-flash` in module 3 of the scenario.

## Prompt template

```
Classify the sentiment of this feedback as Positive, Neutral, or Negative.
Then summarize it in one sentence.

{{satisfaction_level}}

{{free_text_feedback}}

Return your answer in the following exact structure:

{ "sentiment": "[Positive/Neutral/Negative]", "summary": "[Your summary here]" }
```

In Make, the two `{{...}}` slots are populated from the form response:

- `{{satisfaction_level}}` ← `1.answers.<question_id>.textAnswers.answers[].value` (the multiple-choice answer)
- `{{free_text_feedback}}` ← `1.answers.<question_id>.textAnswers.answers[].value` (the long-answer text)

## Output contract

The model must return a single JSON object with exactly two fields:

```json
{
  "sentiment": "Positive",
  "summary": "The user was very satisfied with the experience and praised the support."
}
```

| Field | Type | Allowed values |
|---|---|---|
| `sentiment` | string | `Positive`, `Neutral`, `Negative` |
| `summary` | string | One sentence, plain text |

## Why this design

- **Two fields, no nesting** — keeps the JSON parse module's data structure trivial and robust.
- **Closed enum for sentiment** — makes downstream filters (e.g. routing negatives to email) reliable.
- **One-sentence summary** — fits cleanly into a single sheet cell and avoids verbose model output.
- **No system instruction needed** — the contract is short enough to live entirely in the user message.

## Tuning notes

If the model occasionally returns markdown-fenced JSON (` ```json ... ``` `), add a Make **Tools → Set Variable** step between Gemini and JSON Parse with:

```
{{ replace(replace(3.candidates[].content.parts[].text; "```json"; ""); "```"; "") }}
```

For higher-stakes deployments, consider:

- Adding a confidence rationale field and logging it for QA spot-checks
- Switching to a structured-output / JSON-mode call if Gemini supports it for the model version you're using
- Few-shot examples for edge cases (sarcasm, mixed sentiment, non-English text)
