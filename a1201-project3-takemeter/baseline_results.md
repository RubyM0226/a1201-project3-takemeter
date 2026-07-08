# Milestone 4 — Baseline Results 

## Setup

- **Model:** Groq zero-shot LLM classification (no fine-tuning)
- **Prompt:** label definitions and one example per label, copied directly from `planning.md`, plus the tie-break rule (specific/checkable musical claim → Critique) explicitly stated in the prompt
- **Test set:** 32 held-out examples (15% split), stratified to match overall dataset label proportions
- **Parseable responses:** 32/32 (100%) — an earlier run returned 0/32 parseable due to a parsing-logic mismatch, resolved before this run

## Overall accuracy

**0.781** (25/32 correct)

## Per-class metrics

| Label | Precision | Recall | F1-score | Support |
|---|---|---|---|---|
| Critique | 0.83 | 0.62 | 0.71 | 8 |
| Reaction | 0.55 | 0.86 | 0.67 | 7 |
| Personal Anecdote | 0.92 | 0.92 | 0.92 | 12 |
| Discussion/Question | 1.00 | 0.60 | 0.75 | 5 |
| **Accuracy** | | | **0.78** | 32 |
| **Macro avg** | 0.82 | 0.75 | 0.76 | 32 |
| **Weighted avg** | 0.83 | 0.78 | 0.79 | 32 |

## Reflection: where did the baseline struggle?

**Personal Anecdote is the strongest label by far (0.92 precision / 0.92 recall).** This is because anecdotes carry the most distinctive surface-level signal and are easier to recognize for the model which a zero-shot model can recognize without needing to apply any of the more subtle tie-break logic from `planning.md`.

**Reaction has the weakest precision (0.55) despite high recall (0.86).** The model is over-predicting Reaction, effectively using it as a default/catch-all. My hypothesis: this is the flip side of Critique's moderate recall (0.62). The Critique label requires recognizing a *specific, checkable* musical/production/lyrical reference embedded inside what often reads, on the surface, like an opinion or hype statement. A zero-shot model with no training on this specific boundary is more likely to key off surface tone.

**Discussion/Question has perfect precision (1.00) but the lowest recall (0.60).** When the model predicts this label, it's always correct. Unfortunately, it's missing real instances of it. This lines up with edge case #4 documented in `planning.md`: a long personal narrative that closes with an explicit question should be coded as Discussion/Question but a zero-shot model likely fixates on the anecdote.


## Hypothesis to test after fine-tuning

If fine-tuning is working correctly, I'd expect the fine-tuned model to close the gap between Critique's precision (0.83) and recall (0.62) by getting better at detecting embedded specific claims regardless of surrounding tone because that's a pattern the model can learn directly from labeled examples, rather than needing to apply a rule described only in a prompt. I'd also expect Discussion/Question's recall to improve for the same reason, since the "narrative-ending-in-a-question" pattern appears multiple times in the training set.

## Note on notebook execution order

The notebook's later cells ran during this same session ahead of when this milestone's checkpoint intends for them to be examined. For the record, that run produced 0.344 accuracy with prediction confidences clustered near chance level (~0.27-0.29) across all classes. This will be investigated properly in the fine-tuning milestone. The baseline reflection above was formed independently of those results.