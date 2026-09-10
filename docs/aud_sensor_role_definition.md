# AUD Multimodal Hardware — Evidence-Backed Sensor Role Definition

**Status:** evidence-gathering / prototype-definition artifact

**Date:** 2026-09-10
**Primary project source:** [the existing evidence map](aud_multimodal_hardware_evidence_map.md)

## Evidence provenance and interpretation rules

This document is derived primarily from the existing evidence map. It preserves the map's distinction between established measurement science, AUD-related observations, and unresolved questions. A small number of manufacturer documents and one clinical guideline were independently checked as supplementary verification. Those sources are identified in the ledger.

- **EM** — carried forward from the existing evidence map.
- **SV** — supplementary verification performed for this document.
- **OQ** — open or unresolved question; not an assumption.

Evidence strength is assigned to a particular claim, not to a sensor in the abstract:

- **A — Strong / well established:** repeatedly supported by good-quality literature and/or well-established physiology.
- **B — Moderate:** supported by multiple relevant studies, but with meaningful limitations.
- **C — Exploratory:** plausible and supported by limited, emerging, pilot, or highly context-dependent evidence.
- **D — Indirect / contextual:** useful mainly through physiological context, artifact handling, movement context, cross-validation, or adjacent evidence rather than alcohol-specific evidence.
- **E — Unsupported / insufficient:** a claim that should currently be avoided.

An **A** rating for a physical measurement does not mean that the same sensor has an A-level AUD diagnostic capability. No current candidate directly measures ethanol. The physiological channels are nonspecific and require context, ground truth, quality assessment, and validation.

## 1. Purpose and Scope

This document defines evidence-backed candidate roles for the current sensing modalities. It does not lock the final hardware architecture, physical form factor, sensor inclusion, clinical interpretation, or patent novelty.

The purpose is to turn the current evidence research into a traceable definition of what each candidate could contribute to a multimodal AUD support / rehabilitation prototype. It is not a clinical protocol, a diagnostic specification, a final system design, or a patent analysis.

The current evidence supports a collection of complementary, mostly nonspecific physiological and behavioral channels. It does not support a direct statement that any single channel, or the unvalidated combination of channels, identifies AUD, craving, intoxication, withdrawal, lapse, or relapse in an individual.

The existing evidence map is not modified by this document. Hardware statements are separated into:

1. **IC capability** — what the named integrated circuit is documented to do.
2. **Breakout-board implementation** — what a particular board may expose, which depends on its schematic, passives, layout, power, connectors, and software.
3. **Verified capability of the stated module** — what has actually been established for the exact board named in this project.

Where the third level is not verified, the uncertainty is stated explicitly.

## 2. Current Hardware Candidates

The current candidates are recorded here without selecting, removing, or ranking them for final inclusion:

1. **ESP32 DevKit V1, 30-pin** — an ESP-WROOM-32-family microcontroller development board. “DevKit V1” is a generic carrier-board description; the exact carrier revision and manufacturer remain to be verified.
2. **ProtoCentral tinyGSR GSR/EDA Sensor Board** — legacy hardware, PCB marking **11/22**. The exact schematic revision has not been independently matched to the marking; it is treated provisionally as the original/legacy family, not the current v3 board.
3. **CJMCU-8232 AD8232 Single-Lead ECG / Heart-Rate Monitor Sensor Module** — PCB marking **VS82**. The AD8232 IC is documented; the exact VS82 passive network and layout are not verified from an authoritative CJMCU document.
4. **SmartElex MAX30101 PPG / Photodetector Sensor Breakout Board** — exact SmartElex schematic, optical window, and implementation details are not verified from primary board documentation.
5. **SmartElex TMP117 Digital Temperature Sensor Breakout Board** — the TMP117 IC is a local digital temperature sensor; skin-temperature meaning depends on thermal coupling and the board's construction.
6. **GY-521 MPU-6050** — a generic 6-axis accelerometer + gyroscope breakout module. GY-521 boards are an ecosystem of implementations; exact regulator, pull-ups, filtering, and mounting details are board-specific.

The evidence map and this document use the component names above as candidate identities. They do not imply that the candidates are equivalent to certified medical monitors or that all IC features are present on the stated breakouts.

## 3. Measurement-to-Interpretation Model

The correct reasoning chain is:

```text
direct measurement
        ↓
raw signal / data code
        ↓
derived signal feature
        ↓
physiological or behavioral interpretation
        ↓
possible AUD-related relevance
        ↓
clinical conclusion only if separately validated and clinically authorized
```

Examples for the current candidates:

| Layer | ECG example | EDA example | IMU example |
|---|---|---|---|
| Direct measurement | Skin potential difference along one lead | Skin electrical response associated with conductance/resistance | Acceleration and angular velocity |
| Raw signal | Conditioned analog voltage sampled by an ADC | Relative or calibrated board output, depending on board revision | Digital axis samples from the IMU |
| Derived feature | R-peak times, RR/NN intervals, RMSSD | Tonic trend, phasic response count/amplitude | Band-limited tremor energy or movement variance |
| Physiological interpretation | Cardiac electrical timing and autonomic association | Sympathetic sudomotor activity / arousal association | Motor activity, tremor, or movement context |
| Possible AUD relevance | Group-level HRV differences; withdrawal/craving research context | Cue-evoked arousal and recovery-stress research context | Withdrawal-tremor quantification and activity context |
| Clinical conclusion | Not “AUD” or “withdrawal” from the signal alone | Not “craving” or “stress cause” from the signal alone | Not “withdrawal” or “intoxication” from movement alone |

The layers must not be conflated. An ECG voltage waveform is not HRV; HRV is not a direct autonomic measurement; an autonomic association is not an AUD-specific marker; and an AUD-group association is not an individual clinical diagnosis. The same rule applies to optical, electrodermal, temperature, and inertial data.

## 4. Executive Sensor Role Matrix

| Modality | Direct measurement | Important derived features | Physiological domain | Possible AUD relevance | Evidence strength | Primary candidate role | Major limitations |
|---|---|---|---|---|---|---|---|
| ECG / AD8232 | Conditioned single-lead cardiac biopotential voltage | R peaks, HR, RR/NN, RMSSD, SDNN, quality flags | Cardiac timing; autonomic cardiovascular association | Lower HRV in AUD groups; mixed withdrawal evidence; exploratory craving/relapse context | **A** measurement; **B** AUD-group HRV; **C** individual/context-specific AUD use | Primary physiological measurement; derived feature source; cardiovascular/autonomic information | Motion/EMG, electrodes, respiration, posture, ectopy, exact VS82 filters/gain, ADC timing |
| PPG / MAX30101 | Reflected red/IR/green optical photodetector samples | Pulse rate, inter-pulse intervals, PRV, morphology, perfusion proxies | Peripheral pulse and vascular/perfusion dynamics | Alcohol-related rate changes and multimodal intoxication findings are exploratory; PRV is not ECG HRV | **A** pulse measurement; **B** related variability science; **C** AUD use | Complementary cardiovascular/peripheral measurement; ECG cross-check; fusion input | Motion, pressure, site, perfusion, temperature, optical geometry, algorithm/validation, exact breakout |
| EDA / legacy tinyGSR | Skin electrical response; legacy board output is provisionally relative | Tonic trend, phasic responses, relative event/amplitude features | Sympathetic sudomotor activity / arousal | Alcohol-cue response and AUD-recovery stress monitoring are exploratory; craving/relapse direct use insufficient | **A** general EDA physiology; **C** AUD cue/recovery; **D** withdrawal context | Potentially complementary sympathetic-arousal channel; longitudinal/fusion input | Legacy trimmer-set output, contact/motion, sweat, temperature, hydration, nonspecific arousal |
| Peripheral temperature / TMP117 | Local IC/die temperature; skin estimate only with validated thermal coupling | Baseline, slope, local trend, contact-context features | Peripheral thermal regulation and vascular/perfusion context | Alcohol-related temperature shifts exist but are environmentally and physiologically nonspecific | **A** IC temperature measurement; **C** AUD-related trend | Primarily contextual/peripheral signal; PPG/EDA interpretation support | Ambient conditions, body site, coupling, airflow, activity, perfusion, self-heating, illness |
| Motion + inertial sensing / MPU6050 | 3-axis acceleration and 3-axis angular velocity | Activity, stillness, posture proxies, gait/sway, tremor energy, quality flags | Motor activity and measurement context | Moderate evidence for withdrawal-tremor quantification; exploratory intoxication/gait evidence | **A** inertial measurement; **B** withdrawal-tremor feature; **C** intoxication/gait | Motion-artifact identification; movement/activity information; exploratory motor feature source | Placement/orientation, loose mounting, voluntary movement, task dependence, tremor confounders |
| ESP32 system role | No physiological quantity; receives sensor data | Timestamps, buffers, quality metadata, software features | Acquisition and coordination infrastructure | Enables longitudinal multimodal measurement but has no AUD physiological meaning | **N/A** as a physiological modality | Acquisition/controller; interface; timestamping; transport | Carrier-board variation, ADC behavior, scheduling, timestamp jitter, packet loss, RF/power noise |

## 5. ECG / AD8232

### 5.1 Direct measurement

The physiological quantity of interest is the skin potential difference generated primarily by cardiac depolarization and repolarization along one lead vector. The AD8232 IC is an analog signal-conditioning front end for ECG and other biopotentials. It amplifies and filters the small voltage; it does not directly measure HR, RR intervals, HRV, sympathetic activity, stress, alcohol, craving, or withdrawal ([H1], [H2]).

For the candidate module, the most defensible chain is:

```text
electrode potential difference
    → AD8232 conditioning
    → analog output voltage
    → host ADC sample
```

The measurement is single-lead and depends on electrode geometry and placement. It is not automatically equivalent to a diagnostic 12-lead ECG.

### 5.2 Raw output

The expected raw output is a conditioned analog voltage centered on the module's reference/bias, sampled by the ESP32 ADC if that acquisition path is used. The AD8232 IC documentation supports adjustable high-pass and low-pass filtering, gain, lead-off detection, and right-leg-drive-related circuitry ([H1], [H2]).

The exact CJMCU-8232 **VS82** board implementation is not verified. The board's passive filter cutoff frequencies, gain-setting components, reference arrangement, electrode/RLD topology, tolerances, output range, and lead-off wiring must therefore remain open. A safe description is **conditioned single-lead ECG-like analog data**, not a claim about the exact bandwidth or clinical waveform fidelity.

### 5.3 Candidate derived features

If the raw waveform quality supports reliable beat detection, candidate features include:

