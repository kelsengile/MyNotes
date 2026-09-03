[⬅ Back to README](../../../README.md)

# Introduction to Complexity

This note covers how to measure and reason about the efficiency of an algorithm — independent of the machine it runs on. It builds directly on the DSA Fundamentals topic and is the toolkit you'll use to evaluate every data structure and algorithm that follows.

## Why Complexity Matters
Two solutions can produce the same correct answer and still behave completely differently as input grows — one finishes instantly, the other grinds to a halt. Complexity analysis gives you a language (Big-O and friends) to predict that behavior before you ever run the code, which is essential for choosing the right approach and for technical interviews.

---

## Table of Contents  

1. **[Why We Analyze Algorithms](./[1]-Why-We-Analyze-Algorithms.md)**  
   1.1 Correctness vs. Efficiency  
   1.2 Measuring Performance Independent of Hardware  

2. **[Asymptotic Notation](./[2]-Asymptotic-Notation.md)**  
   2.1 Big-O Notation  
   2.2 Big-Omega and Big-Theta  
   2.3 Common Growth Rates (O(1) to O(n!))  
   2.4 Little-o and Little-Omega Notation  

3. **[Time Complexity Analysis](./[3]-Time-Complexity-Analysis.md)**  
   3.1 Counting Operations  
   3.2 Best, Average, and Worst Case  
   3.3 Analyzing Loops and Nested Loops  
   3.4 Analyzing Recursive Functions  
   3.5 The Substitution Method  

4. **[Space Complexity](./[4]-Space-Complexity.md)**  
   4.1 Auxiliary Space vs. Total Space  
   4.2 Space-Time Tradeoffs  

5. **[Recurrence Relations](./[5]-Recurrence-Relations.md)**  
   5.1 Writing a Recurrence Relation  
   5.2 Solving with the Recursion Tree Method  
   5.3 The Master Theorem  

6. **[Amortized Analysis](./[6]-Amortized-Analysis.md)**  
   6.1 What Is Amortized Complexity?  
   6.2 Aggregate, Accounting, and Potential Methods  

7. **[Complexity Classes](./[7]-Complexity-Classes.md)**  
   7.1 P vs. NP — A Conceptual Overview  
   7.2 Why Some Problems Are Considered "Hard"  
