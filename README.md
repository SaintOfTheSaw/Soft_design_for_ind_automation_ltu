# Luggage Transport Controller

This repository contains the documentation and logic overview for the Luggage Transport Controller system. The system utilizes a Master/Slave distributed architecture over OPC-UA to manage vertical and horizontal pneumatic cylinders for luggage transport.

## Requirements

### Initialization Requirements
* **IR-01:** INITIAL POSITION for the system is when all cylinders are in the retracted state.

### Transport Requirements
* **TR-01:** WHEN luggage enters the lift platform, the Luggage Transport Controller shall extend the vertical cylinder.
* **TR-02:** WHEN luggage is lifted up, the Luggage Transport Controller shall extend the horizontal cylinder.
* **TR-03:** WHEN luggage is pushed off the platform, the Luggage Transport Controller shall retract the horizontal and vertical cylinders.
* **TR-04:** WHILE the platform is empty and the system is in the initial state, the Luggage Transport Controller should wait for the next luggage.

### Safety Requirements
* **SR-01:** IF the horizontal cylinder is extracted, THEN the vertical cylinder should not extract.

---

## System Architecture

### Master/Slave Distributed Architecture
The control system consists of 3 main blocks: one Master Luggage Transport Controller block and two Slave blocks (for the Vertical and Horizontal cylinders).

#### Slave Blocks (Lift & Pusher)
* Responsible for setting model inputs (`LiftExt`, `LiftRet`, and `PushExt`) and reading data from model sensors.
* Send confirmation to the Master block once a cylinder is successfully retracted or extended.

#### Master Block (MasterControl)
* Receives information regarding the luggage status (`LuggageLoaded` and `LuggageLifted`).
* Sends commands to the Slave cylinders to retract or extend and waits to receive confirmation from them.

<p align="center">
  <img src="/Documentation/diagrams/out/sequence_diagram.png" width="600">
  <br>
  <i>Figure 1: Sequence diagram</i>
</p>

---

## Requirements Traceability Matrix

| Requirement | Design Element | How the Requirement is Addressed |
| :--- | :--- | :--- |
| **IR-01** | Lift and Pusher Controller | Cylinders go to initial state only after sensors indicate they are retracted. |
| **TR-01** | Master Control | Handled via the *Lift luggage* state. |
| **TR-02** | Master Control | Handled via the *Push luggage* state. |
| **TR-03** | Master Control | Handled via the *Luggage pushed* state. |
| **TR-04** | Master Control | Handled via the *Initial* state. |
| **SR-01** | Master Control | The `Initial -> LiftLuggage` state transition contains a check for the Pusher cylinder status. |

---

## Communication (OPC-UA)

The system relies on the OPC-UA protocol for communication between the model and the controllers:
* **The Model** acts as an OPC-UA server that exposes the luggage status data.
* **The Master Control and Cylinder Controllers** act as OPC-UA clients.
* **Master Control** reads data from the server regarding the current luggage status.
* **Cylinder Controls** read data from the server regarding cylinder sensor states and send values back to the server for the actuators. 
* Finally, the **Master** reads data from the cylinder controls and sends them events to coordinate the correct work cycle.
