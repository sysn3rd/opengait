# Open Research Questions

## Critical Unknowns (Must Resolve Before Hardware Testing)

### 1. Real-World EMD Compensation

**Question:** Can phase-advance triggering adequately compensate for time-varying electromechanical delay during a 20-minute session?

**What we know:**
- EMD ranges from 50ms (fresh) to 300ms (fatigued)[^1]
- Phase-advance at 50% phase completion reduces timing error to 0.3 ± 4.1%[^2]
- EMD increases non-linearly with fatigue

**What we don't know:**
- Rate of EMD increase during FES walking (vs cycling studies)
- Whether LSTM prediction can track EMD changes in real-time
- Failure modes when prediction diverges from reality

**Planned investigation:**
1. Bench testing with electrical delay simulation
2. Unity simulation with variable EMD parameters
3. Careful progression during human testing

---

### 2. Multi-Sensor Fusion Reliability

**Question:** Can 7 BLE sensor nodes maintain synchronized state during continuous walking?

**Concerns:**
- BLE connection drops under motion
- Clock drift between nodes
- Conflicting gait phase detections from different sensors

**Mitigation strategies to test:**
- Redundant phase detection (shank + foot agreement required)
- Timeout-based state machine recovery
- Centralized fusion with consistency checks

---

### 3. TENS Unit Modulation Limits

**Question:** Can commercial TENS units be reliably modulated via relay control without exceeding duty cycle ratings or introducing timing uncertainty?

**Unknowns:**
- Relay switching time variability
- TENS unit startup transients
- Charge balance during rapid on/off switching
- Heating under continuous pulsed operation

**Testing required:**
- Oscilloscope characterization of relay-switched TENS output
- Thermal monitoring during extended operation
- Electrode voltage monitoring for DC offset

---

## High Priority Questions

### 4. Fatigue Detection Accuracy

**Question:** Can we reliably detect muscle fatigue before it causes gait instability?

**Potential indicators:**
- Increasing stimulation intensity required for same joint angle
- Decreasing peak torque (would need force sensing)
- Changes in EMD (indirect measurement)
- Changes in gait kinematics (step length, cadence)

**Challenge:** No direct force measurement in current architecture.

**Possible solutions:**
- Add load cells to KAFOs
- Infer from gait parameter changes
- Use mechanomyography (MMG) sensors

---

### 5. Fall Prediction Accuracy

**Question:** Can trunk IMU + balance algorithms provide sufficient warning before a fall?

**What research shows:**
- Trunk acceleration >2g indicates fall in progress
- Orientation change >60° from vertical is concerning
- SCI patients show higher reliance on visual feedback

**Unknown:**
- Lead time available before unrecoverable fall
- False positive rate (unnecessary stimulation cutoff)
- Whether warning is actionable (can we prevent or just prepare?)

---

### 6. TinyML Model Generalization

**Question:** Will models trained on synthetic data (Unity simulation) generalize to real FES gait?

**Research suggests:**
- Synthetic IMU data improves prediction by 38-54%[^3]
- Combined synthetic + real data performs best
- Gait patterns differ significantly with FES vs voluntary

**Risk:** Model may fail on real data despite good simulation performance.

**Mitigation:**
- Collect real IMU data during standing/therapy sessions
- Use domain adaptation techniques
- Plan for online model updates

---

## Medium Priority Questions

### 7. Electrode Placement Repeatability

**Question:** How much does day-to-day electrode placement variability affect system performance?

**Factors:**
- Skin resistance changes
- Motor point location accuracy
- Electrode-muscle alignment

**Impact:** May require daily calibration routine or adaptive current adjustment.

---

### 8. Spasticity Management

**Question:** How will the system handle involuntary spastic contractions?

**Challenges:**
- Spasms can override FES-induced patterns
- Unpredictable timing
- May be triggered by stimulation itself

**Possible approaches:**
- Detect spasm via EMG or sudden kinematic change
- Pause stimulation during spasm
- Use ramped stimulation to reduce spasm triggering

---

### 9. Long-Term Component Reliability

**Question:** Will wearable electronics survive months of daily use?

**Failure modes to consider:**
- Sweat ingress to electronics
- Cable fatigue from repeated bending
- Battery cycle degradation
- Electrode lead connector wear

**Mitigation:**
- IP54 or better enclosures
- Strain relief on all cables
- Battery replacement schedule
- Spare components on hand

---

## Development Roadmap Questions

### 10. Unity Simulation Fidelity

**Question:** How accurate does the musculoskeletal simulation need to be for useful synthetic training data?

**Trade-offs:**
- Higher fidelity = slower development, may not matter
- Lower fidelity = faster iteration, risk of model mismatch

**Approach:** Start simple, add complexity only if validation fails.

---

### 11. Regulatory Path (If Ever Shared)

**Question:** What would be required to share OpenGait with other SCI individuals?

**Reality check:**
- FDA 510(k) requires substantial equivalence demonstration
- Custom device exemption limited to 5 units/year with full QMS
- "Open source medical device" is regulatory nightmare
- Personal use likely only viable path

**For now:** Focus on personal use; document thoroughly in case regulatory landscape changes.

---

## Questions We Cannot Answer Without Testing

| Question | Requires |
|----------|----------|
| Actual power consumption during walking | Instrumented prototype |
| Electrode durability under FES walking | Extended use testing |
| User interface usability | Human factors testing |
| Thermal behavior of enclosures | Environmental testing |
| BLE reliability during movement | Walking trials |
| Safety system response times | Fault injection testing |

---

## Research Collaboration Opportunities

### Areas Needing External Expertise

1. **Clinical validation:** Physical therapist with FES experience
2. **Biomechanical modeling:** OpenSim integration expertise
3. **Control systems:** MPC, ILC algorithm development
4. **EMG integration:** Fatigue monitoring implementation
5. **Safety certification:** IEC 60601-2-10 guidance

### Potential Collaboration Models

- Academic research partnership
- Open-source community contribution
- Clinical advisory board
- Peer review of safety design

---

## References

[^1]: PubMed. "The Time-Varying Nature of Electromechanical Delay."
[^2]: Zahradka N et al. "Evaluation of Gait Phase Detection Delay Compensation Strategies." Sensors 2019.
[^3]: Sharifi Renani M et al. "The Use of Synthetic IMU Signals in the Training of Deep Learning Models." Sensors 2021.
