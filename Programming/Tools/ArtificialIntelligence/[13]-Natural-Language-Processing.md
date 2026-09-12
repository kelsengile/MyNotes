[Previous](./[12]-Deep-Learning-Architectures.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[14]-Generative-AI-And-Large-Language-Models.md)

*Neural Networks And Deep Learning*

# Lesson 13 - Natural Language Processing

## 13.1 What Is Natural Language Processing

**Natural Language Processing (NLP)** is the field of AI focused on enabling computers to understand, interpret, and generate human language. This is a uniquely difficult problem because human language is ambiguous, context-dependent, and full of exceptions — the same sentence can mean different things depending on tone, context, or shared background knowledge that isn't stated explicitly. NLP techniques power everyday tools like autocomplete, spam filters, translation apps, and voice assistants.

**🔍 Quick Example — Ambiguity:** "I saw the man with the telescope." Did *you* have the telescope, or did *the man*? Humans resolve this instantly using context; it's genuinely hard for a machine.

---

## 13.2 Tokenization And Embeddings

Before text can be processed by a machine learning model, it needs to be converted into numbers. **Tokenization** is the process of breaking text into smaller units called **tokens** — these might be whole words, sub-word pieces, or even individual characters, depending on the system. Each token is then converted into an **embedding**: a list of numbers (a vector) that represents the token's meaning in a way that captures relationships between words — for example, the embeddings for "king" and "queen" end up mathematically close to each other, and close to the relationship between "man" and "woman." Embeddings are what let a neural network work with language mathematically instead of as raw text.

```
"unhappiness" → tokens: ["un", "happi", "ness"]

king  - man  + woman  ≈  queen     (a famous embedding relationship)
```

> 💡 **Analogy:** Embeddings are like plotting every word as a point on a giant map, where words with similar meanings end up near each other — "happy" and "joyful" are neighbors, while "happy" and "concrete" are on opposite sides of the map.

---

## 13.3 Language Models

A **language model** is a system trained to predict the likelihood of a sequence of words, most commonly by predicting the next word given the words that came before it. By training on enormous amounts of text, a language model implicitly learns grammar, facts, and even some reasoning patterns, simply as a byproduct of getting very good at next-word prediction. **Large Language Models (LLMs)**, covered in depth in the next lesson, are language models scaled up to billions or more of parameters, trained on massive datasets of text.

**🔍 Quick Example:** "The capital of France is ___." A well-trained language model assigns a very high probability to "Paris" and a near-zero probability to "banana" — purely from having seen this pattern (and millions like it) during training.

---

## 13.4 Common NLP Tasks

NLP covers a wide range of practical tasks, including:

- **Sentiment analysis** — determining whether a piece of text expresses a positive, negative, or neutral opinion.
- **Named entity recognition** — identifying and categorizing names of people, places, organizations, and dates within text.
- **Machine translation** — converting text from one language into another while preserving meaning.
- **Summarization** — condensing a longer document into a shorter version that retains its key points.
- **Question answering** — producing a direct answer to a question, either by extracting it from a given document or generating it from learned knowledge.

Modern large language models are often flexible enough to perform many of these tasks without being separately trained for each one, simply by being given instructions in natural language.

| Task | Example Input | Example Output |
|---|---|---|
| Sentiment analysis | "This movie was amazing!" | Positive |
| Named entity recognition | "Apple was founded in Cupertino." | Apple (Org), Cupertino (Place) |
| Machine translation | "Hello" | "Hola" |
| Summarization | 10-paragraph article | 2-sentence summary |


[Previous](./[12]-Deep-Learning-Architectures.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[14]-Generative-AI-And-Large-Language-Models.md)
