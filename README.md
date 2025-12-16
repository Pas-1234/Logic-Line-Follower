# Logic-Gate Differential Line Follower
### Engineering Research Project | E/23/085

![Status](https://img.shields.io/badge/Phase-Simulation-blue) ![Type](https://img.shields.io/badge/Hardware-No_Microcontroller-red)
# Phase 1: Mathematical Modeling & Logic Reduction
**Status:** Completed
**Objective:** To derive the optimal Boolean logic for a 3-sensor differential drive system using the minimum number of 74xx series gates.

## 1.1 Sensor State Definition
We define the sensor array inputs $(L, C, R)$ and motor outputs $(LM, RM)$:
* **Logic 1:** Black Line (Active) / Motor ON
* **Logic 0:** White Surface (Inactive) / Motor OFF

## 1.2 Truth Table Analysis
The system must prioritize "Center Line" tracking while handling sharp turns.

| State | L | C | R | LM | RM | Behavior |
| :--- | :-: | :-: | :-: | :-: | :-: | :--- |
| **Straight** | 0 | 1 | 0 | 1 | 1 | Forward |
| **Drift Left** | 0 | 1 | 1 | 1 | 0 | Soft Right Correction |
| **Sharp Right**| 0 | 0 | 1 | 1 | 0 | Hard Right Turn |
| **Drift Right**| 1 | 1 | 0 | 0 | 1 | Soft Left Correction |
| **Sharp Left** | 1 | 0 | 0 | 0 | 1 | Hard Left Turn |
| **Lost** | 0 | 0 | 0 | 0 | 0 | Safety Stop |

## 1.3 Karnaugh Map (K-Map) Reduction
Using K-Map grouping for the 3-variable system, we derived the simplified Boolean expressions to minimize component count.

**Left Motor Equation:**
$$LM = R + (C \cdot \bar{L})$$
*Interpretation: The Left Motor runs if the Right sensor is active OR if the Center is active (and we are not already turning left).*

**Right Motor Equation:**
$$RM = L + (C \cdot \bar{R})$$
*Interpretation: The Right Motor runs if the Left sensor is active OR if the Center is active (and we are not already turning right).*

## 1.4 Hardware Implementation Plan
Based on this derivation, the control circuit requires:
* **1x 74HC04 (NOT):** To invert L and R inputs.
* **1x 74HC08 (AND):** To combine Center signal with Inverted side signals.
* **1x 74HC32 (OR):** To combine the steering logic.ya*
