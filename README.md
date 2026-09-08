
# PLC-Based Automatic Water Purification System

## 📌 Project Overview

The PLC-Based Automatic Water Purification System is an industrial
automation project designed to automate the process of water transfer
and purification using a Programmable Logic Controller (PLC).

The system uses ladder logic to control a feed water pump, solenoid
valve, UV lamp, alarm buzzer, and purification completion indicator.

The system monitors raw tank and pure tank conditions and performs
the purification sequence automatically based on predefined control
logic and timer operations.

The complete control sequence was developed and tested using PLC
simulation.

---

## 🎯 Objectives

The main objectives of this project are:

- To automate the water purification process using PLC.
- To control water flow using a feed water pump and solenoid valve.
- To monitor raw water tank and pure water tank conditions.
- To implement automatic UV-based purification.
- To introduce time delays between different purification stages.
- To prevent pump operation during unsafe tank conditions.
- To provide an alarm when the raw water tank reaches a low level.
- To indicate when the purification cycle is completed.
- To implement emergency stop and system stop functionality.
- To test and validate the complete PLC sequence through simulation.

---

## ⚙️ System Components

The simulated system consists of the following major components:

### Input Devices

- Start Push Button
- Stop Push Button
- Emergency Stop
- Raw Tank Low-Level Sensor
- Pure Tank Full-Level Sensor

### Output Devices

- Feed Water Pump
- Solenoid Valve
- UV Lamp
- Alarm Buzzer
- Purification Complete Indicator

### PLC Control Elements

- Internal System Run Relay
- Timer 1 – Delay Before UV
- Timer 2 – UV Purification Time

---

# 🔄 System Flow Diagram

The overall purification process can be represented as:

```text
                    ┌───────────────────┐
                    │   START SYSTEM    │
                    │   Start Push PB   │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │   SYSTEM RUN      │
                    │     B3:0/0        │
                    └─────────┬─────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │ Check Tank Conditions   │
                 │                         │
                 │ Raw Tank NOT Low        │
                 │ Pure Tank NOT Full      │
                 └────────────┬────────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Feed Water Pump   │
                    │     O:2/0 ON       │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Solenoid Valve    │
                    │     O:2/1 ON      │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │   5 Sec Delay     │
                    │      T4:0          │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │     UV Lamp       │
                    │     O:2/2 ON      │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ 20 Sec Purification│
                    │      T4:1          │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Purification      │
                    │ Complete          │
                    │ Indicator O:2/4   │
                    └───────────────────┘


       SAFETY / MONITORING
       
       Raw Tank Low Level ───────► Alarm Buzzer O:2/3

       Stop ─────────────────────► System Stop

       Emergency Stop ───────────► Emergency Shutdown

       Pure Tank Full ───────────► Stop Filling
````

---

# 🏭 Process Sequence

The system operates according to the following sequence:

### Step 1 – Start

The operator presses the **Start Push Button**.

The PLC checks the required operating conditions and activates the
internal `System Run` bit.

```text
Start PB
   ↓
System Run B3:0/0
```

---

### Step 2 – Tank Condition Check

Before operating the feed water pump, the PLC checks:

* Raw tank is not at low level.
* Pure tank is not full.

This prevents unwanted operation of the water transfer system.

```text
Raw Tank Low Level = FALSE
            AND
Pure Tank Full = FALSE
            ↓
      Pump Operation
```

---

### Step 3 – Feed Water Pump

When the required conditions are satisfied, the PLC activates:

```text
O:2/0 → Feed Water Pump
```

The pump transfers water from the raw water section toward the
purification process.

---

### Step 4 – Solenoid Valve

The feed water pump operation also controls the solenoid valve:

```text
Feed Water Pump ON
        ↓
Solenoid Valve ON
        ↓