- R-peak time stamps and beat-to-beat intervals.
- Heart rate from beat frequency.
- RR intervals; after appropriate artifact and ectopic-beat handling, NN intervals may be used for HRV analysis.
- Time-domain HRV such as RMSSD and SDNN, with recording duration and preprocessing documented.
- Other interval statistics and beat-quality flags.
- Frequency-domain measures only when duration, stationarity, sampling, respiration, and artifact handling are appropriate.

The Task Force standard is the foundational reference for HRV definitions and measurement methodology ([P1]). RMSSD, SDNN, and frequency-domain measures are not interchangeable and should not be treated as a generic “stress score.”

### 5.4 Physiological meaning

R-peak timing provides a cardiac electrical timing reference. Beat-to-beat variation reflects sinoatrial-node modulation involving autonomic, respiratory, baroreflex, metabolic, postural, sleep, thermoregulatory, and behavioral influences. It is therefore reasonable to describe ECG-derived HRV as information related to cardiac autonomic regulation, not as a direct isolated readout of sympathetic or parasympathetic activity.

Interpretation should control or annotate respiration, posture, duration, activity, sleep, medications, and other context ([P2]). The LF/HF ratio must not be presented as a simple numerical “sympathetic/parasympathetic balance”; that interpretation has been specifically criticized ([P3]).

### 5.5 AUD-related evidence

The strongest AUD-related evidence in the candidate set concerns HRV at the group level. A systematic review and meta-analysis found lower HRV in AUD than in controls, including lower total variability and RMSSD, but also reported substantial heterogeneity ([A1]). A broader systematic review found cardiovascular autonomic dysfunction across a wide range of chronic alcohol-abusing populations, with heterogeneous populations and criteria ([A3]).

Acute alcohol studies and reviews generally support alcohol-related HRV changes, but those changes are not specific to AUD or to a particular individual ([A2]).

Acute withdrawal evidence is mixed. The Bär study of 40 men found increased heart rate at admission but no expected change in classical HRV measures or sympathetic skin response, and concluded that those measures were not suitable for describing the autonomic changes in that cohort ([A4]). Clinical withdrawal assessment recognizes autonomic hyperactivity and increased hand tremor as possible signs, but these signs occur in a clinical syndrome and are not established by a single ECG stream ([H15]).

Reactive HRV findings in craving and relapse research are heterogeneous. The evidence map includes studies associating reactive HRV with craving or relapse-related variables, a 100-day naturalistic study with large individual differences and weak simple mappings, and a 2026 VR cue study in which subjective craving and EDA changed without a significant HR/HRV cue effect ([A2], [A8], [A12]). This supports ECG as a candidate for autonomic and longitudinal research, not an imminent-lapse or craving detector.

### 5.6 Evidence strength

| Claim | Strength | Reason and boundary |
|---|---|---|
| A conditioned single-lead ECG waveform can be acquired with an AD8232-class front end | **A** | Mature instrumentation concept; exact VS82 implementation still needs verification ([H1], [H2]). |
| R peaks, HR, RR/NN intervals, and standard HRV features can be derived from adequate ECG | **A** | Established measurement practice when signal quality and processing requirements are met ([P1], [P2]). |
| AUD groups often show lower HRV than controls | **B** | Meta-analytic support, but high heterogeneity and population-level inference ([A1]). |
| HRV is a useful general AUD autonomic research feature | **B** | Repeated association and physiological rationale, but nonspecific and not an individual diagnostic rule ([A1], [A2], [A3]). |
| ECG HR/HRV alone identifies acute withdrawal, craving, intoxication, or relapse | **C to E**, depending on claim | Some context-specific findings exist, but evidence is mixed, heterogeneous, or insufficient for the exact individual-level claim ([A4], [A8], [A12]). |

### 5.7 Confounders and limitations

Important limitations include:

- Electrode placement, lead orientation, contact impedance, drying/lifting electrodes, and cable movement.
- Skeletal-muscle EMG, gross body motion, local electrode movement, and baseline wander.
- Mains/electromagnetic noise, power coupling, and ADC quantization/noise.
- Respiration, posture, exercise, recovery from exercise, and recording duration.
- Ectopic beats, arrhythmia, missed R peaks, false R peaks, filtering artifacts, and poor-quality windows.
- Age, fitness, sleep/circadian state, illness, fever, hydration, pain, anxiety, medication, nicotine, and caffeine.
- Unknown VS82 filter and gain settings, which limit claims about morphology and HRV suitability.

The AD8232 datasheet describes filtering and motion-related design goals at IC level, but those features do not guarantee artifact-free data from the CJMCU breakout ([H2]).

### 5.8 Possible multimodal role

Evidence-backed candidate roles are:

- **Primary physiological measurement:** cardiac electrical timing.
- **Derived physiological feature source:** HR, RR/NN, HRV, and beat-quality features.
- **Cardiovascular information:** electrical beat timing and rate.
- **Autonomic-state information:** an indirect cardiac-autonomic channel when context and HRV methodology are adequate.
- **Multimodal fusion input:** compare with PPG pulse timing, EDA, temperature, and IMU context.
- **Longitudinal/trend monitoring:** within-person resting or standardized-window changes, subject to repeatability and confounder annotation.
- **Exploratory AUD-related marker:** group-level or individualized research hypotheses, not a clinical marker.

Its highest-value multimodal role may be as an electrical timing reference against which the optical pulse stream can be compared. A motion flag from the IMU should reduce confidence in contaminated intervals; it should not be treated as proof that a corrupted segment has been repaired.

### 5.9 Claims we should NOT currently make

- “The AD8232 directly measures HRV.” It conditions a biopotential; HRV is derived.
- “The ECG detects AUD, craving, relapse, intoxication, or withdrawal.”
- “A low RMSSD proves craving, stress, or withdrawal.”
- “A high heart rate proves alcohol withdrawal.”
- “LF/HF is a direct sympathetic/parasympathetic balance measurement.”
- “The VS82 module has a particular clinical bandwidth, gain, or morphology fidelity” before its schematic is verified.
- “The hobby module is a clinically validated arrhythmia monitor.”

### 5.10 Open questions

1. What exact CJMCU VS82 schematic, gain, filter cutoffs, and electrode/RLD topology are present?
2. Does the physical module preserve R-peak timing adequately for the HRV features under consideration?
3. What recording durations and quality thresholds would be used for each feature?
4. How will ectopy, missed beats, and low-quality intervals be identified and handled?
5. Will respiration, posture, activity, sleep, medication, nicotine, caffeine, hydration, and illness be annotated or measured?
6. Do within-person baselines add information beyond HR, EDA, motion, self-report, and clinical context?
7. Does an ECG-derived feature add independent value to a future multimodal model after motion and EDA are known?

## 6. PPG / MAX30101

### 6.1 Direct measurement

PPG detects changes in reflected light caused by blood-volume and optical changes in a peripheral tissue bed. At IC level, MAX30101 provides selected red, infrared, and green LED illumination, photodetection, ambient-light rejection, a FIFO, and an 18-bit current ADC ([H3]). The direct data are digitized photodetector/optical samples, not a finished HR, PRV, blood-pressure, SpO₂, or alcohol result.

The candidate-specific chain is:

```text
LED illumination + tissue reflection/scattering
    → photodetector current
    → MAX30101 optical AFE/ADC data
    → host-readable samples
```

### 6.2 Raw output

The expected raw output is digital optical data obtained over the IC's digital interface. The SmartElex board is reported by a reseller as a MAX30101 breakout with red/IR/green LEDs and an I²C interface, but no primary SmartElex schematic or clinical algorithm was located ([H11]). The exact optical window, LED-current configuration, power conversion, board layout, mechanical contact, and software are therefore not treated as verified module capabilities.

The IC's product description may call it a heart-rate and pulse-oximetry sensor, but that does not mean the exact breakout directly outputs a validated heart rate or SpO₂. Pulse rate and oxygen saturation require downstream algorithms, signal-quality checks, optical geometry, calibration, and validation. Red/IR hardware alone is not a validated pulse oximeter ([H3], [H14]).

### 6.3 Candidate derived features

When the optical signal is adequate, candidate features include:

- Pulse peaks, pulse onsets, pulse rate, and inter-pulse intervals.
- Pulse-rate variability (PRV).
- AC amplitude, DC level, AC/DC relationships, and perfusion-related trends.
- Pulse-wave rise time, width, slopes, area, derivative features, and notch-related features when sample rate, placement, and quality permit.
- Wavelength-consistency and optical signal-quality indicators.
- SpO₂ only if a suitable red/IR algorithm, calibration, placement, and reference validation are separately established.

PRV is related to but not interchangeable with ECG HRV. Peripheral electromechanical and vascular timing can change independently of cardiac electrical timing ([P5], [P13]).

### 6.4 Physiological meaning

The PPG waveform reflects the arrival of a peripheral arterial pressure/volume pulse as modulated by local vascular tone, perfusion, tissue optics, contact, and motion. The pulse timing is not identical to the ECG electrical event. Pulse morphology can be affected by vascular aging, arterial stiffness, pressure, and other influences, but those are associations rather than direct measurements ([P4], [P6]).

Therefore:

```text
optical samples
    → pulse waveform and timing
    → pulse-rate / PRV / morphology features
    → peripheral cardiovascular and perfusion associations
    → possible alcohol/autonomic research relevance
```

### 6.5 AUD-related evidence

The evidence map supports alcohol-related changes in rate and autonomic state as a plausible reason to investigate PPG-derived pulse features, but direct evidence for this exact MAX30101 breakout is absent. ECG HRV findings in AUD cannot simply be transferred to PPG PRV because PRV includes peripheral vascular and electromechanical effects ([P5], [P13]).

A 2026 controlled pilot used a research-grade wrist device to measure heart rate, skin temperature, EDA, and accelerometry in 28 healthy participants during alcohol exposure. The study found statistically significant changes in each parameter and multimodal classification accuracy as high as 0.80, but it used a different device and a controlled intoxication/impairment task; it does not establish a PPG-only result, a MAX30101-breakout result, or an AUD-rehabilitation result ([A13]).

Potential AUD relevance is therefore **exploratory**: pulse rate may contribute cardiovascular context, PRV may contribute a distinct peripheral variability channel, and PPG may support multimodal cross-validation. None supports an individual alcohol, craving, withdrawal, or relapse claim.

### 6.6 Evidence strength

