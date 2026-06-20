# HDLint Rule Verification

## Description

HDLint is a static analysis tool for SystemVerilog and Verilog RTL designs. It enforces coding rules and design guidelines at the source level — before simulation or synthesis — catching issues such as unintended latches, multi-driven nets, illegal FSM states, unsafe clock practices, and style violations.

This repository documents the verification of HDLint's rule set through a structured test suite. Each test case targets a specific rule, provides a minimal RTL trigger, and records whether the tool correctly detects the violation. The goal is to validate rule coverage, surface false negatives or misleading diagnostics, and track compatibility across ANSI and NON-ANSI port styles.

---

## Status Overview

| Category | Total | Verified | Pending | Issues |
|----------|-------|----------|---------|--------|
| 4.1 Structural & Type | 23 | 22 | 1 | 5 |
| 4.2 Clock | 3 | 3 | 0 | 0 |
| 4.3 Reset | 2 | 2 | 0 | 0 |
| 4.4 Simulation | 3 | 3 | 0 | 1 |
| 4.5 Case / Sim-Synth | 5 | 5 | 0 | 0 |
| 4.6 FSM | 5 | 5 | 0 | 0 |
| 4.7 Assignment | 7 | 7 | 0 | 0 |
| 4.8 Initialization | 1 | 1 | 0 | 0 |
| 4.9 Top-Level | 2 | 2 | 0 | 0 |
| 4.10 Maintainability | 2 | 2 | 0 | 0 |
| 4.11 Style | 6 | 6 | 0 | 0 |
| **Total** | **59** | **58** | **1** | **6** |

> **Legend** —  Verified ·  Pending ·  Issue ·  Disabled by default

---

## 4.1 — Structural & Type Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.1.1 | ENUM-DUP | Two enum members share the same encoded value |   | — |
| 4.1.2 | ENUM-RANGE | Enum member overflows or contains X/Z bits |   | — |
| 4.1.3 | ENUM-WIDTH | Enum literal width mismatches base-type width |   | — |
| 4.1.4 | PARAM-NOINIT | Parameter or localparam not initialized |   | NON-ANSI only; fails on ANSI format |
| 4.1.5 | PARAM-OVERRIDE | Duplicate, mixed, or missing parameter override |   | NON-ANSI only |
| 4.1.6 | STRUCT-CASE-INCOMPLETE | Case without full coverage in combinational context | Detects only when `casez` is used |
| 4.1.7 | STRUCT-CASE-OVERLAP | Overlapping or unreachable case item |   | — |
| 4.1.8 | STRUCT-COMB-OVERLOOP | Signal combinationally depends on itself |   | — |
| 4.1.9 | STRUCT-INPUT-ASSIGN | Input port driven from inside the module |   | Tool reports *Coercion* not *ASSIGN* |
| 4.1.10 | STRUCT-LATCH | Unintended latch inferred |   | — |
| 4.1.11 | STRUCT-MULTIDRIVEN | Net driven by more than one driver |   | Reported as *duplicate definition* |
| 4.1.12 | STRUCT-PORT-WIDTH | Port width mismatch at instantiation |   | — |
| 4.1.13 | STRUCT-RANGE | Constant index or part-select out of declared bounds |   | — |
| 4.1.14 | STRUCT-UNDRIVEN | Net/variable/output exposed but never driven |   | — |
| 4.1.15 | STRUCT-WIDTH-TRUNC | Value implicitly narrowed, dropping high-order bits |   | — |
| 4.1.16 | DEAD-ASSIGN | Signal assigned but never read |   | — |
| 4.1.17 | STRUCT-CASE-UNREACHABLE | Case item can never match the selector |   | — |
| 4.1.18 | STRUCT-NONPOS-WIDTH | Part-select width or replication count resolves to zero/negative |   | Reported as *Range Selected Reversed* |
| 4.1.19 | STRUCT-PIN-MISSING | Instance leaves a port unconnected |   | — |
| 4.1.20 | STRUCT-SIGN-MIX | Implicit signedness change or mixed-sign comparison |   | — |
| 4.1.21 | STRUCT-UNUSED | Unused signal, port, or parameter |   | — |
| 4.1.22 | STRUCT-WIDTH-EXPAND | Operand implicitly widened |   | — |
| 4.1.23 | STRUCT-ZERO-WIDTH | Declared vector elaborates to zero/negative width |   | — |

---

## 4.2 — Clock Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.2.1 | CLK-001 | Clock net driven by combinational logic outside an approved cell |   | — |
| 4.2.2 | CLK-002 | Clock net read in a non-clock (data) context |   | — |
| 4.2.3 | CLK-004 | Clock used on both posedge and negedge (single-edge policy) |   | — |

---

## 4.3 — Reset Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.3.1 | RST-002 | Async reset must be active-low when policy `reset_style async_active_low` is set |   | — |
| 4.3.2 | RST-006 | Single flop has both async set and async reset |   | — |