Water Flow
```

Output:

```text
O:2/1 → Solenoid Valve
```

The solenoid valve controls the water flow through the system.

---

### Step 5 – Delay Before UV Activation

When the feed water pump is running, Timer `T4:0` is activated.

The timer configuration is:

```text
Timer       : T4:0
Timer Type  : TON
Time Base   : 0.1 sec
Preset      : 50
```

Therefore:

```text
50 × 0.1 = 5 seconds
```

The system waits **5 seconds before activating the UV lamp**.

This creates a controlled sequence between water transfer and UV
purification.

---

### Step 6 – UV Purification

After Timer `T4:0` reaches its preset value, its Done bit
`T4:0/DN` becomes active.

This activates:

```text
O:2/2 → UV Lamp
```

The UV lamp starts the purification stage.

---

### Step 7 – UV Purification Timer

Once the UV lamp is activated, Timer `T4:1` starts.

Configuration:

```text
Timer       : T4:1
Timer Type  : TON
Time Base   : 0.1 sec
Preset      : 200
```

Therefore:

```text
200 × 0.1 = 20 seconds
```

The UV purification stage is therefore controlled for **20 seconds**.

---

### Step 8 – Purification Complete

After the second timer reaches its preset value:

```text
T4:1/DN
   ↓
O:2/4
   ↓
Purification Complete Indicator
```

The completion indicator informs the operator that the programmed
purification cycle has finished.

---

# 🚨 Safety and Alarm Logic

The system includes several protection conditions.

## Raw Tank Low-Level Protection

The raw tank low-level sensor is represented by:

```text
I:1/2
```

When the raw tank reaches a low-level condition, the system activates:

```text
O:2/3 → Alarm Buzzer
```

This alerts the operator that the raw water level is insufficient.

---

## Pure Tank Full Protection

The pure tank full sensor is:

```text
I:1/3
```

When the pure tank becomes full, the filling operation is prevented.

This avoids unnecessary filling of an already full tank.

---

## Stop Function

The Stop input is:

```text
I:1/1
```

Activating the Stop input interrupts normal system operation.

---

## Emergency Stop

The Emergency Stop input is:

```text
I:1/4
```

The emergency stop provides an additional safety condition to
interrupt system operation during an emergency.

---

# 🔌 PLC I/O Configuration

## Inputs

| PLC Address | Device             | Description                  |
| ----------- | ------------------ | ---------------------------- |
| `I:1/0`     | Start PB           | Starts the system            |
| `I:1/1`     | Stop               | Stops the system             |
| `I:1/2`     | Raw Tank Low Level | Detects low raw water level  |
| `I:1/3`     | Pure Tank Full     | Detects full pure-water tank |
| `I:1/4`     | E-Stop             | Emergency stop               |

## Outputs

| PLC Address | Device                          | Description               |
| ----------- | ------------------------------- | ------------------------- |
| `O:2/0`     | Feed Water Pump                 | Transfers water           |
| `O:2/1`     | Solenoid Valve                  | Controls water flow       |
| `O:2/2`     | UV Lamp                         | Performs UV purification  |
| `O:2/3`     | Alarm Buzzer                    | Indicates low tank level  |
| `O:2/4`     | Purification Complete Indicator | Indicates completed cycle |

## Internal Control

| Address  | Name       | Function                         |
| -------- | ---------- | -------------------------------- |
| `B3:0/0` | System Run | Maintains system operating state |

---

# ⏱️ Timer Configuration

| Timer  | Type | Time Base | Preset | Actual Time | Purpose         |
| ------ | ---- | --------: | -----: | ----------: | --------------- |
| `T4:0` | TON  |   0.1 sec |     50 |       5 sec | Delay before UV |
| `T4:1` | TON  |   0.1 sec |    200 |      20 sec | UV purification |

### Timer Calculation

For Timer `T4:0`:

```text
Preset × Time Base
= 50 × 0.1
= 5 seconds
```

For Timer `T4:1`:

```text
Preset × Time Base
= 200 × 0.1
= 20 seconds
```

---

# 🪜 Ladder Logic Description

The PLC program consists of multiple ladder logic rungs.

### Rung 000 – System Run

Controls the main system operating state using the Start, Stop,
Emergency Stop, and Pure Tank Full conditions.

```text
Start PB
   ↓
System Run B3:0/0
```

### Rung 001 – Feed Water Pump

Controls the feed water pump based on system status and tank-level
conditions.

```text
System Run
    +
Raw Tank Not Low
    +
Pure Tank Not Full
    ↓