| Claim | Strength | Reason and boundary |
|---|---|---|
| MAX30101 IC supports reflective optical acquisition with red/IR/green LEDs and a photodetector | **A** | Manufacturer documentation; this is IC capability, not complete SmartElex validation ([H3]). |
| Pulse rate can be derived from adequate PPG | **A** | Mature PPG measurement concept, subject to signal quality and placement ([P4]). |
| PRV is a related but distinct variability measure | **B** | Review evidence supports conditional agreement and important differences from HRV ([P5], [P13]). |
| PPG morphology/perfusion features may add vascular context | **B to C** | Physiologically plausible and studied, but highly site-, algorithm-, and quality-dependent ([P4], [P6]). |
| PPG alone identifies AUD, intoxication, withdrawal, craving, or relapse | **E** | No sufficient evidence for the exact claim or exact breakout. |
| Multimodal optical/autonomic features may carry alcohol-related information | **C** | Small controlled studies support feasibility, not generalization or clinical use ([A13]). |

### 6.7 Confounders and limitations

- Motion at the sensor-skin interface and local limb movement.
- Contact pressure, strap tightness, optical alignment, site, and loose attachment.
- Low peripheral perfusion, cold-induced vasoconstriction, vascular disease, and posture.
- Skin temperature, ambient temperature, tissue optical properties, and skin pigmentation.
- Ambient-light leakage, sensor saturation/clipping, LED-current settings, sample rate, and digital configuration.
- Exercise, respiration, hydration, nicotine, medication, and other changes in vascular state.
- Unknown SmartElex optical window and board implementation.
- Proprietary or unvalidated algorithms for pulse, PRV, or SpO₂.

The FDA notes that pulse-oximetry readings can be affected by poor circulation, skin pigmentation, skin temperature, tobacco use, nail polish, and other factors. This reinforces that optical outputs require context and validation ([H14]).

### 6.8 Possible multimodal role

Possible roles justified by current evidence are:

- **Primary physiological measurement:** peripheral pulsatile optical signal.
- **Derived physiological feature source:** pulse rate, inter-pulse timing, PRV, and morphology/perfusion candidates.
- **Cardiovascular information:** peripheral pulse timing and rate.
- **Peripheral physiological information:** perfusion/vascular context.
- **Cross-sensor validation:** compare optical pulses with ECG beats.
- **Motion-artifact identification support:** use the IMU to mark motion-contaminated windows.
- **Multimodal fusion input:** combine with ECG, EDA, temperature, and movement without treating the combination as alcohol-specific.
- **Exploratory AUD-related marker:** only as a future, ground-truth-labeled research feature.

### 6.9 Claims we should NOT currently make

- “MAX30101 directly measures heart rate or SpO₂.” The raw IC output is optical data; these are derived or estimated outputs.
- “The SmartElex breakout is a validated pulse oximeter” without algorithm and reference validation.
- “PPG PRV is the same as ECG HRV.”
- “PPG morphology directly measures blood pressure or arterial stiffness.”
- “ECG + PPG automatically measures PTT or blood pressure.”
- “PPG detects alcohol, AUD, craving, withdrawal, or relapse.”
- “The MAX30101 IC's motion-resilience specification guarantees artifact-free data from this breakout.”

### 6.10 Open questions

1. What exact SmartElex schematic, optical geometry, power rails, LED settings, and board software are present?
2. What body site, pressure, attachment method, and optical barrier will be used in later testing?
3. Is the intended question pulse rate, PRV, morphology, perfusion, or SpO₂? Each requires a different validation plan.
4. Are ECG and PPG clocks and fiducial definitions accurate enough for stable PAT estimation?
5. Does PAT add independent information after HR, EDA, temperature, and motion are included?
6. How does the exact MAX30101 + MPU6050 arrangement behave during realistic motion?
7. If SpO₂ remains a research question, what reference device, calibration, skin-site coverage, and validation criteria are required?

## 7. EDA/GSR / tinyGSR

### 7.1 Direct measurement

EDA describes changes in the electrical properties of the skin associated with sweat-gland activity. Exosomatic EDA systems apply an electrical excitation and measure conductance or resistance; eccrine sweat glands are predominantly under sympathetic cholinergic control ([P8], [P9]).

The exact candidate is a **legacy ProtoCentral tinyGSR board** with PCB marking **11/22**. ProtoCentral's current v3 page explicitly distinguishes the redesigned v3 from the earlier board: v3 reports absolute microsiemens, whereas the earlier board produced a relative, trimmer-set reading for which universal conversion to real units was not available ([H9]). The marking has not been independently matched to a released schematic revision.

Accordingly, the direct measurement statement for this project must remain conditional:

> The legacy board produces a digitized, relative front-end response associated with skin conductance/resistance; absolute µS output is not verified for the stated 11/22 unit.

### 7.2 Raw output

If the supplied 11/22 unit is the legacy implementation described by ProtoCentral, its output should be handled as a trimmer-dependent ADC response or relative digital value. The older tutorial describes the legacy implementation and baseline adjustment, while the current product page states that the original board's hand-trimmed behavior prevents a universal conversion to absolute conductance ([H9], [H10]).

The exact board revision, ADC path, trimmer setting, electrode connection, and repeatability of the physical unit remain open. Until verified, the safe raw-data representation is **relative counts or within-unit response**, not calibrated absolute SCL/SCR in µS.

### 7.3 Candidate derived features

If signal quality permits, candidate features include:

- Relative tonic level or slow baseline trend.
- Relative phasic excursions and event counts.
- Relative response amplitude, rise time, recovery time, width, area, and derivative.
- Baseline-normalized changes within a session or within a person.
- Motion- and contact-quality flags, especially when paired with IMU and temperature.

Standard EDA terminology separates a tonic component, often called skin conductance level, from faster phasic skin conductance responses. For this legacy board, the feature names may be used conceptually, but absolute conductance amplitudes and comparisons across boards/sessions require board-specific calibration evidence ([P8]).

### 7.4 Physiological meaning

EDA is best interpreted as a sympathetic sudomotor activity channel. A phasic response can accompany emotional arousal, cognitive effort, pain, startle, heat, physical effort, speech, respiration, or other events. A tonic shift can reflect thermoregulatory and skin-state changes as well as arousal.

The defensible chain is:

```text
skin electrical response
    → relative tonic/phasic EDA features
    → sympathetic sudomotor association
    → context-dependent arousal interpretation
    → possible AUD cue/stress/withdrawal research relevance
```

EDA is not a direct emotion, stress cause, craving, relapse, alcohol-use, or withdrawal measurement.

### 7.5 AUD-related evidence

A 2026 VR cue-exposure study in 61 patients with alcohol dependence found alcohol-associated scenarios increased nonspecific SCR frequency, with effect sizes ranging from small to large across scenarios, while SCL and HR/HRV did not show the same consistent cue effect. Correlations between subjective craving and EDA were statistically significant but weak (approximately **r = 0.20–0.21**) ([A8]). This is evidence of context-specific cue-related arousal, not a direct craving meter.

Older cue-exposure work found heterogeneous subjective and physiological responses, including physiological response without subjective response and neither response in substantial subgroups ([A9], [A10]).

In an ambulatory proof-of-concept study of 11 adults in AUD recovery, EDA and HRV were collected for up to 14 days. Most EDA samples met that study's clean-signal criteria, and features were associated with self-reported stress, emotions, pain, and discomfort ([A11]). This supports feasibility of ambulatory psychophysiological monitoring, not general relapse prediction.

The 100-day naturalistic recovery study found large inter- and intra-individual differences and no simple physiology-to-lapse mapping ([A12]). Withdrawal sweating is clinically plausible and has been observed in small monitoring studies, but direct EDA evidence is sparse and mixed; sympathetic skin response in the Bär study did not show the expected increased sympathetic activity during acute withdrawal ([A4], [A5]).

The 2026 intoxication pilot found EDA changes in a controlled multimodal setting, but the device, population, task, and labels differ from this candidate board and do not establish EDA-only alcohol detection ([A13]).

### 7.6 Evidence strength

| Claim | Strength | Reason and boundary |
|---|---|---|
| EDA reflects sweat-gland-related skin electrical change with sympathetic sudomotor relevance | **A** | Mature psychophysiological measurement literature ([P8], [P9]). |
| Relative tonic/phasic event features can be explored on the legacy board | **B to C** | Conceptually standard, but exact board calibration, dynamic range, and repeatability are not verified ([H9], [H10]). |
| EDA can respond to alcohol-associated cues in some paradigms | **C** | 2026 and earlier cue studies show heterogeneous, context-dependent effects ([A8], [A9], [A10]). |
| EDA is feasible for AUD-recovery stress/context monitoring | **C** | Small ambulatory proof-of-concept study, not a validated clinical predictor ([A11], [A12]). |
| EDA detects craving, relapse, recent alcohol use, or withdrawal by itself | **E** | Exact individual-level claims are unsupported or insufficient. |

### 7.7 Confounders and limitations

- Motion, cable/electrode movement, contact pressure, electrode lifting, and mechanical skin changes.
- Electrode site, electrode material, gel, sweat-gland density, skin hydration, lotions, washing, and individual skin properties.
- Ambient heat, humidity, airflow, exercise, sweating saturation, and thermoregulation.
- Respiration, speech, sound/noise context, physical effort, and cognitive effort.
- Medication, anticholinergic effects, nicotine, caffeine, anxiety, pain, and illness.
- Baseline drift, acclimation time, ADC behavior, saturation, and hardware noise.
- The legacy board's hand-trimmed front end and unknown 11/22 revision, which add cross-device and cross-session comparability problems.

A recent EDA artifact review specifically identifies movement, respiration, speech, temperature, humidity, environmental context, and electrode/system artifacts as important influences in ambulatory recordings ([P10]).

### 7.8 Possible multimodal role

Possible roles are:

- **Primary physiological measurement:** skin electrical response associated with sudomotor activity.
- **Sympathetic-arousal information:** a nonspecific sudomotor channel.
- **Peripheral physiological information:** skin-state and thermoregulatory context.
- **Potentially complementary candidate:** add a channel that is physiologically different from cardiac timing.
- **Contextual signal:** interpret a response with motion, temperature, activity, and self-report.
- **Motion-artifact identification support:** use IMU to grade periods in which transients may be mechanical or activity-related.
- **Multimodal fusion input:** test whether EDA adds information beyond ECG, PPG, temperature, and motion.
- **Longitudinal/trend monitoring:** preferably within-person and within-unit unless calibration is established.
- **Exploratory AUD-related marker:** cue/stress/recovery hypotheses only.

### 7.9 Claims we should NOT currently make

- “EDA detects craving.”
- “EDA detects stress” without specifying nonspecific sympathetic sudomotor/arousal association and competing explanations.
- “EDA detects recent alcohol use, intoxication, relapse, or withdrawal.”
- “The legacy tinyGSR reports absolute µS.”
- “EDA amplitude is comparable across units or sessions” without board-specific calibration.
- “A phasic response identifies an alcohol cue” without controlled context and independent cue labels.
- “The IMU can prove that every EDA transient is artifact or physiological.”

### 7.10 Open questions

