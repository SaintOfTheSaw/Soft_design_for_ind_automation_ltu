# IEC 61499 Function Blocks Implementation

## 1. Introduction 
The goal of this assignment is to develop skills in creating basic and composite function blocks [cite: 1]. The task is to implement functions from IEC 61499 and verify and validate the implemented blocks [cite: 1].

## 2. Basic Function Blocks 
This section describes the purpose, event/data interfaces, Execution Control Charts (ECC), algorithms used, and the observed execution behavior of the implemented Basic Function Blocks [cite: 1].

### 2.1 Increasing Counter (E_CTU)
**Requirements:**
The `E_CTU` block shall set `Q` to TRUE when the predefined value of `CV` is reached [cite: 1]. When `Reset` is triggered, the `E_CTU` shall stop counting and trigger the `RO` event [cite: 1]. The block must follow this timing diagram:

*[Insert Timing Diagram Here]* [cite: 1]

**Implementation:**
*   **Interface:** 
    *   **Inputs:** 2 input events (`CU` and `R`), and an input `INT` value `PV` associated with the `CU` event [cite: 1]. 
    *   **Outputs:** 2 output events (`CUO` and `RO`), and 2 output data variables `Q` (BOOL) and `CV` (INT) both associated with the `CUO` and `RO` events [cite: 1].
*   **Execution Control Chart (ECC):** Consists of 3 states: START, COUNT, and RESET [cite: 1].
*[Insert ECC Screenshot Here]* [cite: 1]
    *   **State RESET:** Triggers event `RO` and executes algorithm `A_RESET` [cite: 1]. The algorithm sets `Q` to false and `CV` to 0 [cite: 1].
    *   **State COUNT:** Triggers event `CUO` and executes `A_COUNT` [cite: 1]. The algorithm increases `CV` by 1, sets `CU` back to False, and then checks if `CV` has reached `PV` [cite: 1]. If so, `Q` is set to True; else, `Q` is set to False [cite: 1].

**Testing, Verification, and Validation:**
The block can be checked in Debug mode [cite: 1]. The block works correctly and exactly as defined in the timing diagram, successfully copying the standard library block behavior [cite: 1].

### 2.2 RS Flip-Flop (E_RS)
**Requirements:**
The library implements `E_RS` and `E_SR` with the same functionality; in this assignment, `E_RS` is implemented [cite: 1].
The `E_RS` block shall set `Q` to TRUE when an event is triggered on the `S` input [cite: 1]. `Q` should be reset to 0 when event `R` is triggered [cite: 1]. An output event is triggered when the `Q` value is changed [cite: 1]. The block must follow this timing diagram:

*[Insert Timing Diagram Here]* [cite: 1]

**Implementation:**
*   **Interface:** 
    *   **Inputs:** 2 input events (`S` and `R`) and no input data [cite: 1]. 
    *   **Outputs:** 1 output event (`EO`) and a `BOOL` value `Q` associated with it [cite: 1].
*   **Execution Control Chart (ECC):** Consists of 3 states: START, SET, and RESET [cite: 1].
*[Insert ECC Screenshot Here]* [cite: 1]
    *   **State SET:** Triggers event `EO` and executes algorithm `A_SET` [cite: 1]. The algorithm sets `Q` to 1 [cite: 1].
    *   **State RESET:** Triggers event `EO` and executes algorithm `A_RESET` [cite: 1]. The algorithm sets `Q` to 0 [cite: 1].

**Testing, Verification, and Validation:**
The block can be checked in Debug mode [cite: 1]. It works correctly according to the defined timing diagram and copies the library block behavior [cite: 1].

---

## 3. Composite Function Blocks
This section covers the purpose of the Composite Function Blocks, shows the Function Block network, explains how the Basic and Standard Library Function Blocks work together, and validates the implementation against the supplied specifications [cite: 1].

### 3.1 Event Train (E_TRAIN)
**Requirements:**
After the `START` event is triggered, the `EO` event shall be triggered `N` times every `delayTime` interval [cite: 1]. After `N` times triggered `EO` or if a `STOP` event occurs, the block shall stop [cite: 1]. The block must follow this timing diagram:

*[Insert Timing Diagram Here]* [cite: 1]

> **Note:** The library-implemented E_Train also requires a STOP event after every START event [cite: 1].

**Implementation:**
*   **Interface:** 
    *   **Inputs:** 2 input events (`START` and `STOP`) [cite: 1]. Input `UINT` value `N` is associated with the `START` and `STOP` events [cite: 1]. Input value `delayTime` (type TIME) is associated with the `START` event [cite: 1].
    *   **Outputs:** Output event `EO` [cite: 1]. Output data `CV` (UINT) associated with `EO` [cite: 1].
*   **Function Block Network:**
*[Insert Network Screenshot Here]* [cite: 1]
    *   `E_CYCLE`: Triggers time intervals [cite: 1].
    *   `E_CTU`: Counts how many times `E_CYCLE` triggers an output [cite: 1].
    *   `E_PERMIT`: Triggers stop and reset when the `N` value is reached [cite: 1].
    *   `TWO_I_TWO_O`: A composite block consisting of `E_MERGE` and `E_SPLIT` to make a block with two input and output events [cite: 1].
*[Insert TWO_I_TWO_O Screenshot Here]* [cite: 1]

**Testing, Verification, and Validation:**
We can check the block in the Application using the Watch function and running a simulation of the network [cite: 1]. The block works correctly according to the defined time diagram [cite: 1].

### 3.2 Boolean Falling Edge (E_F_TRIGGER)
**Requirements:**
`EO` is triggered when a transition of `QI` from TRUE to FALSE is detected [cite: 1]. If `QI` does not change, or changes from FALSE to TRUE, no `EO` is fired [cite: 1]. The block must follow this timing diagram:

*[Insert Timing Diagram Here]* [cite: 1]

**Implementation:**
*   **Interface:** 
    *   **Inputs:** Input event `EI` and a `BOOL` data `QI` associated with this event [cite: 1]. 
    *   **Outputs:** Output event `EO` and no output variables [cite: 1].
*   **Function Block Network:**
*[Insert Network Screenshot Here]* [cite: 1]
    *   `E_D_FF`: Reacts to the changing of `QI` [cite: 1].
    *   `E_PERMIT`: With `NOT`, it triggers `EO` when the D flip-flop is detected on a LOW state [cite: 1].

**Testing, Verification, and Validation:**
Checked in the Application using the Watch function and running a simulation of the network [cite: 1]. The block works correctly as defined in the time diagram and successfully copies the behavior of the library block [cite: 1].
