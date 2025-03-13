# Project 1: Proof of Work Simulation

## 1. Running the Program Locally (4 points)

We executed the program on a local machine for `k = 2, 3, 4, 5, 6`. The results are summarized below:

| Difficulty (k) | Nonce        | XS                                | Hashed Value | Time Elapsed | Number of Trials |
|--------------|-------------|-----------------------------------|--------------|--------------|-----------------|
| 2            | 1427503295  | 1427503295432286782085215562343426 | 00168f731291f340356f9794dd66706ee3c6ec6ca3b11fb48d2780e1ba988e77 | 2s  | 100 |
| 3            | 384256361   | 384256361432286782085215562343426  | 0001ea1aaa12dca5b88378e48b23d1636882645396db68d5bfb8d6d41beeb106 | 2s  | 10,000 |
| 4            | 1936574461  | 1936574461432286782085215562343426 | 00009e7685bc0a3832201aa4d5084f0d80b17ebd63185a09a58e14c4779b0c46 | 2s  | 50,000 |
| 5            | 1217813277  | 1217813277432286782085215562343426 | 00000b2a46cdf842cc3fc6696bc23cd63b70c9ff157baa6fed77c698a1bd3c3d | 3s  | 500,000 |
| 6            | 733475487   | 733475487432286782085215562343426  | 000000561cd2570de7f9011e6ebe934330bacef8436c4f089ab2cd3089c00724 | 5s  | 2,000,000 |

## 2. Running the Program on GCP (3 points)

For `k = 7`, we executed the program on a Google Cloud Platform (GCP) cluster. The results are as follows:

| Difficulty (k) | XS                                | Hashed Value | Time Elapsed | Number of Trials |
|--------------|-----------------------------------|--------------|--------------|-----------------|
| 7            | 451537276432286782085215562343426 | 00000009244774935b928c97b510201f69f6d486e7dce71d073ae2a83b952058 | 1194s | 400,000,000 |

### Cluster Configuration:
- **Dataproc Version:** 2.2 (Debian 12)
- **Cluster Type:** Standard (1 master, 2 workers)
- **Master Node:**
  - Machine Type: `n2-standard-4` (4 vCPUs, ~16 GB RAM)
  - Disk: `100 GB pd-balanced`
- **Worker Nodes (×2):**
  - Machine Type: `n2-standard-4` (4 vCPUs, ~16 GB RAM each)
  - Disk: `200 GB pd-balanced each`

Total available processing power:


$(1 \text{ master} + 2 \text{ workers}) \times 4 \text{ vCPUs} = 12 \text{ vCPUs total}$

### Estimating the Number of Trials:

We estimated the number of trials using the probability formula:


$1 - e^{-pN}$

where:


$p = \frac{1}{16^7} \approx 3.7 \times 10^{-9}$

For $N = 4 × 10^8$, the probability of finding a valid nonce is approximately **77.5%**, which was considered sufficient for demonstration purposes.

---

## 3. Code Modification & Efficiency Discussion (3 points)

### Code Change:

In `src/main/scala/project_1/main.scala`, replace:

```scala
val nonce = sc.range(0, trials).mapPartitionsWithIndex((indx, iter) => {
  val rand = new scala.util.Random(indx + seed)
  iter.map(x => rand.nextInt(Int.MaxValue - 1) + 1)
})
```

with

```scala
val nonce = sc.range(1, trials + 1)
```

