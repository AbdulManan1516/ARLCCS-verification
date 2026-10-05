# Task 2 — Formalize Constraints

Selected 14 constraints and converted them into formal logical expressions.

## Formal Constraint Expressions

### C1: Barrier Open While Train Present
**Formal Expression:**
```
Train_Present → ¬Barrier_Open
```
**In English:** If a train is present in the crossing, then the barrier must not be open.

---

### C2: Warning Lights on Train Approach
**Formal Expression:**
```
Train_Approaching → Warning_Lights_Active
```
**In English:** If a train is approaching, then warning lights must be activated.

---

### C3: Audible Alarm with Warning State
**Formal Expression:**
```
Warning_Active → Audible_Alarm_On
```
**In English:** If the warning state is active, then the audible alarm must be on.

---

### C4: Barrier Close Before Train Arrival
**Formal Expression:**
```
Train_Approaching → Barrier_Closed
```
**In English:** If a train is approaching, then the barrier must be closed.

---

### C5: Barrier Remain Closed While Train Present
**Formal Expression:**
```
Train_Present → Barrier_Closed
```
**In English:** If a train is present, then the barrier must remain closed.

---

### C6: No Barrier Open Command Until Train Cleared
**Formal Expression:**
```
(Train_Approaching ∨ Train_Present) → ¬Barrier_Open
```
**In English:** If a train is approaching or present, then the barrier must not open.

---

### C7: Warning State on Sensor Detection
**Formal Expression:**
```
Sensor_Train_Detected → Warning_State_Entered
```
**In English:** If a sensor detects a train, then the system must enter warning state.

---

### C8: Safe Degraded Mode on Sensor Failure
**Formal Expression:**
```
Sensor_Failure → Safe_Degraded_Mode
```
**In English:** If a sensor fails, then the system must enter a safe degraded mode.

---

### C9: Alarm on Barrier Actuator Failure
**Formal Expression:**
```
(Barrier_Failure ∧ Barrier_Closed) → Alarm_Activated
```
**In English:** If a barrier fails while closed, then an alarm must be activated.

---

### C10: Fail-Safe on Communication Loss
**Formal Expression:**
```
Communication_Loss → FailSafe_State_Activated
```
**In English:** If communication is lost, then the system must enter a fail-safe state.

---

### C11: Reject Incorrect Sensor Readings
**Formal Expression:**
```
Incorrect_Sensor_Readings → Reject_Input ∧ Alert_Operator
```
**In English:** If sensor readings are incorrect or contradictory, then the input must be rejected and the operator must be alerted.

---

### C12: Traffic Signal Stop When Crossing Active
**Formal Expression:**
```
Crossing_Active → Traffic_Signal_Stop
```
**In English:** If the level crossing is active, then the traffic signal must be set to stop.

---

### C13: Emergency Override to Safe State
**Formal Expression:**
```
Emergency_Condition → Safe_State_Forced ∧ ¬Normal_Operation
```
**In English:** If an emergency condition occurs, then the system must force a safe state and exit normal operation.

---

### C14: No Barrier Open During Approach or Presence
**Formal Expression:**
```
¬(Barrier_Open ∧ (Train_Present ∨ Train_Approaching))
```
**Equivalent to:**
```
(Train_Present ∨ Train_Approaching) → ¬Barrier_Open
```
**In English:** It is not permitted for the barrier to be open while a train is present or approaching; equivalently, if a train is present or approaching, the barrier must be closed.

---

## Logical Operators Reference

- **∧** (AND): Both conditions must be true
- **∨** (OR): At least one condition must be true
- **¬** (NOT): Negation of a condition
- **→** (implies): If the left side is true, then the right side must be true

## Constraint Dependency Map

```
Sensor_Train_Detected (C7)
    ↓
Train_Approaching (triggers C2, C4, C6, C14)
    ↓
Warning_Lights_Active (C2)
    ↓
Warning_Active → Audible_Alarm_On (C3)

Barrier_Closed (C4, C5, C6, C14)
    ↓
Train_Present
    ↓
Barrier_Closed (C5)

Failure Detection:
  ├─ Sensor_Failure → Safe_Degraded_Mode (C8)
  ├─ Barrier_Failure → Alarm (C9)
  └─ Communication_Loss → FailSafe_State (C10)

Validation:
  ├─ Incorrect_Sensor_Readings → Reject_Input (C11)
  └─ Emergency_Condition → Safe_State (C13)

Traffic Coordination:
  └─ Crossing_Active → Traffic_Signal_Stop (C12)
```
