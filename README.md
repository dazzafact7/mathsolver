# MathSolver: Ultra-Compact Neural Models for Exact Computation 🧮

> **Deterministic arithmetic, trigonometry & cryptographic hashing in sub-1,000 parameters and starting at 1.10 KiB.**

**MathSolver** is a modular neural architecture designed for deterministic, 100% bit-exact computation with zero hallucination. By pairing an ultra-compact router (Transformer or pure Non-Transformer MLP) with a deterministic execution core (**1.10 to 1.29 KiB**), it achieves microsecond latencies (<0.41 ms) across multi-digit arithmetic (`+`, `-`, `*`, `/`, `//`), trigonometry (`sin`, `cos`), square roots (`sqrt`), and data integrity hashing (`md5`).

🎯 **Zero Hallucination** • ⚡ **Sub-1k Params (from 718)** • 📦 **< 8 KiB Full Suite** • 📐 **Vector Output**

---

## 1. Quickstart & Installation

### Requirements
- **Python:** 3.8+
- **PyTorch:** `torch >= 1.12.0` (Standard CPU build suffices; no CUDA or GPU required)

```bash
# 1. Clone repository
git clone https://github.com/dazzafact7/mathsolver.git
cd mathsolver

# 2. Install dependencies (pure PyTorch)
pip install torch
```

---

## 2. Sample Execution & Verification

The pipeline detects computational intent directly from character streams, extracts formal operations, and executes bit-exact results without statistical drift:

| Input Expression | Category | Output Result | Latency | Status |
|---|---|---|---:|:---:|
| `19 * 3` | Arithmetic (Multiplication) | `57` | 0.39 ms | ✅ 100% Bit-Exact |
| `12345678 * 87654321` | Arithmetic (8x8 Digits) | `1082152022374638` | 0.41 ms | ✅ 100% Bit-Exact |
| `123456789012 + 987654321098` | Arithmetic (12+12 Digits) | `1111111110110` | 0.40 ms | ✅ 100% Bit-Exact |
| `100000000000 / 2500` | Arithmetic (Large Division) | `40000000` | 0.42 ms | ✅ 100% Bit-Exact |
| `14 // 3` | Arithmetic (Floor Division) | `4` | 0.38 ms | ✅ 100% Bit-Exact |
| `12.5 + 3.25` | Decimal (2 Decimal Places) | `15.75` | 0.39 ms | ✅ 100% Bit-Exact |
| `1 / 1024` | Decimal (Exact Rational) | `0.0009765625` | 0.41 ms | ✅ 100% Bit-Exact |
| `0.111... + 0.222...` (100 Digits) | Extreme Decimal Scaling | Bit-exact 100 digits | 0.45 ms | ✅ 100% Bit-Exact |
| `sin(90)` / `cos(0)` | Trigonometry (Degrees/Rad) | `1` / `1` | 0.39 ms | ✅ 100% Exact |
| `sqrt(144)` | Non-linear (Square Root) | `12` | 0.38 ms | ✅ 100% Exact |
| `md5("hello")` | Cryptography (128-bit MD5) | `5d41402abc4b2a76b9719d911017c592` | 0.40 ms | ✅ 100% Bit-Exact |
| `"What is 100 divided by 4?"` | Natural Language Context | `[MathSolver] Non-computational input rejected.` | 0.27 ms | 🛡️ Fail-Closed |
| `"Peter has 5 apples and 3 pears"` | Conversational Sentence | `[MathSolver] Non-computational input rejected.` | 0.26 ms | 🛡️ Fail-Closed |

---

## 3. Complete Model Suite & Architecture Matrix

MathSolver provides tailored router modules and execution engines across both Transformer and Non-Transformer (MLP) paradigms:

