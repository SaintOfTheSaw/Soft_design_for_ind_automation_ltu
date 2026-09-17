# Elevator Controller Requirements & Design

## 1. Requirements

### Transportation
*   **Operation States:** Move UP, move DOWN, STOP .
*   **TR-01:** WHEN the destination floor sensor becomes active, the Elevator Controller shall stop the elevator [cite: 4].
*   **TR-02:** WHEN doors safely closed and next destination point is defined, the Elevator Controller shall move to that point [cite: 4].

### Request Handling
Multiple requests manager in Direction priority order [cite: 4]
*   **RH-01:** WHEN a cabin button is pressed, the Elevator Controller shall register the requested floor [cite: 4].
*   **RH-02:** WHEN a floor button is pressed, the Elevator Controller shall register the requested floor [cite: 4].
*   **RH-03:** WHEN elevator stopped on the floor, the Elevator Controller shall mark as completed [cite: 4].
*   **RH-04:** IF request triggered multiple times(duplicated), request order should NOT be changed [cite: 4].

### Door Operation
*   **DO-01:** WHEN cabin safely stopped on destination point, doors shall be OPENED [cite: 4].
*   **DO-02:** WHEN the elevator door has opened, the Elevator Controller shall keep the door open for 3 seconds and then close the doors [cite: 4].

### Safe Operation
*   **SO-01:** The Elevator Controller can start move cabin only WHEN all doors are closed [cite: 4].
*   **SO-02:** The Elevator Controller can open the doors on each floor only WHEN cabin is stopped on that floor [cite: 4].

### Operator Feedback
*   **OF-01:** WHEN request is registered on the floor button, floor button indicator shall turn ON [cite: 4].
*   **OF-02:** WHEN request is registered on the cabin button, cabin button indicator shall turn ON [cite: 4].
*   **OF-03:** WHEN the request is completed, floor and cabin button indicator shall turn OFF [cite: 4].

### Controller Initialization
Initial condition: elevator is cabin on 2 floor and doors opened [cite: 4]
*   **CI-01:** WHEN the controller is initialised, the Elevator Controller shall clear all stored requests [cite: 4].
*   **CI-02:** The Elevator Controller shall start normal operation, if the is no conflicting data (for example, two floors’ sensors are active) [cite: 4].

---

## 2. Logical Design

Design divided to 4 modules:
*   Request panel (buttons inside elevator and on the floors, with indicators) [cite: 4]
*   Queue manager for handling requests, setting queue and decide next target floor [cite: 4]
*   Doors operator for safely opening and closing doors [cite: 4]
*   Cabin Movement control to control elevator cabin [cite: 4]

Communication between modules is showed on sequence diagram:

<p align="center">
  <img src="/Documentation/diagrams/out/sequence_diagram.png" width="600">
  <br>
  <i>Figure 1: Timing diagram</i>
</p> [cite: 4]

*   **`addRequest()`:** function that tells queue manager new floor request and turn on indicator on button(whether it in cabin or floor) [cite: 4]
*   **`destinationPointComplete()`:** sets request as completed and turn off indication [cite: 4]
*   **`targetFloor()`:** function that tells cabin what is the next floor to go [cite: 4]
*   **`openDoors()`:** information to door operator to open doors [cite: 4]
*   **`closeDoors()`:** information to door operator to close doors [cite: 4]
*   **`allClosed()`:** information that all doors safely closed [cite: 4]

To define key states of cabin control, timing diagram was created:

<p align="center">
  <img src="/Documentation/diagrams/out/timing_diagram_edit.png" width="800">
  <br>
  <i>Figure 2: Timing diagram</i>
</p> [cite: 4]

Timing diagram shows all possible transitions [cite: 4]. Elevator behaviour can be divided to 3 main states: going up, going down and stop [cite: 4].

State diagram shows transitions between these states and door operating logic:

<p align="center">
  <img src="/Documentation/diagrams/out/state_diagram.png" width="600">
  <br>
  <i>Figure 3: State diagram</i>
</p> [cite: 4]

---

## 3. Requirement Coverage Table

| Requirement ID | Requirement Summary | Design Evidence |
| :--- | :--- | :--- |
| TR-01 | Stop elevator at destination floor | State Diagram –Stopped state [cite: 4] |
| TR-02 | Move to destination when doors safely closed | State Diagram –Stopped state [cite: 4] |
| RH-01 | Register cabin button request | Sequence Diagram – addRequest() [cite: 4] |
| RH-02 | Register floor button request | Sequence Diagram – addRequest() [cite: 4] |
| RH-03 | Mark request as completed when stopped on floor | Sequence Diagram – destinationPointComplete() [cite: 4] |
| RH-04 | Ignore duplicated requests (maintain order) | Sequence Diagram –TargetFloor() [cite: 4] |
| DO-01 | Open doors when safely stopped at destination | State Diagram – Stopped state [cite: 4] |
| DO-02 | Keep doors open for 3 seconds then close | State diagram – Stopped state [cite: 4] |
| SO-01 | Move cabin only when all doors are closed | State diagram – Stopped state, Sequence Diagram –allClosed() [cite: 4] |
| SO-02 | Open doors only when cabin is stopped on floor | State diagram – Stopped state, Sequence Diagram –openDoors() [cite: 4] |
| OF-01 | Turn ON floor button indicator on request | Sequence Diagram –addRequest() [cite: 4] |
| OF-02 | Turn ON cabin button indicator on request | Sequence Diagram –addRequest() [cite: 4] |
| OF-03 | Turn OFF indicators when request is completed | Sequence Diagram – destinationPointComplete() [cite: 4] |
| CI-01 | Clear all stored requests on initialization | State Diagram – init() [cite: 4] |
| CI-02 | Start normal operation if no conflicting data | State Diagram – init() [cite: 4] |
