# Integration Guide

This guide outlines the conceptual pipeline and operational behavior of the provided models.

---

## 1. Pipeline Stages

The system decouples natural language understanding from computational execution:

```text
Step 1: Input Evaluation
  - The router evaluates the incoming token stream via self-attention.
  - Distinguishes computational commands from conversational language.
  - If conversational text is present, the gate remains inactive (Fail-Closed).

Step 2: Core Execution
  - If formal computation is confirmed, the expression is delegated to the execution core.
  - Returns deterministic numerical solutions or cryptographic hashes without statistical drift.
```

---

## 2. Model Variants

### A. Router Modules
- **`math_router_nano.pt` (718 params, 3.20 KiB):**
  Specialized for 4-operator arithmetic (`+`, `-`, `*`, `/`) up to 12 digits per operand. Designed for extreme parameter budgets (< 1,000 parameters).
- **`math_router_mini.pt` (1,922 params, 7.74 KiB):**
  Balanced router variant with multi-head attention support for complex expression formatting.
- **`math_router_full.pt` (2,682 params, 14.14 KiB):**
  Full router supporting arithmetic, non-linear functions (`sqrt`), trigonometry (`sin`, `cos`), and cryptographic hashing (`md5`).

### B. Execution Core Modules
- **`math_core.pt` (5.67 KiB):**
  Base execution core for multi-digit addition, subtraction, multiplication, and division up to 100+ digits.
- **`math_core_full.pt` (8.33 KiB):**
  Extended core supporting arbitrary multi-digit arithmetic plus:
  - `sin(deg/rad)`: Discretized trigonometric sine calculation.
  - `cos(deg/rad)`: Discretized trigonometric cosine calculation.
  - `sqrt(x)`: Exact square root for perfect squares; precision float for arbitrary numbers.
  - `md5(data)`: Deterministic 128-bit MD5 hex digest for strings and numerical payloads.

---

## 3. Key Properties

- **Zero Hallucination:** Operations are solved deterministically rather than generated via statistical next-token prediction.
- **Strict Decoupling:** Isolating formal computation allows the language router to maintain minimal footprint.
- **Low-Latency Execution:** The combined pipeline processes queries in 1.35 to 1.40 ms on standard single-core CPUs.

---

## 4. Input Validation & Diagnostics

When presented with non-computational natural language, empty queries, or syntax anomalies, the system returns a concise diagnostic message referencing the documentation:

```text
Query:  "What is the weather like today?"
Output: "[MathSolver] Non-computational input rejected. Supported: +-*/, sin(), cos(), sqrt(), md5(). Guide: https://github.com/dazzafact7/mathsolver"

Query:  ""
Output: "[MathSolver] Empty input. Guide: https://github.com/dazzafact7/mathsolver"
```
