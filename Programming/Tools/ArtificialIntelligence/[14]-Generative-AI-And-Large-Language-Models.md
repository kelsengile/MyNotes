[Previous](./[13]-Natural-Language-Processing.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[15]-Computer-Vision.md)

*Modern AI And Applications*

# Lesson 14 - Generative AI And Large Language Models

## 14.1 What Is Generative AI

**Generative AI** refers to AI systems that create new content — text, images, audio, video, or code — rather than simply classifying or predicting a value from existing data. Where a classification model might tell you whether a photo contains a cat, a generative model could produce an entirely new photo of a cat that never existed before. Generative AI has advanced rapidly because of improvements in the deep learning architectures covered in Lesson 12, particularly the transformer.

---

## 14.2 Large Language Models

A **Large Language Model (LLM)** is a transformer-based language model (see Lesson 13.3) trained on enormous amounts of text, with parameter counts often reaching into the billions or more. Training happens in stages: an initial **pre-training** phase where the model learns general language patterns by predicting text across a huge, broad dataset, followed by additional stages that make the model more useful and aligned with what people actually want, such as being trained to follow instructions and to produce helpful, appropriately cautious responses. LLMs are the technology behind modern AI chat assistants and many other text-generation tools.

---

## 14.3 Prompting And Fine-Tuning

**Prompting** is the practice of writing input text (a "prompt") designed to get a desired response from an LLM without changing the model itself — for example, giving clear instructions, examples of the desired output format, or relevant context. Because LLMs are sensitive to exactly how a request is phrased, effective prompting has become a practical skill in its own right. **Fine-tuning**, by contrast, involves further training an existing model on a smaller, specialized dataset to adapt its behavior for a particular use case, such as a legal-document assistant fine-tuned on legal text. Prompting requires no retraining and is much cheaper, while fine-tuning can produce more consistent, specialized behavior at a higher cost.

---

## 14.4 Limitations And Hallucination

Despite their fluency, LLMs have important limitations. **Hallucination** refers to a model confidently generating information that sounds plausible but is factually incorrect or entirely made up — because a language model is fundamentally predicting likely text rather than verifying facts against the real world, it has no built-in way to know when it's wrong. LLMs can also reflect biases present in their training data, struggle with tasks requiring precise, multi-step logical or mathematical reasoning, and have a "knowledge cutoff" beyond which they have no built-in awareness of events. Understanding these limitations is essential to using generative AI responsibly — verifying important outputs rather than trusting them automatically.

[Previous](./[13]-Natural-Language-Processing.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[15]-Computer-Vision.md)