1. Which exact schematic and legacy firmware/ADC path correspond to PCB marking 11/22?
2. Can the trimmer setting and board-specific response be measured and reproduced?
3. Are relative features stable across days, electrode changes, and sessions?
4. What electrode site and contact arrangement will be used later, and how does it affect interpretation?
5. Is phasic-event detection robust at the actual board's sample/filter settings?
6. How should high-motion and contact-change windows be masked, down-weighted, or retained?
7. Does within-person EDA add value beyond self-report, HR/HRV, activity, temperature, and clinical assessment?
8. Does withdrawal sweating have a reproducible pattern distinguishable from heat, exercise, anxiety, medication, and ordinary activity?

## 8. Peripheral Temperature / TMP117

### 8.1 Direct measurement

TMP117 is a local digital temperature-sensor IC. The IC measures the temperature of its own sensing die and reports a digital result; TI specifies 16-bit resolution of 0.0078 °C and accuracy up to ±0.1 °C over a specified range ([H4], [H5]).

The direct measurement for the SmartElex breakout is therefore:

> local TMP117 IC/die temperature.

It approximates skin temperature only when mechanical and thermal coupling make the skin the dominant heat source and competing heat paths are controlled. TI's wearable guidance emphasizes thermal contact and isolation from other heat sources ([H13]). The IC's accuracy is not automatically the accuracy of a skin or core-temperature measurement made by an uncharacterized breakout.

### 8.2 Raw output

The expected raw output is a digital temperature code read over the TMP117's I²C-compatible interface. The exact SmartElex board schematic, thermal path, exposed sensing surface, board mass, neighboring heat sources, and mechanical coupling have not been verified from primary SmartElex documentation ([H12]).

The temperature code should initially be described as **local board/sensor temperature**. A skin-temperature estimate requires an empirical comparison with a reference under the intended contact and environmental conditions.

### 8.3 Candidate derived features

If skin coupling is validated, candidate features include:

- Local temperature estimate over a specified site.
- Mean, median, variance, slope, and rate of change over windows.
- Deviation from an individual's baseline.
- Slow and fast temperature trends.
- A possible contact/wear-state cue, but only after experimental validation.
- A contextual covariate for PPG perfusion/amplitude and EDA tonic/phasic interpretation.

### 8.4 Physiological meaning

Peripheral skin temperature depends on local blood flow, vasodilation/vasoconstriction, core-to-skin heat transfer, ambient conditions, clothing, airflow, sweat/evaporation, activity, body site, circadian state, and illness. It can be compatible with autonomic or vascular change without being specific to either.

The defensible chain is:

```text
TMP117 die temperature
    → local temperature / validated skin-temperature estimate
    → peripheral thermal and perfusion context
    → possible autonomic/thermoregulatory association
    → possible AUD-related trend context
```

It is not a direct measurement of core temperature, blood temperature, autonomic tone, fever cause, alcohol concentration, or withdrawal.

### 8.5 AUD-related evidence

Controlled alcohol studies show that alcohol can alter skin temperature and thermoregulation, but the direction and magnitude depend strongly on environmental conditions. A systematic scoping review found only eight eligible studies involving 93 male participants and concluded that the evidence base remains limited ([A15], [A16]).

A small 24-hour withdrawal monitoring study observed temporal patterns in skin temperature and sweating alongside elevated tremor and locomotor activity, but the sample was only six withdrawal subjects ([A5]). This supports trend monitoring as a research possibility, not temperature-based withdrawal diagnosis.

The 2026 controlled multimodal pilot found a wrist skin-temperature change during intoxication, but it used a different research-grade device and a controlled healthy-participant protocol ([A13]). The result is exploratory and does not validate TMP117, skin temperature alone, or AUD rehabilitation use.

### 8.6 Evidence strength

| Claim | Strength | Reason and boundary |
|---|---|---|
| TMP117 is a high-accuracy local digital temperature sensor at IC level | **A** | Manufacturer datasheet and product documentation ([H4], [H5]). |
| A TMP117 breakout can provide a skin-temperature estimate | **B to C** | Depends on thermal/mechanical coupling, body site, and validation; not automatic from IC accuracy ([H5], [H13]). |
| Alcohol can alter peripheral temperature/thermoregulation | **B to C** | Controlled evidence exists, but context and literature are limited ([A15], [A16]). |
| Skin temperature alone identifies alcohol, intoxication, craving, withdrawal, or relapse | **E** | Nonspecific and insufficient for the exact claim. |

### 8.7 Confounders and limitations

- Ambient temperature, airflow, fan/AC, sun, heat sources, clothing, blankets, and environmental transitions.
- Contact pressure, air gaps, body site, device thermal mass, response time, and board self-heating.
- Heat from ESP32 regulators, LEDs, batteries, or neighboring boards.
- Local perfusion, vasoconstriction, vasodilation, sweat, evaporation, activity, and posture.
- Circadian rhythm, sleep, illness/fever, hormones, hydration, and medication.
- Unknown SmartElex thermal construction and coupling.

### 8.8 Possible multimodal role

Possible roles are:

- **Peripheral physiological information:** local thermal and vascular context.
- **Contextual signal:** interpret PPG perfusion/amplitude and EDA changes.
- **Artifact-management support:** help identify environmental or thermal explanations for signal changes.
- **Longitudinal/trend monitoring:** personal baseline and slow trend research, subject to environmental annotation.
- **Exploratory AUD-related marker:** only in multimodal, ground-truth-labeled studies.

Temperature is currently better justified as a context channel than as a primary AUD-related marker. This is a provisional role classification, not a sensor-inclusion decision.

### 8.9 Claims we should NOT currently make

- “TMP117 measures core body temperature” when installed as an unvalidated local breakout.
- “TMP117 detects alcohol, intoxication, craving, relapse, or withdrawal.”
- “A ±0.1 °C IC accuracy specification proves ±0.1 °C skin-temperature accuracy in this prototype.”
- “A temperature rise identifies fever cause, vasodilation cause, or alcohol use.”
- “Temperature alone is an autonomic-state measurement.”
- “Temperature-based wear detection is reliable” without validation at the eventual placement.

### 8.10 Open questions

1. What exact thermal path and skin-contact arrangement are present on the SmartElex board?
2. What are its response time, offset, and self-heating relative to a reference skin thermometer?
3. What body site and environmental range are relevant to later testing?
4. Will ambient temperature or airflow be measured or annotated?
5. Can temperature distinguish contact loss from true local thermal change?
6. Does temperature explain meaningful PPG amplitude/perfusion or EDA baseline variation after activity and environment are modeled?
7. Does a personal temperature trend add independent AUD-recovery information?

## 9. IMU / MPU6050

### 9.1 Direct measurement

The MPU-6050 IC directly measures:

- three-axis accelerometer output representing specific force, including gravity when stationary;
- three-axis gyroscope output representing angular velocity.

TDK documents the MPU-6050 as an obsolete 6-axis digital-output device with I²C and selectable accelerometer and gyroscope full-scale ranges ([H6], [H7]). The GY-521 is a generic breakout family, so its regulator, pull-ups, layout, filtering, and mechanical mounting are not treated as verified from the IC documentation.

The IMU does not directly measure posture category, tremor, restlessness, gait impairment, intoxication, withdrawal, or alcohol.

### 9.2 Raw output

The expected raw output is digital axis data and status/register information over I²C. The raw axes depend on sensor orientation, calibration, full-scale configuration, sample settings, digital filtering, board vibration, and mounting. The GY-521 board's exact configuration and accessible features require physical verification.

### 9.3 Candidate derived features

Candidate features include:

- Acceleration magnitude, vector components, variance, RMS, energy, and jerk.
- Angular velocity, rotation energy, stillness, and movement transitions.
- Orientation/posture proxies relative to gravity during stationary or slowly moving periods.
- Step, gait-cycle, sway, and task-specific movement features.
- Tremor frequency and band-limited energy, including bands used in withdrawal-tremor studies.
- Activity/restlessness proxies, provided “restlessness” is operationally defined and validated.
- Motion-quality flags for ECG, PPG, EDA, and temperature interpretation.

Posture and activity classification are derived algorithms whose validity depends strongly on placement and task. A generic acceleration count is not automatically a clinically meaningful behavioral label.

### 9.4 Physiological / behavioral meaning

The IMU is primarily a motor and context channel. It can provide information about:

- motor oscillation and tremor;
- gait, sway, and movement stability under a defined task;
- activity versus stillness;
- motion that may contaminate physiological sensors;
- body orientation proxies under suitable conditions.

It is not a direct autonomic measurement. A tremor-like signal has many possible causes, including physiological tremor, essential tremor, anxiety, caffeine, medication, fatigue, neurologic conditions, and voluntary movement.

### 9.5 AUD-related evidence

Withdrawal-tremor quantification is the strongest AUD-specific IMU role identified in the evidence map. A 2017 study found a logarithmic relationship between clinician-rated withdrawal tremor/CIWA-related assessment and accelerometer tremor energy in approximately the 5–15 Hz range, with a reported RMSE of 0.91 relative to physician ratings ([A6]). Earlier work also reported a relationship between tremor energy in an approximately 4.4–10 Hz band and expert-rated withdrawal tremor ([A7]). These results concern a tremor component and do not diagnose the full withdrawal syndrome.

The 24-hour withdrawal study found tremor and locomotor activity elevated in withdrawal subjects, but it was small ([A5]). Generic activity or restlessness remains indirect and nonspecific.

Controlled intoxication/gait evidence is exploratory. A 17-participant study using a lumbar smartphone accelerometer during a standardized 20-step walking task reported subject-specific classification of BrAC above 0.08% with mean accuracy of 92.5%, but the task, placement, small sample, and person-specific modeling do not generalize automatically to passive GY-521 data ([A14]). The 2026 multimodal pilot also found changes in aggregate accelerometry, but it used a different wrist device and controlled setting ([A13]).

### 9.6 Evidence strength

| Claim | Strength | Reason and boundary |
|---|---|---|
| MPU-6050 measures 3-axis acceleration and 3-axis angular velocity | **A** | Established IC function; exact GY-521 electrical implementation still needs verification ([H6], [H7]). |
| Accelerometer features can quantify a tremor component associated with alcohol withdrawal | **B** | Multiple studies relate tremor energy to clinician ratings, but placement/protocol and syndrome limits remain ([A6], [A7]). |
| Gait/movement features can classify alcohol intoxication in controlled tasks | **C** | Small, task-specific, often person-specific evidence ([A13], [A14]). |
| Generic activity/restlessness features identify withdrawal or relapse | **D to E** | Contextually plausible but nonspecific and insufficiently operationalized. |
| IMU alone diagnoses AUD, intoxication, or withdrawal | **E** | Unsupported for the exact individual-level claim. |

