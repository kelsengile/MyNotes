[Previous](./[6]-Number-Theory-Basics.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md)

# Lesson 7 - Automata And Formal Languages

This final lesson ties everything together: sets (Lesson 2) define alphabets and languages, graphs (Lesson 4) become the diagrams we draw finite state machines with, and logic (Lesson 1) underlies the rules a machine follows. Automata theory studies abstract machines and the languages (sets of strings) they can recognize — the theoretical backbone of how compilers, parsers, and search tools like regex actually work.

---

## 7.1 Finite State Machines

A **Finite State Machine** (FSM), also called a **finite automaton**, is an abstract machine that reads an input string one symbol at a time and moves between a finite set of **states** based on **transition rules**. Formally, an FSM is defined by 5 components:

| Component | Symbol | Meaning |
|---|---|---|
| States | Q | the finite set of all possible states |
| Alphabet | Σ | the finite set of symbols the machine can read |
| Transition function | δ | rule: given a state and a symbol, which state comes next |
| Start state | q₀ | the state the machine begins in |
| Accepting states | F | states where, if the input ends there, the string is "accepted" |

**Example — an FSM that accepts binary strings ending in "1":**

- States: Q = {S0, S1}
- Alphabet: Σ = {0, 1}
- Start state: S0
- Accepting states: F = {S1}
- Transitions:
  - From S0, reading 0 → stay in S0
  - From S0, reading 1 → go to S1
  - From S1, reading 0 → go back to S0
  - From S1, reading 1 → stay in S1

**Tracing the input "1011":** Start at S0 → read 1 → S1 → read 0 → S0 → read 1 → S1 → read 1 → S1. The machine ends in S1, an accepting state, so "1011" is **accepted** (it does end in 1). Tracing "1010" the same way ends in S0 — **rejected** (it ends in 0).

**Deterministic vs. Nondeterministic:** In a **Deterministic Finite Automaton (DFA)**, every state has exactly one transition per symbol — no ambiguity. A **Nondeterministic Finite Automaton (NFA)** allows multiple possible transitions (or none) for a state/symbol pair, and even transitions on no input at all (called ε-transitions). NFAs are often easier to design, but every NFA can be converted into an equivalent DFA that accepts the exact same language — the two models have identical *power*, just different convenience.

**Why FSMs matter in practice:** they model anything with a finite number of "modes" and clear rules for switching between them — traffic light controllers, vending machines, TCP connection states (LISTEN → SYN-RECEIVED → ESTABLISHED → ...), and text-parsing components inside compilers.

---

## 7.2 Regular Expressions as Formal Languages

A **formal language** is simply a set of strings built from some alphabet — as narrow as "strings representing valid email addresses" or as broad as "all binary strings." A **regular language** is any language that can be recognized by some finite automaton (DFA/NFA). **Regular expressions** are a compact, symbolic way to *describe* regular languages without drawing a full state diagram.

**Core regex building blocks and what they correspond to:**

| Regex piece | Meaning | Example | Matches |
|---|---|---|---|
| Literal | matches itself exactly | `cat` | "cat" |
| `\|` (alternation, OR) | matches either side | `cat\|dog` | "cat" or "dog" |
| `*` (Kleene star) | zero or more repetitions | `ab*` | "a", "ab", "abb", "abbb", ... |
| `+` | one or more repetitions | `ab+` | "ab", "abb", ... (not "a" alone) |
| `?` | zero or one occurrence | `colou?r` | "color" or "colour" |
| `()` (grouping) | applies an operator to a whole group | `(ab)*` | "", "ab", "abab", ... |

**Example — building up a regex for "binary strings ending in 1":** this is exactly the language accepted by the FSM in section 7.1. As a regex: `(0|1)*1` — any sequence of 0s and 1s (`(0|1)*`), followed by a single `1`.

**The deep connection (Kleene's Theorem):** a language is regular **if and only if** it can be described by a regular expression. This is why every regular expression engine can, in principle, be compiled down into a finite automaton internally — and in fact, that's exactly how many regex engines work under the hood.

**A key limitation to know:** not every language is regular. The classic example is "strings of balanced parentheses" — a finite automaton has no memory of *how many* open parentheses it has seen so far (it only has a finite number of states), so it can't correctly track arbitrarily deep nesting. This is why you can't write a single regex to reliably validate arbitrarily nested parentheses or, similarly, deeply nested code blocks — you need a more powerful model (like a **pushdown automaton**, which adds a stack).

---

## 7.3 Why This Matters for Compilers and Parsers

Compilers translate source code into something a machine can execute, and they do it in stages that map directly onto the concepts from this lesson:

**Stage 1 — Lexical Analysis (Tokenizing):** the compiler's **lexer** scans raw source code character by character and groups it into meaningful chunks called **tokens** (keywords, identifiers, numbers, operators). This stage is almost always implemented using finite automata — each *kind* of token (a number, an identifier, a string literal) is defined by a regular expression, and the lexer is effectively a big combined DFA that classifies each chunk of text as it reads.

**Example:** the regex `[a-zA-Z_][a-zA-Z0-9_]*` describes valid identifiers in many programming languages (a letter or underscore, followed by any number of letters, digits, or underscores) — and a lexer built from this regex will correctly recognize `total_count` or `_temp` as identifiers, while rejecting `2fast` (starts with a digit).

**Stage 2 — Syntax Analysis (Parsing):** once the code is a stream of tokens, the **parser** checks whether that sequence of tokens forms a valid structure according to the language's grammar (e.g., "an `if` must be followed by a condition in parentheses, then a block"). Because programming language syntax involves nesting (parentheses, blocks, expressions inside expressions), parsers need more power than plain finite automata provide — this is exactly the "balanced parentheses" limitation from section 7.2. Parsers are typically built using **context-free grammars** and pushdown automata, one level up in computational power from the finite automata used for lexing.

**Putting it together:** when you write `if (x > 0) { return x; }`, the lexer's finite automata first break this into tokens (`if`, `(`, `x`, `>`, `0`, `)`, `{`, `return`, `x`, `;`, `}`), and then the parser — using a more powerful grammar-based model — verifies those tokens are arranged in a structurally valid way and builds a tree representing the code's meaning (a **parse tree** or **abstract syntax tree**, which is, fittingly, the same kind of tree structure covered in Lesson 4).

This is the payoff of the whole topic: logic gave you rigor, sets gave you language to describe collections of strings, graphs gave you the diagrams, and automata theory ties them together into the actual machinery that reads and understands code.

---

[Previous](./[6]-Number-Theory-Basics.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md)
