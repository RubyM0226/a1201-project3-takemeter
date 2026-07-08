# a1201-project3-takemeter


## Community

**r/glassanimals**, chosen after reading across five different thread types to decide labels against real discourse patterns before committing to a project idea. I wanted to choose a glass animals thread because they are my favorite band and I have always loved the community I have met when bonding with other fans and concert lovers. Full reasoning in `planning.md`.

## Labels

| Label | Definition |
|---|---|
| **Critique** | Makes a specific, checkable claim about a musical, production, or lyrical element and gives some reasoning or evidence for it. |
| **Reaction** | States a preference, ranking, or emotional response with no specific supporting musical/production/lyrical reference. |
| **Personal Anecdote** | Centers on the commenter's own history, memory, or relationship with the album/band rather than evaluating the music. |
| **Discussion/Question** | An open-ended question, observation, or shared practice directed at the fan community (not the band) that doesn't stake an evaluative claim, express a clear reaction, or recount personal history. |

Full definitions, examples, and the tie-break rule for resolving overlapping cases (a specific, checkable musical claim overrides any surrounding anecdote/question/reaction framing) are in `planning.md`.

## Dataset

- **210 real comments**, manually collected from r/glassanimals across ~10 different threads (discussion threads, recommendation threads, tier-list/hot-takes threads, concert-experience threads, and discovery-story threads) and labeled with AI assistance applying the definitions and rules in `planning.md` (see AI Usage section for full disclosure).
- Two categories of content were deliberately excluded from collection: direct address to the band (AMA-style questions) and reactions to joke/novelty posts (e.g., an April Fools prank thread) — both are a different speech act than music discourse and don't map onto any of the 4 labels.
- **Label distribution:** Personal Anecdote 38.1% (80), Critique 23.8% (50), Reaction 21.9% (46), Discussion/Question 16.2% (34). No label exceeds the 70% imbalance threshold.
- Full dataset: `dataset.csv` (`text`, `label`, `notes` columns).

##  Hard edge cases

The first edge case is (that should be more difficult to pass) is when a comment that wraps a specific musical idea inside an anecdote or question frame. This is like the example: "remember when they played Gooey at that tiny venue in 2015 and the sound system blew a speaker? worth it tho, best mix I've heard live." This reads structurally like an anecdote but ends with a specific, checkable audio-quality claim. Deciphering which label comments like these will receive is much more difficult. When a comment contains examples of two different labels and at least one specific, checkable reference to a musical element, the specific claim takes priority. 

## Evaluation Metrics

- **Macro-averaged F1** as the primary metric, weighting all 4 labels equally regardless of frequency
- **Per-class precision and recall**, to identify which specific labels a model struggles with
- **Confusion matrix**, to identify directional confusion between label pairs
- **Definition of success (set in advance):** macro F1 ≥ 0.75, no single label's recall below 0.65, and 

## Results

### Baseline (Zero-Shot Groq LLM)

**Overall accuracy: 0.781**

| Label | Precision | Recall | F1-score |
|---|---|---|---|
| Critique | 0.83 | 0.62 | 0.71 |
| Reaction | 0.55 | 0.86 | 0.67 |
| Personal Anecdote | 0.92 | 0.92 | 0.92 |
| Discussion/Question | 1.00 | 0.60 | 0.75 |
| **Macro avg** | 0.82 | 0.75 | 0.76 |

The baseline nearly meets the pre-registered success criteria (macro F1 0.76 vs. target 0.75; Critique precision 0.83 vs. target 0.70).

### Fine-Tuned Model (distilbert-base-uncased)

**Overall accuracy: 0.531** (after iterating hyperparameters — see below)

| Label | Precision | Recall | F1-score |
|---|---|---|---|
| Critique | 0.45 | 0.63 | 0.53 |
| Reaction | 0.33 | 0.14 | 0.20 |
| Personal Anecdote | 0.61 | 0.92 | 0.73 |
| Discussion/Question | undefined (never predicted) | 0.00 | 0.00 |

**The fine-tuned model underperforms the zero-shot baseline by 25 percentage points of accuracy**, and fails to meet any of the three pre-registered success criteria.