### 9.7 Confounders and limitations

- Body location, sensor orientation, loose mounting, attachment resonance, and board vibration.
- Voluntary movement, exercise, transport vibration, table/phone vibration, and task differences.
- Physiological or essential tremor, anxiety, caffeine, nicotine, medication, fatigue, age, and neurologic/musculoskeletal conditions.
- Sampling rate, anti-alias filtering, full-scale selection, gyro bias/drift, calibration, and saturation.
- Gait and sway dependence on footwear, surface, instructions, fatigue, pain, and environment.

Tremor frequency bands in the cited studies should not be copied as a final implementation parameter. Sampling, placement, and analysis requirements remain open design questions.

### 9.8 Possible multimodal role

Possible roles are:

- **Movement/activity information:** activity, stillness, posture/orientation proxies, and task context.
- **Tremor/restlessness information:** exploratory motor features; tremor component has moderate withdrawal evidence.
- **Motion-artifact identification:** especially valuable for PPG, and useful for ECG and EDA quality grading.
- **Contextual signal:** distinguish rest from activity before interpreting HR, EDA, temperature, or PPG amplitude.
- **Cross-sensor validation:** flag disagreement patterns that are more consistent with optical or electrode motion artifact.
- **Multimodal fusion input:** add a motor dimension that differs from autonomic channels.
- **Exploratory AUD-related marker:** tremor and task-specific gait hypotheses only.

The strongest general role is measurement-quality and activity context. The strongest AUD-specific role is a research feature for the tremor component of withdrawal, not complete withdrawal classification.

### 9.9 Claims we should NOT currently make

- “The IMU diagnoses alcohol withdrawal.”
- “IMU tremor means alcohol withdrawal.”
- “Restlessness from one IMU is a validated withdrawal or craving measure.”
- “Gait impairment proves intoxication.”
- “The GY-521 has the exact sensitivity, filtering, regulator, or mounting behavior of every MPU-6050 datasheet setup.”
- “The IMU guarantees that ECG, PPG, or EDA artifacts can be removed.”
- “A motion pattern identifies relapse or alcohol consumption.”

### 9.10 Open questions

1. What placement and mounting can capture the intended tremor component while also serving as an artifact reference?
2. Can one placement support both withdrawal-tremor research and PPG/ECG/EDA quality assessment?
3. What sample-rate, filtering, and calibration requirements apply to the target tremor bands?
4. How will “restlessness” be defined and validated against an independent assessment?
5. Can free-living movement features add value beyond ordinary activity labels and self-report?
6. How much do GY-521 board variation, mounting resonance, and device looseness affect features?
7. Does motion information improve AUD-related models after physical activity, caffeine, medication, anxiety, and fatigue are controlled?

## 10. ESP32 System Role

ESP32 is not a physiological sensing modality. The ESP-WROOM-32-family documentation supports a general-purpose MCU module with GPIO, I²C, SPI, UART, ADC, Wi-Fi, and Bluetooth capabilities ([H8]). “ESP32 DevKit V1 30-pin” remains a generic carrier-board identity; exact pin exposure, USB-UART device, regulator, ADC routing, and clone-specific behavior are not locked by that name.

### 10.1 Acquisition / controller candidate

Potentially justified roles include receiving sensor data, controlling acquisition tasks, applying provisional preprocessing, managing buffers, and attaching quality metadata. The features belong conceptually to their source modality: an RR interval computed in software remains an ECG-derived feature, not an ESP32 physiological measurement.

### 10.2 Sensor-interface platform candidate

The ESP32 may provide digital interfaces for the I²C sensors and an ADC path for the AD8232 analog output, subject to verification of the exact board, input conditioning, electrical levels, and acquisition behavior. This document does not define exact pins, wiring, sampling rates, or final acquisition architecture.

### 10.3 Scheduling and coordination candidate

The controller may coordinate polling, interrupts, buffering, and acquisition windows. Scheduling quality can affect whether ECG, PPG, EDA, temperature, and IMU observations can be compared in a common time context.

### 10.4 Timestamping candidate

Common timestamps, sequence numbers, and explicit missing-data indicators are potentially important for ECG↔PPG timing, IMU artifact flags, and longitudinal trend analysis. Timestamp accuracy is a system-integrity requirement, not an AUD-specific physiological capability.

### 10.5 Buffering and transport candidate

The controller may buffer raw samples or features and transport them through an available communication channel. The final communication protocol, storage policy, packet format, and loss behavior remain unresolved.

### 10.6 System-level limitations

Potential data-integrity limitations include ADC nonlinearity/noise, timestamp jitter, asynchronous sensor clocks, task scheduling, FIFO overrun, packet loss, wireless interference, power-supply coupling, and carrier-board variation. These can invalidate cross-modal timing or quality claims even when individual sensors are functioning.

### 10.7 Claims we should NOT currently make

- “The ESP32 senses AUD physiology.”
- “The ESP32 validates the clinical meaning of a sensor output.”
- “The ESP32 guarantees synchronization, data integrity, or wireless reliability” without measurement.
- “The ESP32 DevKit V1 has one universal 30-pin implementation.”
- “A controller-level algorithm makes nonspecific sensors alcohol-specific.”

### 10.8 Open questions

1. Which exact 30-pin carrier-board revision is present?
2. What ADC behavior, noise, input range, and effective timing apply to the ECG path?
3. How will a common timestamp domain and sensor-clock alignment be established?
4. What data are retained when buffers overflow or transport is interrupted?
5. Can raw data and quality metadata be retained for later re-analysis?
6. Does acquisition coexist with wireless transport without unacceptable timing, power, or RF effects?

## 11. Cross-Modal Relationships

Cross-modal claims are classified below as **ESTABLISHED**, **PLAUSIBLE**, **EXPLORATORY**, or **NOT CURRENTLY JUSTIFIED**. “Established” refers to the measurement relationship or quality rationale, not to an AUD conclusion.

### 11.1 ECG ↔ PPG

**ESTABLISHED**

- Under adequate signal quality, ECG electrical beats and peripheral PPG pulses should correspond in sequence, with the PPG fiducial occurring after the ECG electrical event.
- Synchronized streams can support pulse/beat agreement checks and expose missed or extra optical beats.
- ECG RR timing and PPG inter-pulse timing are related but not interchangeable ([P4], [P5]).

**PLAUSIBLE**

- ECG and PPG disagreement can provide a signal-quality or peripheral-perfusion flag.
- Comparing ECG-derived HR and PPG-derived pulse rate can increase confidence in a rate change when both channels agree under low motion.
- A stable difference between ECG and PPG fiducials may support a pulse-arrival-related feature if sampling, clocks, placement, and fiducial definitions are adequate.

**EXPLORATORY**

- Pulse arrival time (PAT) as an exploratory cardiac–vascular coupling feature.
- PAT or PPG morphology as a possible autonomic/alcohol-related research feature.
- Whether PRV-vs-HRV divergence provides useful peripheral vascular context in AUD recovery.

The timing relation from ECG R wave to a distal PPG fiducial is **PAT**, not pure PTT, because it contains pre-ejection/electromechanical delay as well as vascular propagation time ([P7]).

**NOT CURRENTLY JUSTIFIED**

- Calling ECG + one distal PPG channel “pure PTT.”
- Inferring clinically meaningful blood pressure from PAT/PTT without a validated model and calibration.
- Treating ECG and PPG as redundant or identical waveforms.
- Treating agreement between two nonspecific signals as proof of alcohol use, craving, withdrawal, or relapse.

### 11.2 IMU ↔ ECG/PPG/EDA

**ESTABLISHED**

- Motion is a major source of wearable PPG artifact; simultaneous accelerometer/gyroscope data are a recognized reference for motion-artifact detection or suppression ([P11], [P12]).
- Movement and muscle activity can degrade ambulatory ECG and EDA quality; IMU data can mark high-risk periods or activity context ([P10]).

**PLAUSIBLE**

- High-motion windows can be rejected, down-weighted, or assigned lower confidence for physiological features.
- IMU can distinguish rest from gross activity sufficiently to change the interpretation of HR, EDA, temperature, and PPG amplitude.
- A PPG rate jump with high IMU activity but stable ECG rate is more compatible with optical artifact than with a confirmed cardiac rate change.
- A concordant ECG/PPG rate change with low IMU activity is more likely physiological than an isolated optical change, while remaining nonspecific.

**EXPLORATORY**

- Whether model residuals after motion adjustment carry useful AUD-related information.
- Whether a single placement can capture withdrawal tremor and provide useful artifact reference signals simultaneously.
- Whether movement quality features improve individualized recovery monitoring.

**NOT CURRENTLY JUSTIFIED**

- Claiming that the IMU automatically removes all motion artifact.
- Treating every high-motion interval as invalid or every low-motion interval as valid without validation.
- Treating IMU–physiology concordance as alcohol-specific.

### 11.3 EDA ↔ cardiac signals

**ESTABLISHED**

- EDA and ECG/PPG measure different aspects of physiology: sympathetic sudomotor skin response versus cardiac electrical timing/peripheral pulse.
- Combining complementary channels can characterize arousal and cardiovascular context more richly than either channel alone in a research setting ([P8], [P9], [A11]).

**PLAUSIBLE**

- EDA elevation with a cardiac rate/variability change during low motion is compatible with a broader arousal event.
- Concordance and non-concordance may both be informative because sudomotor and cardiac responses need not change together.
- A cardiac change with no EDA change, or an EDA change with no cardiac change, should be retained as a possible physiological pattern rather than forcibly reconciled.

**EXPLORATORY**

- Whether joint features improve cue-reactivity, stress, intoxication, withdrawal, or recovery research outcomes after confounders and self-report are included ([A8], [A11], [A13]).
- Whether individualized EDA + HRV patterns predict later outcomes; current naturalistic evidence is heterogeneous ([A12]).

**NOT CURRENTLY JUSTIFIED**

- Calling simultaneous HR and EDA elevation a craving, withdrawal, or relapse event without independent context/ground truth.
- Treating EDA as a direct sympathetic measurement that resolves all autonomic ambiguity.

### 11.4 Temperature ↔ EDA/PPG

**ESTABLISHED**

- Temperature, sweating, peripheral perfusion, vasoconstriction, and contact conditions can affect EDA and PPG interpretation.
- A local temperature sensor can provide an additional context variable for peripheral signal changes, but the exact TMP117 breakout's skin coupling is not verified ([H5], [H13]).

**PLAUSIBLE**

- Low peripheral temperature with reduced PPG amplitude can make vasoconstriction or reduced perfusion a competing explanation.
- EDA and temperature changes occurring during heat or exercise should be interpreted differently from an EDA change under stable thermal and motion conditions.
- Temperature may help flag contact or environmental transitions.

