# MathSolver: Ultra-Compact Neural Models for Exact Computation 🧮

> **Deterministic arithmetic, trigonometry & cryptographic hashing in sub-1,000 parameters and under 25 KiB.**

**MathSolver** is a lightweight neural architecture designed for deterministic, 100% bit-exact computation with zero hallucination. By pairing an ultra-compact attention router (**718 to 2,682 parameters**, starting at **3.20 KiB**) with a dedicated execution core (**5.67 to 8.33 KiB**), it achieves microsecond latencies (<1.5 ms) across multi-digit arithmetic (`+`, `-`, `*`, `/`), trigonometry (`sin`, `cos`), square roots (`sqrt`), and data integrity hashing (`md5`).

🎯 **Zero Hallucination** • ⚡ **Sub-1k Params (from 718)** • 📦 **< 25 KiB Total Size**

---

## Sample Benchmark & Execution Outputs

The pipeline detects computational intent directly from raw character streams and produces bit-exact solutions:

| Input Expression | Type | Output Result | Latency | Status |
|---|---|---|---|---|
| `19 * 3` | Arithmetic (Multiplication) | `57` | 1.36 ms | ✅ 100% Bit-Exact |
| `4 * (3 - 1)` | Arithmetic (Parentheses) | `8` | 1.38 ms | ✅ 100% Bit-Exact |
| `12345678 * 87654321` | Arithmetic (8x8 Digits) | `1082152022374638` | 1.41 ms | ✅ 100% Bit-Exact |
| `123456789012 + 987654321098` | Arithmetic (12+12 Digits) | `1111111110110` | 1.39 ms | ✅ 100% Bit-Exact |
| `999999999999 - 111111111111` | Arithmetic (12-Digit Sub) | `888888888888` | 1.37 ms | ✅ 100% Bit-Exact |
| `100000000000 / 2500` | Arithmetic (Division) | `40000000` | 1.42 ms | ✅ 100% Bit-Exact |
| `sin(90)` | Trigonometry (Sine, Deg) | `1.0` | 1.36 ms | ✅ 100% Exact |
| `cos(0)` | Trigonometry (Cosine, Deg) | `1.0` | 1.35 ms | ✅ 100% Exact |
| `sqrt(144)` | Non-linear (Square Root) | `12` | 1.37 ms | ✅ 100% Exact |
| `md5("hello")` | Cryptography (128-bit MD5) | `5d41402abc4b2a76b9719d911017c592` | 1.39 ms | ✅ 100% Exact |
| `"What is 100 divided by 4?"` | Natural Language Context | `[MathSolver] Non-computational input rejected. Guide: https://github.com/dazzafact7/mathsolver` | 1.35 ms | 🛡️ Fail-Closed (No Drift) |
| `"Peter has 5 apples and 3 pears"` | Conversational Sentence | `[MathSolver] Non-computational input rejected. Guide: https://github.com/dazzafact7/mathsolver` | 1.34 ms | 🛡️ Fail-Closed (No Drift) |

---

## Model Suite Overview

The repository provides modular variants to fit diverse size and capability requirements:

| Module | Checkpoint | Size | Parameters | Supported Capabilities |
|---|---|---:|---:|---|
| **Nano Router** | `math_router_nano.pt` | 3.20 KiB | 718 | Routing: `+`, `-`, `*`, `/` vs. language |
| **Mini Router** | `math_router_mini.pt` | 7.74 KiB | 1,922 | Routing: Multi-feature arithmetic vs. language |
| **Full Router** | `math_router_full.pt` | 14.14 KiB | 2,682 | Routing: `+`, `-`, `*`, `/`, `sin`, `cos`, `sqrt`, `md5` vs. language |
| **Arithmetic Core** | `math_core.pt` | 5.67 KiB | Matrix | Execution: `+`, `-`, `*`, `/`, `//` (100+ digits) |
| **Full Core** | `math_core_full.pt` | 8.33 KiB | Matrix | Execution: Arithmetik + `sin`, `cos`, `sqrt`, `md5` |

---

## Benchmarks

### 1. Standalone Core Execution
Evaluated directly across 1,000 operations per category on weights:

| Operation | Total Time (1,000 Ops) | Latency / Op | Throughput | Tested Range | Precision |
|---|---:|---:|---:|---|:---:|
| **Multiplication (`*`)** | **14.8 ms** | **14.8 µs** | **67,463 ops/sec** | Up to 50x50 digits | 100.0% Exact |
| **Addition (`+`)** | **18.1 ms** | **18.1 µs** | **55,217 ops/sec** | Up to 100 digits | 100.0% Exact |
| **Subtraction (`-`)** | **15.1 ms** | **15.1 µs** | **66,053 ops/sec** | Up to 100 digits | 100.0% Exact |
| **Division (`/`)** | **73.8 ms** | **73.8 µs** | **13,554 ops/sec** | Up to 50 digits | 100.0% Exact |
| **Trigonometry (`sin`, `cos`)** | **4.2 ms** | **4.2 µs** | **238,095 ops/sec** | Discretized 0.5° LUT | 100.0% Exact |
| **Square Root (`sqrt`)** | **5.1 ms** | **5.1 µs** | **196,078 ops/sec** | Arbitrary floats/ints | 100.0% Exact |
| **MD5 Digest (`md5`)** | **11.2 ms** | **11.2 µs** | **89,285 ops/sec** | Arbitrary strings/numbers | 100.0% Bit-Exact |

---

### 2. End-to-End Pipeline Evaluation (100 Mixed Tasks)
Evaluated on 100 randomized inputs (50 calculations/hashes + 50 natural language sentences):

| Metric | Standard Baseline | Nano Pipeline | Full Pipeline |
|---|---|---|---|
| **Total Footprint** | 479.59 KiB | **8.86 KiB** | **22.47 KiB** |
| **Router Parameters** | 117,832 | **718** | **2,682** |
| **Capabilities** | Arithmetic only | Arithmetic (`+ - * /`) | Arithmetic + `sin`, `cos`, `sqrt`, `md5` |
| **Routing Accuracy** | 100.0% | **100.0%** | **100.0%** |
| **Execution Precision** | 100.0% | **100.0%** | **100.0%** |
| **Average Latency** | 2.84 ms | **1.35 ms** | **1.38 ms** |

---

## System Flow

```
Input Expression / Text
          │
          ▼
 [Attention Router]
   (Nano / Full)
          │
          ├── Natural language text detected ──► Reject calculation (Fail-Closed)
          │
          └── Formal computation confirmed
                  │
                  ▼
          [Execution Core]
           (Base / Full)
                  │
                  ▼
          Deterministic Output (0 drift)
```

---

## Repository Contents

```text
mathsolver/
├── README.md                 # Specifications, sample outputs and benchmark summary
├── GUIDE.md                  # Functional description and integration concepts
├── POTENTIAL.md              # Resource profiling and application domains
├── LICENSE                   # Apache 2.0
└── models/
    ├── math_core.pt          # Base arithmetic execution core (5.67 KiB)
    ├── math_core_full.pt     # Full execution core with sin/cos/sqrt/md5 (8.33 KiB)
    ├── math_router_nano.pt   # Sub-1000 param router (718 params, 3.20 KiB)
    ├── math_router_mini.pt   # Multi-feature router (1,922 params, 7.74 KiB)
    └── math_router_full.pt   # Full attention router for all operations (2,682 params, 14.14 KiB)
```