### Training diagnostics: overfitting

The per-epoch training log (20 epochs, the final hyperparameter configuration used) shows a clear overfitting pattern: training loss decreased continuously to near-zero (0.041 by epoch 20), while validation loss bottomed out around epoch 10 (0.817) and then climbed steadily back up to 0.999 by epoch 20. Validation accuracy peaked at epochs 6 and 16 (0.710) but never improved beyond that, even as training loss kept shrinking.


#### Confusion matrix (fine-tuned model, final run)

| True \\ Predicted | Critique | Reaction | Personal Anecdote | Discussion/Question |
|---|---|---|---|---|
| Critique | 5 | 1 | 2 | 0 |
| Reaction | 4 | 1 | 2 | 0 |
| Personal Anecdote | 1 | 0 | 11 | 0 |
| Discussion/Question | 1 | 1 | 3 | 0 |


## Failure Analysis

Before writing this analysis, all 15 misclassified test examples were reviewed together as a set (rather than one at a time) to surface cross-cutting patterns, then each candidate pattern was manually verified against the actual text.

**Pattern A — Reaction→Critique confusion, driven by named entities without reasoning.** 4 of 6 Reaction errors (comments naming "the ZABA sound," "I Don't Wanna Talk," "Mama's Gun," "the stripped version") all name a specific song/album/version but attach no reasoning. The model appears to have learned "mentions a specific title → Critique" as a shortcut, missing the second half of the tie-break rule from `planning.md` (a claim requires *reasoning*, not just a name).

**Pattern B — Discussion/Question has no coherent learned signal at all, not a directional confusion.** Its 5 errors scatter across three different predicted labels rather than consistently landing on one — a different diagnosis than "confuses X for Y." It indicates the model never developed any decision boundary for this class, consistent with its complete collapse across all three training attempts.

**Pattern checked and discarded:** short comment length seemed like a plausible confound (two errors are very short: "HELIUM IS BAD????" and "Yeah that vibraslap is kinda funny"). Manually checking word counts across all 15 errors showed most misclassified comments are normal length — this didn't hold up under verification and isn't included as a real finding.

### Three examples analyzed in depth

**1. "ZABA is the best album and Glass Animals were my Spotify top 1 for two years because of that album. So perfect, and so underrated."**
True: Personal Anecdote | Predicted: Critique (confidence: 0.39)

