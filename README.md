# Logic-Gate Differential Line Follower
### Engineering Research Project | E/23/085

![Status](https://img.shields.io/badge/Phase-PCB_Design-orange) ![Type](https://img.shields.io/badge/Hardware-No_Microcontroller-red)

# Phase 1: Mathematical Modeling & Logic Reduction
**Status:** ✅ Completed
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
* **1x 74HC32 (OR):** To combine the steering logic.

---

# Phase 2: Mixed-Signal Simulation & Verification
**Status:** ✅ Verified
**Tools:** Falstad Circuit Simulator (Manual Build)

## 2.1 Simulation Setup
Due to the complexity of mixed-signal modeling (Analog + Digital), I constructed a manual test bench to verify the control logic.
* **Logic Core:** Implemented the reduced Boolean equations ($LM = R + C\bar{L}$) using discrete NOT, AND, and OR gates.
* **Test Protocol:** Inputs were manually toggled to simulate the robot drifting left/right over the track line.
* **Indicators:** LEDs configured to represent Left and Right Motor drive states.

## 2.2 Experimental Results
The simulation successfully demonstrated the differential steering behavior:

### Test Case A: Center Line Tracking (Straight)
* **Input State:** `L=0`, `C=1`, `R=0`
* **Observation:** Both Left and Right Motor LEDs remained **Solid ON** (Logic High).
* **Conclusion:** The robot drives forward at full speed when centered.

### Test Case B: Soft Turn Correction
* **Input State:** `L=0`, `C=1`, `R=1` (Drifting Left)
* **Observation:**
    * **Left Motor:** Remained Solid ON (Maintain forward drive).
    * **Right Motor:** Turned OFF.
* **Conclusion:** The logic successfully cut power to the inner wheel to correct the path.

### 2.3 Visual Proof
<img width="881" height="260" alt="Simulation Proof" src="https://github.com/user-attachments/assets/c712121c-325a-4e11-8273-a3a06e7b1e93" />

**Figure 2.1:** Falstad simulation verifying the logic states.

---

# Phase 3: PCB Design & Hardware Architecture
**Status:** 🚧 In Progress (Schematic Completed, PCB Layout Started)
**EDA Tool:** EasyEDA (Standard Edition)

## 3.1 Control Architecture Revision (Refinement)
During the schematic capture phase, I made a critical engineering decision to simplify the control topology.
* **Decision:** Removed the PWM Mixing Stage (NE555 Timer).
* **Rationale:** Adopting a **Pure Boolean Direct-Drive** architecture eliminates signal propagation delay and potential EMI noise from onboard switching frequencies. This prioritizes "Bang-Bang" steering response speed over variable speed control, which is more robust for high-speed line tracking on high-contrast tracks.
* **New Power Strategy:** Upgraded from generic 9V standard to **2S Li-Ion (7.4V)** configuration. This provides high-current discharge capability to prevent logic brownouts during motor stall conditions.

## 3.2 Schematic Implementation (Verified)
The circuit has been successfully digitized in EasyEDA with the following professional-grade components:
* **Power Regulation:** L7805 (TO-220) to step down 7.4V $\rightarrow$ 5.0V Logic Power.
* **Logic Core:** 74HC04 (NOT), 74HC08 (AND), 74HC32 (OR) implemented in DIP packages for serviceability.
* **Motor Drive:** L293D H-Bridge configured for non-inverting direct drive.
* **Connectivity:** Standard 2.54mm Headers for modular sensor replacement.

## 3.3 PCB Layout (Current Status)
* **Dimensions:** 80mm x 60mm (Compact Chassis Profile).
* **Placement Strategy:** "Sensor-Forward" design with rear-mounted power distribution to optimize center-of-gravity.
* **Progress:** Board outline defined; component placement finalized. Routing of high-current power traces pending.