| Module | Checkpoint | Architecture | Size | Parameters | Latency (Fwd) | Primary Capabilities |
|---|---|---|---:|---:|---:|---|
| **Nano Router** | `math_router_nano.pt` | Attention Transformer (1 Block) | **2.20 KiB** | **718** | 0.55 ms | Sub-1k param routing: `+ - * /` vs. language |
| **MLP Router & Vectors** | `math_router_mlp.pt` | **Non-Transformer MLP** (RMSNorm + Skip) | **4.29 KiB** | **1,690** | **0.27 ms** | **Fastest.** 16-dim embeddings, 10-dim probs & all ops |
| **Full Router** | `math_router_full.pt` | Full Attention Transformer | **5.84 KiB** | **2,682** | 0.77 ms | Complete routing: `+ - * / sin cos sqrt md5` |
| **Mini Router** | `math_router_mini.pt` | Multi-Head Transformer | **6.68 KiB** | **3,056** | 1.15 ms | Multi-feature arithmetic intent routing |
| **Arithmetic Core** | `math_core.pt` | Deterministic Weight Matrix | **1.10 KiB** | Discrete | 0.01 ms | Multi-digit arithmetic execution (100+ digits) |
| **Full Core** | `math_core_full.pt` | Extended Weight Matrix | **1.29 KiB** | Discrete | 0.01 ms | Arithmetic + `sin`, `cos`, `sqrt`, `md5` execution |

---

## 4. End-to-End Performance & Benchmark Comparison

Evaluated on mixed workloads (formal calculations, multi-digit operations, decimal precision, functions, and natural language inputs) on a standard single-core CPU:

| Pipeline / Engine | Router Type | Total Footprint | Avg E2E Latency | Throughput | Precision / Accuracy |
|---|---|---:|---:|---:|:---:|
| **MLP Pipeline (Fastest)** | **Non-Transformer MLP** (`math_router_mlp.pt` + `math_core_full.pt`) | **5.58 KiB** | **0.41 ms** | **2,443 ops/sec** | **100.0% Bit-Exact** |
| **Full Transformer Pipeline** | Full Attention Router (`math_router_full.pt` + `math_core_full.pt`) | **7.13 KiB** | **0.65 ms** | **1,548 ops/sec** | **100.0% Bit-Exact** |
| **Nano Transformer Pipeline** | Ultra-Compact Router (`math_router_nano.pt` + `math_core.pt`) | **3.30 KiB** | **0.92 ms** | **1,085 ops/sec** | **100.0% Bit-Exact** |
| **Mini Transformer Pipeline** | Multi-Head Router (`math_router_mini.pt` + `math_core.pt`) | **7.78 KiB** | **1.73 ms** | **578 ops/sec** | **100.0% Bit-Exact** |
| **Standalone Arithmetic Core** | Zero Router Overhead (`math_core.pt`) | **1.10 KiB** | **0.015 ms** | **66,000+ ops/sec** | **100.0% Bit-Exact** |
| **Standalone Full Core** | Zero Router Overhead (`math_core_full.pt`) | **1.29 KiB** | **0.011 ms** | **89,000+ ops/sec** | **100.0% Bit-Exact** |

---

## 5. Usage & Integration Guide

### A. End-to-End Pipeline: Non-Transformer MLP (Fastest & Vector-Enabled)

The MLP variant processes queries in **0.41 ms** without attention matrix overhead and connects directly to the execution core:

```python
from mlp_math_model import MLPMathRouter

# 1. Initialize router and core
router = MLPMathRouter.laden(
    checkpoint_pfad="models/math_router_mlp.pt",
    core_checkpoint="models/math_core_full.pt"
)

# 2. Exact evaluation (Arithmetic, Trigonometry, Hashes)
print(router.loese("19 * 3"))                     # -> '57'
print(router.loese("sin(90) + sqrt(144)"))        # -> '13'
print(router.loese('md5("admin")'))               # -> '21232f297a57a5a743894a0e4a801fc3'

# 3. Fail-Closed protection on conversational language
print(router.loese("What is 100 divided by 4?"))  # -> Rejected (Fail-Closed)
```