- *Which labels confused?* Personal Anecdote → Critique.
- *Why is this boundary hard?* Strong evaluative language ("best," "so perfect," "underrated") surface-pattern-matches Critique, even though the actual content — a personal Spotify listening statistic — is what makes it an Anecdote per the rule documented in `planning.md` (difficult case #1).
- *Labeling or data/prompt problem?* A labeling-boundary problem, not inconsistency: the label follows the documented rule, but that rule (ranking language is incidental; the statistic is the real content) is a subtle judgment call, hard to learn from few similar examples.
- *What would fix it?* More training examples that explicitly contrast "evaluative language + personal statistic" (Anecdote) against "evaluative language + specific musical claim" (Critique).

**2. "You can't have a song that has over a billion streams and be underground... Glass Animals stopped being underground when they got signed to Virgin Records."**
True: Discussion/Question | Predicted: Personal Anecdote (confidence: 0.44)

- *Which labels confused?* Discussion/Question → Personal Anecdote.
- *Why is this boundary hard?* This belongs to the "band popularity/status debate" pattern I explicitly folded into Discussion/Question as a scope decision (`planning.md`, difficult case #3), not a category with a strong shared surface signal. It cites specific facts (streams, a record label) that plausibly resemble personal-knowledge-sharing.
- *Labeling or data/prompt problem?* More of a data/task-design problem than an annotation-consistency one: this comment type was labeled consistently, but by my own admission it was "not a perfect conceptual fit" for the label to begin with.
- *What would fix it?* Either collecting enough status-debate examples for the model to learn this specific subtype, or giving this pattern its own label if it recurs often in a larger dataset.

**3. "I've recently been listening to the Leaflings EP again and I love it. It feels like a cool precursor to Zaba"**
True: Critique | Predicted: Personal Anecdote (confidence: 0.34)

- *Which labels confused?* Critique → Personal Anecdote.
- *Why is this boundary hard?* This is one of the two canonical example sentences used to define Critique in `planning.md` itself. It opens with a first-person listening statement ("I've recently been listening to...") that closely resembles the opening of many real Personal Anecdote examples.
- *Labeling or data/prompt problem?* A data/model problem: labeling is correct and consistent here, but the model appears to weight sentence-opening phrasing more heavily than the comparative claim that follows.
- *What would fix it?* More Critique training examples that open with first-person listening statements before pivoting to a specific claim, so the model learns that opening phrasing alone doesn't predict Personal Anecdote.

## Sample Classifications

Five test examples run through the fine-tuned model, showing predicted label and confidence:

| # | Text | True Label | Predicted Label | Confidence | Correct? |
|---|---|---|---|---|---|
| 1 | "Zaba was my first glass animals experience. I was on 4 grams of magic mushrooms and my buddy played..." | Personal Anecdote | Critique | 0.64 | ✗ |
| 2 | "I feel like the rest of the album isn't as accessible or is too experimental than Gooey is, which is..." | Critique | Critique | 0.50 | ✓ |
| 3 | "You are asking in a GA fandom subreddit if people like something GA related. The answer is yes, many..." | Critique | Critique | 0.28 | ✓ |
| 4 | "The Dolby mix of Dreamland is fun. Listening to music in surround is like an upgraded quadraphonic s..." | Critique | Critique | 0.38 | ✓ |
| 5 | "It's a shame it gets to the chorus so quickly and resorts back to it so often. Until the first time..." | Critique | Critique | 0.44 | ✓ |


## Reflection: What the Model Captured vs. What I Intended

My label definitions were designed around a specific kind of judgment: whether a comment contains a specific, checkable claim with reasoning, regardless of the tone or frame surrounding it. That's a rule about the deep content of a comment, deliberately built to look past hype language, personal framing, or question phrasing to find (or fail to find) a real claim underneath.

The fine-tuned model, at the data size I trained it on, did not capture that rule. Instead, it appears to have latched onto shallow, surface-level proxies that are correlated with my labels but aren't the actual thing I defined:
- It treated *the presence of a named song/album title* as a proxy for Critique, rather than *reasoning about* a named title.
- It treated *first-person opening phrasing* ("I've been listening to...", "I discovered...") as a proxy for Personal Anecdote, rather than the actual presence of personal history as the comment's substantive content.
- For Discussion/Question — the label with no strong positive lexical signal and the most rule-dependent, sometimes negatively-defined boundary — it captured essentially nothing at all.

What it *did* capture well was Personal Anecdote's most common surface pattern (concert dates, discovery stories), which is also the label with the most stereotyped, repeatable phrasing in my dataset. That's a meaningful gap.

## Spec Reflection

**One way the spec helped guide implementation:** requiring a written `planning.md` — with explicit label definitions, a tie-break rule, and documented edge cases — *before* collecting any data forced me to think through boundary cases in the abstract first, which made real annotation decisions faster and more consistent than if I'd been designing the label scheme reactively while labeling. 


## AI Usage

AI assistance (Claude) was used throughout this project:

**Instance 1 — Label stress-testing (Milestone 2).** I directed Claude to generate 8 constructed boundary posts designed to sit at the edge between two labels, based on my draft label definitions and edge-case description. Claude produced 8 posts, three of which (a concert/sound-quality anecdote, a "does anyone else think the mixing ruined it" question, and a personal-story-plus-string-arrangement post) exposed that my original tie-break rule was being applied inconsistently case-by-case. **What I changed:** I rewrote the rule as an explicit, general priority statement ("a specific claim overrides any surrounding frame") in `planning.md`, rather than accepting Claude's suggested resolutions for those 8 posts individually.

**Additional uses:** Claude assisted with debugging a Colab notebook errors (a missing `pandas` import after a runtime reset). It was also used to explain Milestones in depth.