**EXPLORATORY**

- Whether joint temperature + EDA/PPG trajectories add independent AUD-recovery or intoxication information after environment and activity are modeled ([A13], [A15], [A16]).

**NOT CURRENTLY JUSTIFIED**

- Calling temperature + EDA a direct alcohol or stress detector.
- Treating a TMP117 reading as core temperature or as a direct autonomic measurement.

### 11.5 Other evidence-backed combinations

- **ECG + IMU:** established as a quality/context rationale; exploratory as a way to preserve only motion-qualified HRV windows.
- **PPG + EDA + temperature + IMU:** exploratory multimodal research combination. The 2026 controlled pilot shows feasibility of related channels on a different research device, not validation of the present breakouts ([A13]).
- **ECG + PPG + EDA + temperature + IMU:** exploratory multimodal fusion hypothesis. Multiple channels may separate some competing explanations and provide complementary dimensions, but fusion cannot create biochemical specificity from nonspecific signals.
- **ESP32 timestamps + all sensors:** technically necessary candidate infrastructure for valid temporal comparison, with no physiological or AUD meaning of its own.

No current relationship justifies the claim that the complete candidate set detects ethanol. Direct breath, blood, or validated transdermal alcohol measures are conceptually different because they measure alcohol or a closer chemical proxy; none of the current candidates does so.

## 12. Confounder / Artifact Matrix

Legend: **High** = commonly important; **Medium** = relevant depending on setup; **Context** = primarily changes interpretation rather than corrupting the raw signal; **Low** = possible but usually secondary; **—** = not a primary issue for that signal. Applicability depends on placement and acquisition conditions.

| Confounder | ECG | PPG | EDA | Temperature | IMU | Interpretation consequence |
|---|---|---|---|---|---|---|
| Gross body motion | High artifact | High artifact | Medium/High artifact | Medium context | Directly measured / task context | Rate, HRV, EDA, and temperature changes may reflect activity or contact disturbance. |
| Local limb motion | High artifact | High artifact | High artifact | Medium contact effect | Directly measured | Affected channel may lose confidence even when the rest of the body is still. |
| Exercise / physical effort | High context/artifact | High context/artifact | High context/artifact | High context | Directly measured | HR, EDA, perfusion, temperature, and movement are jointly altered; alcohol-specific interpretation weakens. |
| Posture / posture transition | Medium | High | Medium | Medium | Orientation proxy | Changes venous return, pulse amplitude, HRV, perfusion, and local thermal state. |
| Respiration | Medium/High HRV influence | Medium waveform influence | Medium artifact/context | Low/Medium | Low unless movement coupling | HRV and phasic signals may change with breathing; frequency-domain interpretation requires control/annotation. |
| Emotional stress | Context | Context | High context | Low/Medium | Low/Context | Arousal pattern is nonspecific and cannot be attributed to alcohol without context. |
| Anxiety | Context | Context | High context | Low/Medium | Medium movement | Can mimic autonomic arousal or restlessness and alter cardiac variability. |
| Ambient temperature | Low/Medium | Medium/High | High | High | Low | Changes vasoconstriction, sweat, EDA baseline, PPG amplitude, and sensor temperature. |
| Local skin temperature | Low/Medium | High | Medium/High | Directly relevant | Low | Peripheral perfusion and sweat state can change PPG and EDA without an AUD cause. |
| Sweating / sweat saturation | Low electrode effect | Medium contact/optical effect | High | Medium evaporation | Low | EDA may drift/saturate; electrode and optical contact may change. |
| Hydration | Medium context | Medium perfusion | Medium skin conductance | Medium thermoregulation | Low/Context | Changes vascular and skin responses; confounds person-to-person comparisons. |
| Caffeine / nicotine | Medium/High context | Medium | Medium | Low/Medium | Medium tremor/arousal | May produce tachycardia, arousal, vasoconstriction, or tremor-like activity. |
| Medication / other substances | High context | Medium | Medium | Medium | High for tremor/motion | Drug effects may mimic or mask AUD-related physiological patterns. |
| Circadian effects | Medium | Medium | Medium | High | Medium activity/sleep | Baseline and trend comparisons require time-of-day context. |
| Sleep / sleep loss | Medium | Medium | Medium | Medium | Medium | Alters HRV, arousal, temperature, movement, and symptom presentation. |
| Electrode/contact quality | High | — | High | Medium | — | Can create false ECG/EDA changes and invalidate derived features. |
| Sensor placement | High | High | High | High | High | Determines lead vector, optical perfusion, sweat relevance, thermal coupling, and tremor observability. |
| Skin properties / pigmentation / lotions | Medium electrode | High optical | High electrodermal | Medium thermal contact | — | Changes calibration, optical quality, conductance, and contact; avoid universal thresholds. |
| Peripheral perfusion | Low/Medium | High | Low/Medium | High | — | PPG amplitude/timing and local temperature may change independently of cardiac electrical timing. |
| Pressure / contact changes | Medium electrode | High optical | High electrode | High thermal | Medium mounting | Can mimic physiological change and alter signal quality. |
| Optical interference / ambient light | — | High | — | — | — | PPG may clip, drift, or generate false pulse features. |
| Sensor saturation / clipping | Medium | High | High | Low | Medium | Derived features become unreliable; quality flags are required. |
| Electrical noise / mains / RF coupling | High | Low/Medium | Medium | Low | Low | ECG/EDA raw signals may be contaminated; ESP32 transport activity may contribute system noise. |
| Board self-heating / neighboring electronics | Low | Low/Medium | Low | High | Low | TMP117 may measure board/system thermal state instead of skin. |
| Sampling / clock mismatch | High for HRV timing | High for PAT/PRV | Medium | Low | Medium | Cross-modal timing and derived features can be biased even when waveforms look plausible. |

The matrix consolidates the confounders in the evidence map and measurement literature, including HRV methodology ([P1], [P2]), PPG limitations ([P4], [P11], [P12], [H14]), EDA artifact guidance ([P8], [P10]), and TMP117 thermal-coupling guidance ([H5], [H13]). It is not an exhaustive clinical differential diagnosis.

## 13. Provisional Role Classification

This is a provisional evidence classification for future design discussion. It is **not a sensor-inclusion decision** and does not mean that a component should be kept, removed, or used clinically.

| Modality | Provisional role category | Reason | Confidence |
|---|---|---|---|
| ECG / AD8232 | **Evidentially central candidate**; primary physiological measurement; derived feature source; cardiovascular/autonomic information | ECG provides the clearest route to standardized beat timing and HRV, with the strongest AUD-group evidence in this candidate set. Exact breakout behavior and individual AUD inference remain unresolved. | **Moderate-high** for measurement/research role; **moderate** for AUD relevance; **low** for clinical inference. |
| PPG / MAX30101 | **Potentially complementary candidate**; peripheral cardiovascular information; cross-sensor validation; fusion input | Adds peripheral pulse/perfusion information and can cross-check ECG, but PRV, morphology, PAT, and SpO₂ require separate validation and exact board/placement knowledge. | **Moderate** for pulse/context role; **low-moderate** for AUD-specific role. |
| EDA / legacy tinyGSR | **Potentially complementary candidate**; sympathetic-arousal information; longitudinal/fusion input | Provides a physiologically different sudomotor channel with cue/stress research relevance, but the legacy board's relative output and nonspecificity limit comparability and interpretation. | **Moderate** for EDA physiology/context; **low-moderate** for AUD-specific role. |
| Peripheral temperature / TMP117 | **Primarily contextual/artifact-management candidate**; peripheral physiological information; exploratory trend input | Useful for thermal/perfusion context and interpretation of PPG/EDA, but local die temperature and poor alcohol specificity limit it as a primary AUD-related feature. | **Moderate** for context role; **low** for standalone AUD role. |
| IMU / MPU6050 | **Primarily contextual/artifact-management candidate** and **potentially complementary candidate**; movement/activity; tremor/restlessness; quality flags | Strong rationale for motion context and PPG artifact handling; moderate evidence for withdrawal-tremor quantification; generic movement remains nonspecific. | **High** for context/artifact role; **moderate** for tremor research; **low** for standalone AUD inference. |
| ESP32 | **Acquisition/controller platform candidate**, outside physiological sensor-inclusion classification | Can coordinate acquisition, interfaces, timestamps, buffering, and transport; it has no intrinsic AUD meaning. Exact carrier behavior and synchronization quality remain open. | **Moderate-high** for general platform role; **low** for exact board-level claims until verified. |

The labels identify what the evidence currently supports each channel doing. They do not settle whether any candidate is necessary, redundant, practical, or suitable for a later physical architecture.

## 14. Evidence Gaps

### Hardware identity and implementation

- Exact tinyGSR 11/22 schematic, firmware, ADC path, trimmer setting, and revision match.
- Exact CJMCU VS82 schematic, passive component values, gain, filter cutoffs, electrode/RLD topology, and output range.
- Exact SmartElex MAX30101 schematic, optical window, LED settings, power conversion, and software.
- Exact SmartElex TMP117 board thermal path, exposed sensing surface, response time, offset, and neighboring heat sources.
- Exact GY-521 regulator, pull-ups, filtering, board orientation, mounting, and genuine MPU-6050 identity.
- Exact ESP32 30-pin carrier manufacturer/revision, ADC routing, regulator, USB interface, and RF/power behavior.

### Measurement validity

- Whether the ECG module supports the proposed R-peak and HRV features under the intended acquisition conditions.
- Whether legacy tinyGSR relative features are stable within a unit across days, electrodes, and sessions.
- Whether PPG pulse, PRV, morphology, or SpO₂ is the actual research objective; each requires different processing and validation.
- Whether the TMP117 reading tracks skin rather than board/die/environmental temperature in the intended contact arrangement.
- Whether the IMU placement can capture the target tremor component and useful artifact context at the same time.

### Synchronization and data integrity

- Common time base and measurable synchronization error across ECG, PPG, EDA, temperature, and IMU.
- Timing accuracy required for ECG↔PPG PAT and beat/pulse agreement.
- Sampling, buffering, packet-loss, task-scheduling, FIFO, and timestamp-jitter behavior.
- Whether raw signals, quality metadata, and missing-data markers will be retained.

### Context and confounders

- Body placement, co-location, electrode/optical/thermal contact, and whether one placement can serve multiple roles.
- Respiration, posture, exercise, ambient temperature, airflow, sleep, circadian state, hydration, illness, medication, nicotine, caffeine, and pain annotation.
- A policy for rejecting, down-weighting, or modeling high-motion and poor-contact windows.
- A validated definition of “restlessness” separate from ordinary activity and anxiety.

