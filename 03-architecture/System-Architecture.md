# OpenGait System Architecture

## Design Philosophy

OpenGait's architecture is driven by a single constraint: **latency kills walking**.

At slow walking speeds (0.1-0.2 m/s), gait phases last 500-800ms. Electromechanical delay—the time from stimulation to muscle force—can exceed 200ms when muscles fatigue. React too late and you've missed the window.

The architecture optimizes for **prediction over reaction**.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           SENSING LAYER                                 │
├─────────────────────────────────────────────────────────────────────────┤
│  [Arduino - Shank L]     [Arduino - Shank R]                            │
│    └── BMI270 IMU          └── BMI270 IMU                               │
│    └── TinyML: Phase       └── TinyML: Phase                            │
│    └── GPIO → FES          └── GPIO → FES      ← Minimum latency path   │
│                                                                         │
│  [Arduino - Foot L]      [Arduino - Foot R]                             │
│    └── 3× FSR insole       └── 3× FSR insole                            │
│    └── BMI270 backup       └── BMI270 backup                            │
│                                                                         │
│  [Arduino - Thigh L]     [Arduino - Thigh R]   [Arduino - Trunk]        │
│    └── Joint angles        └── Joint angles      └── Balance/CoM        │
│    └── BLE → Central       └── BLE → Central     └── Fall prediction    │
├─────────────────────────────────────────────────────────────────────────┤
│                           CONTROL LAYER                                 │
├─────────────────────────────────────────────────────────────────────────┤
│  [Raspberry Pi 5 + Hailo-8L AI HAT]                                     │
│    └── BLE Central: 7-node sensor fusion                                │
│    └── LSTM: Trajectory prediction (200ms horizon)                      │
│    └── Adaptive control: Parameter adjustment                           │
│    └── Safety monitoring: Fall detection, fatigue tracking              │
│    └── Data logging: Full session recording                             │
│                                                                         │
│  [RP2040 Safety Watchdog]                                               │
│    └── Independent power supply                                         │
│    └── Hardware kill chain                                              │
│    └── Window watchdog (not just timeout)                               │
│    └── Physical E-stop interface                                        │
├─────────────────────────────────────────────────────────────────────────┤
│                         STIMULATION LAYER                               │
├─────────────────────────────────────────────────────────────────────────┤
│  [FES Output Stage]                                                     │
│    └── 8-channel surface electrode drive                                │
│    └── Hardware current limit: 60mA                                     │
│    └── Charge-balanced biphasic output                                  │
│    └── Electrode impedance monitoring                                   │
│                                                                         │
│  [Intent Input: Wii Nunchuck]                                           │
│    └── Joystick: Direction (relative to user)                           │
│    └── Buttons: Mode transitions (walk/stand/sit)                       │
│    └── Physical E-stop button                                           │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Key Architectural Decisions

### Decision 1: Distributed TinyML Instead of Centralized Processing

**Problem:** BLE latency (7.5-30ms per hop) plus central processing creates unacceptable delay.

**Solution:** Run gait phase classification on each Arduino locally.

| Approach | Latency | Bandwidth |
|----------|---------|-----------|
| Raw IMU streaming (100Hz × 6 axes) | 15-30ms BLE + processing | ~4.8 kbps per node |
| **On-device classification** | **~50ms inference, 1ms trigger** | **~100 bps (events only)** |

Shanks nodes output GPIO directly to FES trigger lines for minimum-latency critical path.

### Decision 2: Hybrid Rule-Based + ML Detection

**Problem:** ML alone has higher latency; rules alone have lower accuracy on abnormal gait.

**Solution:** Finite State Machine (FSM) with ML-enhanced phase detection.

```
Primary: Rule-based detection (gyroscope zero-crossings)
  ├── Heel-strike: Sagittal gyro negative → positive crossing
  ├── Toe-off: Sagittal gyro positive → negative crossing
  └── Accuracy: 97-99% with <50ms latency

Backup: TinyML classifier
  ├── LSTM or CNN on IMU features
  ├── Handles abnormal gait patterns
  └── Accuracy: >99% with 50-80ms latency
```

### Decision 3: FSR Insoles Despite Added Complexity

**Problem:** IMUs alone achieve only 90% ground contact detection accuracy[^1].