### B. Vector Extraction Mode (For Vector Databases & LLM Routing)

Extract continuous semantic representations and discrete operator distribution vectors:

```python
vektor_info = router.extrahiere_vektor("25 * 4")

# 16-dimensional dense embedding (for Vector DB search & retrieval):
emb = vektor_info["embedding_vektor"]

# Gate & operator probabilities:
gate_p = vektor_info["gate_wahrscheinlichkeiten"]      # {'text': 0.000036, 'mathe': 0.999964}
op_p   = vektor_info["operator_wahrscheinlichkeiten"]  # {'ADD': 0.0, 'MUL': 1.0, ...}

# 10-dimensional concatenated continuous vector [Gate + Ops]:
combo  = vektor_info["kombinierter_vektor"]
```

### C. Standalone Core Execution (Zero Overhead: 14 µs)

If input validation is handled upstream and raw execution speed is needed:

```python
from math_solver_full_engine import MathSolverFullEngine

# Load 1.29 KiB execution core
core = MathSolverFullEngine("models/math_core_full.pt")

# Multi-digit operations with 100% precision
result = core.evaluiere_ausdruck("123456789012345 * 987654321098765")
print(result) # -> 121932631112635246901358901125
```

---

## 6. Global Configuration (`config.json`)

All execution pipelines are governed centrally via `config.json`:

```json
{
  "max_digits": 4,
  "allow_negative": true
}
```

- **`max_digits` (Integer, default: `4`):** Defines the maximum number of decimal places for floating-point and rational division results (rounded half-up). Whole numbers remain exact integers without trailing zeros.
- **`allow_negative` (Boolean, default: `true`):** When set to `false`, negative numbers and intermediate subtractions below zero trigger an immediate Fail-Closed rejection.

---

## 7. Model Training & Re-Generation

All routers can be retrained and re-quantized (`float16` + LZMA) through the unified CLI:

```bash
# Train a specific model variant:
python test_scripts/math_solver/train_router.py --model mlp     # Non-Transformer MLP (1,690 params)
python test_scripts/math_solver/train_router.py --model nano    # Nano Attention Transformer (718 params)
python test_scripts/math_solver/train_router.py --model full    # Full Attention Transformer (2,682 params)
python test_scripts/math_solver/train_router.py --model mini    # Multi-Head Transformer (3,056 params)

# Train all models and synchronize checkpoints:
python test_scripts/math_solver/train_router.py --model all

# Freshly render all 5 checkpoints + models:
python test_scripts/math_solver/render_all_checkpoints.py
```

---

## 8. Verification & Test Suite

Run the full verification suite to audit mathematical correctness, weights integrity, and null-probe assertions:

```bash
# 1. Comprehensive 8-stage audit (100% PASS):
python test_scripts/math_solver/test_math_solver_full.py

# 2. Weights integrity & G0-T null-probe check:
python test_scripts/math_solver/pruefe_nullprobe.py

# 3. High-throughput execution benchmark:
python test_scripts/math_solver/benchmark_math_solver.py
```

---

## 9. System Flow & Architecture

```text
Input Expression / Text
          │
          ▼
   [ Router Model ]
 (Transformer / MLP)
          │
          ├── Natural language context detected ──► Fail-Closed Rejection (No Drift)
          │
          ├── Vector Extraction Mode ─────────────► [16-dim Embedding & 10-dim Probs]
          │
          └── Formal computation confirmed
                  │
                  ▼
          [ Execution Core ]
         (1.10 to 1.29 KiB)
                  │
                  ▼
          Deterministic Output (100% Bit-Exact)
```

---

## Author & Maintainer

- **Author:** Stephan Krol
- **Email:** [stephan.krol.83@gmail.com](mailto:stephan.krol.83@gmail.com)
- **Repository:** [https://github.com/dazzafact7/mathsolver](https://github.com/dazzafact7/mathsolver)
- **License:** Apache 2.0
