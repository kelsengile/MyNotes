*Natural Language Processing*

# Lesson 37 - Introduction to Transformers & LLMs

[Previous](./[36]-Sentiment-Analysis-and-Text-Classification.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[38]-Saving-and-Loading-Models.md)

---

## 37.1 The Transformer Architecture

Introduced in 2017, the **Transformer** architecture replaced the step-by-step processing of RNNs (Lesson 34) with a mechanism called **self-attention**, which lets the model weigh the relevance of every word in a sequence to every other word, all at once and in parallel. This made it possible to train on far larger datasets far more efficiently, and largely displaced RNNs as the default architecture for language tasks.

---

## 37.2 Self-Attention, Intuitively

For each word, self-attention computes how much it should "attend to" every other word in the sequence when building its representation. For example, in the sentence "The animal didn't cross the street because *it* was too tired," attention helps the model figure out that "it" refers to "the animal" rather than "the street," by learning to weigh relevant context words more heavily.

---

## 37.3 Pretraining and Large Language Models (LLMs)

Modern **Large Language Models (LLMs)** are Transformers trained on massive amounts of text using self-supervised learning (Lesson 21) — commonly by predicting the next word in a sequence, over and over, across huge datasets. This pretraining phase lets the model learn grammar, facts, and reasoning patterns from raw text without needing human-labeled examples.

After pretraining, models are often further refined through:

- **Fine-tuning** — additional training on a smaller, task-specific labeled dataset.
- **Instruction tuning / RLHF** (Reinforcement Learning from Human Feedback) — training the model to follow instructions and produce more helpful, safe responses.

---

## 37.4 Using Pretrained Models

Rather than training an LLM from scratch (which requires enormous compute resources), most practitioners use pretrained models directly, or fine-tune small existing models for a specific task:

```python
from transformers import pipeline

classifier = pipeline("sentiment-analysis")
result = classifier("This course made deep learning finally click for me!")
# [{'label': 'POSITIVE', 'score': 0.999}]
```

Libraries like Hugging Face's `transformers` provide easy access to thousands of pretrained models for classification, translation, summarization, and text generation — making state-of-the-art NLP accessible without needing to train a model from scratch.

---

[Previous](./[36]-Sentiment-Analysis-and-Text-Classification.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[38]-Saving-and-Loading-Models.md)
