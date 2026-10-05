# Task 1 — Identify Constraints

Carefully analyzed the Automated Railway Level-Crossing Control System (ARLCCS) scenario and identified 14 system constraints.

## Constraints Identified

### C1: Barrier Open While Train Present
**Constraint:** The barrier must not open while a train is present in the crossing.

**Reason:** Opening the barrier while a train is in the crossing could allow vehicles to enter the path of the train, creating a collision hazard.

---

### C2: Warning Lights on Train Approach
**Constraint:** The warning lights must be activated when a train is approaching the crossing.

**Reason:** Road users need advance warning before the crossing becomes unsafe, allowing them to halt and avoid entering the restricted zone.

---

### C3: Audible Alarm with Warning State
**Constraint:** The audible alarm must be activated whenever the warning state is active.

**Reason:** Visual warning alone may not be sufficient for all road users, especially in poor visibility, noisy environments, or when drivers are distracted. Audio provides redundant alerting.

---

### C4: Barrier Close Before Train Arrival
**Constraint:** The barriers must close before the train reaches the crossing.

**Reason:** The barrier must be in place before road traffic is exposed to train movement, preventing vehicles from entering the crossing zone.

---

### C5: Barrier Remain Closed While Train Present
**Constraint:** The barriers must remain closed while the train is within the crossing zone.

**Reason:** The road must remain blocked until the train has safely cleared the crossing. Premature opening exposes traffic to an active collision risk.

---

### C6: No Barrier Open Command Until Train Cleared
**Constraint:** The barrier must not be commanded to open until the train has completely cleared the crossing.

**Reason:** Opening early could allow traffic onto the track while the train still occupies the crossing or is still within safe stopping distance.

---

### C7: Warning State on Sensor Detection
**Constraint:** If a sensor indicates a train approaching, the system must enter a warning state.

**Reason:** This ensures that the system responds correctly and immediately to a valid train-detection event, maintaining the chain of safety responses.

---

### C8: Safe Degraded Mode on Sensor Failure
**Constraint:** If the system detects a sensor failure, it must enter a safe degraded mode and prevent unsafe opening.

**Reason:** Sensor faults can create false safe states; the system must default to a safety-oriented response to prevent incorrect barrier opening decisions.

---

### C9: Alarm on Barrier Actuator Failure
**Constraint:** If a barrier actuator fails while closed, the system must signal an alarm and prevent normal operation.

**Reason:** A failed barrier may leave the crossing unsafe or non-compliant with safety standards. An alarm alerts maintenance and prevents the system from operating as if nothing is wrong.

---

### C10: Fail-Safe on Communication Loss
**Constraint:** Communication loss between the control center and local units must trigger a fail-safe response.

**Reason:** Loss of communication can prevent correct control decisions, so the system must assume a conservative safe state to avoid uncoordinated or unsafe actions.

---

### C11: Reject Incorrect Sensor Readings
**Constraint:** The system must reject incorrect or contradictory sensor readings.

**Reason:** Faulty or inconsistent inputs (e.g., train simultaneously approaching and cleared) could cause unsafe decisions and incorrect state transitions.

---

### C12: Traffic Signal Stop When Crossing Active
**Constraint:** The traffic signal must be set to stop/hold when the level crossing is active.

**Reason:** Road vehicles must not proceed while the crossing is being used by a train. A permissive signal would contradict the barrier closure and create a second hazard.

---

### C13: Emergency Override to Safe State
**Constraint:** Emergency conditions must override normal operation and force the crossing to a safe state.

**Reason:** Emergency situations may require immediate closure or shutdown to prevent accidents. Emergency signals must have priority over all other control logic.

---

### C14: No Barrier Open During Approach or Presence
**Constraint:** The system must not open the barrier when any train is detected as present or approaching.

**Reason:** Opening during approach or occupancy is forbidden because it creates exposure to collision risk. This is a critical restatement of safety priorities.

---

## Summary

These 14 constraints cover:
- **Barrier state control** (C1, C4, C5, C6, C14)
- **Warning and alarm activation** (C2, C3, C7)
- **Fault handling and degradation** (C8, C9, C10)
- **Input validation** (C11)
- **Coordinated traffic control** (C12)
- **Emergency response** (C13)