### Ground truth and study design

- Which future labels will be used: clinician assessment, CIWA-Ar item/total, BrAC/BAC, craving EMA, observed alcohol use, treatment event, lapse, or another reference.
- Whether outcomes are group-level associations, within-person trends, cue reactivity, withdrawal symptom tracking, intoxication classification, or something else.
- Whether a model trained on another device/population transfers to these exact breakouts.
- Whether personal baselines outperform population thresholds and how baseline drift is handled.
- Whether any multimodal feature adds value beyond self-report, activity, clinical observation, and simpler sensor subsets.

### Clinical boundary

- No current candidate has been established here as a certified clinical monitor for the intended use.
- No current candidate directly measures ethanol.
- No current evidence justifies using these channels alone for diagnosis, treatment decisions, emergency triage, or relapse intervention.

## 15. Claims to Avoid at This Stage

The following claims are unsupported, overly strong, or ambiguous for the current evidence and hardware state:

1. “The prototype diagnoses AUD.”
2. “EDA detects craving.”
3. “EDA detects stress” without qualifying it as nonspecific sympathetic sudomotor/arousal information.
4. “HRV detects relapse” or reliably predicts imminent relapse in an individual.
5. “HRV measures sympathetic/parasympathetic balance” by treating LF/HF as a simple ratio.
6. “AD8232 directly measures HRV.”
7. “MAX30101 directly measures heart rate or SpO₂” without an algorithm and validation.
8. “PPG PRV is the same as ECG HRV.”
9. “ECG + PPG gives PTT” without the PAT/PEP distinction.
10. “ECG + PPG measures blood pressure” without validated calibration and model testing.
11. “TMP117 measures core body temperature.”
12. “Temperature detects alcohol use or withdrawal.”
13. “The legacy tinyGSR reports absolute µS” without exact-board calibration evidence.
14. “IMU tremor means alcohol withdrawal.”
15. “Gait impairment means intoxication.”
16. “Motion data automatically correct all ECG/PPG/EDA artifacts.”
17. “The candidate set detects recent alcohol consumption.” None of the candidates directly senses ethanol.
18. “Multimodal fusion makes these signals alcohol-specific.” Fusion may reduce ambiguity; it does not create biochemical specificity.
19. “A population-level correlation is an individual clinical decision rule.”
20. “An IC datasheet specification proves the same capability, calibration, or clinical validity for the named breakout board.”

## 16. Questions Carried Forward to Prototype Design

These are questions, not decisions:

- Which modalities actually need continuous acquisition for the next research question?
- Which modality is necessary primarily for artifact rejection or context?
- Which signals require co-location, and which can be separated without invalidating the intended feature?
- What synchronization accuracy would ECG↔PPG beat comparison or PAT require?
- Which physiological features are sufficiently evidence-backed to justify designing around them?
- Which sensor roles are complementary versus redundant for the selected ground truth?
- Which roles depend strongly on eventual body placement?
- What raw data, quality flags, calibration data, and context annotations must be preserved?
- How will the legacy tinyGSR's relative output be normalized, if at all?
- How will contact quality, motion, environmental temperature, exercise, respiration, sleep, medication, nicotine, caffeine, hydration, and illness be captured?
- What reference methods will validate ECG HR/HRV, PPG pulse/PRV, EDA features, local temperature, tremor, and system timestamps?
- What clinical or research labels will be used, and who will provide them?
- What counts as a valid low-motion window for each physiological feature?
- Does each added modality improve a predefined research endpoint after accounting for burden and missingness?
- How will findings be kept at the level supported by the evidence rather than converted into diagnostic or predictive claims?

This document intentionally does not answer wearable-versus-non-wearable, wrist-versus-chest placement, enclosure, electrode placement, battery, wiring, pin assignment, sampling-rate, communication-protocol, acquisition-architecture, or form-factor questions.

## 17. Source / Evidence Ledger

The ledger records sources supporting important claims. **EM** means the source was already included in the existing evidence map. **SV** means the source was newly checked or newly added as supplementary verification for this document. A source's presence does not elevate a claim beyond the evidence strength assigned in the text.

