[Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[2]-History-Of-Artificial-Intelligence.md)

*Foundations*

# Lesson 1 - What Is Artificial Intelligence

## 1.1 Defining Artificial Intelligence

Artificial Intelligence (AI) is the branch of computer science concerned with building systems that can perform tasks that would normally require human intelligence, such as understanding language, recognizing images, making decisions, or learning from experience. Rather than a single technique, AI is best thought of as a goal — getting a machine to behave intelligently — that can be pursued through many different approaches, from hand-written rules to statistical learning from data.

A useful working definition is: AI is the study and design of agents that perceive their environment and take actions that maximize their chance of achieving a given goal. This definition is broad enough to cover a thermostat-like rule follower and a modern language model, but the field usually focuses on systems complex enough that their behavior isn't trivially predictable from their code.

> 💡 **Analogy:** Think of AI the way you'd think of "vehicles." A skateboard, a bicycle, and a rocket ship are all vehicles, but they work completely differently and solve very different problems. AI is the same — a spam filter and a self-driving car are both "AI," but under the hood they may share almost nothing.

```mermaid
flowchart LR
    A[Environment] -->|Perceives| B(AI Agent)
    B -->|Takes Action| A
    B -->|Goal: Maximize Success| C[Outcome]
```

---

## 1.2 Narrow AI vs General AI

**Narrow AI** (also called "weak AI") refers to systems designed to perform one specific task, or a limited set of related tasks, very well — a spam filter, a chess engine, or a recommendation system are all narrow AI. Nearly every AI system in use today, including advanced language models and image generators, is narrow AI: highly capable within its domain, but without general understanding outside of it.

**Artificial General Intelligence (AGI)** refers to a hypothetical system with the flexible, broad reasoning ability of a human — able to learn any intellectual task a person can. AGI does not yet exist, and there is active debate among researchers about how close current systems are to it, and even about how it should be measured. Keeping this distinction in mind helps set realistic expectations about what today's AI can and cannot do.

**🔍 Quick Example:** A chess engine like Stockfish can beat any human on Earth at chess — but ask it to write a poem or drive a car, and it has no idea where to even begin. That's narrow AI: superhuman in one lane, blank outside it.

| | Narrow AI | AGI |
|---|---|---|
| Exists today? | ✅ Yes, everywhere | ❌ Not yet |
| Example | Spam filter, chess engine | Hypothetical human-level reasoner |
| Range of tasks | One or a few | Any intellectual task |

---

## 1.3 Why AI Matters

AI matters because it changes what tasks can be automated and at what scale. Tasks that once required a human — reading a document and summarizing it, recognizing a face in a photo, translating between languages — can now be performed by software, often in milliseconds and at very low cost. This has downstream effects on how products are built, how work is organized, and how quickly information can be processed.

AI also matters as a field of study because it forces us to be explicit about things people usually do intuitively: what counts as "understanding," what makes a decision "fair," and how much a system's confidence should be trusted. Studying AI is as much about clarifying these questions as it is about writing code.

> 💡 **Try this thought experiment:** How many "invisible" AI systems have touched your day already? Your phone's keyboard predicting your next word, a streaming app's recommendation row, a spam folder quietly filtering junk — AI is often felt, not seen.

---

## 1.4 Common Misconceptions About AI

**"AI thinks like a human."** Most AI systems, including modern neural networks, do not reason the way people do. They typically find statistical patterns in data rather than forming a conceptual understanding, even when their output looks humanlike.

**"AI is neutral and objective."** AI systems learn from data created by people, and they can reproduce or amplify the biases present in that data. An AI's output reflects its training process, not some independent ground truth.

**"AI will imminently replace all jobs" or, conversely, "AI is just hype."** Both extremes oversimplify a technology that is genuinely powerful in specific domains (pattern recognition, language generation, prediction) and genuinely weak in others (common-sense reasoning, tasks requiring real-world accountability). A grounded view treats AI as a powerful but limited tool, not as magic or as a fad.

[Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[2]-History-Of-Artificial-Intelligence.md)
