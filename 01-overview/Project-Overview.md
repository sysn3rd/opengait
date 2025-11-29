# OpenGait Project Overview

## Executive Summary

**OpenGait** is an open-source closed-loop Functional Electrical Stimulation (FES) walking system designed to restore ambulatory capability for individuals with complete spinal cord injury (SCI). The project targets a hardware cost under $2,000—an order of magnitude less than commercial alternatives—while implementing closed-loop control that doesn't exist in any commercially available surface FES system.

---

## The Problem

### Commercial FES Walking Systems Are Inaccessible

The **Parastep I** (Sigmedics Inc.) is the only FDA-approved surface FES walking system in the United States[^1]. Approved in 1994, it:

- Costs approximately $13,000 (excluding training)[^2]
- Uses **open-loop control**: pre-programmed stimulation sequences triggered by manual button presses on a walker[^3]
- Provides no sensor feedback, no adaptation to gait phase, no compensation for muscle fatigue
- Achieves walking distances of only 6-9m for most users[^4]
- Only 34% of users achieve independent ambulation[^5]

### Research Systems Show What's Possible

The Cleveland FES Center has demonstrated implanted 16-channel systems achieving:
- Walking speeds of 0.4-0.5 m/s[^6]
- Walking distances up to 309m[^7]
- 10x speed improvements over pre-implant capability[^7]

But these remain locked in research labs, costing $50,000+ for implanted systems[^8].

### The Gap

| System | Cost | Control Type | Walking Speed | Availability |
|--------|------|--------------|---------------|--------------|
| Parastep I | ~$13,000 | Open-loop | 6-9m distance | Commercial |
| Cleveland FES (implanted) | >$50,000 | Closed-loop | 0.4-0.5 m/s | Research only |
| Commercial Exoskeletons | >$90,000 | Powered | 0.26-0.31 m/s | Commercial |
| **OpenGait (target)** | **<$2,000** | **Closed-loop** | **0.1-0.2 m/s** | **Open-source** |

---

## Project Goals

### Primary Objectives

1. **Closed-Loop Control**: Implement sensor-driven stimulation timing that adapts to actual gait phase—not available in any commercial surface FES system
2. **Latency Compensation**: Use predictive algorithms to compensate for 50-150ms electromechanical delay
3. **Accessible Cost**: Hardware BOM under $2,000
4. **Open-Source**: Full documentation enabling replication and community contribution
5. **Personal Use**: Designed for the project creator's own rehabilitation (L4 complete SCI)

### Target Performance

| Metric | Target | Rationale |
|--------|--------|-----------|
| Walking speed | 0.1-0.2 m/s | Consistent with surface FES literature |
| Session duration | 15-30 min | Fatigue-limited |
| Gait phase accuracy | >97% | FSM + TinyML hybrid |
| Trigger latency | <60ms | Before EMD compensation |

---

## Technical Approach

### Distributed Edge Architecture

Rather than a centralized controller, OpenGait uses **distributed TinyML inference** on wearable sensor nodes:

1. **7 Arduino Nano 33 BLE Sense Rev2** sensor nodes run gait phase classification locally
2. Nodes transmit only **state transitions**, not raw IMU data, reducing BLE bandwidth
3. **Raspberry Pi 5 + Hailo-8L AI HAT** handles multi-sensor fusion and trajectory prediction
4. **Direct GPIO triggering** from shank sensors provides minimum-latency stimulation path

### Predictive Control for Latency Compensation

The fundamental challenge isn't "make muscles contract"—it's **timing**. Total system delay can reach 200-400ms:

| Component | Delay |
|-----------|-------|
| Electronics (sensing → stimulation) | 35-100 ms |
| Electromechanical delay (unfatigued) | 50-100 ms |
| Electromechanical delay (fatigued) | 100-300 ms |

OpenGait compensates through:
- **Phase-advance triggering**: Stimulate for next phase at 50-75% current phase completion[^9]
- **LSTM trajectory prediction**: 200ms lookahead on Hailo AI accelerator
- **Adaptive timing**: Learn from previous gait cycles

### Safety-First Design

- Dedicated **RP2040 watchdog MCU** with independent power supply
- **Hardware current limiting** at 60mA (cannot be overridden by software)
- **Charge-balanced biphasic pulses** with DC blocking capacitor
- **Physical emergency stop** with direct opening action per ISO 13850

---

## Current Status

**Phase:** Architecture & Documentation

| Completed | In Progress | Planned |
|-----------|-------------|---------|
| System architecture | Hardware sourcing | Sensor node assembly |
| Component selection | Power system design | Safety system validation |
| BOM validation | Notebook compilation | Unity3D simulation |
| Safety design | | Bench testing |

No hardware has been assembled. No walking has occurred.

---

## Why Document First?

This documentation-first approach serves multiple purposes:

1. **Forces explicit architectural decisions** before committing to hardware
2. **Enables external review** of safety-critical design before building
3. **Supports open-source goal**: If documentation is complete enough for someone else to build, the architecture is probably clear enough to work
4. **Captures research** that would otherwise be lost in browser tabs

---

## References

[^1]: FEP Blue. (2024). "Functional Neuromuscular Electrical Stimulation." Medical Policy Manual.
[^2]: Sigmedics Inc. "Parastep I FAQ."
[^3]: Graupe D, Kohn KH. "Functional neuromuscular stimulator for short-distance ambulation." Surgical Neurology 1998.
[^4]: ScienceDirect. "Functional Electrical Stimulation Control of Standing and Stepping After Spinal Cord Injury."
[^5]: Christopher Reeve Foundation. (2024). "ParaStep."
[^6]: Kobetic R, Triolo RJ, Marsolais EB. "Muscle selection and walking performance of multichannel FES systems." IEEE Trans Rehabil Eng 1997.
[^7]: Hardin E et al. "Walking after incomplete spinal cord injury using an implanted FES system." JRRD 2007.
[^8]: DiMarco AF et al. "Costs associated with procedures and devices." PM&R 2016.
[^9]: Zahradka N et al. "Evaluation of Gait Phase Detection Delay Compensation Strategies." Sensors 2019.
