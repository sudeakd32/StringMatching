Bonus Homework Report
String Matching Algorithms

Zeki Furkan Yıldız (21050111019)
Sudenur Akdogan (2350111017)

Introduction

In this homework, we worked on string matching algorithms. The main goal was to implement the Boyer–Moore algorithm and create a custom algorithm called GoCrazy. We also wrote a Pre-Analysis class to choose the best algorithm automatically.

During this process, we realized that theoretical speed is not always the same as real-world speed, especially due to Java’s overhead costs and JVM optimizations.

Boyer–Moore Implementation

We implemented the Boyer–Moore algorithm in the Analysis.java file, focusing specifically on the Bad Character Heuristic.

For the implementation, we used an integer array of size 256 for ASCII characters.

Optimization: Threshold Check

We observed that building the badChar lookup table itself has a cost.
If the pattern length is less than 20 characters, the Naive algorithm is often faster in practice. Therefore, we added a threshold check to fall back to Naive for short patterns.

Resources Used

To understand the Boyer–Moore logic and the Bad Character rule, we used:

https://www.tutorialspoint.com/data_structures_algorithms/boyer_moore_algorithm.htm

https://www.geeksforgeeks.org/dsa/boyer-moore-algorithm-for-pattern-searching/

We also reviewed this GitHub repository for implementation structure ideas:

https://github.com/likejazz/boyer-moore-string-search

GoCrazy Algorithm

For the creative part, we wanted an algorithm faster than standard Boyer–Moore in real-world cases. We designed GoCrazy, a hybrid approach inspired by:

Sunday’s Algorithm

FJS (Fast–Jesus–Sunday)

Core Idea

When a mismatch occurs, instead of examining the mismatched character inside the window, Sunday’s algorithm checks the character immediately after the window. This often allows larger jumps.

To avoid worst-case behavior on repetitive patterns, we also integrated KMP-style prefix logic for safety.

Resources Used

We researched hybrid approaches and adapted ideas from the following repositories:

https://github.com/johannburkard/StringSearch

(Sunday’s Algorithm logic)

https://github.com/CGJennings/fjs-string-matching

(FJS / KMP hybrid ideas)

Pre-Analysis Logic

This was the most critical and complex part of the assignment.

Initially, we noticed that the Naive algorithm was winning almost all standard test cases. This led us to an important realization:

In modern Java, simple often beats theoretically optimal for small and medium inputs.

Why Does Naive Win?

Two main reasons explain this behavior:

Overhead Cost
Creating lookup tables (HashMap, int[256], etc.) takes microseconds.
For searches that finish in 1–2 microseconds, this setup cost dominates.

JIT Optimization
The JVM aggressively optimizes simple loops.
Naive string matching benefits heavily from JIT optimizations.

Our Strategy

Based on our findings, we implemented a layered decision system in the StudentPreAnalysis class:

Instant Edge Cases

Empty pattern

Pattern longer than text
→ Return Naive immediately.

Ultra-Short Patterns (M ≤ 3)
Analysis overhead is larger than the search itself.
→ Force Naive.

Repetitive Patterns
Using a helper method hasHighRepetition (checking the first 8 characters), we detect patterns like "AAAA".
→ Switch to KMP to avoid worst-case O(N·M).

Small Text Optimization (N < 128)
Heuristic table construction cost outweighs benefits.
→ Force Naive.

Large Texts (N > 512)

Small Alphabet (DNA detection)
Using a fast bitmask (isSmallAlphabet), we detect alphabets like {A, C, G, T}.
→ Prefer KMP for stability.

Long Patterns (M > 30)
→ Boyer–Moore benefits from good suffix jumps.

The “Sweet Spot” – GoCrazy
For standard texts and medium-length patterns, we default to GoCrazy, which showed the best benchmark results due to aggressive skipping.

Analysis of Results

Our final results show that the adaptive strategy performs significantly better than using a single static algorithm.

Overall Accuracy
Correctly predicted the fastest algorithm in 20 out of 30 cases (66.7%).

Very Long Text

Naive: 34.98 μs

GoCrazy: 21.03 μs
→ Strategy correctly avoided Naive.

Repetitive Patterns
Detected repetition and selected KMP (9.68 μs).
Although Naive was slightly faster in this case, KMP guaranteed safety against worst-case inputs.

Pattern Longer Than Text
Instantly selected Naive, resulting in 0.47 μs.

Our Journey

This homework taught us that Big-O notation alone is not enough.

Theoretically, Boyer–Moore should be extremely fast. However, in practice, initialization overhead makes it slower than Naive for small inputs.

We initially spent a lot of time debugging, assuming our Boyer–Moore implementation was wrong. Eventually, we realized the issue was not correctness—but overhead.

Exploring algorithms like FJS and Sunday helped us design a much better hybrid approach.

In the end, we learned that:

Optimization is not only about asymptotic complexity

Microsecond-level costs matter

The best algorithm is the one that fits the current workload, not the most complex one

AI Utilization & Methodology

During strategy development, benchmarking, and debugging overhead issues, we utilized AI assistants (specifically Gemini) to analyze console outputs and evaluate performance trade-offs.

To maintain transparency, below are the exact prompts we used:

GoCrazy Algorithm (Sunday / FJS Logic)

“I found these resources online:
https://github.com/johannburkard/StringSearch

,
https://github.com/CGJennings/fjs-string-matching

.
Can you update the GoCrazy algorithm using the logic of these algorithms?
We will cite them as references. Please provide renewed, efficient Java code that looks unique and original.”

Boyer–Moore Optimization

“https://www.tutorialspoint.com/data_structures_algorithms/boyer_moore_algorithm.htm

,
https://www.geeksforgeeks.org/dsa/boyer-moore-algorithm-for-pattern-searching/
,
https://github.com/likejazz/boyer-moore-string-search

.
How can we make Boyer–Moore more efficient and suitable for the instructor’s requirements?
Give me original code and only modify the Boyer–Moore part.”

These prompts helped us combine theory with practice while preserving academic integrity.
