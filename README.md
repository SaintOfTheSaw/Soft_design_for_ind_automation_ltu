# Elevator Controller Documentation

### Description

This project focuses on engineering the control logic for a three-floor elevator automation system. While the physical system and Human-Machine Interface (HMI) are pre-developed, the core objective is to design the controller that coordinates all system behavior, starting with requirement definition and logical design.

Elevator Operation
The elevator services three levels (Floor 0, Floor 1, and Floor 2). Users can request the elevator in two ways:

* Pressing a floor call button located on any floor.

* Pressing a destination button inside the elevator cabin.

Controller Responsibilities
The implemented controller acts as the central brain of the automation system and is directly responsible for:

* Managing and queuing pending user requests.

* Determining when requests have been successfully completed.

* Controlling the request indicator lights for user feedback.

* Coordinating safe elevator cabin movement and synchronized door operation.

## 1. Requirements

### Transportation
*   **Operation States:** Move UP, move DOWN, STOP, WAIT
*   **TR-01:** WHEN the destination floor sensor becomes active, the Elevator Controller shall stop the elevator.
*   **TR-02:** WHEN doors safely closed and next destination point is defined, the Elevator Controller shall move to that point.

### Request Handling
Multiple requests manager in Direction priority order
*   **RH-01:** WHEN a cabin button is pressed, the Elevator Controller shall register the requested floor.
*   **RH-02:** WHEN a floor button is pressed, the Elevator Controller shall register the requested floor.
*   **RH-03:** WHEN elevator stopped on the floor, the Elevator Controller shall mark as completed.
*   **RH-04:** IF request triggered multiple times(duplicated), request order should NOT be changed.

### Door Operation
*   **DO-01:** WHEN cabin safely stopped on destination point, doors shall be OPENED.
*   **DO-02:** WHEN the elevator door has opened, the Elevator Controller shall keep the door open for 3 seconds and then close the doors.

### Safe Operation
*   **SO-01:** The Elevator Controller can start move cabin only WHEN all doors are closed.
*   **SO-02:** The Elevator Controller can open the doors on each floor only WHEN cabin is stopped on that floor.

### Operator Feedback
*   **OF-01:** WHEN request is registered on the floor button, floor button indicator shall turn ON.
*   **OF-02:** WHEN request is registered on the cabin button, cabin button indicator shall turn ON.
*   **OF-03:** WHEN the request is completed, floor and cabin button indicator shall turn OFF.

### Controller Initialization
Initial condition: elevator is cabin on 2 floor and doors closed
*   **CI-01:** WHEN the controller is initialised, the Elevator Controller shall clear all stored requests.
*   **CI-02:** The Elevator Controller shall set first targetFloor to 2

---

## 2. Logical Design

Design divided to 3 modules:
*   Request panel (buttons inside elevator and on the floors, with indicators) with integrated Queue manager for handling requests, setting queue and decide next target floor
*   Doors operator for safely opening and closing doors
*   Cabin Movement control to control elevator cabin

Communication between modules is showed on sequence diagram:

<p align="center">
  <img src="path/to/Figure_1_Sequence_diagram.png" width="600">
  <br>
  <i>Figure 1: Sequence diagram</i>
</p>

To define key states of cabin control, timing diagram was created:

<p align="center">
  <img src="path/to/Figure_2_Timing_diagram.png" width="800">
  <br>
  <i>Figure 2: Timing diagram</i>
</p>

Timing diagram shows all possible transitions. Elevator behaviour can be divided to 4 main states: going up, going down ,stop and wait
*   Going up sends instruction to go UP
*   Going down sends instruction to go DOWN
*   Stop stops the cabin and communicates with door manager to start cycle of door opening
*   Wait state appears when all requests are complete and elevator is waiting for new request

State diagram shows transitions between these states and door operating logic

<p align="center">
  <img src="path/to/Figure_3_State_diagram.png" width="800">
  <br>
  <i>Figure 3: State diagram</i>
</p>

<p align="center">
  <img src="path/to/Figure_4_Door_State_Machine.png" width="300">
  <br>
  <i>Figure 4: State diagram of STOP state</i>
