# OpenGait Power System Requirements Research

**Document Version:** 1.0
**Date:** 2024-11-29
**Project:** OpenGait - Open-Source FES Walking System

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Component Power Consumption](#1-component-power-consumption)
3. [FES Stimulation Power Analysis](#2-fes-stimulation-power-analysis)
4. [Battery Technology Options](#3-battery-technology-options)
5. [Power Management Strategies](#4-power-management-strategies)
6. [Safety Requirements](#5-safety-requirements)
7. [System Power Budget](#6-system-power-budget)
8. [Recommendations](#7-recommendations)
9. [References](#references)

---

## Executive Summary

This document analyzes the power system requirements for the OpenGait portable FES walking system. The system comprises distributed sensor nodes, a central processing unit with AI acceleration, safety monitoring, and 8-channel functional electrical stimulation.

**Key Findings:**

| Subsystem | Estimated Power |
|-----------|-----------------|
| 7x Arduino Nano 33 BLE Sense Rev2 | 0.7-2.1 W total |
| Raspberry Pi 5 + Hailo-8L | 5-12 W typical |
| RP2040 Safety Watchdog | <50 mW |
| 8-channel FES Stimulation | 2-8 W (load dependent) |
| FSR Insoles + Conditioning | <100 mW |
| **Total System (Typical)** | **8-22 W** |

**Recommended Battery Capacity:** 50-100 Wh (for 4-8 hours operation)

---

## 1. Component Power Consumption

### 1.1 Arduino Nano 33 BLE Sense Rev2

The Arduino Nano 33 BLE Sense Rev2 uses the Nordic nRF52840 SoC with integrated BLE and multiple onboard sensors [^1].

#### nRF52840 Core Power Consumption

| Operating Mode | Current (DC/DC @ 3V) | Notes |
|----------------|----------------------|-------|
| CPU Active (64 MHz) | 3-4 mA | Cortex-M4 running |
| BLE TX (+8 dBm) | 16.4 mA | Maximum TX power |
| BLE TX (+4 dBm) | 8.7 mA | Typical TX power |
| BLE TX (0 dBm) | 6.4 mA | Low power TX |
| BLE RX (1 Mbps) | 6.26 mA | Receive mode |
| System ON Idle | ~3 mA | Peripherals active |
| Sleep (RAM retention) | 0.9 mA | Achievable with optimizations |

**Source:** Nordic nRF52840 Product Specification [^2]

#### TinyML Inference Power

Edge Impulse reports TinyML inference on nRF52840 can complete in ~1 ms using 1.5 KB RAM [^3]. During inference bursts, expect:

- **Inference active:** 3-4 mA (CPU at 64 MHz)
- **Duty-cycled (10% inference):** ~1.5 mA average

#### Board-Level Estimates (per node)

| Mode | Current @ 3.3V | Power |
|------|----------------|-------|
| Active (IMU + BLE TX + Inference) | 25-40 mA | 82-132 mW |
| Idle (BLE connected, IMU sampling) | 10-15 mA | 33-50 mW |
| Deep sleep (minimal activity) | 0.9-3 mA | 3-10 mW |

**For 7 nodes (worst case active):** 7 x 132 mW = **924 mW (~1 W)**

**Optimization Tips from Arduino [^4]:**
- Disable power LED: `digitalWrite(LED_PWR, LOW);`
- Disable sensors when unused: `digitalWrite(PIN_ENABLE_SENSORS_3V3, LOW);`
- Disable I2C pull-ups: `digitalWrite(PIN_ENABLE_I2C_PULLUP, LOW);`

---

### 1.2 Raspberry Pi 5

The Raspberry Pi 5 has significantly higher power requirements than previous generations [^5][^6].

| Operating State | Power Consumption | Notes |
|-----------------|-------------------|-------|
| Powered Off (EEPROM default) | 1.3 W | Standby state |
| Powered Off (optimized) | 0.01-0.05 W | POWER_OFF_ON_HALT=1 |
| Idle (desktop) | 2.2-3.2 W | Varies with RAM (2GB-8GB) |
| Idle (headless, optimized) | 2.0-2.5 W | No display |
| Typical workload | 4-7 W | Light processing |
| CPU stress (Cortex-A76) | 6.8-8 W | All 4 cores loaded |
| Peak (stress + USB + video) | 12-16 W | Maximum draw |

**New 2GB Pi 5 (D0 stepping):** ~30% idle power savings compared to original [^7]

**Critical Notes:**
- Requires 5V/5A (27W) power supply for full USB current capability [^8]
- USB ports limited to 600 mA total if <5A supply detected
- USB-PD negotiation uses non-standard 5V/5A profile

---

### 1.3 Hailo-8L AI HAT

The Hailo-8L provides 13 TOPS of AI inference acceleration [^9].

| Operating State | Power Consumption |
|-----------------|-------------------|
| Idle | ~0.5 W (estimated) |
| Typical inference | 1.5 W |
| Heavy inference (60 FPS video) | 1-2 W |
| Peak | ~4-5 W |

**Efficiency:** ~1 W per 3 TOPS, or approximately **2.6 TOPS/W** [^10]

**Combined RPi5 + Hailo-8L System:**

| Scenario | Total Power |
|----------|-------------|
| Idle | 3-4 W |
| Typical AI workload | 5-7 W |
| Heavy inference | 8-12 W |

**Note:** The Hailo-8L AI Kit does not include power measurement capabilities on the module itself [^11].

---

### 1.4 RP2040 Safety Watchdog

The RP2040 has limited low-power capabilities compared to other MCUs [^12].

| Operating State | Current | Power @ 3.3V |
|-----------------|---------|--------------|
| Active (both cores, 125 MHz) | <100 mA | <330 mW |
| Active (single core, reduced clock) | 10-25 mA | 33-82 mW |
| Sleep (clocks stopped) | 0.39 mA | 1.3 mW |
| Dormant (all clocks off) | 0.18 mA | 0.6 mW |
| **Pico Board Sleep** | 0.8 mA | 2.6 mW |
| **Pico Board Dormant** | 1.3 mA | 4.3 mW |

**Board vs Chip Power:** The RP2040 chip alone draws ~180 uA dormant, but development boards include voltage regulators and flash that increase total draw [^13].

**Alternative Consideration:** The RP2350 (Pico 2) achieves ~10 uA sleep current and would be preferable for a dedicated watchdog application [^14].

**For Safety Watchdog Application:**
- Running simple watchdog code at reduced clock
- Estimated: **10-20 mA = 33-66 mW**

---

### 1.5 Force Sensing Resistors (FSRs)

FSRs are passive devices with inherently low power consumption [^15][^16].

| Component | Current | Power |
|-----------|---------|-------|
| FSR sensor (passive) | <1 uA | Negligible |
| Voltage divider circuit (per sensor) | 0.1-1 mA | 0.3-3.3 mW |
| ADC/Signal conditioning (per insole) | 5-10 mA | 16-33 mW |

**For 2 insoles with 4-8 FSRs each:**
- Sensor power: Negligible
- Conditioning circuits: **20-50 mW total**

---

### 1.6 TENS/FES Stimulator Units

Commercial TENS units typically use small rechargeable batteries [^17]:

| Device Type | Battery | Estimated Power |
|-------------|---------|-----------------|
| Basic TENS | 700 mAh @ 4.8V (Ni-MH) | 100-500 mW active |
| Dual-channel TENS | 600-1200 mAh LiPo | 200-800 mW active |
| Medical FES (8-ch) | Custom battery pack | 1-5 W active |

**Note:** Actual stimulation power depends heavily on pulse parameters and tissue impedance.

---

## 2. FES Stimulation Power Analysis

### 2.1 Stimulation Parameters

Typical FES parameters for walking assistance [^18][^19]:

| Parameter | Range | Typical for Gait |
|-----------|-------|------------------|
| Compliance Voltage | 60-135 V | 80 V |
| Current Amplitude | 10-110 mA | 10-30 mA |
| Pulse Width | 50-500 us | 150-300 us |
| Frequency | 10-150 Hz | 20-50 Hz |
| Waveform | Biphasic symmetric | Charge-balanced |

### 2.2 Power Calculation

#### Per-Channel Average Power

For a single biphasic pulse:

```
Pulse Energy = V_compliance x I_stim x t_pulse x 2 (biphasic)
             = 80 V x 0.020 A x 300 us x 2
             = 80 x 0.020 x 0.0003 x 2
             = 0.96 mJ per pulse
```

At 40 Hz stimulation frequency:

```
Average Power = Pulse Energy x Frequency
              = 0.96 mJ x 40 Hz
              = 38.4 mW per channel
```

#### Duty Cycle Considerations

Electrical duty cycle (pulse on-time ratio):

```
Duty Cycle = Pulse Width x Frequency
           = 300 us x 40 Hz
           = 0.012 = 1.2%
```

During walking, channels are active in patterns (not all simultaneously):
- Swing phase: 2-4 channels active (~40% of gait cycle)
- Stance phase: 2-4 channels active (~60% of gait cycle)

**Effective stimulation duty cycle:** 50-80% during walking

### 2.3 Total Stimulation Power Demand

#### Scenario Analysis

| Scenario | Active Channels | Current/Ch | Power @ Output |
|----------|-----------------|------------|----------------|
| Minimal | 2 | 15 mA | 76 mW |
| Typical walking | 4 | 20 mA | 307 mW |
| Maximum (all ch) | 8 | 30 mA | 1.23 W |
| Peak transient | 8 | 30 mA (burst) | 2-3 W |

### 2.4 Boost Converter Considerations

High-voltage generation requires efficient DC-DC conversion [^20][^21].

| Topology | Efficiency | Notes |
|----------|------------|-------|
| Basic boost converter | 70-80% | Simple, adequate for low power |
| Flyback converter | 75-85% | Better for isolation |
| Optimized flyback (FES-specific) | 80-90% | Research designs [^22] |
| Synchronous boost (high-end) | 90-95% | Higher component cost |

**Battery-side power requirement:**

```
P_battery = P_stimulation / Efficiency
          = 1.23 W / 0.80
          = 1.54 W (typical efficiency)

          = 1.23 W / 0.70
          = 1.76 W (worst case efficiency)
```

**For the complete FES subsystem:**

| Component | Power |
|-----------|-------|
| Stimulation output | 0.3-1.2 W |
| Boost converter losses | 0.1-0.4 W |
| Control electronics | 0.1-0.2 W |
| **Total FES Subsystem** | **0.5-2.0 W typical** |
| **Peak (all channels, max)** | **3-4 W** |

---

## 3. Battery Technology Options

### 3.1 Technology Comparison

| Technology | Energy Density | Cycle Life | Safety | Cost |
|------------|----------------|------------|--------|------|
| Li-ion 18650 | 200-265 Wh/kg | 500-1000 | Good (metal case) | Low |
| LiPo Pouch | 250-300 Wh/kg | 300-500 | Moderate | Medium |
| LiFePO4 | 90-160 Wh/kg | 2000+ | Excellent | Medium |
| Li-ion Pouch (medical grade) | 200-250 Wh/kg | 500-800 | Good | High |

**Sources:** [^23][^24]

### 3.2 18650 Cell Analysis

Standard 18650 specifications:

| Parameter | Value |
|-----------|-------|
| Nominal voltage | 3.6-3.7 V |
| Capacity range | 2000-3500 mAh |
| Weight | ~45-50 g per cell |
| Dimensions | 18 mm dia x 65 mm |
| Max discharge | 1-3C typical (protected) |

**Configuration for OpenGait:**

| Config | Voltage | Capacity | Energy | Weight | Notes |
|--------|---------|----------|--------|--------|-------|
| 2S2P (4 cells) | 7.4 V nominal | 6000-7000 mAh | 44-52 Wh | 180-200 g | Minimum viable |
| 3S2P (6 cells) | 11.1 V nominal | 6000-7000 mAh | 67-78 Wh | 270-300 g | Better headroom |
| 4S2P (8 cells) | 14.8 V nominal | 6000-7000 mAh | 89-104 Wh | 360-400 g | Airline limit |

**Note:** FAA/TSA limit for carry-on is 100 Wh without approval [^25].

### 3.3 LiPo Pouch Cell Options

LiPo offers form-factor flexibility crucial for wearables [^26]:

**Advantages for OpenGait:**
- Custom shapes to fit body contours
- Higher energy density (Wh/L)
- Lighter for equivalent capacity
- Thinner profiles possible

**Disadvantages:**
- More susceptible to damage
- Shorter cycle life under stress
- Requires more robust protection
- Higher swelling risk

**Recommended specifications for medical wearables:**
- Capacity: 5000-10000 mAh
- Discharge rate: 1-2C continuous
- Operating temp: 0-45 C
- Certified: UL2054, IEC62133

### 3.4 Commercial Power Bank Analysis

Using USB-PD power banks presents challenges [^27][^28]:

| Requirement | Challenge |
|-------------|-----------|
| 5V @ 5A (27W) | Non-standard USB-PD mode |
| Continuous operation | Most banks have auto-off |
| Peak current handling | Inrush may trip protection |
| Form factor | Often bulky for wearables |

**Viable Options:**

1. **Dedicated 5V/5A converter boards** (e.g., Pichondria) that accept standard USB-PD and output 5V/5A [^29]

2. **Direct battery + buck converter approach:**
   - Use 3S or 4S LiPo/Li-ion pack
   - High-current buck converter (6A+ rating)
   - Configure Pi 5: `PSU_MAX_CURRENT=5000` in EEPROM

3. **Split power architecture:**
   - Separate battery for RPi5 (needs 5V/5A)
   - Separate battery for FES and sensors (3.7-12V)

### 3.5 Weight vs Capacity Analysis

For a wearable walking system, weight is critical:

| Runtime Target | Energy Needed | 18650 Config | Weight | LiPo Weight |
|----------------|---------------|--------------|--------|-------------|
| 2 hours | 20-44 Wh | 2S2P | 200 g | 150 g |
| 4 hours | 40-88 Wh | 3S2P | 300 g | 230 g |
| 8 hours | 80-176 Wh | 4S3P | 550 g | 420 g |

**Target:** <500 g total battery weight for acceptable wearability

---

## 4. Power Management Strategies

### 4.1 Architecture Options

#### Option A: Centralized Power

```
[Battery Pack] --> [Main DCDC] --> [Power Distribution Board]
                                          |
                    +---------------------+---------------------+
                    |          |          |          |          |
                 [RPi5]    [FES HV]   [Sensors]  [Watchdog]
                 5V/5A     12V->80V    3.3V       3.3V
```

**Pros:**
- Single BMS and protection circuit
- Simplified charging
- Centralized monitoring

**Cons:**
- Single point of failure
- Complex power sequencing
- Heavy cable routing

#### Option B: Distributed Power (Recommended)

```
[Main Battery] --> [BMS] --> [Bus 12V] --> [Distributed to subsystems]
                                |
            +-------------------+-------------------+
            |                   |                   |
    [RPi5 Buck 5V/5A]    [FES Boost 80V]    [Sensor 3.3V LDO]
            |                   |                   |
    [Safety Watchdog]--[Kill Switch]--[Isolation Relay]
```

**Pros:**
- Fault isolation between subsystems
- Optimized converters per load
- Watchdog can kill FES independently
- Lower cable losses

**Cons:**
- More components
- Multiple voltage domains
- Complex system integration

### 4.2 Startup Surge Handling

The Raspberry Pi 5 presents significant startup challenges [^30]:

#### Measured Behavior
- Boot inrush: Can exceed 3A momentarily
- Typical boot power: 5-8W during kernel load
- Settling time: 10-30 seconds to idle

#### Mitigation Strategies

1. **Soft-start sequencing:**
   ```
   Power-on sequence:
   1. Enable watchdog MCU (immediate)
   2. Enable sensor nodes (100ms delay)
   3. Enable FES subsystem (disabled until needed)
   4. Enable RPi5 (soft-start, 500ms ramp)
   5. RPi5 signals ready -> enable FES output stage
   ```

2. **Bulk capacitance:**
   - Add 1000-4700 uF electrolytic on 5V rail
   - Helps absorb transients
   - Size for: C = I_peak x dt / dV_allowed

3. **Current-limited startup:**
   - Use eFuse or hot-swap controller
   - TPS2596x or similar rated for 5V/6A
   - Programmable current limit and slew rate

4. **Pre-boost battery voltage:**
   - Use 4S pack (14.8V nominal) with buck to 5.2V
   - Higher input voltage = lower input current
   - More headroom for transients

### 4.3 Sleep Modes and Dynamic Power Scaling

#### Raspberry Pi 5 Power States

| State | Power | Wake Time | Use Case |
|-------|-------|-----------|----------|
| Active | 5-12 W | - | Normal operation |
| Idle | 2.5-3 W | - | Waiting for input |
| Suspend | ~1.3 W | ~2 s | Short pauses |
| Halt (optimized) | 0.05 W | 5-10 s | Extended standby |

#### Arduino Nano 33 BLE Power States

| State | Current | Wake Source |
|-------|---------|-------------|
| Active (BLE + IMU) | 15-30 mA | - |
| BLE advertising only | 5-10 mA | BLE connection |
| System ON sleep | 3-5 mA | Timer, GPIO |
| Deep sleep | 0.9 mA | GPIO interrupt |

**Recommended Strategy:**

1. **During active walking:**
   - RPi5: Full active (AI inference)
   - Sensors: Active, BLE connected
   - FES: Active as needed
   - Total: 10-15 W

2. **Standing/paused:**
   - RPi5: Idle (reduced clock)
   - Sensors: Reduced sample rate
   - FES: Standby (HV converter disabled)
   - Total: 4-6 W

3. **Extended pause (>5 min):**
   - RPi5: Suspend
   - Sensors: Deep sleep
   - FES: Off (power isolated)
   - Watchdog: Active (monitoring)
   - Total: 0.5-1.5 W

### 4.4 Thermal Considerations

#### Heat Sources

| Component | Dissipation | Location |
|-----------|-------------|----------|
| RPi5 + Hailo | 5-10 W | Central unit |
| FES boost converter | 0.5-1 W | FES module |
| Battery pack | 0.5-2 W | During discharge |
| Buck converters | 0.2-0.5 W | Distributed |

#### Thermal Management Requirements

1. **Raspberry Pi 5:**
   - Active cooling required above 7W sustained
   - Official cooler: adds 0.3-0.5W fan power
   - Passive: thermal throttling above ~80C
   - Recommended: Active cooler with PWM control

2. **Battery pack:**
   - Operating range: 0-45C (discharge), 10-40C (charge)
   - Avoid body heat accumulation
   - Consider thermal barrier between battery and skin
   - NTC thermistor for temperature monitoring

3. **FES boost converter:**
   - Size heatsink for 1-2W dissipation
   - Ensure airflow or thermal path to enclosure
   - Monitor with thermistor, reduce output if >60C

---

## 5. Safety Requirements

### 5.1 Regulatory Framework

FES devices must comply with medical device regulations [^31][^32]:

| Standard | Scope |
|----------|-------|
| IEC 60601-1 | General safety for medical electrical equipment |
| IEC 60601-2-10 | Particular requirements for nerve/muscle stimulators |
| IEC 62133 | Battery safety |
| IEC 60601-1-11 | Home healthcare environment |
| ISO 13485 | Quality management systems |

#### Patient Protection Classification

FES devices typically require **Type BF (Body Floating)** classification [^33]:

| Requirement | Type BF Specification |
|-------------|----------------------|
| Isolation (input to output) | 4000 VAC |
| Isolation (output to ground) | 1500 VAC |
| Patient leakage (normal) | <100 uA |
| Patient leakage (fault) | <500 uA |
| Earth leakage (normal) | <500 uA |

### 5.2 Fail-Safe Power Design

#### Watchdog Power Isolation Architecture

```
                    [Main Battery]
                          |
                    [Main BMS]
                          |
            +-------------+-------------+
            |                           |
    [Watchdog Supply]           [System Bus]
    (independent LDO)                   |
            |                    [Watchdog-Controlled]
    [RP2040 Watchdog]------->[Power Relay/eFuse]
            |                           |
    [Hardware Timer]            [FES HV Supply]
            |                           |
    [Manual E-Stop]             [Stimulation Output]
```

#### Critical Safety Features

1. **Independent watchdog power:**
   - Separate LDO from battery (not through main DCDC)
   - Survives main system failure
   - RP2040 can operate from 1.8-3.3V
   - Add supercap for brief battery disconnect

2. **Watchdog timeout behavior:**
   - Window watchdog (not just standard WDT) [^34]
   - Must receive signal within time window
   - Too early OR too late = safety trigger
   - Recommended: 100ms window, 50ms timeout

3. **Hardware kill chain:**
   ```
   [Watchdog Output] --> [Logic AND] --> [FES Enable]
                              ^
   [E-Stop Button] -----------+
   [Overcurrent Flag] --------+
   [Thermal Flag] ------------+
   ```

4. **Power isolation methods:**

   | Method | Response Time | Current Rating | Cost |
   |--------|---------------|----------------|------|
   | P-FET + gate driver | 10-100 us | 5-20 A | Low |
   | eFuse IC | 1-10 us | 1-10 A | Medium |
   | Relay (latching) | 5-20 ms | 10-30 A | Low |
   | Solid-state relay | 100 us - 1 ms | 1-5 A | Medium |

   **Recommended:** eFuse for fast electronic cutoff + relay for galvanic isolation

### 5.3 Battery Protection Requirements

#### BMS Features Required

| Feature | Requirement | Notes |
|---------|-------------|-------|
| Overvoltage protection | 4.25V/cell max | Prevents thermal runaway |
| Undervoltage protection | 2.8V/cell min | Prevents cell damage |
| Overcurrent protection | 2-3x rated capacity | Prevents heating |
| Short circuit protection | <100us response | Prevents fire |
| Temperature monitoring | NTC per cell group | Charge/discharge limits |
| Cell balancing | Active or passive | For multi-cell packs |
| Pre-charge | Soft-start for capacitive loads | RPi5 bulk caps |

**Sources:** [^35][^36]

#### Secondary Protection

For medical applications, consider dual-layer protection [^37]:

1. **Primary:** BMS IC (integrated)
2. **Secondary:**
   - Fuse (non-resettable, last resort)
   - PTC (resettable overcurrent)
   - Temperature cutoff (TCO)
   - Current interrupt device (CID)

### 5.4 Output Stage Safety

#### Stimulation Safety Limits

| Parameter | Limit | Rationale |
|-----------|-------|-----------|
| Maximum current | 50 mA | Above this, risk of burns |
| Maximum charge/phase | 50 uC | Tissue damage threshold |
| Maximum voltage | 100 V | Typical for surface FES |
| DC offset | <10 uA | Prevents electrolysis |
| Charge imbalance | <1% | Prevents tissue damage |

#### Implementation Requirements

1. **Charge balancing:**
   - Use biphasic pulses with matched phases
   - Add blocking capacitor in series (10-47 uF)
   - Monitor DC offset, shut down if >10 uA

2. **Current limiting:**
   - Hardware current sense + comparator
   - Set trip point at 60 mA (above max, below damage)
   - Response time <10 us

3. **Electrode impedance monitoring:**
   - Measure before stimulation
   - Normal range: 200 ohm - 2 kohm
   - Too low (<100 ohm): possible short/wet electrode
   - Too high (>5 kohm): poor contact, risk of burns

---

## 6. System Power Budget

### 6.1 Operating Scenarios

#### Scenario 1: Active Walking (Maximum)

| Component | Count | Unit Power | Total |
|-----------|-------|------------|-------|
| RPi5 + Hailo (inference) | 1 | 10 W | 10 W |
| Nano 33 BLE (active TX) | 7 | 130 mW | 0.9 W |
| RP2040 watchdog | 1 | 50 mW | 0.05 W |
| FES (4 channels active) | 1 | 2 W | 2 W |
| FSR conditioning | 2 | 30 mW | 0.06 W |
| DC-DC conversion losses | - | ~15% | 2 W |
| Active cooling | 1 | 0.5 W | 0.5 W |
| **Total Maximum** | | | **15.5 W** |

#### Scenario 2: Active Walking (Typical)

| Component | Count | Unit Power | Total |
|-----------|-------|------------|-------|
| RPi5 + Hailo (typical) | 1 | 6 W | 6 W |
| Nano 33 BLE (duty-cycled) | 7 | 50 mW | 0.35 W |
| RP2040 watchdog | 1 | 50 mW | 0.05 W |
| FES (2 channels avg) | 1 | 1 W | 1 W |
| FSR conditioning | 2 | 30 mW | 0.06 W |
| DC-DC losses | - | ~10% | 0.75 W |
| Cooling (PWM) | 1 | 0.2 W | 0.2 W |
| **Total Typical** | | | **8.4 W** |

#### Scenario 3: Standby (Paused)

| Component | Count | Unit Power | Total |
|-----------|-------|------------|-------|
| RPi5 (idle) | 1 | 2.5 W | 2.5 W |
| Hailo (idle) | 1 | 0.5 W | 0.5 W |
| Nano 33 BLE (idle) | 7 | 15 mW | 0.1 W |
| RP2040 watchdog | 1 | 50 mW | 0.05 W |
| FES (standby) | 1 | 0.2 W | 0.2 W |
| DC-DC quiescent | - | - | 0.1 W |
| **Total Standby** | | | **3.5 W** |

### 6.2 Battery Sizing

| Target Runtime | Scenario | Battery Capacity Needed |
|----------------|----------|------------------------|
| 2 hours | Walking | 17-31 Wh |
| 4 hours | Walking | 34-62 Wh |
| 4 hours | Mixed (50% walk/50% stand) | 24-40 Wh |
| 8 hours | Walking | 67-124 Wh |
| 8 hours | Mixed | 48-80 Wh |

**Recommended capacity:** 50-75 Wh provides 4+ hours walking, 8+ hours mixed use

---

## 7. Recommendations

### 7.1 Power Architecture Recommendation

**Primary recommendation: Distributed 4S Li-ion architecture**

```
[4S Li-ion Pack (14.8V, 5Ah, 74Wh)]
              |
         [Main BMS]
              |
    +---------+---------+
    |         |         |
[Watchdog] [Buck    [Boost
 LDO]      5.2V/6A] 80V/150mA]
    |         |         |
[RP2040]   [RPi5]    [FES]
    |         |      Output
    |    [Hailo-8L]    |
    |                  |
    +--[Kill Relay]----+
```

**Key specifications:**
- Battery: 4S2P or 4S3P 18650 cells (Samsung 35E or similar)
- Main buck: LM5146 or similar (6A, 5.2V output)
- FES boost: Flyback topology, 80V, 200mA
- BMS: 4S, 20A continuous, with I2C interface
- Watchdog: RP2040 with independent 3.3V LDO

### 7.2 Component Selection Summary

| Subsystem | Recommended Component | Rationale |
|-----------|----------------------|-----------|
| Battery cells | Samsung INR18650-35E | High capacity (3500mAh), reliable |
| BMS | Texas Instruments BQ76952 | Medical-grade features, I2C |
| Main buck | TI LM5146 | 6A, high efficiency, integrated FETs |
| FES boost | LT3757 flyback | Up to 100V, adjustable |
| Watchdog LDO | TPS7A02 | 0.75uA quiescent, always-on |
| Power path | TPS2595 eFuse | Fast protection, adjustable limit |
| Kill relay | Omron G6K-2F | Signal relay, low power, latching option |

### 7.3 Development Phases

**Phase 1: Proof of Concept**
- Use commercial 5V/5A adapter for RPi5
- Separate USB power bank for sensors
- Bench power supply for FES development
- Focus on functional validation

**Phase 2: Integrated Prototype**
- Build custom 4S battery pack with basic BMS
- Integrate main buck converter on PCB
- Add basic watchdog (RP2040 + relay)
- Test thermal behavior

**Phase 3: Optimized System**
- Custom PCB with all power management
- Medical-grade BMS with full monitoring
- Windowed watchdog with redundancy
- Regulatory testing preparation

### 7.4 Testing Requirements

Before deployment, validate:

1. **Power consumption:** Measure actual vs. calculated under all scenarios
2. **Thermal:** Long-duration testing at maximum load
3. **Startup:** Verify sequencing under various battery states
4. **Fault response:** Test watchdog trigger scenarios
5. **Runtime:** Validate battery life predictions
6. **Charging:** Test charge time, balancing behavior
7. **Safety:** Verify all protection circuits function correctly

---

## References

[^1]: Arduino. "Nano 33 BLE Sense Rev2." Arduino Documentation. https://docs.arduino.cc/hardware/nano-33-ble-sense-rev2/

[^2]: Nordic Semiconductor. "nRF52840 Product Specification v1.1." https://infocenter.nordicsemi.com/pdf/nRF52840_PS_v1.1.pdf

[^3]: Edge Impulse. "Nordic and Edge Impulse Bring TinyML to Bluetooth Low Energy." https://www.edgeimpulse.com/blog/nordic-embedded-machine-learning/

[^4]: Arduino. "How to reduce power consumption on the Nano 33 BLE." Arduino Help Center. https://support.arduino.cc/hc/en-us/articles/4402394378770-How-to-reduce-power-consumption-on-the-Nano-33-BLE

[^5]: Geerling, Jeff. "Reducing Raspberry Pi 5's power consumption by 140x." Jeff Geerling Blog, 2023. https://www.jeffgeerling.com/blog/2023/reducing-raspberry-pi-5s-power-consumption-140x

[^6]: CNX Software. "Raspberry Pi 5 review - Part 2: Raspberry Pi OS Bookworm, benchmarks, power consumption, and more." November 2023. https://www.cnx-software.com/2023/11/05/raspberry-pi-5-review-raspberry-pi-os-bookworm-benchmarks-power-consumption/

[^7]: Geerling, Jeff. "New 2GB Pi 5 has 33% smaller die, 30% idle power savings." Jeff Geerling Blog, 2024. https://www.jeffgeerling.com/blog/2024/new-2gb-pi-5-has-33-smaller-die-30-idle-power-savings

[^8]: Raspberry Pi Forums. "How does Pi5 determine Power Supply capacity." https://forums.raspberrypi.com/viewtopic.php?t=358576

[^9]: Hailo. "Hailo-8L Entry-Level AI Accelerator Solutions." https://hailo.ai/products/ai-accelerators/hailo-8l-ai-accelerator-for-ai-light-applications/

[^10]: The Register. "Raspberry Pi unveils Hailo-powered AI Kit for model 5." June 2024. https://www.theregister.com/2024/06/04/raspberry_pi_ai_kit/

[^11]: Hailo Community. "Measuring power consumption on Hailo8L with RP5 (AI KIT)." https://community.hailo.ai/t/measuring-power-consumption-on-hailo8l-with-rp5-ai-kit/5234/2

[^12]: Raspberry Pi. "RP2040 Datasheet." https://files.seeedstudio.com/wiki/XIAO-RP2040/res/rp2040_datasheet.pdf

[^13]: Raspberry Pi Forums. "Current consumption on sleep modes." https://forums.raspberrypi.com/viewtopic.php?t=316458

[^14]: EEVblog Forum. "Deep Sleep? RP2040 for 24/7 battery-powered device." https://www.eevblog.com/forum/microcontrollers/deep-sleep-rp2040-for-247-battery-powered-device/

[^15]: PubMed. "Evaluation of force-sensing resistors for gait event detection to trigger electrical stimulation." https://pubmed.ncbi.nlm.nih.gov/12173736/

[^16]: MDPI Sensors. "A Systematic Approach to the Design and Characterization of a Smart Insole for Detecting Vertical Ground Reaction Force (vGRF) in Gait Analysis." https://www.mdpi.com/1424-8220/20/4/957

[^17]: Amazon. "UpBright 4.8V AC/DC Adapter Compatible with EMSI Flex-it TENS." https://www.amazon.com/EMSI-Flex-TENS-Muscle-Stimulator-Power/dp/B00JAZMNCY

[^18]: Cyclone Mobility. "Functional Electrical Stimulation - The Ultimate Guide to FES." https://www.cyclonemobility.com/functional-electrical-stimulation-the-ultimate-guide-to-fes/

[^19]: PMC/NCBI. "Neuromuscular Electrical Stimulation for Skeletal Muscle Function." https://pmc.ncbi.nlm.nih.gov/articles/PMC3375668/

[^20]: PMC/NCBI. "Power Strategy in DC/DC Converters to Increase Efficiency of Electrical Stimulators." https://pmc.ncbi.nlm.nih.gov/articles/PMC5128965/

[^21]: Analog Devices. "80V Synchronous 4-Switch Buck-Boost Controller." https://www.analog.com/en/resources/technical-articles/80v-synchronous-4-switch-buck-boost-controller-delivers-hundreds-of-watts-with-99-efficiency.html

[^22]: arXiv. "Development of a Compact High-Voltage Functional Electrical Stimulation Device." December 2024. https://arxiv.org/html/2412.12064

[^23]: Global Batteries. "18650 Cell vs. LiPo Battery Cell: Which is Better?" https://www.global-batteries.com/18650-cell-vs-lipo-battery-cell-which-is-better/

[^24]: Grepow. "How to Choose Lipo Battery for Medical Device." https://www.grepow.com/blog/how-to-choose-lipo-battery-for-medical-device.html

[^25]: FAA. "Batteries & Battery-Powered Devices." https://www.faa.gov/hazmat/packsafe/more_info/?hazmat=7

[^26]: DNK Power. "Lithium Polymer Battery in Medical Equipment." https://www.dnkpower.com/lithium-polymer-battery-in-medical/

[^27]: Raspberry Pi Forums. "Once more... Pi 5 & Powerbank." https://forums.raspberrypi.com/viewtopic.php?t=361748

[^28]: Pichondria. "Power RaspberryPi 5 (5V 5A) with USB-PD Power Banks." August 2024. https://pichondria.com/2024/08/06/power-rpi5-using-powerbank/

[^29]: Raspberry Pi Forums. "[Solved] Portable Pi5 power supply? For different projects." https://forums.raspberrypi.com/viewtopic.php?t=366413

[^30]: Raspberry Pi Forums. "Raspberry Pi 5 5V/5A power issues, and possible solution." https://forums.raspberrypi.com/viewtopic.php?t=381080

[^31]: VDE. "Electrical safety in active medical devices: The IEC 60601-1 standard." https://www.vde.com/topics-en/health/consulting/electrical-safety-in-active-medical-devices--the-iec-60601-1-standard

[^32]: Wikipedia. "IEC 60601." https://en.wikipedia.org/wiki/IEC_60601

[^33]: Advanced Energy. "Safety Requirements in Medical Equipment: Designing for BF and CF Classifications." https://www.advancedenergy.com/en-us/about/news/blog/safety-requirements-in-medical-equipment-designing-for-bf-and-cf-classifications/

[^34]: Analog Devices. "Improving Industrial Functional Safety Compliance with High Performance Supervisory Circuits." https://www.analog.com/en/resources/analog-dialogue/articles/improving-industrial-functional-safety-part-3.html

[^35]: Epec. "Battery Packs for Medical Devices Requirements and Certification - Q&A." https://blog.epectec.com/battery-packs-for-medical-devices-requirements-and-certification-q-and-a

[^36]: Large Battery. "How to Select the Right Battery Management System (BMS) for Medical Devices." https://www.large-battery.com/blog/select-battery-management-system-for-medical-devices-guide/

[^37]: CSA Group. "Making Sense of Regulations for Medical Device Batteries." https://www.csagroup.org/article/making-sense-regulations-medical-device-batteries/

---

*Document generated for OpenGait project - November 2024*
