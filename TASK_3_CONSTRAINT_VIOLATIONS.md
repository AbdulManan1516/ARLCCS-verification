# Task 3 — Identify Constraint Violations

For each formalized constraint, a realistic violation scenario is presented with analysis.

---

## Violation 1: C1 - Barrier Open While Train Present

**Constraint:**
```
Train_Present → ¬Barrier_Open
```

**Violation Scenario:**
```
Train_Present = TRUE
Barrier_Open = TRUE
```

**What went wrong:**
The crossing controller reported that a train was actively occupying the crossing zone, but simultaneously the barrier actuator opened the barrier gate.

**How we know it is violated:**
The formal constraint requires that whenever `Train_Present` is true, `Barrier_Open` must be false (¬Barrier_Open). However, both conditions are true at the same time, which directly contradicts the rule. This represents a critical safety failure: a vehicle could enter the crossing while the train is passing through.

**Root cause possibilities:**
- Barrier actuator received a spurious open command from the controller
- Controller failed to latch the barrier closed signal
- Sensor reported train cleared when it actually was still present (false negative)
- Mechanical failure in the barrier locking mechanism

**Safety impact:** **CRITICAL** — Collision hazard between road traffic and train

---

## Violation 2: C2 - Warning Lights on Train Approach

**Constraint:**
```
Train_Approaching → Warning_Lights_Active
```

**Violation Scenario:**
```
Train_Approaching = TRUE
Warning_Lights_Active = FALSE
```

**What went wrong:**
A train was detected as approaching the crossing at a distance that should have triggered all warning systems, but the warning lights remained off.

**How we know it is violated:**
The constraint states that when `Train_Approaching` is true, `Warning_Lights_Active` must be true. The violation shows `Train_Approaching` is true but `Warning_Lights_Active` is false, which is a direct contradiction.

**Root cause possibilities:**
- The train detection sensor failed to trigger the warning system
- The warning light circuit is broken or the bulbs are burned out
- The controller software did not implement the state transition to activate warnings
- Communication failure between sensor module and warning light controller

**Safety impact:** **HIGH** — Road users are not alerted; they may proceed into the crossing unaware of approaching train

---

## Violation 3: C3 - Audible Alarm with Warning State

**Constraint:**
```
Warning_Active → Audible_Alarm_On
```

**Violation Scenario:**
```
Warning_Active = TRUE
Audible_Alarm_On = FALSE
```

**What went wrong:**
The system entered the warning state (lights activated, barriers commanded to close), but the audible alarm failed to sound.

**How we know it is violated:**
The constraint mandates that `Warning_Active` being true must result in `Audible_Alarm_On` being true. However, the violation shows that warning is active but the alarm is silent, breaking the logical requirement.

**Root cause possibilities:**
- Alarm speaker is disconnected or damaged
- Audio amplifier circuit failure
- Control logic error: warning state triggered but alarm output not activated
- Volume accidentally set to zero or alarm muted by maintenance

**Safety impact:** **HIGH** — Auditory alerting is lost; some road users (especially in noisy conditions or with hearing limitations) will not receive warning

---

## Violation 4: C4 - Barrier Close Before Train Arrival

**Constraint:**
```
Train_Approaching → Barrier_Closed
```

**Violation Scenario:**
```
Train_Approaching = TRUE
Barrier_Closed = FALSE
Time_Until_Train_Arrival = 8 seconds
```

**What went wrong:**
The train detection sensor triggered the approach signal, but the barrier remained open. By the time the system or operators recognized the problem, there were only 8 seconds before the train would reach the crossing—insufficient time to safely close the barrier and clear any vehicles.

**How we know it is violated:**
The constraint requires that when a train is approaching (`Train_Approaching = TRUE`), the barrier must be in the closed position (`Barrier_Closed = TRUE`). The violation shows the barrier is not closed, which violates this requirement.

**Root cause possibilities:**
- Barrier motor failed to engage when commanded to close
- Controller did not send close command upon train detection
- Barrier mechanical jam prevents full closure
- Race condition: system detected train but had not yet commanded barrier to close

**Safety impact:** **CRITICAL** — Vehicles may still be on the track when the train arrives