</p>

---

## 3. Implementation

Some helper blocks were created:

### currentFloor

<p align="center">
  <img src="path/to/currentFloor_block.png" width="600">
</p>

Algorithms set output to current floor value. For example for f0 :
`currentFloor:=0;`

### allClosed

<p align="center">
  <img src="path/to/allClosed_block.png" width="400">
</p>

### Request panel + Queue Manager
Request panel and Queue Manager are implemented in block LED_MANAGER

<p align="center">
  <img src="path/to/LED_MANAGER_block.png" width="400">
</p>

This composite function block consists of
3 RS_LED blocks
Queue_manager

**RS_LED modules**

<p align="center">
  <img src="path/to/RS_LED_block.png" width="400">
</p>

This block sets or resets LEDs 

**Queue Manager**

<p align="center">
  <img src="path/to/Queue_manager_block.png" width="400">
</p>

Queue_manager basic block creates queue. It stores internal value FloorsRequests in INT ARRAY[0..2]
TRUE in FloorsRequests[i] means that is request for floor i. Manager decides next floor (targetFloor) by the current direction elevator moves. It loocks through the FloorsRequests in direction of move. If it reaches edge floor, it starts looking in other direction. If there are no requests it sets queueIsEmpty to True.

<p align="center">
  <img src="path/to/Queue_manager_state_machine.png" width="600">
</p>

### CabinMovementControl

<p align="center">
  <img src="path/to/cabinMovementControl_block.png" width="400">
</p>

Is a basic function block that controls movement of cabin

<p align="center">
  <img src="path/to/cabinMovementControl_state_machine.png" width="600">
</p>

If block in GOING_UP or GOING_DOWN and door would open, elevator will go to state STOP

### Door_manager

<p align="center">
  <img src="path/to/DOOR_MANAGER_block.png" width="400">
</p>

It consists of 
door_control
STOP_SM
TIMER

**Door_control**

<p align="center">
  <img src="path/to/Door_control_block.png" width="400">
</p>

This composite block opens or closes the door. It consists of 3 RS_DOOR blocks

**STOP_SM**

<p align="center">
  <img src="path/to/STOP_SM_block.png" width="400">
</p>

<p align="center">
  <img src="path/to/STOP_SM_state_machine.png" width="600">
</p>

**TIMER**

TIMER it used to count 3 second and also resets counting if button was pushed on the same floor as elevator currently stays

<p align="center">
  <img src="path/to/TIMER_logic.png" width="500">
</p>

---

## 4. Requirement Coverage Table

| Requirement ID | Requirement Summary | Design Evidence |
| :--- | :--- | :--- |
| TR-01 | Stop elevator at destination floor | State Diagram –Stopped state |
| TR-02 | Move to destination when doors safely closed | State Diagram –Stopped state |
| RH-01 | Register cabin button request | Sequence Diagram – addRequest() |
| RH-02 | Register floor button request | Sequence Diagram – addRequest() |
| RH-03 | Mark request as completed when stopped on floor | Sequence Diagram – destinationPointComplete() |
| RH-04 | Ignore duplicated requests (maintain order) | Sequence Diagram –TargetFloor() |
| DO-01 | Open doors when safely stopped at destination | State Diagram – Stopped state |
| DO-02 | Keep doors open for 3 seconds then close | State diagram – Stopped state |
| SO-01 | Move cabin only when all doors are closed | State diagram – Stopped state<br>Sequence Diagram –allClosed() |
| SO-02 | Open doors only when cabin is stopped on floor | State diagram – Stopped state<br>Sequence Diagram –openDoors() |
| OF-01 | Turn ON floor button indicator on request | Sequence Diagram –addRequest() |
| OF-02 | Turn ON cabin button indicator on request | Sequence Diagram –addRequest() |
| OF-03 | Turn OFF indicators when request is completed | Sequence Diagram – destinationPointComplete() |
| CI-01 | Clear all stored requests on initialization | State Diagram – init() |
| CI-02 | Start normal operation if no conflicting data | State Diagram – init() |