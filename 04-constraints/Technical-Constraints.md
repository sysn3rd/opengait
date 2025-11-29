# Technical Constraints

## The Fundamental Timing Problem

Walking involves precisely sequenced muscle activations across the gait cycle. Miss the timing window by 100ms and you get a stumble, not a step.

OpenGait must solve a multi-layered timing problem where each layer adds delay:

```
Event occurs → Sensor detects → Algorithm classifies → System triggers → Muscle contracts
     ↑                                                                          ↓
     └──────────────────── 150-400ms total delay ─────────────────────────────┘
```

---

## Latency Budget Analysis

### Component-Level Delays

| Component | Minimum | Typical | Maximum | Notes |
|-----------|---------|---------|---------|-------|
| **Sensing** |
| IMU sampling | 5ms | 10ms | 20ms | 100Hz sample rate |
| FSR sampling | 1ms | 5ms | 10ms | Analog to digital |
| **Processing** |
| TinyML inference | 15ms | 50ms | 80ms | On Arduino |
| BLE communication | 7.5ms | 15ms | 30ms | Connection interval |
| RPi processing | 5ms | 15ms | 30ms | LSTM prediction |
| **Output** |
| Stimulator response | 1ms | 3ms | 5ms | Relay + pulse generation |
| **Electronics Subtotal** | **35ms** | **100ms** | **175ms** | |
| **Physiological** |
| EMD (unfatigued) | 30ms | 50ms | 100ms | Fresh muscle |
| EMD (fatigued) | 100ms | 150ms | 300ms | After 15+ min |
| **Total System** | **65ms** | **250ms** | **475ms** | |

### Gait Phase Windows

At target walking speed (0.1-0.2 m/s):

| Phase | Duration | Percentage | Available Window |
|-------|----------|------------|------------------|
| Loading response | 300-500ms | 10% | Must support weight |
| Midstance | 600-900ms | 20% | Knee extension critical |
| Terminal stance | 600-900ms | 20% | Push-off preparation |
| Pre-swing | 300-400ms | 10% | Transition phase |
| Swing | 1000-1500ms | 40% | Foot clearance critical |

**Critical observation:** Even worst-case 475ms delay is manageable if predicted 500ms+ in advance.

---

## Electromechanical Delay (EMD)

### Definition

EMD is the time between electrical muscle activation and mechanical force production[^1]:

```
Electrical activation → Action potential propagation → Excitation-contraction coupling
                     → Series elastic component stretch → Force at tendon
```

### Measured Values

| Muscle | Condition | EMD | Source |
|--------|-----------|-----|--------|
| Quadriceps (RF) | Voluntary | 49.73 ± 6.99ms | [^2] |
| Quadriceps | Supramaximal stim | 8.5ms | [^2] |
| Tibialis anterior | Force-dependent | Logarithmic decrease | [^3] |
| General | Fatigued | 100-300ms | [^4] |

### Time-Varying Nature

**Critical design constraint:** EMD is not constant[^4]:

- Increases significantly with muscle fatigue
- Can double or triple during a 30-minute session
- Control algorithms must adapt in real-time

This rules out simple fixed-offset compensation. OpenGait must use **adaptive prediction**.

---

## Muscle Fatigue Constraints

### Fatigue Characteristics Under FES

FES causes faster fatigue than voluntary contraction due to[^5]:

1. **Reverse recruitment order:** Type II (fatigable) fibers recruited before Type I
2. **Synchronous activation:** All recruited motor units fire simultaneously
3. **Spatially fixed pattern:** Same motor units stimulated repeatedly
4. **No rotation:** Cannot alternate which fibers contribute

### Practical Limits

| Parameter | Typical Value |
|-----------|---------------|
| Session duration | 15-30 minutes |
| Reps before fatigue | 10-15 contractions |
| Force decay | 50% reduction over session |
| Recovery needed | Hours between sessions |

### Mitigation Strategies Implemented in OpenGait

1. **Lower frequencies:** 35-40 Hz vs higher frequencies that accelerate fatigue
2. **Intermittent stimulation:** Active only during required gait phases
3. **Adaptive intensity:** Increase current as fatigue develops (within limits)
4. **Fatigue monitoring:** Track force decline, reduce session if excessive

---

## Surface Stimulation Limits