**Solution:** Add FSR insoles (3 sensors per foot: heel, 1st metatarsal, 5th metatarsal).

| Detection Method | Accuracy | Ground Contact Detection |
|------------------|----------|--------------------------|
| IMU only | 90% | Inferred from kinematics |
| **IMU + FSR** | **99-100%** | **Direct measurement** |

The ~$50-100 additional cost is justified by the reliability improvement for safety-critical gait event detection.

### Decision 4: Modulated TENS Units Instead of Custom Stimulator

**Problem:** Building a safe, charge-balanced stimulator from scratch is high-risk.

**Solution:** Use FDA-cleared TENS units with relay-controlled modulation (openEMSstim approach[^2]).

**Advantages:**
- Known safe output characteristics
- Existing charge balancing
- Reduced regulatory/safety burden
- Faster development

**Limitations:**
- Less precise timing control
- Fixed pulse parameters on some units
- May need multiple units for 8 channels

### Decision 5: Independent Safety Watchdog

**Problem:** If the main controller crashes, stimulation must stop immediately.

**Solution:** Dedicated RP2040 with:
- Independent power supply (separate LDO from battery)
- Window watchdog (not just timeout—too early OR too late = fault)
- Hardware kill chain (AND gate with E-stop, thermal, overcurrent)
- Survives main system failure

```
Safety Priority Chain:
  [Physical E-Stop] ───┐
  [Watchdog Timeout] ──┼── AND Gate ─→ [Power Relay] ─→ FES Enable
  [Overcurrent Trip] ──┤
  [Thermal Trip] ──────┘
```

---

## Latency Budget

### Critical Trigger Path (Shank → FES)

| Stage | Time | Cumulative |
|-------|------|------------|
| IMU sampling (100Hz) | 10ms | 10ms |
| TinyML inference | 50ms | 60ms |
| GPIO trigger | 1ms | 61ms |
| Stimulator response | 2ms | **63ms** |

This 63ms electronics latency is then added to:
- **EMD (unfatigued):** 50-100ms → **Total: 113-163ms**
- **EMD (fatigued):** 100-300ms → **Total: 163-363ms**

### Compensation Strategy

At 0.15 m/s walking speed:
- Gait cycle: ~3 seconds
- Swing phase: ~1.2 seconds (40%)
- Stance phase: ~1.8 seconds (60%)

Phase-advance triggering at 50% phase completion provides ~600-900ms advance notice—sufficient to compensate even worst-case EMD.

---

## Communication Architecture

### BLE Topology

```
                    ┌─────────────────┐
                    │   RPi5 Central  │
                    │   (Coordinator)  │
                    └────────┬────────┘
                             │ BLE Central
         ┌───────────────────┼───────────────────┐
         │                   │                   │
    ┌────┴────┐         ┌────┴────┐         ┌────┴────┐
    │ Shank L │         │ Shank R │         │  Trunk  │
    │  Node   │         │  Node   │         │  Node   │
    └────┬────┘         └────┬────┘         └─────────┘
         │                   │
    Direct GPIO         Direct GPIO
         │                   │
    ┌────┴────┐         ┌────┴────┐
    │ FES L   │         │ FES R   │
    │ Trigger │         │ Trigger │
    └─────────┘         └─────────┘
```

### Data Rates

| Node | Data Type | Rate | BLE Mode |
|------|-----------|------|----------|
| Shanks | Phase events | ~2-4 Hz | Notify |
| Feet | Ground contact | ~10 Hz | Notify |
| Thighs | Joint angles | ~20 Hz | Notify |
| Trunk | Balance state | ~10 Hz | Notify |

Total BLE bandwidth: <10 kbps (well within BLE 5.0 capacity)

---

## Power Architecture

See [[06-power/Power-Requirements|Power Requirements]] for detailed analysis.

**Summary:**
- 4S Li-ion battery (14.8V nominal, 50-75Wh)
- Distributed buck/boost converters
- Independent watchdog power supply
- Estimated runtime: 4-8 hours mixed use

---

## References

[^1]: PMC. "Towards Inertial Sensor Based Mobile Gait Analysis."
[^2]: Lopes P et al. openEMSstim: Open-source electrical muscle stimulation toolkit.
