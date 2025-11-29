# Bill of Materials

## Summary

| Category | Estimated Cost |
|----------|----------------|
| Sensing (7 Arduinos + FSRs + controller) | $336 |
| Control (RPi5 + AI HAT + safety MCU) | $134 |
| Stimulation (TENS units + relays + electrodes) | $155 |
| Infrastructure (batteries, PCBs, enclosures) | $300 |
| Contingency (15%) | $139 |
| **Total** | **~$1,064** |

**Note:** Excludes KAFOs (typically covered by insurance) and represents raw component costs only.

---

## Sensing Layer

### IMU Sensor Nodes

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Arduino Nano 33 BLE Sense Rev2 | 7 | $35 | $245 | BMI270 IMU, nRF52840 BLE |
| Silicone straps/mounts | 7 | $5 | $35 | Body attachment |
| JST connectors | 14 | $1 | $14 | Power and GPIO |

**Subtotal: $294**

### Force Sensing Insoles

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| FSR 402 sensors | 6 | $8 | $48 | 3 per foot |
| Thin flex PCB | 2 | $15 | $30 | Custom insole board |
| Flat ribbon cable | 2 | $5 | $10 | Ankle routing |

**Subtotal: $88**

Alternative: Commercial smart insoles (~$200-300) provide better sensor quality but higher cost.

### Intent Controller

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Wii Nunchuck | 1 | $15 | $15 | Joystick + 2 buttons |
| Nunchuck adapter | 1 | $3 | $3 | I2C breakout |
| Emergency stop button | 1 | $10 | $10 | Physical, latching |

**Subtotal: $28**

---

## Control Layer

### Central Processing

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Raspberry Pi 5 (4GB) | 1 | $60 | $60 | Main controller |
| Hailo-8L AI HAT | 1 | $70 | $70 | 13 TOPS inference |
| Active cooler | 1 | $10 | $10 | Required for sustained load |
| MicroSD card (64GB) | 1 | $15 | $15 | OS + logging |
| Real-time clock module | 1 | $8 | $8 | DS3231 for timestamps |

**Subtotal: $163**

### Safety System

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Raspberry Pi Pico (RP2040) | 1 | $4 | $4 | Watchdog MCU |
| Latching relay | 2 | $8 | $16 | Omron G6K-2F or similar |
| eFuse IC | 2 | $5 | $10 | TPS2595 or similar |
| Voltage supervisor | 1 | $3 | $3 | MAX6369 or similar |
| Optocouplers | 4 | $1 | $4 | Signal isolation |

**Subtotal: $37**

---

## Stimulation Layer

### FES Output

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| TENS unit (dual channel) | 4 | $25 | $100 | 8 channels total |
| Signal relays | 8 | $3 | $24 | SSR or mechanical |
| Relay driver IC | 2 | $4 | $8 | ULN2803 or similar |
| DC blocking capacitors | 8 | $1 | $8 | 10-47μF, 100V rated |
| Current sense resistors | 8 | $0.50 | $4 | 0.1Ω, 1% |

**Subtotal: $144**

### Electrodes

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Reusable electrodes (5×9cm) | 20 | $2 | $40 | Quadriceps, gluteals |
| Reusable electrodes (5×5cm) | 20 | $1.50 | $30 | TA, gastrocnemius |
| Electrode lead wires | 10 | $5 | $50 | 2mm pin connectors |
| Conductive gel | 2 | $10 | $20 | Electrode prep |

**Subtotal: $140**

**Note:** Electrodes are consumables; ongoing cost ~$50-100/month with regular use.

---

## Power System

### Battery Pack

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Samsung 35E 18650 cells | 8 | $6 | $48 | 4S2P configuration |
| 4S BMS board | 1 | $15 | $15 | 20A continuous |
| Cell holders | 2 | $5 | $10 | 4-cell holders |
| Balance connector | 1 | $3 | $3 | JST-XH 5-pin |
| Battery charger (4S) | 1 | $25 | $25 | 2A balanced charging |

**Subtotal: $101**