### What Surface Electrodes Cannot Do

| Limitation | Impact on Design |
|------------|------------------|
| Cannot reach deep muscles (iliopsoas) | No direct hip flexor stimulation |
| Less selective recruitment | Multiple muscles may activate |
| Variable electrode contact | Daily placement variability |
| Skin resistance changes | Current requirements vary |

### Compensation Strategies

**Hip flexion problem:** Surface electrodes cannot directly stimulate iliopsoas.

Solutions implemented:
1. **Peroneal nerve stimulation:** Triggers flexor withdrawal reflex (combined hip/knee/ankle flexion)[^6]
2. **Trunk lean detection:** Forward lean initiates swing phase
3. **KAFO assistance:** Mechanical joint provides backup

---

## BLE Communication Constraints

### Theoretical Limits

| Parameter | Value | Constraint |
|-----------|-------|------------|
| Connection interval | 7.5ms minimum | iOS limitation: 15ms minimum |
| Data rate | 2 Mbps (BLE 5.0) | Practical: 1-2 Mbps |
| Number of peripherals | 7 recommended | nRF52840 limit |
| Latency jitter | ±7.5ms | Connection interval dependent |

### OpenGait Design Choices

| Choice | Rationale |
|--------|-----------|
| 7 nodes (not more) | Within reliable peripheral limit |
| Event-based (not streaming) | Reduces bandwidth by 50x |
| Direct GPIO for critical path | Bypasses BLE for timing-critical triggers |
| 15ms connection interval | Balance between latency and power |

---

## Processing Constraints

### Arduino Nano 33 BLE Sense Rev2

| Resource | Available | TinyML Usage | Remaining |
|----------|-----------|--------------|-----------|
| Flash | 1 MB | ~200-400 KB | 600+ KB |
| RAM | 256 KB | ~50-100 KB | 150+ KB |
| CPU | 64 MHz | Inference: 100% during ~50ms | Idle between |

**Inference time budget:** 50ms maximum for gait phase classification

### Raspberry Pi 5 + Hailo-8L

| Task | Compute | Latency |
|------|---------|---------|
| LSTM trajectory prediction | Hailo-8L | ~10-20ms |
| Sensor fusion | ARM cores | ~5-10ms |
| Safety monitoring | ARM cores | ~5ms |
| Logging | SSD I/O | Async |

**Total processing latency:** 15-30ms on central unit

---

## Safety System Constraints

### IEC 60601-2-10 Limits

| Parameter | Limit | OpenGait Design |
|-----------|-------|-----------------|
| Maximum current | 60mA safe | Hardware limit at 60mA |
| Charge density | ≤30 μC/cm² | Electrode sizing |
| Maximum energy | 300mJ per pulse | Pulse parameter limits |
| DC offset | <10 μA | Biphasic + blocking cap |

### Response Time Requirements

| Fault | Required Response | Implementation |
|-------|-------------------|----------------|
| Overcurrent | <10μs | Hardware comparator |
| Watchdog timeout | 100ms window | RP2040 independent |
| E-stop | <20ms | Direct relay control |
| Electrode fault | <1 pulse | Impedance monitoring |

---

## Environmental Constraints

### Operating Conditions

| Parameter | Range | Notes |
|-----------|-------|-------|
| Temperature | 10-35°C | Indoor use primarily |
| Humidity | 20-80% RH | Electrode adhesion affected |
| Motion artifacts | Continuous | Must filter from signals |

### Wearability

| Constraint | Target | Impact |
|------------|--------|--------|
| Total weight | <2 kg | Battery + electronics |
| Cable routing | Minimal | BLE wireless sensors |
| Donning time | <10 min | Electrode + sensor setup |

---

## References

[^1]: PMC. "Detection of the electromechanical delay and its components."
[^2]: PMC. "Detection of the electromechanical delay during voluntary isometric contraction of the quadriceps."
[^3]: PubMed. "Electromechanical delay in the tibialis anterior muscle."
[^4]: PubMed. "The Time-Varying Nature of Electromechanical Delay and Muscle Control Effectiveness."
[^5]: Physiopedia. "Functional Electrical Stimulation Cycling for Spinal Cord Injury."
[^6]: Salisbury FES. "The use of electrical stimulation for correction of dropped foot."