| ID | Claim supported | Source | Source type | DOI or stable URL | Strength | Notes / provenance |
|---|---|---|---|---|---|---|
| H1 | AD8232 is a single-lead ECG/biopotential conditioning front end with filtering, gain, lead-off, and RLD-related features | Analog Devices, **AD8232 product page** | Manufacturer documentation | [analog.com/en/products/ad8232.html](https://www.analog.com/en/products/ad8232.html) | A for IC capability | EM; IC capability, not VS82 board implementation. |
| H2 | AD8232 output is an amplified/filtered biopotential suitable for host ADC acquisition; filter settings are circuit-dependent | Analog Devices, **AD8232 Rev. D datasheet** | Manufacturer datasheet | [AD8232.pdf](https://www.analog.com/media/en/technical-documentation/data-sheets/AD8232.pdf) | A for IC capability | **SV; newly added.** Does not establish exact CJMCU VS82 passives. |
| H3 | MAX30101 contains red/IR/green LEDs, photodetector, optical electronics, ambient-light rejection, FIFO, and current ADC | Analog Devices, **MAX30101 product page and datasheet** | Manufacturer documentation/datasheet | [product page](https://www.analog.com/en/products/max30101.html); [datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX30101.pdf) | A for IC capability | EM; raw optical capability is not a validated clinical algorithm. |
| H4 | TMP117 is a local digital temperature sensor with high specified IC accuracy | Texas Instruments, **TMP117 product page** | Manufacturer documentation | [ti.com/product/TMP117](https://www.ti.com/product/TMP117) | A for IC capability | EM; local/die measurement boundary retained. |
| H5 | TMP117 resolution/accuracy and thermal/self-heating specifications apply to the IC, not automatically to skin | Texas Instruments, **TMP117 Rev. D datasheet** | Manufacturer datasheet | [tmp117.pdf](https://www.ti.com/lit/ds/symlink/tmp117.pdf) | A for IC capability; B/C for skin estimate | **SV; newly added.** Breakout thermal coupling remains open. |
| H6 | MPU-6050 is a 6-axis I²C inertial sensor with selectable accelerometer/gyro ranges; obsolete status | TDK Product Center, **MPU-6050** | Manufacturer documentation | [TDK product center](https://product.tdk.com/en/search/sensor/mortion-inertial/imu/info?part_no=MPU-6050) | A for IC capability | EM; GY-521 board details remain board-specific. |
| H7 | MPU-6050 product specification and register map describe digital axis output and ranges | InvenSense/TDK, **MPU-6000/MPU-6050 Product Specification** | Manufacturer datasheet | [MPU-6000-Datasheet.pdf](https://invensense.tdk.com/wp-content/uploads/2015/02/MPU-6000-Datasheet.pdf) | A for IC capability | **SV; newly added.** Not a GY-521 schematic. |
| H8 | ESP-WROOM-32-family module provides general MCU, GPIO, digital interfaces, ADC, Wi-Fi, and Bluetooth | Espressif, **ESP32-WROOM-32 datasheet** | Manufacturer datasheet | [Espressif datasheet](https://documentation.espressif.com/esp32-wroom-32_datasheet_en.html) | A for platform capability | EM; generic DevKit V1 carrier remains unverified. |
| H9 | Current tinyGSR v3 page distinguishes v3 absolute µS from original relative trimmer-set board | ProtoCentral, **tinyGSR product/history page** | Manufacturer/product documentation | [protocentral.com tinyGSR](https://protocentral.com/product/protocentral-tinygsr-gsr-eda-digital-output-sensor-board-qwiic-stemma-qt/) | B for exact legacy-board history | EM; current page is not an independent match to PCB 11/22. |
| H10 | Legacy tinyGSR tutorial documents original implementation/baseline adjustment | ProtoCentral, **legacy tinyGSR tutorial** | Manufacturer-associated tutorial | [Hackster legacy tutorial](https://www.hackster.io/protocentral/measuring-emotions-with-gsr-using-tinygsr-arduino-1f038e) | C for exact 11/22 board | EM; used cautiously because exact revision is unresolved. |
| H11 | Reseller listing identifies a SmartElex MAX30101 photodetector breakout | The Engineer Store, **SmartElex MAX30101 listing** | Reseller listing | [theengineerstore.in MAX30101](https://www.theengineerstore.in/products/smartelex-photodetector-breakout-max30101) | D for board identity only | EM; not primary schematic or clinical validation. |
| H12 | Reseller listing identifies a SmartElex TMP117 breakout | The Engineer Store, **SmartElex TMP117 listing** | Reseller listing | [theengineerstore.in TMP117](https://www.theengineerstore.in/collections/sensors/products/smartelex-high-precision-temperature-sensor-tmp117) | D for board identity only | EM; not primary schematic or thermal validation. |
| H13 | Wearable temperature interpretation depends on thermal contact and isolation from heat sources | Texas Instruments, **wearable temperature design guidance** | Manufacturer application guidance | [TI SSZT563](https://www.ti.com/document-viewer/lit/html/SSZT563) | B for skin-coupling rationale | EM. |
| H14 | Pulse-oximetry readings are estimates affected by circulation, skin temperature, pigmentation, and other factors | U.S. FDA, **Pulse Oximeter Basics** | Government/medical-device guidance | [FDA Pulse Oximeter Basics](https://www.fda.gov/consumers/consumer-updates/pulse-oximeter-basics) | A for stated limitations | EM. |
| H15 | Clinical alcohol-withdrawal criteria include autonomic hyperactivity, sweating, increased pulse, and hand tremor; clinical assessment is broader than a sensor | American Society of Addiction Medicine, **Clinical Practice Guideline on Alcohol Withdrawal Management** | Clinical practice guideline | [ASAM guideline PDF](https://www.asam.org/docs/default-source/quality-science/the_asam_clinical_practice_guideline_on_alcohol-1.pdf?sfvrsn=ba255c2_2%2C13%2C17%2C38) | A for guideline content; not sensor validation | **SV; newly added.** Used to anchor, not replace, clinical assessment. |
| P1 | HRV definitions, metrics, and methodological requirements | Task Force of ESC/NASPE, 1996 | Consensus/standards paper | [doi:10.1161/01.CIR.93.5.1043](https://doi.org/10.1161/01.CIR.93.5.1043) | A | EM. |
| P2 | HRV interpretation requires attention to respiration, posture, activity, duration, and context | Laborde, Mosley & Thayer, 2017 | Peer-reviewed methodological guidance | [doi:10.3389/fpsyg.2017.00213](https://doi.org/10.3389/fpsyg.2017.00213) | A/B | EM. |
| P3 | LF/HF should not be treated as a simple sympathovagal-balance measure | Billman, 2013 | Peer-reviewed physiological review | [doi:10.3389/fphys.2013.00026](https://doi.org/10.3389/fphys.2013.00026) | A | EM. |
| P4 | PPG measures peripheral blood-volume-related optical change and yields pulse timing | Allen, 2007 | Peer-reviewed physiological review | [doi:10.1088/0967-3334/28/3/R01](https://doi.org/10.1088/0967-3334/28/3/R01) | A | EM. |
| P5 | PRV is related to but not universally interchangeable with ECG HRV | Mejía-Mejía et al., 2020 | Peer-reviewed review | [doi:10.1088/1361-6579/ab998c](https://doi.org/10.1088/1361-6579/ab998c) | A/B | EM. |
| P6 | PPG morphology is associated with vascular/hemodynamic factors but is not a direct measurement of them | Charlton et al., 2022 | Peer-reviewed physiological/instrumentation paper | [doi:10.1152/ajpheart.00392.2021](https://doi.org/10.1152/ajpheart.00392.2021) | B | EM. |
| P7 | ECG-to-PPG timing is PAT and contains PEP plus vascular propagation components | Mohammadpoor Faskhodi et al., 2024 | Peer-reviewed instrumentation paper | [doi:10.1371/journal.pone.0298354](https://doi.org/10.1371/journal.pone.0298354) | A/B for terminology; C for AUD use | EM. |
| P8 | EDA terminology, tonic/phasic decomposition, and reporting practice | Boucsein et al., 2012 | Peer-reviewed consensus/recommendations | [doi:10.1111/j.1469-8986.2012.01384.x](https://doi.org/10.1111/j.1469-8986.2012.01384.x) | A | EM. |
| P9 | EDA is linked to sweat-gland activity and sympathetic sudomotor physiology | Posada-Quintero & Chon, 2020 | Peer-reviewed review | [doi:10.3390/s20020479](https://doi.org/10.3390/s20020479) | A | EM. |
| P10 | Ambulatory EDA artifacts include motion, respiration, speech, temperature, humidity, and system/electrode effects | Bari, Aldosky & Martinsen, 2026 | Peer-reviewed artifact review | [doi:10.1088/1361-6579/ae7bae](https://doi.org/10.1088/1361-6579/ae7bae) | A/B for limitations | EM. |
| P11 | Accelerometry/gyroscopy can be used as a motion reference for wearable PPG artifact handling | Wearable PPG cardiovascular monitoring review | Peer-reviewed review | [PMC7612541](https://pmc.ncbi.nlm.nih.gov/articles/PMC7612541/) | A/B for quality rationale | EM. |
| P12 | Motion artifact is a major challenge for wearable PPG heart-rate tracking | Ismail, Akram & Siddiqi, 2021 | Peer-reviewed review | [link.springer.com article](https://link.springer.com/article/10.1186/s13634-020-00714-2) | A/B | EM. |
| P13 | Selected controlled PRV-vs-HRV comparisons do not justify broad equivalence across free-living contexts | Xu et al., 2026 | Systematic review/meta-analysis | [doi:10.3390/s26165192](https://doi.org/10.3390/s26165192) | B | EM. |
| A1 | Lower HRV in AUD groups with high heterogeneity | Cheng, Huang & Huang, 2019 | Systematic review/meta-analysis | [doi:10.1016/j.drugalcdep.2019.05.030](https://doi.org/10.1016/j.drugalcdep.2019.05.030) | B | EM; group-level association, not diagnosis. |
| A2 | Acute alcohol and AUD research show HRV changes, with heterogeneous craving/relapse associations | Ralevski et al., 2019 | Peer-reviewed review | [doi:10.1016/j.pbb.2018.12.003](https://doi.org/10.1016/j.pbb.2018.12.003) | B/C | EM. |
| A3 | Alcohol-induced autonomic dysfunction literature is broad but heterogeneous | Julian et al., 2020 | Systematic review | [doi:10.1007/s10286-019-00618-8](https://doi.org/10.1007/s10286-019-00618-8) | B | EM. |
| A4 | Classical HRV and sympathetic skin response did not show expected acute-withdrawal autonomic changes in a 40-man cohort | Bär et al., 2006 | Peer-reviewed clinical study; PubMed | [PubMed 16930222](https://pubmed.ncbi.nlm.nih.gov/16930222/); [DOI](https://doi.org/10.1111/j.1530-0277.2006.00191.x) | B for mixed evidence | EM; important counterexample. |
| A5 | Small ambulatory withdrawal study observed sweating, tremor, skin-temperature, and locomotor patterns | White et al., 1994 | Peer-reviewed clinical monitoring study | [doi:10.1007/BF01828833](https://doi.org/10.1007/BF01828833) | C | EM; n=6 withdrawal subjects. |
| A6 | Accelerometer tremor energy related to clinician-rated withdrawal tremor in a 2017 study | Biomedical Signal Processing and Control, 2017 | Peer-reviewed instrumentation/clinical study | [doi:10.1016/j.bspc.2016.11.006](https://doi.org/10.1016/j.bspc.2016.11.006) | B | EM; tremor component, not full syndrome. |
| A7 | Earlier accelerometer tremor study related tremor energy bands to expert ratings | EMBC 2014 | Peer-reviewed conference study | [doi:10.1109/EMBC.2014.6944865](https://doi.org/10.1109/EMBC.2014.6944865) | B/C | EM; protocol and placement matter. |
| A8 | Alcohol-associated VR cues increased nonspecific SCR frequency with weak craving–EDA correlations and no consistent HR/HRV cue effect | Lütt et al., 2026 | Peer-reviewed exploratory clinical study | [doi:10.2196/84156](https://doi.org/10.2196/84156); [JMIR article](https://games.jmir.org/2026/1/e84156) | C | EM; n=61 alcohol-dependent patients; cue paradigm, not passive detection. |
| A9 | Alcohol cue exposure produced heterogeneous subjective/physiological response patterns | Cue-reactivity study, PubMed PMID 10943912 | Peer-reviewed clinical study | [PubMed 10943912](https://pubmed.ncbi.nlm.nih.gov/10943912/) | C | EM. |
| A10 | Alcohol-cue study reported subjective craving with comparatively weak/nonspecific physiological changes | Cue-exposure study, PubMed PMID 16604310 | Peer-reviewed clinical study | [PubMed 16604310](https://pubmed.ncbi.nlm.nih.gov/16604310/) | C | EM; used as a caution against one-to-one mapping. |
| A11 | EDA/HRV data collection was feasible in 11 adults in AUD recovery, with associations to self-reported outcomes | Alinia et al., 2021 | Peer-reviewed development/usability study | [doi:10.2196/27891](https://doi.org/10.2196/27891) | C | EM; small sample and self-report outcomes. |
| A12 | Naturalistic 100-day monitoring found large person-to-person variation and no simple physiology-to-lapse mapping | van Lier et al., 2022 | Peer-reviewed naturalistic study | [PMC9287639](https://pmc.ncbi.nlm.nih.gov/articles/PMC9287639/) | C | EM. |
| A13 | A 28-person controlled pilot found multimodal HR/temperature/EDA/accelerometry changes and up to 0.80 impairment-model accuracy | Kaczor et al., 2026 | Peer-reviewed conference proceedings / open author manuscript | [PubMed 41550855](https://pubmed.ncbi.nlm.nih.gov/41550855/); [PMC12805382](https://pmc.ncbi.nlm.nih.gov/articles/PMC12805382/); [HICSS DOI](https://doi.org/10.24251/HICSS.2026.422) | C | EM; different research device, healthy participants, controlled intoxication task; newly linked DOI in this document. |
| A14 | Standardized walking-task accelerometry showed exploratory, subject-specific BrAC classification | Suffoletto et al., 2020 | Peer-reviewed clinical/engineering study | [PubMed 32800088](https://pubmed.ncbi.nlm.nih.gov/32800088/) | C | EM; n=17, task- and placement-specific. |
| A15 | Experimental alcohol exposure changed skin temperature in an environment-dependent way | Risbo et al., 1981 | Peer-reviewed controlled human study | [doi:10.1111/j.1399-6576.1981.tb01639.x](https://doi.org/10.1111/j.1399-6576.1981.tb01639.x) | B/C | EM. |
| A16 | Literature on alcohol and thermoregulatory response to heat stress is small and poorly characterized | Morris, Ravanelli & Chaseling, 2024 | Systematic scoping review | [doi:10.1186/s12940-024-01113-y](https://doi.org/10.1186/s12940-024-01113-y) | B/C | EM; eight studies, 93 male participants. |

### Sources newly added or newly linked during this task

The following external sources were added as supplementary material or as stronger stable links while preparing this document:

- [AD8232 Rev. D datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/AD8232.pdf) — official Analog Devices IC-level verification.
- [TMP117 Rev. D datasheet](https://www.ti.com/lit/ds/symlink/tmp117.pdf) — official Texas Instruments IC-level and thermal/self-heating details.
- [MPU-6000/MPU-6050 Product Specification](https://invensense.tdk.com/wp-content/uploads/2015/02/MPU-6000-Datasheet.pdf) — official InvenSense/TDK IC-level verification.
- [ASAM Clinical Practice Guideline on Alcohol Withdrawal Management](https://www.asam.org/docs/default-source/quality-science/the_asam_clinical_practice_guideline_on_alcohol-1.pdf?sfvrsn=ba255c2_2%2C13%2C17%2C38) — authoritative clinical boundary for withdrawal signs and assessment.
- [HICSS DOI for Kaczor et al. (2026)](https://doi.org/10.24251/HICSS.2026.422) — stable bibliographic link newly checked for a study already summarized in the evidence map.

## Bottom-line role definition

The current evidence supports the following factual foundation for a later prototype-design stage:

- ECG is the strongest route to standardized cardiac beat timing and HRV, with the strongest AUD-group evidence in the candidate set.
- PPG adds peripheral pulse/perfusion information and can support ECG cross-validation, but PRV is not automatically HRV and SpO₂ is not automatic.
- Legacy tinyGSR can contribute relative tonic/phasic EDA research features if its exact board behavior and repeatability are verified; absolute µS should not be assumed.
- TMP117 is best treated as local temperature and possible skin/perfusion context until thermal coupling is validated.
- MPU-6050 supplies movement, tremor, and artifact/context information; withdrawal-tremor quantification is more directly supported than generic intoxication or restlessness inference.
- ESP32 is an acquisition, coordination, timestamping, buffering, and transport candidate with no physiological or AUD meaning of its own.
- Multimodal fusion may improve context, quality assessment, and research discrimination among competing explanations, but it does not make these nonspecific signals alcohol-specific.

This remains a role-definition artifact, not a final sensor-selection, form-factor, clinical, or patent decision.
