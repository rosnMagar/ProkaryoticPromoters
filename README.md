# Prokaryotic Promoters

This working prototype that reads a single string, verifies that it is a valid DNA sequence, finds exact matches to common promoter motifs, and reports an overall match score for each proposed promoter.

###  The DNA verification process

DNA normally consists of 4 different nucleotieds **A, T, G, and C.** We can scan the string to check if we find any string other than these characters. Linear Scanning can perform this operation in **O(n)** time.

### Promoter Matching Process

We can locate promoters using an exact matching algorithm. This process is very straightforward however is not always accurate. Fuzzy matching process can locate multiple possible promoter regions. Fuzzy matching technique generates a list of possible promoter regions with their corresponding probability of being a promoter.

##### Exact Matching

For exact promoter matching, Two regions indicating a promoter found together are the  **TTGACA (-35) and the TATAAT (-10)** regions. Which means, we can simply scan for these two regions to locate the promoter start regions. 

We can keep track of how spread apart **TTGACA(-35) and TATAAT (-10)** are, making sure their **distance (d)** $\le$ 21 and $\ge$ 15

To make things simple, for exact matching we will be looking for **TTGACA** and **TATAAT** or **TAXXXT** blocks.

To calculate the overall confidence score:
    
- We can use the normal distribution formula constrained between the range [21,15] where $\mu = 17$, and $\sigma = 0.8$.
- We calculate the (-35) or (-10) block probability using the corresponding hashtable (Dan's Research) for each block type we have.

### Exact matching algorithm with hash table

If we consider a nucleotide $\tau$ at position $n$ in a DNA sequence $\Delta$ as the start of the -35 sequence

Given an example DNA Sequence ...TAGACAC...

Considering $\tau=$ **T** with position $n$ then we can generate a scoring system like below using the `tf_hashtable`:
| T | A | G | A | C | A | $\mu$ |
|---|---|---|---|---|---|-------|
|78 | 10| 68| 20|52 | 54| $\frac{47}{100}$   |

where $\mu$ is the score of the possible -35 sequence.


Once this step is done, we move forward i.e we go to $n + 1$ location where $\tau=$ A where we get another score for the nucleotide subset:

| A | G | A | C | A | C | $\mu$ |
|---|---|---|---|---|---|-------|
| 10| 68| 20| 52| 54| 5 | $\frac{-26}{100}$   |

Ideal way of calculating $\mu$ after the first subset is to subtract score at $n$ and add the score at $n + 1 + 5$ 

We store the list key=n value=[-35 sequence, score] ($\Beta$) as a list. For the **-10** sequence we create a dictionary with [-10 sequence, n, score] ($\Alpha$).