### Power Distribution

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Buck converter (5V/6A) | 1 | $15 | $15 | LM5146 module |
| LDO (3.3V/500mA) | 2 | $2 | $4 | Watchdog + sensors |
| Boost converter (80V) | 1 | $20 | $20 | FES high voltage |
| Power distribution PCB | 1 | $30 | $30 | Custom or protoboard |
| Fuses and holders | 5 | $2 | $10 | Various ratings |
| Wire and connectors | 1 | $25 | $25 | XT60, JST, barrel |

**Subtotal: $104**

---

## Infrastructure

### Enclosures

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Main enclosure (waist-worn) | 1 | $30 | $30 | IP54 rated, ~15×10×5cm |
| Sensor node enclosures | 7 | $5 | $35 | 3D printed or commercial |
| Stimulator enclosure | 1 | $20 | $20 | For relay board |

**Subtotal: $85**

### PCBs and Interconnects

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Custom PCBs (JLCPCB) | 5 | $15 | $75 | Power, sensor, safety boards |
| Flex cables | 10 | $5 | $50 | Various lengths |
| Heat shrink, tape, misc | 1 | $20 | $20 | Consumables |

**Subtotal: $145**

### Development and Testing

| Component | Quantity | Unit Price | Total | Notes |
|-----------|----------|------------|-------|-------|
| Logic analyzer | 1 | $50 | $50 | Signal debugging |
| Bench power supply | 1 | $60 | $60 | Development testing |
| Oscilloscope probe | 2 | $20 | $40 | If scope available |

**Subtotal: $150**

---

## Component Specifications

### Arduino Nano 33 BLE Sense Rev2

| Specification | Value |
|---------------|-------|
| MCU | nRF52840 (ARM Cortex-M4F @ 64MHz) |
| Flash | 1 MB |
| RAM | 256 KB |
| IMU | BMI270 (6-axis) + BMM150 (magnetometer) |
| BLE | Bluetooth 5.0 |
| Operating voltage | 3.3V (5V via USB) |
| Current (active) | 15-30 mA |
| Dimensions | 45 × 18 mm |

### Raspberry Pi 5 (4GB)

| Specification | Value |
|---------------|-------|
| CPU | Broadcom BCM2712 (4× Cortex-A76 @ 2.4GHz) |
| RAM | 4 GB LPDDR4X |
| Storage | MicroSD + optional NVMe |
| Power | 5V/5A (27W max) |
| Idle power | 2.5-3W |
| Typical power | 5-8W |

### Hailo-8L AI HAT

| Specification | Value |
|---------------|-------|
| Performance | 13 TOPS (INT8) |
| Power | 1-2W typical, 5W peak |
| Interface | PCIe Gen3 x1 |
| Supported frameworks | TensorFlow, PyTorch, ONNX |

---

## Sourcing Notes

### Recommended Vendors

| Component Type | Vendors |
|----------------|---------|
| Arduinos | Arduino Store, Mouser, DigiKey |
| Raspberry Pi | Approved resellers list |
| Electronic components | DigiKey, Mouser, LCSC |
| PCB fabrication | JLCPCB, PCBWay, OSH Park |
| Batteries | 18650BatteryStore, IMRbatteries |
| Electrodes | Discount TENS, Amazon |

### Lead Times

| Category | Typical Lead Time |
|----------|-------------------|
| Standard components | 1-2 weeks |
| Custom PCBs | 1-2 weeks (JLCPCB) |
| Specialty ICs | 2-4 weeks |
| Batteries (quality cells) | 1-2 weeks |

---

## Cost vs Commercial Comparison

| System | Hardware Cost | Control Type |
|--------|---------------|--------------|
| Parastep I | ~$13,000 | Open-loop |
| L300 Go (foot drop) | ~$6,000 | Closed-loop (single muscle) |
| ReWalk exoskeleton | ~$91,000 | Powered |
| **OpenGait** | **~$1,000** | **Closed-loop (8 channel)** |

**Important caveat:** Commercial systems include training, support, warranty, and regulatory compliance. OpenGait's cost is hardware-only for personal development.