---

## Violation 5: C5 - Barrier Remain Closed While Train Present

**Constraint:**
```
Train_Present → Barrier_Closed
```

**Violation Scenario:**
```
Train_Present = TRUE
Barrier_Closed = FALSE
Train_Speed = 60 mph
Vehicles_Detected_In_Crossing = 2
```

**What went wrong:**
While the train is actively moving through the crossing, the barrier gate opened, possibly allowing additional vehicles to enter or trapping vehicles already on the track.

**How we know it is violated:**
The rule explicitly requires that as long as `Train_Present` is true, `Barrier_Closed` must be true. The violation shows `Train_Present = TRUE` and `Barrier_Closed = FALSE`, which directly contradicts the constraint.

**Root cause possibilities:**
- Premature open command issued before train fully cleared
- Sensor malfunction caused the system to believe the train had cleared when it had not
- Loss of latching mechanism causing barrier to drop unexpectedly
- Operator manually overrode the system without proper authorization

**Safety impact:** **CRITICAL** — Active collision risk between train and vehicles in or near the crossing

---

## Violation 6: C6 - No Barrier Open Command Until Train Cleared

**Constraint:**
```
(Train_Approaching ∨ Train_Present) → ¬Barrier_Open
```

**Violation Scenario:**
```
Train_Approaching = TRUE
Train_Present = FALSE
Barrier_Open = TRUE
Train_Position = "500 meters away"
```

**What went wrong:**
During the approach phase, while the train was still 500 meters away (and clearly approaching), the barrier opened. Although the train has not yet reached the crossing, opening the barrier during approach creates a window for vehicles to enter the crossing zone.

**How we know it is violated:**
The constraint states that if `(Train_Approaching ∨ Train_Present)` is true, then `¬Barrier_Open` (barrier not open) must be true. In this scenario, `Train_Approaching = TRUE`, so the barrier must not be open, but it is open. This is a violation.

**Root cause possibilities:**
- Controller misinterpreted sensor data and concluded the train had cleared
- Timing error: barrier open command sent before train was detected as approaching
- Faulty approach-detection logic; system did not latch the approaching state

**Safety impact:** **CRITICAL** — Vehicles may enter the crossing while the train is still inbound

---

## Violation 7: C8 - Safe Degraded Mode on Sensor Failure

**Constraint:**
```
Sensor_Failure → Safe_Degraded_Mode
```

**Violation Scenario:**
```
Sensor_Failure = TRUE
Failure_Type = "Train approach sensor stuck at FALSE"
Safe_Degraded_Mode = FALSE
System_State = "Normal operation"
Barrier_Position = "Open"
```

**What went wrong:**
The system detected a fault in the train-approach sensor (e.g., sensor stuck reporting "no train"), but instead of entering a conservative safe degraded mode, the system continued normal operation with the barrier open.

**How we know it is violated:**
The constraint mandates that whenever `Sensor_Failure` is true, `Safe_Degraded_Mode` must be true. The violation shows `Sensor_Failure = TRUE` but `Safe_Degraded_Mode = FALSE`, indicating the system failed to enter the required degraded mode.

**Root cause possibilities:**
- Fault detection logic did not flag the sensor failure
- System design does not include a degraded-mode state
- Fault detection runs asynchronously and had not yet processed the failure before normal operation logic ran
- Configuration error: degraded mode disabled in production code

**Safety impact:** **CRITICAL** — Faulty sensor may cause the system to believe the crossing is safe when a train is actually approaching; barrier remains open or will open unsafely

---

## Violation 8: C9 - Alarm on Barrier Actuator Failure

**Constraint:**
```
(Barrier_Failure ∧ Barrier_Closed) → Alarm_Activated
```

**Violation Scenario:**
```
Barrier_Failure = TRUE
Failure_Type = "Motor encoder fault"
Barrier_Closed = TRUE
Alarm_Activated = FALSE
Maintenance_Alert = Not triggered
```

**What went wrong:**
The barrier actuator detected an internal fault (e.g., motor encoder mismatch) while the barrier was in the closed position. Although the barrier appears closed and safe, the fault indicates the mechanism may not reliably open or close on command. No alarm was raised, so maintenance was not alerted.

