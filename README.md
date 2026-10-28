# MathSolver: Dual-Module Architecture for Exact Arithmetic

A compact dual-module neural architecture combining a lightweight attention router with a dedicated arithmetic core for deterministic multi-digit calculations.

---

## Sample Benchmark & Execution Outputs

The pipeline detects mathematical intent directly from raw character streams and produces bit-exact solutions:

| Input Expression | Type | Output Result | Latency | Status |
|---|---|---|---|---|
| `19 * 3` | Arithmetic (Multiplication) | `57` | 1.36 ms | ✅ 100% Bit-Exact |
| `4 * (3 - 1)` | Arithmetic (Parentheses) | `8` | 1.38 ms | ✅ 100% Bit-Exact |
| `12345678 * 87654321` | Arithmetic (8x8 Digits) | `1082152022374638` | 1.41 ms | ✅ 100% Bit-Exact |
| `123456789012 + 987654321098` | Arithmetic (12+12 Digits) | `1111111110110` | 1.39 ms | ✅ 100% Bit-Exact |
| `999999999999 - 111111111111` | Arithmetic (12-Digit Sub) | `888888888888` | 1.37 ms | ✅ 100% Bit-Exact |
| `100000000000 / 2500` | Arithmetic (Division) | `40000000` | 1.42 ms | ✅ 100% Bit-Exact |
| `"What is 100 divided by 4?"` | Natural Language Context | *Rejected* | 1.35 ms | 🛡️ Fail-Closed (No Drift) |
| `"Peter has 5 apples and 3 pears"` | Conversational Sentence | *Rejected* | 1.34 ms | 🛡️ Fail-Closed (No Drift) |

---

## Benchmarks

### 1. Standalone Arithmetic Core (`math_core.pt`, 5.67 KiB)
Evaluated directly across 1,000 arithmetic operations per category on model weights:

| Operation | Total Time (1,000 Ops) | Latency / Op | Throughput | Tested Range | Precision |
|---|---:|---:|---:|---|:---:|
| **Multiplication (`*`)** | **14.8 ms** | **14.8 µs** | **67,463 ops/sec** | Up to 50x50 digits | 100.0% Exact |
| **Addition (`+`)** | **18.1 ms** | **18.1 µs** | **55,217 ops/sec** | Up to 100 digits | 100.0% Exact |
| **Subtraction (`-`)** | **15.1 ms** | **15.1 µs** | **66,053 ops/sec** | Up to 100 digits | 100.0% Exact |
| **Division (`/`)** | **73.8 ms** | **73.8 µs** | **13,554 ops/sec** | Up to 50 digits | 100.0% Exact |
| **Mixed Operations (`+`, `-`, `*`, `/`)** | **29.0 ms** | **29.0 µs** | **34,469 ops/sec** | Multi-digit terms | 100.0% Exact |

---

### 2. Full Dual-Module Pipeline (100 Mixed Tasks)
Evaluated on 100 randomized inputs (50 raw arithmetic formulas up to 12 digits + 50 natural language sentences with numbers):

| Metric | Standard Model Baseline | Nano MathSolver Pipeline | Efficiency Gain |
|---|---|---|---|
| **Total Footprint** | 479.59 KiB | **8.86 KiB** | **54.1x smaller** |
| **Router Parameters** | 117,832 | **718** | **38.6x fewer parameters** |
| **Routing Accuracy** | 100.0% (100/100) | **100.0% (100/100)** | *Identical (Lossless)* |
| **Arithmetic Precision** | 100.0% (50/50) | **100.0% (50/50)** | *Bit-exact across all digits* |
| **Average Latency / Task** | 2.84 ms | **1.35 ms** | **2.11x faster** |
| **Total Throughput** | 352.2 ops/sec | **743.4 ops/sec** | **+111.1% throughput** |

---

## Specifications

| Metric | Router (`math_router_nano.pt`) | Core (`math_core.pt`) | Full Pipeline |
|---|---|---|---|
| **Architecture** | Lightweight Attention Module | Dedicated Arithmetic Module | Dual-Module System |
| **Parameters** | 718 | Compact Matrix | 2,984 equivalent |
| **File Size** | 3.19 KiB | 5.67 KiB | 8.86 KiB |
| **Input Format** | Character stream | Formal Expression | Raw Text / Formula |
| **Operators** | Detection: `+`, `-`, `*`, `/` | Execution: `+`, `-`, `*`, `/` | `+`, `-`, `*`, `/` |
| **Tested Range** | Up to 12 digits per operand | Tested up to 100+ digits | 100+ digits |
| **Accuracy** | 100.0% routing precision | 100.0% numerical precision | 100.0% bit-exact |
| **Inference Time** | ~1.38 ms | ~0.02 ms | ~1.40 ms |

---

## System Flow

```
Input Expression
      │
      ▼
[Router (3.19 KiB)]
      │
      ├── Natural language text detected ──► Reject calculation (Fail-Closed)
      │
      └── Raw arithmetic confirmed
              │
              ▼
      [Arithmetic Core (5.67 KiB)]
              │
              ▼
      Deterministic Result (Exact, 0 drift)
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
    ├── math_core.pt          # Dedicated arithmetic module (5.67 KiB)
    ├── math_router_nano.pt   # Compact router (718 params, 3.19 KiB)
    └── math_router_mini.pt   # Alternative multi-feature router (7.74 KiB)
```
