# TakeMeter

> A text classifier that labels fan discourse on *Mean Girls* characters.

---

## What I Built

TakeMeter is a text classifier that labels comments from the Mean Girls movie fandom as either **Defense**, **Condemnation**, **Mixed**, or **Hot Take**.

---

## Dataset

| Field | Value |
|---|---|
| **Source** | r/MeanGirls + YouTube comments |
| **Total examples** | 200 |
| **Collection method** | Manual |
| **Label distribution** | Defense: 58 · Condemnation: 55 · Mixed: 36 · Hot Take: 50 |

---

## Labels

| Label | Definition | Example |
|---|---|---|
| **Defense** | Justifies a character's actions via external factors | *"She never actively bullied anyone 'just because'..."* |
| **Condemnation** | Directly criticizes behavior without excusing it | *"She manipulated a new student to do her bidding..."* |
| **Mixed** | Explicitly combines both defense and condemnation | *"She used Cady... that said, she made Cady feel welcome."* |
| **Hot Take** | Strong opinion with no supporting argument | *"Gretchen is the real villain."* |

---

## Results

**Macro F1-Score: 0.13** (target: > 0.75) · **Accuracy: 0.23** (30 test samples)

### Per-Class Breakdown

| Label | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| Defense | 0.25 | 0.67 | 0.36 | 9 |
| Condemnation | 0.17 | 0.12 | 0.14 | 8 |
| Mixed | 0.00 | 0.00 | 0.00 | 6 |
| Hot Take | 0.00 | 0.00 | 0.00 | 7 |
| **Macro avg** | **0.10** | **0.20** | **0.13** | **30** |

### Training Progress

| Epoch | Training Loss | Validation Loss | Accuracy |
|---|---|---|---|
| 1 | — | 1.3924 | 0.2333 |
| 2 | 1.4008 | 1.3834 | 0.2333 |
| 3 | 1.3772 | 1.3664 | 0.3000 |

---

## Analysis

### What Worked

Defense was the only class the model predicted with any consistency, achieving a recall of 0.67 — it correctly identified 6 out of 9 Defense examples. This likely reflects that Defense is the largest class in the training set (58 examples), giving the model the most signal to learn from.

### Where It Struggled

The model completely failed on **Mixed** and **Hot Take**, predicting neither class a single time across all 30 test samples (F1 = 0.00 for both). The model appears to have collapsed toward predicting Defense and, to a lesser extent, Condemnation — essentially ignoring two of the four classes.

Three likely causes:
1. **Class imbalance** — Mixed (36) and Hot Take (50) have fewer training examples than Defense (58) and the model defaulted to the majority class.
2. **Insufficient training** — losses were still high after 3 epochs (validation loss ~1.37), and accuracy had only reached 30% before evaluation.
3. **Label ambiguity** — Mixed by definition shares surface features with both Defense and Condemnation, making it hard to distinguish without more examples.

### AI Tool Usage

Claude was used to interpret training metrics (epoch logs and per-class classification reports).

---

## Limitations

- **Small dataset**: 200 examples total, with only 30 used for testing, makes results noisy and hard to generalize.
- **Domain specificity**: All data comes from Mean Girls fandom spaces (Reddit and YouTube). The model would not transfer to other fandoms or topics.
- **Single annotator**: All labels were assigned by one person, introducing subjectivity, particularly for Mixed vs. Condemnation boundary cases.
- **Short training**: Only 3 epochs with validation loss still declining suggests the model was underfitted at evaluation time.

---

## If I Had More Time

- I will remove Mixed and Hot Take form the label set and focus on Defense and Condemnation, which are more clearly defined and easier to distinguish.