---

## 4.4 — Simulation Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.4.1 | SIM-001 | Sequential NB assignment lacks a shoot-through delay |   | Requires `set_policy shoot_through_delay required` |
| 4.4.2 | SIM-002 | Physical delay on a clock-path assignment |   | Not detected; fires as CLK-002 instead |
| 4.4.3 | SIM-003 | Delay present when shoot-through delay is forbidden |   | Requires `set_policy shoot_through_delay forbidden` |

---

## 4.5 — Case & Simulation-Synthesis Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.5.1 | CASE-001 | `full_case` / `parallel_case` pragma present |   | Flags pragma only when it can affect synthesis |
| 4.5.2 | CASE-002 | `casex` used in design |   | Also visible at compile time |
| 4.5.3 | SIMSYN-001 | Case-equality (`===` / `!==`) in synthesizable code |   | Verified for both operators |
| 4.5.4 | SIMSYN-002 | `initial` block in synthesizable RTL |   | — |
| 4.5.5 | CASE-003 | `casez` used outside approved decoder patterns |   | — |

---

## 4.6 — FSM Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.6.1 | FSM-001 | FSM state variable not enum-typed |   | Disabled by default |
| 4.6.2 | FSM-002 | FSM deadlock — state cannot return to reset state |   | — |
| 4.6.3 | FSM-003 | Unreachable FSM state |   | — |
| 4.6.4 | FSM-004 | Case over enum without full coverage |   | Disabled by default |
| 4.6.5 | FSM-005 | FSM terminal state with no exit transition |   | Same RTL trigger as FSM-003 |

---

## 4.7 — Assignment Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.7.1 | ASGN-001 | Signal assigned in multiple `always` blocks |   | — |
| 4.7.2 | ASGN-002 | Blocking assignment inside a sequential block |   | — |
| 4.7.3 | ASGN-003 | Non-blocking assignment inside a combinational block |   | — |
| 4.7.4 | ASGN-004 | Mixed blocking and non-blocking assignments to one signal |   | — |
| 4.7.5 | ASGN-005 | Non-blocking assignment to a clock net |   | — |
| 4.7.6 | ASGN-006 | Procedural assignment to a net |   | — |
| 4.7.7 | ASGN-007 | Legacy `always` used where `always_comb`/`always_ff` is available |   | — |

---

## 4.8 — Initialization Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.8.1 | INIT-002 | `initial` block sets a signal also driven by an `always` block |   | ANSI + NON-ANSI |

---

## 4.9 — Top-Level Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.9.1 | TOP-001 | Structure-only module contains `always` or `initial` blocks |   | ANSI + NON-ANSI |
| 4.9.2 | TOP-002 | No top module declared with multiple modules present |   | ANSI + NON-ANSI |

---

## 4.10 — Maintainability Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.10.1 | MAINT-001 | Excessive `always` blocks in a single file |   | ANSI + NON-ANSI |
| 4.10.2 | MAINT-002 | File exceeds maximum line count |   | Disabled by default |

---

## 4.11 — Style Rules

| ID | Rule | Description | Status | Notes |
|----|------|-------------|--------|-------|
| 4.11.1 | STYLE-EXPLICIT-PORT | Ports must be connected by name, not by position |   | ANSI + NON-ANSI |
| 4.11.2 | STYLE-GENERATE-LABEL | All `generate` blocks must have a label |   | ANSI + NON-ANSI |
| 4.11.3 | STYLE-HEADER | File must begin with a descriptive comment header |   | ANSI + NON-ANSI |
| 4.11.4 | STYLE-NAME-MODULE-FILE | Module name must match the filename |   | ANSI + NON-ANSI |
| 4.11.5 | STYLE-NAME-PARAM | Parameter names must be ALL_CAPS | . | ANSI + NON-ANSI |
| 4.11.6 | STYLE-ONE-MODULE | Only one module allowed per file |   | ANSI + NON-ANSI |

---

## Rules Requiring Manual Activation

The following rules are disabled by default or require a policy directive to fire.

| Rule | Action Required |
|------|----------------|
| FSM-001 | Enable FSM enum-type checking in tool configuration |
| FSM-004 | Enable FSM full-case coverage checking |
| MAINT-002 | Enable file line-count limit |
| SIM-001 | Add `set_policy shoot_through_delay required` |
| SIM-003 | Add `set_policy shoot_through_delay forbidden` |

---

## Compatibility Notes

Rules marked **NON-ANSI only** do not function correctly with ANSI-style port declarations.

| Rule | Compatibility |
|------|--------------|
| PARAM-NOINIT (4.1.4) | NON-ANSI only |
| PARAM-OVERRIDE (4.1.5) | NON-ANSI only |
| All others | ANSI + NON-ANSI |