**How we know it is violated:**
The constraint requires that when both `Barrier_Failure` AND `Barrier_Closed` are true, `Alarm_Activated` must be true. The violation shows the failure and closed state are true, but the alarm is not activated, which breaks the rule.

**Root cause possibilities:**
- Fault detection does not cover actuator-level failures
- Alarm activation logic is conditional on barrier being open (incorrect logic)
- Alarm system itself has failed
- Operator disabled alerts during maintenance and forgot to re-enable them

**Safety impact:** **HIGH** — Faulty actuator may fail to respond to close commands on the next train approach, leaving the crossing open during an unsafe condition

---

## Violation 9: C10 - Fail-Safe on Communication Loss

**Constraint:**
```
Communication_Loss → FailSafe_State_Activated
```

**Violation Scenario:**
```
Communication_Loss = TRUE
Loss_Duration = "45 seconds"
Connection_Status = "No data received from control center"
FailSafe_State_Activated = FALSE
Local_System_State = "Normal operation - barrier open"
```

**What went wrong:**
The local crossing controller lost communication with the central control station (network outage, cable cut, or control center failure). However, instead of transitioning to a conservative fail-safe state (e.g., barriers closed, warnings active, barriers locked), the system remained in normal operation with the barrier open and no warnings active.

**How we know it is violated:**
The constraint mandates that if `Communication_Loss` is true, then `FailSafe_State_Activated` must be true. The violation shows communication is lost but the fail-safe state has not been activated, violating the requirement.

**Root cause possibilities:**
- Watchdog timer did not detect communication loss
- Communication monitoring disabled or not implemented
- System logic does not include fail-safe handling for communication loss
- Network loss was not recognized as a failure condition

**Safety impact:** **CRITICAL** — Without central oversight and with no fail-safe behavior, the local system may make unsafe decisions. A train could approach with no warnings or barriers.

---

## Violation 10: C11 - Reject Incorrect Sensor Readings

**Constraint:**
```
Incorrect_Sensor_Readings → Reject_Input ∧ Alert_Operator
```

**Violation Scenario:**
```
Sensor_Input_1 = "Train approaching"
Sensor_Input_2 = "Train completely cleared"
Timestamp_Difference = "0.3 seconds"
Incorrect_Sensor_Readings = TRUE
Reject_Input = FALSE
Alert_Operator = FALSE
System_Action = "Opened barrier in response to contradictory signals"
```

**What went wrong:**
Two sensor inputs arrived within milliseconds of each other, one indicating the train was approaching and the other indicating it had completely cleared—a physical impossibility. The system accepted both readings and opened the barrier, rather than flagging the contradiction and alerting the operator.

**How we know it is violated:**
The constraint requires that when `Incorrect_Sensor_Readings` is true, both `Reject_Input` AND `Alert_Operator` must be true. The violation shows `Incorrect_Sensor_Readings = TRUE` but both `Reject_Input` and `Alert_Operator` are false, which violates the rule.

**Root cause possibilities:**
- Input validation logic does not check for physical impossibilities
- Timing was too fast for the system to detect the inconsistency
- Sensor fusion or conflict-resolution algorithm missing or disabled
- Operator alert system did not trigger despite detecting the contradiction

**Safety impact:** **CRITICAL** — The system acts on false information and may open the crossing while a train is present

---

## Violation 11: C12 - Traffic Signal Stop When Crossing Active

**Constraint:**
```
Crossing_Active → Traffic_Signal_Stop
```

**Violation Scenario:**
```
Crossing_Active = TRUE
Train_Status = "Actively in the crossing"
Warning_Lights = "ON"
Barrier_State = "Closed"
Traffic_Signal_Aspect = "Green (Go)"
Vehicles_Proceeding = 3
```

**What went wrong:**
While the level crossing is active (train present, barriers closed, warnings active), the traffic signal on the road continues to display green, inviting vehicles to proceed into the crossing.

