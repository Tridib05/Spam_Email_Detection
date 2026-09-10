# An Explainable and Human-Centric AI Framework for Lightweight Spam Email Classification Using Deep Learning

## Overview

Spam email remains a practical security and productivity problem. A useful classifier must do more than predict `spam` or `ham`: it should be lightweight enough for resource-constrained environments, provide understandable evidence for its decisions, and allow a human reviewer to handle uncertain cases.

This project explores a human-centric deep learning framework for spam email classification. The framework is designed around three goals:

- **Accurate classification:** identify spam and legitimate messages from email text.
- **Explainable predictions:** show the words, phrases, and signals that influenced a decision.
- **Human oversight:** route low-confidence or high-risk messages for review instead of making an opaque automatic decision.

The repository currently contains public email datasets for experimentation and benchmarking. Model training, explainability analysis, and evaluation can be added on top of these datasets.

## Key Ideas

### Lightweight deep learning

The intended solution should balance performance and efficiency. Suitable approaches may include a compact text encoder, word or subword embeddings, a small CNN/RNN, or a distilled transformer. The model should be evaluated not only by accuracy, but also by parameter count, inference time, and memory usage.

### Explainability by design

Each prediction should be accompanied by a human-readable explanation, such as:

- influential words or phrases;
- confidence or probability scores;
- detected patterns such as suspicious links, urgency, financial offers, or excessive promotion;
- a short reason code that a reviewer can understand.

Explanations should be checked for stability and faithfulness instead of being treated as decoration.

### Human-in-the-loop decisions

Predictions can be divided into three operational zones:

| Confidence zone | Suggested action |
| --- | --- |
| High confidence | Automatically classify and quarantine or deliver |
| Uncertain | Send to a human reviewer |
| High-risk or policy-sensitive | Require review, even when confidence is high |

This design reduces the risk of silently misclassifying important legitimate emails.

## Dataset

The `Dataset/` directory contains three CSV files with different schemas:

| File | Main fields | Description |
| --- | --- | --- |
| `emails.csv` | `text`, `spam` | Email text with a binary spam label (`1` for spam, `0` for ham) |
| `spam_ham_dataset.csv` | `label`, `text`, `label_num` | Labeled email messages with both text labels and numeric labels |
| `spam.csv` | `v1`, `v2` | Message label and message text; the extra columns are mostly empty |

Before training, the datasets should be normalized into a common format such as:

```text
text, label
"message content...", 0
"message content...", 1
```

Recommended preparation steps:

1. Remove empty rows and duplicate messages.
2. Normalize label names to `ham` and `spam` or to `0` and `1`.
3. Preserve useful email signals such as subject text, URLs, punctuation, and sender metadata when available.
4. Use a stratified train/validation/test split.
5. Check for near-duplicate messages across splits to reduce data leakage.

## Proposed Pipeline

```text
CSV datasets
	|
	v
Data cleaning and label normalization
	|
	v
Tokenization and feature preparation
	|
	v
Compact deep learning classifier
	|
	+--> Prediction confidence
	+--> Explanation of influential signals
	+--> Human review queue for uncertain cases
	|
	v
Final classification and feedback collection
```

Human reviewer feedback can later be used to monitor drift and improve the model with carefully verified examples.

## Evaluation Plan

Because false negatives can expose users to malicious or fraudulent content, evaluation should include more than a single accuracy score:

- Precision, recall, F1-score, and support for both classes;
- confusion matrix and false-negative analysis;
- ROC-AUC or PR-AUC when probability scores are available;
- calibration and coverage of the human-review zone;
- model size, CPU inference latency, and memory consumption;
- explanation faithfulness, stability, and usefulness to reviewers.

The final model should be compared with simple baselines such as TF-IDF plus logistic regression or a linear SVM. A compact deep model is valuable only when it offers a meaningful trade-off between quality, cost, and interpretability.

## Responsible Use and Limitations

- Dataset labels may contain noise, duplicates, outdated language, or sampling bias.
- Spam tactics change over time, so performance should be monitored after deployment.
- Explanations indicate influential signals; they are not proof that an email is malicious.
- Sensitive email content should be protected during storage, training, logging, and review.
- Automatic quarantine actions should include a recovery path for false positives.

## Repository Structure

```text
Spam_Email_Detection/
|-- README.md
`-- Dataset/
	|-- emails.csv
	|-- spam_ham_dataset.csv
	`-- spam.csv
```

## Future Work

- Add a reproducible preprocessing and training script.
- Implement a compact neural classifier and compare it with classical baselines.
- Add local explanations using model-appropriate attribution methods.
- Build a reviewer interface with confidence thresholds and feedback capture.
- Test robustness against obfuscated words, unusual punctuation, and adversarial email text.
- Add experiment tracking, dataset versioning, and automated evaluation reports.

## Project Goal

The long-term goal is not simply to maximize classification accuracy. It is to develop a practical spam detection system that is efficient, transparent, reviewable, and respectful of the people who rely on its decisions.