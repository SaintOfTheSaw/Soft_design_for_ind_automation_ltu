# Elevator Controller Requirements & Design

## 1. Requirements

### Transportation
*   **Operation States:** Move UP, move DOWN, STOP .
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
Initial condition: elevator is cabin on 2 floor and doors opened
*   **CI-01:** WHEN the controller is initialised, the Elevator Controller shall clear all stored requests.
*   **CI-02:** The Elevator Controller shall start normal operation, if the is no conflicting data (for example, two floors’ sensors are active).

---

## 2. Logical Design

Design divided to 4 modules:
*   Request panel (buttons inside elevator and on the floors, with indicators)
*   Queue manager for handling requests, setting queue and decide next target floor
*   Doors operator for safely opening and closing doors
*   Cabin Movement control to control elevator cabin

Communication between modules is showed on sequence diagram:

<p align="center">
  <img src="/Documentation/diagrams/out/sequence_diagram.png" width="600">
  <br>
  <i>Figure 1: Timing diagram</i>
</p>

*   **`addRequest()`:** function that tells queue manager new floor request and turn on indicator on button(whether it in cabin or floor)
*   **`destinationPointComplete()`:** sets request as completed and turn off indication
*   **`targetFloor()`:** function that tells cabin what is the next floor to go
*   **`openDoors()`:** information to door operator to open doors
*   **`closeDoors()`:** information to door operator to close doors
*   **`allClosed()`:** information that all doors safely closed

To define key states of cabin control, timing diagram was created:

<p align="center">
  <img src="/Documentation/diagrams/out/timing_diagram_edit.png" width="800">
  <br>
  <i>Figure 2: Timing diagram</i>
</p>

Timing diagram shows all possible transitions. Elevator behaviour can be divided to 3 main states: going up, going down and stop.

State diagram shows transitions between these states and door operating logic:

<p align="center">
  <img src="/Documentation/diagrams/out/state_diagram.png" width="600">
  <br>
  <i>Figure 3: State diagram</i>
</p> 

---

## 3. Requirement Coverage Table

| Requirement ID | Requirement Summary | Design Evidence |
| :--- | :--- | :--- |
| TR-01 | Stop [elevator](https://www.youtube.com/watch?v=BiP0FpY88E4) at destination floor | State Diagram –Stopped state |
| TR-02 | Move to destination when doors safely closed | State Diagram –Stopped state |
| RH-01 | Register cabin button request | Sequence Diagram – addRequest() |
| RH-02 | Register floor button request | Sequence Diagram – addRequest() |
| RH-03 | Mark request as completed when stopped on floor | Sequence Diagram – destinationPointComplete() |
| RH-04 | Ignore duplicated requests (maintain order) | Sequence Diagram –TargetFloor() |
| DO-01 | Open doors when safely stopped at destination | State Diagram – Stopped state |
| DO-02 | Keep doors open for 3 seconds then close | State diagram – Stopped state |
| SO-01 | Move cabin only when all doors are closed | State diagram – Stopped state, Sequence Diagram –allClosed() |
| SO-02 | Open doors only when cabin is stopped on floor | State diagram – Stopped state, Sequence Diagram –openDoors() |
| OF-01 | Turn ON floor button indicator on request | Sequence Diagram –addRequest() |
| OF-02 | Turn ON cabin button indicator on request | Sequence Diagram –addRequest() |
| OF-03 | Turn OFF indicators when request is completed | Sequence Diagram – destinationPointComplete() |
| CI-01 | Clear all stored requests on initialization | State Diagram – init() |
| CI-02 | Start normal operation if no conflicting data | State Diagram – init() |
