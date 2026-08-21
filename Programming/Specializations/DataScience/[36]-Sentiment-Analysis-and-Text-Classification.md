*Natural Language Processing*

# Lesson 36 - Sentiment Analysis & Text Classification

[Previous](./[35]-Text-Preprocessing-and-Tokenization.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[37]-Introduction-to-Transformers-and-LLMs.md)

---

## 36.1 What is Text Classification?

**Text classification** assigns a predefined category to a piece of text — this is simply supervised learning (Lesson 21) applied to text data. Once text is converted to numbers (Lesson 35), any standard classification model (logistic regression, random forests, neural networks) can be applied. Common applications: spam detection, topic labeling, and language identification.

---

## 36.2 Sentiment Analysis

**Sentiment analysis** is a specific, very common text classification task: determining whether a piece of text expresses a positive, negative, or neutral opinion. It's widely used to analyze product reviews, social media posts, and customer support tickets at scale.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline

pipeline = Pipeline([
    ("tfidf", TfidfVectorizer(max_features=5000)),
    ("classifier", LogisticRegression()),
])

pipeline.fit(train_texts, train_labels)
predictions = pipeline.predict(test_texts)
```

Using a `Pipeline` bundles preprocessing and modeling into a single object, ensuring the exact same transformation is applied consistently to both training and new data.

---

## 36.3 Lexicon-Based Sentiment Analysis

An alternative to training a model is using a prebuilt **sentiment lexicon** — a dictionary that scores words as positive or negative — and combining word scores for a whole piece of text. Tools like `VADER` (tuned for social media text) work this way and require no training data:

```python
from nltk.sentiment import SentimentIntensityAnalyzer

sia = SentimentIntensityAnalyzer()
scores = sia.polarity_scores("I absolutely loved this product!")
# {'neg': 0.0, 'neu': 0.3, 'pos': 0.7, 'compound': 0.65}
```

Lexicon-based methods are fast and require no labeled data, but are less accurate than a trained model on domain-specific text (e.g. sarcasm or industry jargon can easily fool them).

---

## 36.4 Evaluating Text Classifiers

Text classification is evaluated the same way as any classification model (Lesson 27) — using accuracy, precision, recall, and F1 score, comparing predictions against a labeled test set. Class imbalance is especially common in text data (e.g. far more neutral reviews than extremely negative ones), so precision/recall/F1 per class are usually more informative than plain accuracy.

---

[Previous](./[35]-Text-Preprocessing-and-Tokenization.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[37]-Introduction-to-Transformers-and-LLMs.md)
