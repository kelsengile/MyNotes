[Previous](./[3]-Types-Of-AI-Systems.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[5]-Search-And-Problem-Solving.md)

*Core Concepts*

# Lesson 4 - Data And Features

## 4.1 Why Data Matters

Data is the raw material that most modern AI systems learn from — without it, a machine learning model has nothing to learn a pattern from. The quality, quantity, and relevance of data used to train a system directly determines how well that system will perform, often more than the choice of algorithm does. This is why "garbage in, garbage out" is one of the most repeated phrases in AI: even a sophisticated model trained on poor data will produce poor, unreliable results.

---

## 4.2 Features And Labels

A **feature** is an individual measurable property of the data used as input to a model — for example, a house's square footage, number of bedrooms, and location could all be features used to predict its price. A **label** is the target value a model is trying to predict, such as the actual sale price in that example. Choosing which features to include, and how to represent them numerically, is called **feature engineering**, and it remains one of the most impactful (and often most manual) parts of building an effective machine learning system, even as some modern deep learning methods can learn useful features automatically from raw data.

---

## 4.3 Structured vs Unstructured Data

**Structured data** is organized into a predictable format, typically rows and columns, like a spreadsheet of customer transactions with clearly defined fields (date, amount, category). It's straightforward to feed into traditional machine learning algorithms.

**Unstructured data** doesn't fit neatly into rows and columns — text documents, images, audio recordings, and video are all unstructured. Working with unstructured data typically requires additional processing (like the tokenization discussed in Lesson 13, or the image processing discussed in Lesson 15) to convert it into a numerical form a model can use. Much of the progress in deep learning has come specifically from getting better at learning directly from unstructured data.

---

## 4.4 Data Quality And Bias

Data quality issues — missing values, incorrect labels, duplicate records, or measurement errors — can silently degrade a model's performance, which is why data cleaning is a major part of any real AI project. Beyond simple errors, data can also be **biased**: if the data used to train a system underrepresents certain groups or situations, or reflects historical inequities, the resulting model can make systematically worse or unfair predictions for those groups. This issue is significant enough that it's covered in more depth in Lesson 17 (Ethics And Bias In AI), but it's worth internalizing early: a model is only as good, and as fair, as the data it was trained on.

[Previous](./[3]-Types-Of-AI-Systems.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[5]-Search-And-Problem-Solving.md)
