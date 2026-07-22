<div align="center">

# 🔲 RTL-to-GDSII Physical Design Flow - SRAM

**Complete digital physical design implementation: RTL → Synthesis → Place & Route → GDSII**

![Flow](https://img.shields.io/badge/Flow-RTL_to_GDSII-blue)
![Tools](https://img.shields.io/badge/Tools-Genus_%7C_Innovus_%7C_NCLaunch-orange)
![Status](https://img.shields.io/badge/Timing-Clean_(WNS_0.008ns)-success)
![Signoff](https://img.shields.io/badge/DRC%2FLVS-Clean-success)

</div>

---

## 🧠 Overview

This project implements the complete **digital physical design flow** for an SRAM controller, from RTL description through synthesis, floorplanning, placement, clock tree synthesis, routing, and final GDSII generation using the industry-standard Cadence digital toolchain (NCLaunch, Genus, Innovus).

The goal wasn't just to push a design through the flow, but to **resolve real timing violations along the way** the project includes a documented case of a setup-timing failure (WNS = ‑0.815 ns) at the placement stage, which was iteratively optimized to timing closure (WNS = +0.008 ns) before proceeding to CTS and routing.

## 🎯 Objectives

- Design and functionally verify an SRAM controller at RTL (Verilog) with a clean FSM structure
- Synthesize to a gate-level netlist under timing/power/area constraints (Cadence Genus)
- Execute full physical implementation floorplan, power plan, place, CTS, route (Cadence Innovus)
- Achieve timing closure (zero violating paths) prior to tape-out-ready GDSII export
- Produce a DRC/LVS-clean, manufacturable GDSII layout

## 🔁 Physical Design Flow

```
RTL (Verilog) → Synthesis (Genus) → Floorplan + Power Plan (Innovus)
→ Placement → Pre-CTS Timing Check → Clock Tree Synthesis
→ Detailed Routing (NanoRoute) → Post-Route Timing Sign-off → GDSII
```

| Stage | Tool | Purpose |
|---|---|---|
| RTL Design & Simulation | Cadence NCLaunch | Functional verification of the SRAM FSM |
| Synthesis | Cadence Genus | RTL → gate-level netlist, under timing/power/area constraints |
| Floorplanning & Power Planning | Cadence Innovus | Chip area definition, macro placement, power ring/stripe/rail planning |
| Placement | Cadence Innovus | Standard-cell placement optimized for wirelength & congestion |
| Pre-CTS Timing Analysis | Cadence Innovus | WNS/TNS check prior to clock tree synthesis |
| Clock Tree Synthesis (CTS) | Cadence Innovus | Balanced clock distribution, skew minimization |
| Detailed Routing | NanoRoute (Innovus) | Timing-driven, SI-driven multi-layer routing |
| Sign-off & GDSII Export | Cadence Innovus | DRC/LVS/timing verification → final GDSII |

## 🧩 RTL Design SRAM Controller

**Module I/O:**

| Signal | Direction | Width | Description |
|---|---|---|---|
| `clk` | Input | 1 | Clock signal |
| `addr` | Input | 4-bit | Memory address |
| `we` | Input | 1 | Write-enable |
| `data_in` | Input | 8-bit | Data to be written |
| `data_out` | Output | 8-bit | Data read from memory |

**FSM States:** `Idle` → `Read` / `Write`, with functional correctness verified via RTL simulation in NCLaunch across all state transitions.

<p><sub>RTL simulation waveform: SRAM FSM, NCLaunch</sub></p>

## ⚙️ Synthesis (Cadence Genus)

The Verilog RTL was synthesized into a gate-level netlist, mapped to standard cells from the target technology library under timing/area/power constraints. Synthesis runs were automated and standardized via a Tcl script (loading RTL, applying constraints, defining the target library) to ensure consistent, repeatable results across design iterations.

<p><sub>Synthesized gate-level schematic, Cadence Genus</sub></p>

## 🏗️ Physical Implementation (Cadence Innovus)

<details>
<summary><b>Floorplanning & Power Planning</b></summary>
<br>

Chip area and macro placement were defined first, followed by a power distribution network (rings, stripes, rails) engineered to minimize IR drop and ensure consistent supply across the design.

</details>

<details>
<summary><b>SRoute Special Routing</b></summary>
<br>

FollowPin connections were established for accurate pin-aligned routing, vias were generated and cleaned of DRC violations, and initial multi-layer signal routing was completed.

</details>

<details>
<summary><b>Placement</b></summary>
<br>

Standard cells were placed automatically to minimize wirelength and congestion, followed by targeted timing/area optimization.


</details>

<details>
<summary><b>⏱️ Pre-CTS Timing Violation Found & Resolved</b></summary>
<br>

Before CTS, timing analysis surfaced a real setup violation:

| Metric | Before Optimization | After Optimization |
|---|---|---|
| **WNS (Worst Negative Slack)** | ‑0.815 ns | **+0.008 ns** |
| **TNS (Total Negative Slack)** | ‑31.458 ns | **0.000 ns** |
| **Violating Paths** | 82 / 136 | **0 / 136** |
| **Routing Density** | 72.7% | 73.5% |

Placement and routing were iteratively adjusted to close all 82 violating paths, achieving a clean, positive-slack design before proceeding to clock tree synthesis.


</details>

<details>
<summary><b>Clock Tree Synthesis (CTS)</b></summary>
<br>

A balanced clock tree was synthesized with automatic buffer/inverter insertion to minimize skew across all sequential elements. Post-CTS timing re-verification confirmed setup/hold integrity was preserved.


</details>

<details>
<summary><b>Detailed Routing (NanoRoute)</b></summary>
<br>

Final detailed routing was completed with Timing-Driven and SI-Driven options enabled, followed by post-route timing analysis to confirm setup/hold closure.


</details>

## 📊 Final Results

- ✅ **Timing closure achieved:** WNS improved from ‑0.815 ns → **+0.008 ns**, TNS from ‑31.458 ns → **0.000 ns**, all 136 paths passing
- ✅ **Zero DRVs** (max cap / max transition / max fanout / max length) at sign-off
- ✅ **Routing overflow:** 0.13% (H), 0.00% (V) negligible congestion
- ✅ **DRC/LVS clean**, GDSII exported and tape-out ready
  

## 🧰 Tools
`Cadence NCLaunch` · `Cadence Genus` · `Cadence Innovus` · `NanoRoute` · `Verilog` · `Tcl`

## 📚 References
1. Cadence Design Environment Tutorial — ITU.
2. Kim, D. H., Washington State University. *Innovus Tutorial for EE434*.
3. Cadence Community Forum IMPSP-9099 Scan Chain error discussion.

---

