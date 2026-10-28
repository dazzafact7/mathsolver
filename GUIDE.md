# Integration Guide

This guide outlines the conceptual pipeline and operational behavior of the provided models.

---

## 1. Pipeline Stages

The system decouples natural language understanding from mathematical computation:

```text
Step 1: Input Evaluation
  - The router processes the incoming text via attention mechanisms.
  - Generates confidence scores for mathematical intent vs. text context.
  - If language context is present, the gate remains inactive (Fail-Closed).

Step 2: Arithmetic Evaluation
  - If pure arithmetic is detected, the expression is evaluated by the dedicated core.
  - Returns exact numerical outputs without token-prediction drift.
```

---

## 2. Model Roles

### A. Router Module (`math_router_nano.pt`)
- **Function:** Intent filtering and expression validation.
- **Fail-Closed Principle:** Non-mathematical phrases, mixed conversation, or ambiguous queries are rejected by design. This avoids erroneous execution triggers.

### B. Core Module (`math_core.pt`)
- **Function:** Numerical computation.
- **Behavior:** Operates as a specialized computational unit designed for multi-digit precision across standard operators (`+`, `-`, `*`, `/`).

---

## 3. Key Properties

- **Zero Hallucination:** Computations are evaluated deterministically rather than generated via statistical sampling.
- **Decoupled Footprint:** Offloading formal math from the language processor allows the router to remain under 1,000 parameters.
- **Resource Efficiency:** The entire two-module pipeline executes in under 2 milliseconds on standard CPU cores.