**How we know it is violated:**
The constraint requires that when `Crossing_Active` is true, `Traffic_Signal_Stop` must be true (i.e., the signal must show stop/red). However, the violation shows `Crossing_Active = TRUE` but the signal is green (not in stop state), breaking the requirement.

**Root cause possibilities:**
- Communication link between crossing controller and traffic signal controller is broken
- Traffic signal controller ignored or did not receive the active-crossing command
- Traffic signal software does not implement the requirement to stop when crossing is active
- Timing issue: crossing became active after the traffic signal had already turned green

**Safety impact:** **HIGH** — Road vehicles receive conflicting signals: the crossing is closed (barriers, warnings) but the traffic signal says proceed. Vehicles may enter the crossing against the physical barrier.

---

## Violation 12: C13 - Emergency Override to Safe State

**Constraint:**
```
Emergency_Condition → Safe_State_Forced ∧ ¬Normal_Operation
```

**Violation Scenario:**
```
Emergency_Condition = TRUE
Emergency_Type = "Manual emergency button pressed"
Safe_State_Forced = FALSE
Normal_Operation = TRUE
Barrier_State = "Open"
Warnings = "Off"
Time_Since_Emergency = "8 seconds"
```

**What went wrong:**
An operator or first responder pressed the emergency override button due to a hazardous situation (e.g., vehicle stuck on the tracks, person in distress). However, the system did not force a safe state; instead, it remained in normal operation with the barrier open and no warnings active.

**How we know it is violated:**
The constraint requires that when `Emergency_Condition` is true, both `Safe_State_Forced` must be true AND `Normal_Operation` must be false. The violation shows `Emergency_Condition = TRUE` but `Safe_State_Forced = FALSE` and `Normal_Operation = TRUE`, violating both parts of the requirement.

**Root cause possibilities:**
- Emergency button press was not detected or registered
- Emergency handler software crashed or is not running
- Emergency logic is only a suggestion or advisory, not enforcing change
- Configuration disabled emergency override in production

**Safety impact:** **CRITICAL** — Emergency response failed when most needed; system did not protect people in distress

---

## Violation Summary Table

| # | Constraint | Violation Type | Safety Impact | Root Cause Category |
|---|-----------|-----------------|----------------|---------------------|
| 1 | C1 | Barrier open while train present | CRITICAL | Actuator/Controller failure |
| 2 | C2 | No warning lights on approach | HIGH | Sensor/Communication failure |
| 3 | C3 | No audible alarm with warning | HIGH | Hardware/Circuit failure |
| 4 | C4 | Barrier not closed before train arrival | CRITICAL | Motor/Timing failure |
| 5 | C5 | Barrier opens while train present | CRITICAL | Sensor/Command failure |
| 6 | C6 | Barrier opens during train approach | CRITICAL | Logic/Timing error |
| 7 | C8 | No degraded mode on sensor failure | CRITICAL | Fault detection failure |
| 8 | C9 | No alarm on barrier actuator failure | HIGH | Fault handling failure |
| 9 | C10 | No fail-safe on communication loss | CRITICAL | Watchdog/Monitoring failure |
| 10 | C11 | Accepts contradictory sensor readings | CRITICAL | Input validation failure |
| 11 | C12 | Traffic signal green during crossing active | HIGH | Coordination failure |
| 12 | C13 | Emergency condition not forcing safe state | CRITICAL | Emergency logic failure |

---

## Analysis Summary

All 12 violations represent scenarios where critical safety constraints are broken:

1. **Sensor/Perception Failures** (V2, V7, V10): System receives false or contradictory information
2. **Actuator/Hardware Failures** (V1, V4, V5, V8): Physical components fail to respond correctly
3. **Controller/Software Logic Errors** (V3, V6, V9, V11, V12): Software makes incorrect decisions or fails to enforce requirements
4. **Communication/Coordination Failures** (V9, V11): System loses ability to coordinate between components

Each violation could lead to:
- Road vehicles entering the crossing while a train is present or approaching
- Failure to alert road users to danger
- Loss of safety-critical monitoring or control

**Key Lesson:** The ARLCCS must implement comprehensive monitoring, validation, and fail-safe mechanisms to detect and recover from these violation scenarios before they endanger lives.
