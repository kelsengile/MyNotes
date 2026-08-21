*Natural Language Processing*

# Lesson 35 - Text Preprocessing & Tokenization

[Previous](./[34]-Recurrent-Neural-Networks-and-Sequence-Models.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[36]-Sentiment-Analysis-and-Text-Classification.md)

---

## 35.1 Why Preprocess Text?

Raw text is messy and inconsistent — different capitalization, punctuation, typos, and irrelevant words can all obscure meaningful patterns. **Natural Language Processing (NLP)** almost always starts with a preprocessing pipeline to turn raw text into a cleaner, more consistent form before analysis or modeling.

---

## 35.2 Common Preprocessing Steps

```python
import re
import nltk
from nltk.corpus import stopwords
from nltk.stem import PorterStemmer

text = "The Quick Brown Foxes are jumping!!"

text = text.lower()                              # lowercase
text = re.sub(r"[^\w\s]", "", text)                 # remove punctuation

stop_words = set(stopwords.words("english"))
words = [w for w in text.split() if w not in stop_words]  # remove common filler words

stemmer = PorterStemmer()
stemmed = [stemmer.stem(w) for w in words]            # reduce words to their root form
```

**Stemming** crudely chops words to a root form (e.g. "jumping" -> "jump"), while **lemmatization** uses vocabulary and grammar rules to find the true dictionary base form (e.g. "better" -> "good"), which is slower but more accurate.

---

## 35.3 Tokenization

**Tokenization** splits text into smaller units ("tokens") — usually words or sub-word pieces — for a model to process.

```python
from nltk.tokenize import word_tokenize

tokens = word_tokenize("The quick brown fox jumps.")
# ['The', 'quick', 'brown', 'fox', 'jumps', '.']
```

Modern deep learning models (including the transformers in Lesson 37) typically use **subword tokenization** (like Byte-Pair Encoding), which breaks rare or unknown words into smaller, frequently-seen pieces — this lets a model handle words it never saw during training.

---

## 35.4 Turning Text into Numbers

Models need numeric input, so tokens must be converted into vectors:

- **Bag of Words / TF-IDF** — represent each document as a count (or weighted count) of the words it contains, ignoring word order.
- **Word embeddings** (e.g. Word2Vec, GloVe) — represent each word as a dense vector of numbers, learned so that words with similar meanings end up with similar vectors.

```python
from sklearn.feature_extraction.text import TfidfVectorizer

vectorizer = TfidfVectorizer()
X = vectorizer.fit_transform(documents)   # sparse matrix of TF-IDF scores
```

---

[Previous](./[34]-Recurrent-Neural-Networks-and-Sequence-Models.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[36]-Sentiment-Analysis-and-Text-Classification.md)
