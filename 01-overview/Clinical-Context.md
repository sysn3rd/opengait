# Clinical Context: L4 Complete Spinal Cord Injury

## Injury Classification

### ASIA Impairment Scale

OpenGait is designed for **complete motor/sensory SCI at L4** (ASIA A classification):

| Level | Motor Function | Sensory Function |
|-------|----------------|------------------|
| Above L4 | Preserved | Preserved |
| L4 and below | Absent | Absent |

**L4 neurological level** means:
- No voluntary motor control below L4
- No sensation below L4
- Preserved hip flexion, knee extension (L2-L4 innervation is spared proximally but not distally)
- Lost: ankle dorsiflexion, plantarflexion, toe movements

### Implications for FES Walking

**What works:**
- Quadriceps respond to surface FES (L2-L4 innervation)
- Tibialis anterior can be stimulated via common peroneal nerve
- Gastrocnemius responds to surface stimulation
- Gluteal muscles accessible for hip extension

**What doesn't:**
- No proprioceptive feedback from lower limbs
- No voluntary adjustment during gait
- System must replace entire sensorimotor feedback loop

---

## Physiological Considerations

### Muscle Changes Post-SCI

Within the first year after complete SCI[^1]:

| Change | Magnitude |
|--------|-----------|
| Quadriceps cross-sectional area | -40% |
| Shift from Type I to Type IIB fibers | Significant |
| Oxidative capacity | Reduced |
| Capillary density | Reduced |
| Fatigue resistance | Dramatically reduced |

**Implication:** Muscles fatigue rapidly during FES. Session duration is physiologically limited to 15-30 minutes.

### FES vs Voluntary Contraction

FES causes **reverse-order motor unit recruitment**[^2]:
- Normal voluntary: Type I (slow-twitch, fatigue-resistant) recruited first
- FES: Type II (fast-twitch, fatigable) recruited first

This leads to:
- Faster fatigue onset
- Lower force maintenance
- Need for higher stimulation intensity over time

### Bone Density Concerns

Complete SCI leads to rapid bone density loss[^3]:
- Trabecular bone loss: 4% per month initially
- Cortical bone: Slower but progressive loss
- Fracture risk elevated, especially in femur and tibia

**Safety requirement:** Medical clearance and potentially DEXA scan before weight-bearing FES walking.

---

## Autonomic Considerations

### Autonomic Dysreflexia Risk

**Critical safety concern** for injuries above T6. At L4, risk is lower but not zero.

**Symptoms to monitor:**
- Pounding headache
- Elevated blood pressure (can be life-threatening)
- Flushing above injury level
- Bradycardia or tachycardia

**FES-specific triggers:**
- High pulse durations can trigger dysreflexia[^4]
- Bladder/bowel distension during session
- Tight straps or electrodes

### Orthostatic Hypotension

Standing after prolonged sitting can cause blood pressure drop:
- Pooling of blood in lower extremities
- Reduced venous return
- Dizziness, weakness, syncope

**Mitigation:**
- Gradual standing protocol
- Compression garments
- Abdominal binder

---

## KAFO Requirements

### Stance-Control Knee-Ankle-Foot Orthoses

OpenGait is designed to work **with KAFOs**, not as a standalone system:

| Feature | Purpose |
|---------|---------|
| Stance-control knee joint | Locks during stance, unlocks for swing |
| Rigid ankle-foot section | Provides ankle stability |
| Lightweight construction | Reduces energy cost |

**Why KAFOs are necessary:**
- FES cannot prevent knee hyperextension during stance overload
- Ankle stability cannot be guaranteed with surface FES alone
- Provides mechanical backup if stimulation fails

### Insurance Coverage

KAFOs are typically covered by insurance separate from the FES system:
- Medicare: Covered under DME benefit
- Private insurance: Usually covered with prescription

---

## Contraindications

### Absolute Contraindications for FES Walking

- Pregnancy[^5]
- Severe osteoporosis (fracture risk)
- Active osteomyelitis
- Blood clot within 3 months
- Fracture within 3 months
- Cardiac pacemaker
- Uncontrolled autonomic dysreflexia

### Relative Contraindications (Require Medical Clearance)

- Metal implants at stimulation site
- Skin infections or irritations
- Open wounds at electrode sites
- History of seizures
- Cardiac arrhythmias

---

## Pre-Implementation Requirements

Before any FES walking attempts:

1. **Medical clearance** from SCI specialist
2. **DEXA scan** to assess fracture risk
3. **Cardiac evaluation** if history of arrhythmias
4. **KAFO fitting** by qualified orthotist
5. **Physical therapy assessment** for range of motion, spasticity
6. **Skin integrity assessment** for electrode placement
7. **Standing frame training** to assess orthostatic tolerance

---

## References

[^1]: Physiopedia. "Functional Electrical Stimulation Cycling for Spinal Cord Injury."
[^2]: Effects of Muscle Fatigue on FES Assisted Walking of SCI Patients. SpringerLink.
[^3]: MSKTC. "Functional Electrical Stimulation for SCI."
[^4]: Nature. "Evidence of autonomic dysreflexia during functional electrical stimulation." Spinal Cord.
[^5]: Myolyn. "Understanding the Contraindications of Functional Electrical Stimulation."
