# FES Gait Biomechanics and Sensing Approaches for Complete Spinal Cord Injury

## 1. Gait Cycle Phases

### 1.1 Standard Gait Cycle Breakdown

The gait cycle is traditionally divided into two main phases:

- **Stance Phase**: 60% of the total gait cycle, during which some part of the foot is in contact with the ground[^1]
- **Swing Phase**: 40% of the total gait cycle, during which the foot is not in contact with the ground and bodyweight is borne by the other leg[^1]

#### Stance Phase Sub-divisions (as % of gait cycle):
1. **Initial Contact**: 0-3%[^2]
2. **Loading Response**: 3-12%
3. **Midstance**: 12-31%[^2]
4. **Terminal Stance**: 31-50%[^2]
5. **Pre-swing**: 50-62%[^2]

#### Swing Phase Sub-divisions (as % of gait cycle):
1. **Initial Swing**: 62-75%
2. **Mid Swing**: 75-87%
3. **Terminal Swing**: 87-100%[^2]

**Double Support Periods**: Both feet are in contact with the ground for 20% of the total gait cycle - 10% at the beginning of stance phase and 10% at the end of stance phase[^1]

### 1.2 Timing Percentages at Slow Walking Speeds (0.1-0.2 m/s)

At very slow walking speeds (0.1-0.2 m/s), gait parameters differ significantly from normal walking speeds:

- **Increased stance phase percentage**: At slower speeds, the stance phase can increase beyond the normal 60%, with older adults showing stance phases of approximately 63.9% at slow walking speeds[^3]
- **Increased double support time**: More cautious gait patterns used to maintain stability result in prolonged bipedal floor contact[^4]
- **Swing phase percentage decreases**: Correspondingly, swing phase decreases to approximately 36.1% of the gait cycle at slower speeds[^3]

**Speed-dependent changes**: With increasing speed of 0.1 m/s, stance duration decreases while swing duration increases by approximately 0.3%[^5]. All distinct stance sub-phases change significantly with speed[^5].

In the speed range of 10-30 m/min (approximately 0.17-0.5 m/s), step-to gait patterns show higher efficiency compared to normal gait[^6].

### 1.3 Muscle Activation During Each Phase

#### Multi-Muscle FES Stimulation Protocol[^7]:

**Stance Phase (0-60% of gait cycle)**:
- **Quadriceps**: Stimulated continuously during mid-stance and late-stance phases to provide knee extension and weight-bearing support
- **Gastrocnemius/Soleus**: Stimulated during stance phase for ankle plantar flexion and push-off

**Swing Phase (60-100% of gait cycle)**:
- **Hamstrings**: Stimulated during swing phase for knee flexion
- **Tibialis Anterior**: Stimulated during swing phase for ankle dorsiflexion and foot clearance

#### Traditional Muscle Activation Timing[^8]:

**Loading Response (3-12%)**:
- Quadriceps (knee extension)
- Tibialis anterior (eccentric control of plantarflexion)

**Midstance (12-31%)**:
- Quadriceps (knee stabilization)
- Gastrocnemius/soleus (beginning ankle stabilization)

**Terminal Stance (31-50%)**:
- Gastrocnemius/soleus (push-off preparation)

**Pre-swing (50-62%)**:
- Gastrocnemius/soleus (push-off)

**Swing Phase (62-100%)**:
- Tibialis anterior (dorsiflexion for foot clearance)
- Hamstrings (knee flexion)

### 1.4 Differences in FES-Assisted Gait vs Normal Gait

**Challenges in FES-assisted gait**[^9]:
- Lack of lower limb proprioception
- Inability to control complex joint trajectory
- Rapid energy consumption
- Unstable and insufficient muscle force due to motor unit recruitment strategy
- Higher rate of fatigue during FES-elicited contraction compared to voluntary contractions

**Gait pattern modifications**[^10]:
- Wider stance and gait base
- Shorter step length
- Reduced foot lift height during swing phase
- Slower overall walking speed
- Altered muscle activation patterns compared to normal walking