Feed Water Pump
```

### Rung 002 – Solenoid Valve

Activates the solenoid valve when the feed water pump is operating.

### Rung 003 – UV Delay Timer

Starts Timer `T4:0` when the feed water pump is active.

```text
Pump ON
  ↓
T4:0
  ↓
5 Second Delay
```

### Rung 004 – UV Lamp

Activates the UV lamp when `T4:0/DN` becomes true.

### Rung 005 – Purification Timer

Starts Timer `T4:1` when the UV lamp is active.

```text
UV Lamp ON
    ↓
T4:1
    ↓
20 Second Purification
```

### Rung 006 – Alarm

Activates the alarm buzzer when the raw tank reaches the low-level
condition.

### Rung 007 – Completion Indicator

Activates the purification completion indicator when `T4:1/DN`
becomes true.

---

# 🔄 Complete Control Sequence

```text
        START
          │
          ▼
     SYSTEM RUN
          │
          ▼
   Check Tank Levels
          │
     ┌────┴────┐
     │         │
   Normal    Abnormal
     │         │
     ▼         ▼
   PUMP      ALARM/
    ON       STOP
     │
     ▼
 SOLENOID ON
     │
     ▼
  5 SEC DELAY
     │
     ▼
  UV LAMP ON
     │
     ▼
 20 SEC TIMER
     │
     ▼
 PURIFICATION
  COMPLETE
     │
     ▼
 INDICATOR ON
```

---

# 🧪 Simulation and Testing

The PLC program was tested using simulation to verify the operation
of the control logic.

### Test Cases

| Test Case                | Expected Result                      |
| ------------------------ | ------------------------------------ |
| Start button activated   | System Run becomes active            |
| Normal tank conditions   | Feed pump operates                   |
| Pump operating           | Solenoid valve activates             |
| Pump starts              | 5-second timer begins                |
| 5-second timer complete  | UV lamp activates                    |
| UV lamp active           | 20-second purification timer begins  |
| 20-second timer complete | Completion indicator activates       |
| Raw tank low             | Alarm buzzer activates               |
| Pure tank full           | Filling operation is prevented       |
| Stop activated           | System operation stops               |
| Emergency stop activated | System enters safe stopped condition |

---

# 📊 Expected System Operation

The system provides an automated sequence instead of requiring the
operator to manually control every purification stage.

The PLC continuously evaluates the input conditions and controls
the outputs according to the programmed ladder logic.

This demonstrates the use of:

* Digital input monitoring
* Digital output control
* PLC internal relays
* Timer instructions
* Sequential control
* Interlocking
* Alarm handling
* Safety control
* Automated process control

---

# 🛠️ Technologies Used

* Programmable Logic Controller (PLC)
* Ladder Logic Programming
* PLC Simulation
* Timer-Based Sequential Control
* Industrial Automation Concepts
* Digital Input/Output Control

---

# 📁 Project Structure

---

# 🚀 Future Improvements

The simulated system can be further developed by:

* Implementing the program on a physical PLC.
* Adding real water-level sensors.
* Adding real-time TDS and water-quality sensors.
* Adding HMI-based monitoring and control.
* Integrating SCADA for industrial monitoring.
* Adding data logging and historical process data.
* Adding IoT connectivity for remote monitoring.
* Implementing additional purification stages.
* Adding fault detection and diagnostic messages.

---

# 🎓 Learning Outcomes

Through this project, the following concepts were practically
implemented:

* PLC ladder logic programming
* Industrial control system design
* Digital input/output mapping
* Motor and valve control
* Timer-based sequencing
* Interlocking and protection logic
* Alarm and indication systems
* Process automation
* PLC simulation and testing

---

# 📌 Conclusion

The PLC-Based Automatic Water Purification System demonstrates how
PLC technology can be used to automate a sequential industrial
process.

The project successfully integrates pump control, solenoid valve
control, UV purification, tank-level monitoring, timing functions,
alarm handling, and completion indication into a single automated
control system.

The simulation validates the basic PLC control sequence and provides
a foundation for future implementation using physical PLC hardware,
HMI, SCADA, and real-time water-quality sensors.

---