**Motor unit recruitment differences**:
- FES causes reverse-order recruitment (Type II fast-twitch fibers recruited before Type I slow-twitch fibers), leading to rapid fatigue
- Normal voluntary contraction recruits Type I fibers first (Henneman's size principle)

## 2. IMU-Based Gait Detection

### 2.1 Optimal Sensor Placement for Gait Phase Detection

**Shank-mounted IMU placement**: The most effective location is on the shank, immediately above the malleolus (ankle bone)[^11]. This placement provides optimal sensitivity to gait events.

**Advantages of shank placement**[^12]:
- More sensitive to anterior-posterior acceleration movement during static phase
- Superior to foot sensors for detecting certain gait events
- Provides better information for swing phase detection

**Bilateral sensor configuration**: Two IMUs (accelerometer and gyroscope) should be mounted on both shanks for complete gait analysis[^11].

### 2.2 Shank-Mounted IMU Effectiveness

Shank-mounted IMUs have demonstrated high effectiveness for gait phase detection:

**Detection accuracy**[^13]:
- **Initial Contact (IC)**: 100% detection accuracy
- **Foot Flat (FFS)**: 100% detection accuracy
- **Toe-Off (TO)**: 100% detection accuracy
- **Heel-Off (HO)**: 98.3% detection accuracy

**Cross-validation studies**[^14]:
- F1 measures ranging from 0.89 to 1.0 for straight walking (>0.99)
- Slightly lower accuracy during turning maneuvers

**Comparison with force sensors**: Mean absolute sample difference between shank IMU detection and force-sensitive resistor (FSR) methods is approximately 2 samples, equivalent to 20 ms difference[^15].

### 2.3 Gyroscope Signatures for Heel-Strike and Toe-Off Detection

**Shank angular velocity patterns**[^16]:
- The gyroscope signal from the shank in the sagittal plane exhibits a distinctive pattern
- Characterized by two negative peaks on each side of a prominent positive peak
- This pattern is highly repeatable across gait cycles

**Heel-Strike (Initial Contact) signature**:
- Characterized by a negative angle reaching its highest values at stance phase initiation[^17]
- Local minima in shank angular velocity
- Distinct deceleration signature

**Toe-Off (Terminal Contact) signature**:
- Characterized by a maximum positive angle[^17]
- Local maxima in shank angular velocity
- Distinct acceleration signature

**Signal processing approach**[^15]:
- Local maxima, minima, and zero crossings of the shank's angular velocity and acceleration are used to detect heel strike, toe strike, and toe-off
- The gyroscope provides more reliable gait event detection than accelerometer alone

### 2.4 Algorithm Approaches (Rule-Based vs ML)

#### Rule-Based Approaches

**Advantages**:
- Computationally efficient
- Real-time implementation possible
- Transparent decision-making process
- No training data required

**Typical implementation**[^18]:
- Uses threshold-based detection on gyroscope signals
- Identifies peaks, valleys, and zero-crossings
- Can be enhanced with finite state machines for phase tracking

**Performance**: Detection accuracy of approximately 90% using gyroscope alone, improving to 99-100% when combined with accelerometer data[^15][^19]

#### Machine Learning Approaches

**Superior performance**[^20]:
- ML-based algorithms achieve accuracy over 99% in detecting gait events
- Better handling of gait variability and abnormal patterns
- Can adapt to different walking speeds and conditions

**Neural network implementations**[^21]:
- Overall accuracy of 92% across all phone positions and walking speeds
- Right-side predictions: ~94% accuracy
- Left-side predictions: ~91% accuracy
- Median time error <3% of gait cycle duration (~77 ms)

**Advanced architectures**[^22]:
- Hidden Markov Models (HMM)
- Gaussian Continuous Wavelet Transformation (CWT)
- Long Short-Term Memory (LSTM) networks
- Convolutional Neural Networks (CNN)

**LSTM-specific performance**[^23]:
- Can predict gait trajectories up to 200 ms ahead
- Correlation coefficients >0.98 with measured trajectories
- Swing phase prediction accuracy: 95% in inter-subject implementation
- Normalized mean squared error: 2.82-5.31% for LSTM autoencoders

### 2.5 Accuracy Rates from Research Literature

**Timing accuracy**[^24]:
- Event detection rate: ~99%
- Detection offset: <17 ms (0.017 seconds)
- Median absolute error: <0.1 seconds for automatic GE detection

**Parameter-specific accuracy**[^24]:
- Relative RMSE: 0.90% to 4.40% for most temporal parameters
- Parameters requiring spatial information show higher errors (step width: 35.20% RMSE)
- Step length estimation: mean error of -0.12 ± 4.49 cm with ML algorithms[^20]

**Comparison with gold standard systems**[^25]:
- RMSE differences between camera-based and IMU-based systems range from 1.83 to 3.98
- Tolerance close to 1%
- Results indicate IMU-based systems can replace camera-based systems

**Detection delays**[^26]:
- Mean delay for Terminal Contact (TC): ~100 ms
- Mean delay for Initial Contact (IC): ~50 ms
- Note: These values can be reduced with higher sampling rates (>50 Hz recommended)

## 3. Force Sensing for Ground Contact Detection

### 3.1 Why IMUs Alone Are Insufficient for Ground Contact Detection

**Fundamental limitations**[^27]:
- IMUs provide kinematic information (motion, orientation, acceleration) but not direct contact information
- Cannot reliably distinguish between near-ground swing phase and actual ground contact
- Gyroscope-only detection achieves only 90% accuracy for initial contact[^19]

**Need for fusion approaches**[^28]:
- Combination of inertial sensors and foot sensors increases the number of detectable gait phases
- IMUs provide sufficient information for swing phase, but stance phase requires ground contact information
- Pressure insoles and foot switches provide reliable signals of ground contact

**Specific detection challenges**:
- Detecting exact moment of heel strike requires pressure information
- Toe-off timing is more accurately detected with force information
- Load acceptance and weight transfer phases need force magnitude data

### 3.2 FSR Placement in Insoles

**Standard FSR configuration**[^29]:
- Two sets of FSRs embedded at the ball and heel of each shoe
- Minimum viable system: 2 FSRs per foot (heel and forefoot)
- Advanced systems: Up to 14 piezo-resistive sensors per insole for detailed pressure mapping

**Specific placement zones**[^30]:
1. **Heel region**: Detects initial contact and heel strike
2. **Metatarsal heads**: Detects midstance and toe-off
3. Optional: **Toe region**: For more detailed gait analysis
4. Optional: **Midfoot**: For arch support monitoring

**Sampling parameters**[^29]:
- FSR signals digitalized by 16-bit AD converters
- Sample frequency: 1000 Hz for research applications
- Lower frequencies (100-200 Hz) sufficient for basic gait detection

**Sensor specifications**[^31]:
- FlexiForce A301 sensors commonly used
- FSR demonstrated to be most effective sensor type for smart insole applications
- Piezoelectric sensors useful for detecting start/end of gait cycle but less effective for continuous pressure monitoring

### 3.3 Research Systems Using Force Sensing

**FES walking applications**[^32]:
- Foot switches located under the heel obtain gait phase information
- Stimulation frequency: 40 Hz
- Pulse duration: 400 μsec using biphasic asymmetrical pulses
- Force data used to trigger multi-muscle stimulation sequences

**Ground Reaction Force (GRF) estimation systems**[^33]:
- Low-cost electronic systems with master and slave nodes
- Sample and store data from force insoles at participant's feet
- Coupling FSR insoles with 1D Convolutional Neural Networks yields vertical GRF estimates with ~9% average error compared to treadmill ground-truth

**Adaptive gait pattern detection**[^34]:
- Real-time gait pattern detection through measuring ground contact forces
- Adjustable method based on statistical analysis of constant false alarm rate
- Enables detection of heel strike and toe-off events in each gait cycle

**Clinical applications**:
- Assists in diagnosis of persons with walking injuries, diseases, or limitations
- Gait pattern sequence of hospital patients differs from healthy individuals
- Force data critical for FES timing in spinal cord injury rehabilitation

## 4. Muscle Stimulation Timing

### 4.1 Quadriceps Activation Timing (Loading Response)

**Activation window**: 3-50% of gait cycle (loading response through terminal stance)[^35]

**Specific timing**[^7]:
- Begins at initial contact (~0% gait cycle)
- Peak activation during loading response (3-12%)
- Continues through midstance (12-31%) for knee stabilization
- Maintains activation through terminal stance (31-50%)
- Deactivates during pre-swing (~50%)

**Stimulation parameters**[^32]:
- Frequency: 30-40 Hz
- Pulse duration: 300-400 μsec
- Pulse amplitude: 20-30 mA (patient-specific)

**Purpose in FES walking**:
- Knee extension during stance
- Weight-bearing support
- Prevention of knee buckling during loading response

**Coordination requirements**[^36]:
- 0.2-0.4 second delay between onset of quadriceps and gluteus muscles
- 0.2 s delay recommended for heavier patients
- Slower ramps (0.4 s) required for slim patients

### 4.2 Tibialis Anterior for Dorsiflexion (Swing Phase)

**Activation window**: 60-100% of gait cycle (entire swing phase)[^7]

**Specific timing**:
- Activation begins at toe-off (~60% gait cycle)
- Maintains activation through initial swing (62-75%)
- Peak activation during mid-swing (75-87%)
- Continues through terminal swing (87-100%)
- Deactivates at heel strike (0%)

**Stimulation parameters**[^37]:
- Frequency: 40 Hz
- Pulse duration: 400 μsec
- Surface electrodes or common peroneal nerve stimulation

**Purpose in FES walking**:
- Ankle dorsiflexion for foot clearance
- Prevention of foot drop
- Controlled heel strike at initial contact

### 4.3 Gastrocnemius for Push-Off

**Activation window**: 31-60% of gait cycle (terminal stance through pre-swing)[^7]

**Specific timing**:
- Begins activation during terminal stance (~31%)
- Peak activation during pre-swing (50-62%)
- Push-off occurs at ~60% gait cycle
- Deactivates at toe-off

**Stimulation parameters**[^8]:
- Frequency: 30 Hz typical
- Pulse amplitude: 20-30 mA
- Applied to gastrocnemius/soleus muscle group

**Purpose in FES walking**:
- Ankle plantar flexion
- Push-off propulsion
- Forward momentum generation
- Better stability at foot contact

### 4.4 Common Peroneal Nerve Stimulation for Flexor Withdrawal

**Mechanism**[^38]:
- Stimulation of common peroneal nerve elicits flexor withdrawal reflex
- Simultaneously enhances hip flexion, knee flexion, and ankle dorsiflexion
- More effective than isolated tibialis anterior stimulation

**Electrode placement**[^39]:
- Primary electrode: Over common peroneal nerve as it passes over head of fibula
- Secondary electrode: Motor point of tibialis anterior
- For enhanced knee flexion: Indifferent electrode placed over common peroneal nerve in popliteal fossa

**Clinical effectiveness**[^40]:
- Randomized controlled trial showed 20.5% mean increase in walking speed with FES
- Control group: only 5.2% increase
- Improves minimum toe clearance during swing phase
- Effective for foot drop correction in SCI patients

**Optimal placement for maximum effect**[^39]:
- Position that produces both dorsiflexion and eversion is best for withdrawal reflex
- This placement also improves knee and hip flexion
- Results in better overall swing phase kinematics

### 4.5 Ramping Parameters (Ramp-Up/Down Times)

**Definition**[^41]:
- Ramp-up time: Period to increase stimulation from baseline to target amplitude
- Ramp-down time: Period to decrease stimulation from target to baseline
- Essential for providing natural movement and avoiding sudden on/off transitions

**Typical ramp parameters**[^41]:
- **Standard protocol**: 0.5 s ramp-up, 1.0 s plateau, 0.5 s ramp-down
- **Conservative protocol**: 1.0 s ramp-up, 3.0 s plateau, 1.0 s ramp-down
- **Commercial systems**: Fixed ramp times ranging from 1-8 seconds

**Application-specific recommendations**[^36]:
- Standing applications: 0.2-0.4 s ramps depending on patient weight
- Walking applications: 0.5 s ramps typical
- Heavier patients: Faster ramps (0.2 s)
- Slimmer patients: Slower ramps (0.4 s)

**Benefits**[^42]:
- Minimizes stretch reflex activation
- Reduces spasticity
- Improves patient comfort
- Decreases accommodation effects
- Provides opportunity to actively hold contraction after stimuli ends

**Fatigue considerations**[^42]:
- Ramp times affect comfort more than fatigue
- Non-rectangular waveforms (including ramp-up/down) can delay early muscle fatigue
- Variable pulse frequency and duration during ramps reduces summation and accommodation

## 5. Electrode Placement

### 5.1 Motor Point Locations for Key Muscles

**Quadriceps motor points**[^43]:
- **Vastus Lateralis (VL)**: Lateral thigh, approximately mid-muscle belly
- **Vastus Medialis (VM)**: Medial thigh, distal portion near knee
- **Rectus Femoris (RF)**: Anterior mid-thigh
- **Vastus Intermedius**: Deep to rectus femoris

**Note**: Research indicates no difference in muscle architecture changes when stimulating VL versus VM motor points, so either can be chosen clinically[^44]

**Gastrocnemius motor points**:
- **Medial head**: Proximal medial calf
- **Lateral head**: Proximal lateral calf
- Location approximately 1/3 down from popliteal fossa

**Tibialis Anterior motor point**[^39]:
- Located on anterior lateral aspect of upper leg
- Approximately 1/3 distance from knee to ankle
- Alternative: Over common peroneal nerve at fibular head

**Hamstring motor points**[^45]:
- **Biceps Femoris**: Lateral posterior thigh
- **Semitendinosus**: Medial posterior thigh, more superficial
- **Semimembranosus**: Medial posterior thigh, deeper

**Gluteal motor points**[^45]:
- **Gluteus Maximus**: Posterior portion, upper buttock
- **Gluteus Medius**: Lateral hip region

### 5.2 Electrode Sizes for Different Muscles

**Research-tested electrode sizes**[^43]:
- Small: 2 × 2 cm (4 cm²)
- Medium: 5 × 5 cm (25 cm²)
- Large rectangular: 5 × 9 cm (45 cm²)

**Muscle-specific recommendations**:

**Quadriceps**[^43]:
- Optimal for comfort: Large electrodes (5 × 9 cm)
- Traditional clinical: Two electrodes at opposite ends of muscle
- Novel multi-electrode systems: Various sizes (10 × 20 cm, 3 × 18 cm, 10 × 7.5 cm, 7 × 14 cm)

**Gastrocnemius**[^46]:
- Preferred size: 36 cm² or larger
- Larger electrodes (20.25-40.3 cm²) more comfortable than smaller (2.25-9 cm²)
- Larger electrodes produce greater muscle force generation
- No significant movement difference between small and large electrodes

**Tibialis Anterior**:
- Medium to large electrodes preferred
- Approximately 20-36 cm² effective

**General principles**[^47]:
- Larger electrodes generally more comfortable
- Larger electrodes produce stronger, more diffuse contractions
- Smaller electrodes provide more selective activation
- Electrode size should match muscle bulk

### 5.3 Configuration (Longitudinal vs Transverse)

**Definitions**:
- **Longitudinal**: Electrodes placed along the length of muscle fiber direction
- **Transverse**: Electrodes placed across the width of the muscle
- **Short-transverse (ST)**: Small electrode spacing
- **Long-transverse (LT)**: Wide electrode spacing

**Quadriceps configuration**[^48]:

**For maximum torque production**:
- Longitudinal placement produces significantly more torque than transverse
- Traditional placement: Distal vastus medialis oblique and proximal vastus lateralis
- Places electrodes at opposite ends of muscle
- Produces more complete contraction with deeper stimulation

**For comfort at low-intensity stimulation**[^43]:
- Long-transverse (LT) placement exhibits significantly better comfort
- LT placement requires lower current intensity than short-transverse or longitudinal
- Optimal for therapeutic applications prioritizing patient comfort

**Gastrocnemius configuration**:
- Longitudinal placement along medial and lateral heads
- Electrodes positioned approximately 1/3 and 2/3 down the muscle length

**General recommendations**[^47]:
- Position electrodes close to motor points for optimal activation
- Avoid placement over bony prominences
- Place over muscle bulk rather than tendons
- Consider individual anatomical variations
- For bilateral muscles, ensure symmetric placement

**Critical placement considerations**[^47]:
- If electrodes not optimally placed, higher stimulation amplitude required
- Suboptimal placement leads to patient discomfort
- Electrode positioning may vary between individuals due to anatomical differences
- Motor point mapping with pen electrode recommended for precise placement

## 6. Predictive Control Approaches

### 6.1 Phase-Advance Triggering Strategies

**Need for phase-advance triggering**[^49]:
- Gait phase detection introduces delays in stimulation delivery
- Electromechanical delay (EMD) exists between stimulation and muscle contraction
- Without compensation, stimulation arrives too late for optimal muscle activation

**Delay compensation performance**[^49]:
- Five different stimulator triggers tested for delay compensation
- Appropriate system delay compensations reduce stimulation delivery delays by average of 6.7% of gait cycle
- Root mean square errors for gait phase onset detection:
  - Mid-swing (MSw): 35 ms
  - Terminal swing (TSw): 105 ms

**Triggering strategy types**:

1. **Direct triggering**: Stimulation begins immediately upon phase detection (no compensation)
2. **Fixed time-advance**: Predetermined time offset added to trigger
3. **Percentage-based advance**: Trigger occurs at percentage before target phase
4. **Predictive model-based**: Uses trajectory prediction to anticipate phase transitions
5. **Adaptive triggering**: Learns from previous gait cycles to optimize timing

**Implementation considerations**:
- Gait phase detection (GPD) validated as viable finite-state control
- Must account for both detection delay and electromechanical delay
- Total compensation typically 100-150 ms

### 6.2 LSTM and ML-Based Trajectory Prediction

**LSTM architecture advantages**[^23]:
- Can learn order dependence in sequence prediction
- Handles time-series gait data effectively
- Predicts segment trajectories up to 200 ms ahead

**Prediction capabilities**[^23]:
- Angular velocity prediction for thigh, shank, and foot segments
- Five gait phase prediction with 95% accuracy for swing phase
- All predicted trajectories strongly correlated with measured (r > 0.98)
- Normalized mean squared error: 2.82-5.31% for LSTM autoencoders

**Architecture comparison**[^50]:
Four LSTM architectures evaluated:
1. **Vanilla LSTM**: Basic implementation
2. **Stacked LSTM**: Multiple LSTM layers
3. **Bidirectional LSTM**: Forward and backward processing
4. **LSTM Autoencoders**: Best performance for trajectory prediction (2.82-5.31% error)

**Prediction horizon**[^50]:
- Effective prediction up to 100 ms ahead
- Longer prediction horizons decrease accuracy
- Optimal for compensating typical electromechanical delays

**Clinical application to SCI**[^51]:
- EEG and IMU sensor fusion for motor imagery-based FES
- Pearson correlation: 0.6 for kinesthetic motor imagery
- R² score: 0.36 for FES-produced involuntary movement
- Shows promise for brain-computer interface control of FES

**Gait recovery prediction**[^52]:
- RNN significantly outperforms linear regression (RMSE: 0.3738 vs 2.2831)
- Top predictors: Lower-extremity motor strength, neurological level of injury
- Can guide rehabilitation therapy planning

### 6.3 Compensating for Electromechanical Delay

**Electromechanical Delay (EMD) characteristics**[^53]:
- Time between onset of electrical activation and force production
- Includes electrochemical processes: synaptic transmission, action potential propagation, excitation-contraction coupling
- Includes mechanical processes: force transmission through series elastic components

**Reported EMD values**:
- **Range**: 8.5 ms (supramaximally stimulated) to 125 ms (voluntary contraction)[^53]
- **Quadriceps (Rectus Femoris)**[^54]:
  - EMG-to-Force delay: 49.73 ± 6.99 ms
  - EMG-to-MMG: 20.5 ± 4.73 ms
  - EMG-to-US: 28.63 ± 6.31 ms
- **Tibialis Anterior**[^55]: EMD decreases logarithmically with increased force slope

**Time-varying nature of EMD**[^56]:
- EMD increases significantly with muscle fatigue
- Control effectiveness (linear slope of recruitment curve) decreases with fatigue
- Presents challenge for closed-loop FES control systems

**Compensation strategies**:

**Model-based predictive control**[^57]:
- Linear MPC using linearized musculoskeletal model
- Nonlinear MPC (NMPC) for improved performance outside linearization region
- Dynamic surface control with delay compensation term

**Predictor-based compensation**[^58]:
- Addresses unknown nonlinear muscle force-length relationships
- Accounts for muscle force-velocity relationships
- Prevents degraded performance and instability

**Hybrid control approaches**[^57]:
- Combines FES with electric motors (hybrid neuroprosthesis)
- Muscle synergy-inspired control framework
- Dynamic postural synergies between FES and motors
- Feedforward control with synergy activation

**Adaptive and reflexive control**[^59]:
- Integrates reflexive FES controller with iterative learning algorithm
- Real-time gait phase detection system for accurate feedback
- Adapts to changing muscle properties during exercise

**Key design considerations**:
- Must address model uncertainty
- Account for cascaded muscle activation dynamics
- Handle dynamic disturbances (EMD and fatigue)
- Ensure stability despite disparate FES and motor dynamics

**Practical implementation**:
- Total delay compensation: Detection delay (35-105 ms) + EMD (50-125 ms)
- Typical advance triggering: 100-200 ms before desired activation
- Adaptive algorithms adjust compensation as fatigue develops

## References

[^1]: [The Gait Cycle - Physiopedia](https://www.physio-pedia.com/The_Gait_Cycle)

[^2]: [Gait Cycle: Phases & Biomechanics | OrthoFixar](https://orthofixar.com/basic-science/gait-cycle/)

[^3]: [Normative Spatiotemporal Gait Parameters in Older Adults - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC3104090/)

[^4]: [Gait disorders in adults and the elderly: A clinical guide - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC5318488/)

[^5]: [Effect of walking speed on gait sub phase durations - PubMed](https://pubmed.ncbi.nlm.nih.gov/26256534/)

[^6]: [Frontiers | Efficiency and Stability of Step-To Gait in Slow Walking](https://www.frontiersin.org/journals/human-neuroscience/articles/10.3389/fnhum.2021.779920/full)

[^7]: [The Orthotic Effects of Different Functional Electrical Stimulation Protocols on Walking Performance in Individuals with Incomplete Spinal Cord Injury](https://meridian.allenpress.com/tscir/article/29/Supplement/142/496765/The-Orthotic-Effects-of-Different-Functional)

[^8]: [Sensor-driven four-channel stimulation of paretic leg: Functional electrical walking therapy - ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0165027009001988)

[^9]: [Effects of Muscle Fatigue on FES Assisted Walking of SCI Patients: A Review | SpringerLink](https://link.springer.com/chapter/10.1007/978-3-319-02913-9_121)

[^10]: [Gait Post Spinal Cord Injury - Physiopedia](https://www.physio-pedia.com/Gait_Post_Spinal_Cord_Injury)

[^11]: [Estimation of stride-by-stride spatial gait parameters using inertial measurement unit attached to the shank with inverted pendulum model | Scientific Reports](https://www.nature.com/articles/s41598-021-81009-w)

[^12]: [A Systematic Approach to the Design and Characterization of a Smart Insole for Detecting Vertical Ground Reaction Force (vGRF) in Gait Analysis - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC7070759/)

[^13]: [Automatic gait events detection with inertial measurement units: healthy subjects and moderate to severe impaired patients - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11184826/)

[^14]: [Real-time gait event detection for lower limb amputees using a single wearable sensor | Academia.edu](https://www.academia.edu/83988607/Real_time_gait_event_detection_for_lower_limb_amputees_using_a_single_wearable_sensor)

[^15]: [Gait Phase Detection for Normal and Abnormal Gaits Using IMU | IEEE Xplore](https://ieeexplore.ieee.org/document/8620997/)

[^16]: [A novel method for accurate division of the gait cycle into seven phases using shank angular velocity - ScienceDirect](https://www.sciencedirect.com/science/article/pii/S096663622400095X)

[^17]: [Assessing Smart Algorithms for Gait Phases Detection](https://arxiv.org/pdf/2310.09735)

[^18]: [Real-time gait event detection for lower limb amputees using a single wearable sensor | Academia.edu](https://www.academia.edu/83988607/Real_time_gait_event_detection_for_lower_limb_amputees_using_a_single_wearable_sensor)

[^19]: [Towards Inertial Sensor Based Mobile Gait Analysis: Event-Detection and Spatio-Temporal Parameters - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6339047/)

[^20]: [Full article: Accurate detection of gait events using neural networks and IMU data mimicking real-world smartphone usage](https://www.tandfonline.com/doi/full/10.1080/10255842.2024.2423252)

[^21]: [Full article: Accurate detection of gait events using neural networks and IMU data mimicking real-world smartphone usage](https://www.tandfonline.com/doi/full/10.1080/10255842.2024.2423252)

[^22]: [Automatic gait events detection with inertial measurement units: healthy subjects and moderate to severe impaired patients - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11184826/)

[^23]: [Gait Trajectory and Gait Phase Prediction Based on an LSTM Network - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC7764336/)

[^24]: [Towards Inertial Sensor Based Mobile Gait Analysis: Event-Detection and Spatio-Temporal Parameters - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6339047/)

[^25]: [Evaluation of Validity and Reliability of Inertial Measurement Unit-Based Gait Analysis Systems - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6325313/)

[^26]: [Automatic gait events detection with inertial measurement units: healthy subjects and moderate to severe impaired patients - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11184826/)

[^27]: [The use of accelerometry to detect heel contact events for use as a sensor in FES assisted walking | ResearchGate](https://www.researchgate.net/publication/8998165_The_use_of_accelerometry_to_detect_heel_contact_events_for_use_as_a_sensor_in_FES_assisted_walking)

[^28]: [A Systematic Approach to the Design and Characterization of a Smart Insole for Detecting Vertical Ground Reaction Force (vGRF) in Gait Analysis - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC7070759/)

[^29]: [Adjustable Method for Real-Time Gait Pattern Detection Based on Ground Reaction Forces Using Force Sensitive Resistors - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6263965/)

[^30]: [FSR location and numbering for both left and right insole | ResearchGate](https://www.researchgate.net/figure/FSR-location-and-numbering-for-both-left-and-right-insole_fig1_280557402)

[^31]: [A Systematic Approach to the Design and Characterization of a Smart Insole for Detecting Vertical Ground Reaction Force (vGRF) in Gait Analysis](https://www.mdpi.com/1424-8220/20/4/957)

[^32]: [The Orthotic Effects of Different Functional Electrical Stimulation Protocols on Walking Performance in Individuals with Incomplete Spinal Cord Injury](https://meridian.allenpress.com/tscir/article/29/Supplement/142/496765/The-Orthotic-Effects-of-Different-Functional)

[^33]: [Design of a low-cost force insoles to estimate ground reaction forces during human gait - ScienceDirect](https://www.sciencedirect.com/science/article/pii/S246806722400083X)

[^34]: [Adjustable Method for Real-Time Gait Pattern Detection Based on Ground Reaction Forces Using Force Sensitive Resistors - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6263965/)

[^35]: [Gait cycle: phases, muscles and joints involved | Kenhub](https://www.kenhub.com/en/library/anatomy/gait-cycle)

[^36]: [Stimulation parameter optimization for FES supported standing up and walking in SCI patients - PubMed](https://pubmed.ncbi.nlm.nih.gov/15725221/)

[^37]: [Functional Electrical Stimulation Cycling for Spinal Cord Injury - Physiopedia](https://www.physio-pedia.com/Functional_Electrical_Stimulation_Cycling_for_Spinal_Cord_Injury)

[^38]: [The use of electrical stimulation for correction of dropped foot](http://www.salisburyfes.com/dropfoot.htm)

[^39]: [Odstock Medical Limited - Electrode Position Guide](https://odstockmedical.com/wp-content/uploads/electrode_position_revision_0.pdf)

[^40]: [The effects of common peroneal stimulation on the effort and speed of walking - PubMed](https://pubmed.ncbi.nlm.nih.gov/9360032/)

[^41]: [The duty cycle in Functional Electrical Stimulation research. Part II - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6317134/)

[^42]: [Foundations of Electrical Stimulation](https://media.lanecc.edu/users/howardc/PTA101/101FoundationsofEstim/101FoundationsofEstim_print.html)

[^43]: [Effects of electrode size and placement on comfort and efficiency during low-intensity neuromuscular electrical stimulation - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC8761348/)

[^44]: [Does Electrode Sensor Positioning over Motor Points Affect Different Portions of Quadriceps Muscle Architecture - Wiley](https://onlinelibrary.wiley.com/doi/10.1155/2023/4871277)

[^45]: [Functional electrical stimulation therapy for restoration of motor function after spinal cord injury and stroke - BioMedical Engineering OnLine](https://biomedical-engineering-online.biomedcentral.com/articles/10.1186/s12938-020-00773-4)

[^46]: [An investigation of the effect of electrode size and electrode location on comfort during stimulation of the gastrocnemius muscle - ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1350453304001390)

[^47]: [Functional Electrical Stimulation in Spinal Cord Injury: From Theory to Practice - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC3584753/)

[^48]: [Effect of Longitudinal Versus Transverse Electrode Placement on Torque Production - JOSPT](https://www.jospt.org/doi/10.2519/jospt.1990.11.11.530)

[^49]: [Evaluation of Gait Phase Detection Delay Compensation Strategies - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6603781/)

[^50]: [Prediction of gait trajectories based on the Long Short Term Memory neural networks - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC8341582/)

[^51]: [Cycling Lower-Limb Movement Analysis and Decoding by LSTM for a Motor Imagery-Based FES Rehabilitation System - SpringerLink](https://link.springer.com/chapter/10.1007/978-3-031-49407-9_18)

[^52]: [Deep Learning-Based Prediction Model for Gait Recovery after a Spinal Cord Injury](https://www.mdpi.com/2075-4418/14/6/579)

[^53]: [Detection of the electromechanical delay and its components during voluntary isometric contraction of the quadriceps femoris muscle - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC4274888/)

[^54]: [Detection of the electromechanical delay and its components during voluntary isometric contraction of the quadriceps femoris muscle - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC4274888/)

[^55]: [Electromechanical delay in the tibialis anterior muscle during time-varying ankle dorsiflexion - PubMed](https://pubmed.ncbi.nlm.nih.gov/28813795/)

[^56]: [The Time-Varying Nature of Electromechanical Delay and Muscle Control Effectiveness - PubMed](https://pubmed.ncbi.nlm.nih.gov/27845664/)

[^57]: [A Control Scheme That Uses Dynamic Postural Synergies to Coordinate a Hybrid Walking Neuroprosthesis - Frontiers](https://www.frontiersin.org/journals/neuroscience/articles/10.3389/fnins.2018.00159/full)

[^58]: [Predictor-based compensation for electromechanical delay during neuromuscular electrical stimulation - PubMed](https://pubmed.ncbi.nlm.nih.gov/21968792/)

[^59]: [An adaptive reflexive control strategy for walking assistance system based on functional electrical stimulation - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC9450861/)
