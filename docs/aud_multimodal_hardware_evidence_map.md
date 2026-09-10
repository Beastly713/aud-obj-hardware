# Multimodal Physiological Monitoring for AUD Support / Rehabilitation — Evidence Collection

> **Scope:** Evidence mapping and hardware-capability clarification only. This document does **not** select a final configuration, establish clinical utility for the complete prototype, or imply that any candidate sensor is necessary, sufficient, validated, or clinically justified for Alcohol Use Disorder (AUD) care.
>
> **Scientific rule used throughout:** **measurement → derived feature → physiological association → possible AUD relevance**. A physiological association is not treated as an AUD-specific marker, and an AUD-group association is not treated as an individual-level diagnostic or predictive capability.

## Evidence-strength vocabulary used here

- **Well established** — supported by a mature physiological measurement literature and, for an AUD-specific claim, by systematic review/meta-analysis or multiple consistent clinical studies. This still does **not** imply diagnostic specificity.
- **Moderately supported** — multiple relevant studies or a strong review-level rationale, but heterogeneous populations, protocols, effect sizes, or limited direct replication.
- **Exploratory / emerging** — promising findings from small, pilot, single-cohort, or early multimodal studies; useful for hypothesis generation, not an established individual-level inference.
- **Indirect / speculative** — physiologically plausible or supported in adjacent populations/contexts, but not sufficiently demonstrated for the stated AUD use.
- **Not applicable** — the component is not itself a physiological sensing modality.

---

## Concise comparison table

| Sensor | Direct measurement | Important derived features | Physiological significance | Possible AUD relevance | Evidence strength | Major confounders | Potential multimodal role |
|---|---|---|---|---|---|---|---|
| **ESP32 DevKit V1, ESP-WROOM-32-family** | No AUD-relevant physiological quantity. It acquires digital sensor data and can sample analog voltage from the ECG front end. | Timestamps, synchronized streams, data-quality flags, windowed aggregates only after software processing. | None intrinsically; it is the acquisition/processing/communications controller. | Enables synchronized longitudinal measurement but has no AUD biomarker meaning itself. | **Not applicable** | ADC behavior, clock/timestamp alignment, packet loss, task scheduling, power/RF noise, carrier-board variation. | Time alignment, multimodal fusion, buffering, logging, preprocessing; synchronization quality determines whether cross-sensor timing features are meaningful. |
| **ProtoCentral tinyGSR legacy board (PCB 11/22)** | Electrical response of skin through a GSR/EDA analog front end, digitized by an onboard ADC. For the original/legacy board, ProtoCentral states the output is **relative/trimmer-set**, not recoverable as absolute µS. | Within-unit tonic level/trend, phasic changes, event/SCR-like counts and amplitudes in relative units if signal quality allows. Absolute SCL/SCR amplitudes in µS are **not verified for this legacy board**. | Eccrine sweat-gland activity under sympathetic cholinergic control; an index of sympathetic sudomotor arousal, not a specific “stress” readout. | Cue-evoked arousal, stress monitoring, withdrawal-associated sweating, and multimodal intoxication/recovery research have relevant literature. | **Moderately supported** for general sympathetic arousal; **exploratory-to-moderate** for AUD cue/stress contexts; **weak/insufficient** for craving/relapse detection. | Motion, electrode pressure/contact, skin site, hydration/skin properties, ambient temperature/humidity, physical effort, speech/respiration, medication, nicotine/caffeine, sweat saturation. | Complement HR/HRV with a sympathetic sudomotor channel; distinguish arousal from movement using IMU; temperature helps interpret thermoregulatory effects; useful for multimodal trend/fusion. |
| **CJMCU-8232 / AD8232 single-lead ECG module, PCB VS82** | Conditioned **single-lead cardiac biopotential** as an analog waveform; AD8232 itself amplifies/filters small biopotentials. | R peaks, heart rate, RR/NN intervals, RMSSD, SDNN and other HRV metrics; frequency-domain HRV only under appropriate recording/processing conditions. | Cardiac electrical timing; HR and beat-to-beat variability reflect combined autonomic, respiratory, cardiovascular and contextual influences. | Reduced resting HRV is documented at group level in AUD; acute alcohol often reduces HRV; reactive HRV has been associated with craving/relapse in some studies. Acute withdrawal-specific evidence is mixed. | **Well established** that AUD groups show lower HRV overall; **moderately supported** for chronic autonomic dysregulation; **exploratory/moderate** for craving/relapse; **mixed** for acute withdrawal HRV. | Movement, electrode placement/contact, muscle activity, mains noise, respiration, posture, exercise, age, sleep, medications, nicotine/caffeine, fever, dehydration, ectopy, filtering/sampling. | ECG reference for PPG pulse timing; beat-to-beat cross-validation; synchronized ECG+PPG gives **pulse arrival time (PAT)**, not pure PTT; IMU can mark motion-contaminated intervals. |
| **SmartElex MAX30101 breakout** | Red/IR/green reflected-light photodetector samples via MAX30101 optical AFE/ADC; raw values represent returned optical intensity, not “SpO₂” directly. | Pulse waveform, pulse rate, inter-pulse intervals, PRV, pulse amplitude/morphology, perfusion-related features; SpO₂ only with an adequately validated red/IR algorithm and calibration. | Peripheral pulsatile blood-volume dynamics plus vascular/perfusion influences. | Pulse rate can track alcohol-related tachycardic/autonomic changes; PRV may carry autonomic information but is not universally interchangeable with ECG HRV; multimodal intoxication work is exploratory. | **Well established** for pulse detection under suitable conditions; **moderately supported** for PRV as a related-but-distinct variability measure; **exploratory** for AUD/intoxication inference. | Motion, contact pressure, site, low perfusion, vasoconstriction, ambient light, skin pigmentation, skin temperature, posture, optical geometry. | ECG cross-validation and PAT; IMU-based artifact detection/removal; temperature helps interpret perfusion; multi-wavelength consistency and multimodal fusion. |
| **SmartElex TMP117 breakout** | The TMP117 reports **local IC/die temperature**. It can act as a skin-temperature sensor only when thermal/mechanical coupling makes skin the dominant heat source. | Local skin-temperature estimate, baseline, slope/rate of change, deviations from personal baseline; wear/contact-state cues in some designs. | Peripheral thermoregulation and cutaneous perfusion are influenced by environment, activity and autonomic vascular control. | Withdrawal can include fever/temperature dysregulation; acute alcohol can alter skin temperature/vasodilation; small wearable studies show temperature shifts during intoxication. Specificity is poor. | **Well established** as a temperature sensor; **exploratory-to-moderate** for alcohol-related peripheral-temperature changes; **insufficient** for alcohol detection alone. | Ambient temperature, airflow, clothing, contact quality, body site, circadian rhythm, activity, fever/illness, vasoconstriction, sweat, device/self-heating. | Context for EDA and PPG; distinguish environmental/activity-related changes from concordant autonomic changes; possible contact/wear validation if experimentally established. |
| **GY-521 / MPU-6050 IMU** | 3-axis acceleration (specific force, including gravity) and 3-axis angular velocity. | Activity level, acceleration magnitude, posture/orientation proxies, gait features, tremor frequency/energy, restlessness proxies, motion-quality flags. | Motor activity, tremor, gait stability and body-motion context; not a direct autonomic or alcohol measure. | Tremor is a recognized withdrawal sign; accelerometer tremor energy has correlated with clinician-rated withdrawal tremor. Controlled studies also find gait features change with intoxication. | **Moderately supported** for objective withdrawal-tremor quantification; **exploratory** for intoxication/gait classification; **contextual** for activity/restlessness. | Placement/orientation, voluntary movement, exercise, essential/physiologic tremor, caffeine, anxiety, medications, fatigue, device looseness, vibration, task differences. | Strong artifact/context channel for PPG; can flag motion-contaminated ECG/EDA windows; separates physiologic arousal from physical activity; contributes motor phenotype to fusion. |

---

# Hardware-capability boundary: IC capability vs breakout-board implementation

This distinction matters because the candidate list names **breakout boards**, while the strongest technical documentation often describes the **underlying IC**.

### ESP32 DevKit V1 (30-pin, ESP-WROOM-32-family)

Espressif documents the **ESP32-WROOM-32 module** as a general Wi‑Fi/Bluetooth MCU module with GPIO, I²C/SPI/UART and ADC capability; it is not a biosensor ([Espressif ESP32-WROOM-32 datasheet, HTML](https://documentation.espressif.com/esp32-wroom-32_datasheet_en.html)). “DevKit V1 30-pin” is a generic carrier-board description used by multiple vendors rather than a uniquely defined Espressif product revision. Therefore, exact carrier-board pin mapping, regulator, USB interface, ADC input conditioning, and clone-specific behavior should be verified from the physical board/vendor before implementation claims are locked.

### ProtoCentral tinyGSR — legacy/original hardware

This candidate needs unusually explicit version control. ProtoCentral’s current v3 page says v3 is a “ground-up redesign” and that the **earlier board produced a relative, trimpot-set reading with no valid universal conversion to real units**. ProtoCentral further explains that the hand-adjusted front-end gain meant the same ADC count could correspond to different conductance on different units, and that “absolute units genuinely cannot be recovered” from the original hardware ([ProtoCentral tinyGSR product/history](https://protocentral.com/product/protocentral-tinygsr-gsr-eda-digital-output-sensor-board-qwiic-stemma-qt/)). The older ProtoCentral tutorial describes use of a TLA2022 ADC and baseline adjustment for the legacy implementation ([ProtoCentral/Hackster legacy tinyGSR tutorial](https://www.hackster.io/protocentral/measuring-emotions-with-gsr-using-tinygsr-arduino-1f038e)).

**Consequence for PCB marking 11/22:** it is reasonable to treat the board as the legacy/original family because that is the supplied identification, but the marking itself has not been independently matched here to a released schematic revision. The report therefore treats **absolute µS as unverified/unavailable** unless the exact board schematic/calibration proves otherwise.

### CJMCU-8232 AD8232 module — PCB marking VS82

Analog Devices verifies the **AD8232 IC** as a single-lead ECG/biopotential signal-conditioning front end with high gain, adjustable high/low-pass filtering, lead-off functions and right-leg-drive support ([Analog Devices AD8232 product page](https://www.analog.com/en/products/ad8232.html)). Common CJMCU-8232 listings expose a 3.3 V supply, analog `OUTPUT`, `LO+`, `LO-`, `SDN`, and electrode connections, but no authoritative CJMCU manufacturer document for **PCB marking VS82** was located in this search.

Therefore:
- **Verified at IC level:** capability to amplify/filter a single-lead biopotential and provide analog output.
- **Commonly documented at breakout level:** analog output and lead-off pins.
- **Not verified for the exact VS82 board:** exact passive filter cutoff frequencies, gain implementation, electrode/RLD topology and component tolerances.

Those board-level details materially affect whether the waveform is suitable merely for robust R-peak detection or for any more ambitious morphology analysis.

### SmartElex MAX30101 breakout

Analog Devices verifies that MAX30101 integrates red/IR/green LEDs, photodetector, optical elements, ambient-light rejection, FIFO and an **18-bit current ADC** ([MAX30101 product page](https://www.analog.com/en/products/max30101.html); [official datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX30101.pdf)). A reseller listing specifically naming the **SmartElex Photodetector Breakout – MAX30101** reports I²C address `0x57`, red/IR/green LEDs, 18-bit ADC, onboard 1.8 V regulation and a 5 V step-up converter ([The Engineer Store listing](https://www.theengineerstore.in/products/smartelex-photodetector-breakout-max30101)).

No primary SmartElex technical page/schematic was located in this search. Accordingly, IC functionality should not be confused with a board-provided clinical algorithm: **MAX30101 raw optical acquisition capability is verified; a clinically validated SpO₂/HR algorithm on this exact breakout is not.**

### SmartElex TMP117 breakout

TI verifies TMP117 as a high-accuracy **local digital temperature sensor**, 16-bit, 0.0078 °C/LSB, with up to ±0.1 °C accuracy over −20 to 50 °C ([TI TMP117 product page](https://www.ti.com/product/TMP117)). A reseller listing specifically names the SmartElex TMP117 breakout ([The Engineer Store listing](https://www.theengineerstore.in/collections/sensors/products/smartelex-high-precision-temperature-sensor-tmp117)). No primary SmartElex schematic was located.

The key scientific boundary is that the IC’s calibration/accuracy describes **the temperature at the sensor die**, not automatically the subject’s skin or core temperature. TI’s wearable guidance emphasizes mechanical/thermal contact and isolation from other heat sources when using TMP117 for skin-temperature measurement ([TI wearable temperature design guidance](https://www.ti.com/document-viewer/lit/html/SSZT563)).

### GY-521 / MPU-6050

TDK identifies MPU-6050 as an obsolete but documented 6-axis InvenSense MEMS MotionTracking device with I²C output, selectable accelerometer ranges ±2/4/8/16 g and gyro ranges ±250/500/1000/2000 °/s ([TDK MPU-6050 product center](https://product.tdk.com/en/search/sensor/mortion-inertial/imu/info?part_no=MPU-6050)). “GY-521” is a generic breakout-board ecosystem rather than one uniquely traceable manufacturer design; regulator and pull-up implementations can vary. Therefore, the report relies on the MPU-6050 IC for sensing capability and treats exact breakout electrical details as board-specific.

---

# Modality 1 — ECG / AD8232

## 1. What it directly measures

The physiological phenomenon is the **electrical potential difference at the skin generated primarily by cardiac depolarization/repolarization**, observed along one lead vector. The AD8232 does not directly measure “heart rate” or “HRV.” It is an analog front end that extracts, amplifies and filters a small biopotential so a downstream ADC can sample it ([Analog Devices AD8232](https://www.analog.com/en/products/ad8232.html)).

For the CJMCU-8232 board, the most defensible raw-data description is:

**electrode potential difference → AD8232 analog conditioning → analog output voltage → ESP32 ADC samples.**

That sampled waveform should be treated as a **conditioned single-lead ECG-like signal**, whose morphology depends on the exact CJMCU passive filter network and electrode geometry.

### Directly measured variable

- Conditioned single-lead biopotential voltage over time.

### Not directly measured

- Heart rate.
- RR interval.
- HRV.
- “Sympathetic activity.”
- “Stress.”
- Alcohol concentration.
- Craving or withdrawal severity.

All of those require additional processing and/or inference.

## 2. What can be derived from it

If R peaks can be detected reliably:

1. **Heart rate (HR)** from beat frequency.
2. **RR intervals**; after artifact/ectopic handling, physiologically appropriate intervals are often called NN intervals.
3. **Time-domain HRV**, including:
   - **RMSSD** — strongly influenced by short-term vagally mediated variability.
   - **SDNN** — overall variability over the analyzed segment; interpretation depends strongly on recording duration.
   - other statistics such as pNN50, median/mean RR, coefficient-of-variation measures.
4. **Frequency-domain HRV**, e.g. HF and LF power, but only with appropriate segment duration, stationarity, sampling and artifact handling.
5. Potential signal-quality indices and beat irregularity flags.

The classic Task Force standard remains foundational for HRV definitions and measurement methodology ([Task Force, *Circulation*, 1996, DOI: 10.1161/01.CIR.93.5.1043](https://doi.org/10.1161/01.CIR.93.5.1043)). Psychophysiology guidance emphasizes control/reporting of respiration, posture, measurement duration, activity and other contextual variables ([Laborde, Mosley & Thayer, 2017, DOI: 10.3389/fpsyg.2017.00213](https://doi.org/10.3389/fpsyg.2017.00213)).

### Critical interpretation warning: LF/HF

The LF/HF ratio should **not** be treated as a simple numeric “sympathetic/parasympathetic balance.” That interpretation has been strongly criticized because LF power is not a pure sympathetic measure and both branches plus baroreflex/respiratory mechanisms contribute ([Billman, 2013, DOI: 10.3389/fphys.2013.00026](https://doi.org/10.3389/fphys.2013.00026)).

## 3. Physiological interpretation

The ECG’s R-to-R timing is the electrical timing reference for successive ventricular depolarizations. Beat-to-beat variation reflects autonomic modulation of the sinoatrial node, but also respiration, baroreflex activity, posture, metabolic demand, thermoregulation, sleep/wake state, illness and many behavioral factors.

A useful hierarchy is:

**ECG voltage → R peaks → RR/NN series → HR/HRV features → cardiac autonomic association → possible AUD-related group differences.**

A low RMSSD does **not** mean “the person is craving alcohol,” nor does a high HR prove withdrawal. It means a cardiovascular timing pattern has changed; the context determines possible interpretation.

## 4. Evidence linking it to AUD / alcohol withdrawal / recovery

### AUD generally and chronic autonomic dysregulation — **well established at group level**

A 2019 systematic review/meta-analysis included 15 studies in quantitative analysis and found significantly lower HRV in AUD than controls: **Hedges g = −0.4301, 95% CI −0.7601 to −0.1000; p=0.0106**, although heterogeneity was high (**I²=83.8%**). Total variability and RMSSD were also significantly lower; HF alone was not consistently different ([Cheng et al., 2019, *Drug and Alcohol Dependence*, DOI: 10.1016/j.drugalcdep.2019.05.030](https://doi.org/10.1016/j.drugalcdep.2019.05.030)).

A broader systematic review of alcohol-induced autonomic dysfunction included 55 studies and reported autonomic dysfunction by cardiovascular reflex tests in **16–73%** of chronic alcohol-abusing populations, while emphasizing major heterogeneity in populations and test criteria ([Julian et al., 2020, DOI: 10.1007/s10286-019-00618-8](https://doi.org/10.1007/s10286-019-00618-8)).

**Interpretation:** chronic heavy alcohol exposure/AUD is associated with altered autonomic cardiovascular regulation at the population level. This does not turn HRV into a diagnostic test for AUD.

### Acute alcohol — **moderately supported**

A review of 33 articles concluded that acute alcohol generally reduced resting HRV in healthy subjects and that resting HRV tended to be lower in AUD/heavier-drinking groups ([Ralevski et al., 2019, DOI: 10.1016/j.pbb.2018.12.003](https://doi.org/10.1016/j.pbb.2018.12.003)).

This supports HR/HRV as a plausible alcohol-responsive physiological channel, but acute alcohol is not the only cause of such changes.

### Acute alcohol withdrawal — **mixed evidence**

Withdrawal commonly includes autonomic hyperactivity clinically, including tachycardia, hypertension, sweating and tremor. But a neurophysiology study of **40 men** with mild vs moderate/severe acute alcohol withdrawal found increased HR at admission while classical HRV measures and sympathetic skin response did **not** show the expected autonomic dysfunction; the authors concluded that classical HRV/SSR measures were not suitable for characterizing the acute autonomic changes in that cohort ([Bär et al., 2006, DOI: 10.1111/j.1530-0277.2006.00191.x](https://doi.org/10.1111/j.1530-0277.2006.00191.x)).

**Implication:** withdrawal-related tachycardia is clinically relevant, but “HRV = withdrawal severity” is not supported as a general rule.

### Craving, stress and relapse-risk research — **moderately supported to exploratory**

The Ralevski review reported that in some AUD studies, reactive HRV was associated with more craving, faster relapse and negative mood; recovery of resting/reactive HRV with abstinence was heterogeneous ([Ralevski et al., 2019](https://doi.org/10.1016/j.pbb.2018.12.003)).

However, a 2026 VR cue-exposure study in **61 patients with alcohol dependence** found that alcohol-risk scenes increased subjective craving but **did not significantly change HR/HRV**, whereas phasic EDA and respiration did change ([Lütt et al., 2026, DOI: 10.2196/84156](https://doi.org/10.2196/84156)).

A 100-day naturalistic case-series in **10 people attempting recovery** found large person-to-person differences and only negligible-to-weak cardiovascular associations with craving; the study did not support a simple physiology→imminent lapse mapping ([van Lier et al., 2022, *Addictive Behaviors Reports*](https://pmc.ncbi.nlm.nih.gov/articles/PMC9287639/)).

**Conclusion for evidence mapping:** ECG-derived HR/HRV is relevant to autonomic regulation and is scientifically connected with AUD, but craving/relapse inference should remain individualized and exploratory.

## 5. What it cannot establish by itself

A single-lead AD8232 signal cannot by itself establish:

- AUD diagnosis.
- alcohol consumption or blood/breath alcohol concentration.
- craving.
- relapse or imminent relapse.
- alcohol withdrawal diagnosis or withdrawal severity.
- psychological stress.
- sympathetic activation in isolation.
- clinically validated arrhythmia diagnosis from this hobby breakout.

The last point is especially important because the exact CJMCU filter network and validation status are unknown.

## 6. Important confounders / artifacts

- Electrode placement and lead orientation.
- Skin-electrode impedance, drying/lifting electrodes and cable motion.
- Skeletal-muscle EMG contamination.
- Whole-body movement.
- 50/60 Hz mains interference and electromagnetic coupling.
- Respiration, especially for vagally mediated HRV.
- Posture and recent posture transitions.
- Exercise/activity and recovery from exercise.
- Age and fitness.
- sleep/circadian state.
- fever/illness and hydration.
- pain/anxiety.
- caffeine/nicotine and other stimulants.
- medications affecting autonomic or cardiac function.
- ectopic beats/arrhythmias.
- R-peak detector errors.
- recording length, sampling rate and filtering choices.

## 7. Potential role when combined with the other modalities

### ECG + PPG: beat correspondence and cross-validation

The ECG R wave is an electrical event; the PPG pulse is a later peripheral mechanical/vascular event. When synchronized, the streams can check whether an apparent PPG pulse corresponds to an ECG beat and can help identify missed/false optical beats during motion.

### ECG + PPG: pulse arrival time, not pure pulse transit time

The interval from ECG R wave to a distal PPG fiducial point is **pulse arrival time (PAT)**. PAT contains the **pre-ejection period (PEP) + vascular pulse transit component**, so it must not be labeled pure PTT ([Mohammadpoor Faskhodi et al., 2024, DOI: 10.1371/journal.pone.0298354](https://doi.org/10.1371/journal.pone.0298354); [Allen et al./multi-site PPG terminology review](https://pmc.ncbi.nlm.nih.gov/articles/PMC6912608/)).

PAT may provide exploratory information about heart–arterial coupling/vascular state. It should **not** be converted into blood pressure without a specifically validated calibration/model.

### ECG + IMU

The IMU can label movement-intensive periods where R-peak detection or baseline quality may degrade. It is best regarded first as a **signal-quality/context channel**, not as permission to algorithmically “fix” every corrupted ECG sample.

### ECG + EDA + temperature

Concordant tachycardia/HRV change + EDA change + stable low motion can strengthen the interpretation that a change is physiological rather than solely movement-driven. Even then, the state remains nonspecific: fear, heat, pain, exercise recovery, nicotine, caffeine, illness and many other causes remain plausible.

## 8. Evidence gaps / questions still needing investigation

1. Exact CJMCU-8232 VS82 schematic, gain and filter cutoff frequencies.
2. Whether ESP32 ADC performance and chosen sampling architecture preserve R-peak timing sufficiently for the target HRV features.
3. Recording duration/windowing needed for each HRV metric in the intended use case.
4. Whether respiration will be measured or at least contextually controlled for vagal HRV interpretation.
5. How ectopic beats/poor-quality segments will be identified.
6. Whether personalization/baseline normalization improves AUD-recovery relevance more than population thresholds.
7. Whether any proposed reactive-HRV metric adds information beyond HR, EDA, movement and self-report in this specific population.

---

# Modality 2 — PPG / MAX30101

## 1. What it directly measures

Photoplethysmography (PPG) is an optical method for detecting changes in light absorption/scattering caused by changes in blood volume in the microvascular tissue bed. A classic review describes PPG as detecting **blood-volume changes in the microvascular bed of tissue**, with a pulsatile cardiac-synchronous AC component superimposed on a slower DC baseline ([Allen, 2007, DOI: 10.1088/0967-3334/28/3/R01](https://doi.org/10.1088/0967-3334/28/3/R01)).

At the MAX30101 hardware level, the direct data are **digitized photodetector current samples** obtained while selected red, infrared and/or green LEDs illuminate the tissue. The official MAX30101 documentation verifies red/IR/green emitters, photodetector, ambient-light cancellation, FIFO and 18-bit current ADC ([Analog Devices MAX30101](https://www.analog.com/en/products/max30101.html)).

### Directly measured

- Reflected optical intensity / photodiode current samples per enabled wavelength.

### Not directly measured

- Heart rate.
- PRV.
- blood pressure.
- vascular stiffness.
- SpO₂.
- alcohol concentration.

Those are derived/estimated quantities.

## 2. What can be derived from it

### Strongly conventional derived features

- Pulse peaks or pulse onsets.
- **Pulse rate (PR)**.
- Inter-pulse intervals.
- **Pulse-rate variability (PRV)**.
- PPG AC amplitude, DC level and AC/DC relationships.
- Pulse-wave morphology: rise time, width, slopes, area, derivative features, notch-related features when signal quality and sampling permit.
- Perfusion-related trend/proxy features.

PPG waveform shape/timing is influenced by vascular aging, arterial stiffness, BP and atherosclerosis, but those are associations, not direct measurements ([Charlton et al., 2022, DOI: 10.1152/ajpheart.00392.2021](https://doi.org/10.1152/ajpheart.00392.2021)).

### SpO₂ — possible IC use, but not a direct breakout output

Pulse oximetry estimates arterial oxygen saturation using wavelength-dependent pulsatile absorption, commonly with red and IR channels. The MAX30101 contains the optical hardware for pulse-oximetry applications, but an accurate SpO₂ result depends on algorithmic calibration, optical geometry, placement and validation. The existence of red/IR LEDs does **not** establish that this SmartElex breakout plus arbitrary software is a validated pulse oximeter.

The FDA emphasizes that pulse-ox readings are estimates and can be affected by poor circulation, skin pigmentation, skin temperature and other factors ([FDA Pulse Oximeter Basics](https://www.fda.gov/consumers/consumer-updates/pulse-oximeter-basics)).

## 3. Physiological interpretation

The PPG pulse reflects the arrival of the arterial pressure/volume pulse at the peripheral measurement site, modulated by local vascular tone and optical/tissue factors. Therefore:

**optical samples → pulse waveform → pulse timing/morphology → peripheral cardiovascular/perfusion association → possible alcohol/autonomic relevance.**

The slower PPG baseline can reflect respiration, sympathetic activity and thermoregulation, among other influences, but the signal origins are composite rather than specific ([Allen, 2007](https://doi.org/10.1088/0967-3334/28/3/R01)).

## 4. Evidence linking it to AUD / alcohol withdrawal / recovery

### Pulse rate / cardiovascular response — **moderately supported, nonspecific**

Alcohol can affect resting HR/autonomic state. Therefore, PPG-derived pulse rate can capture some of the same **rate** changes as ECG-derived HR when pulse detection is reliable. This does not mean PPG-derived variability is identical to ECG HRV.

### PRV vs HRV — **related but not interchangeable**

A 2020 review concluded that PRV **must not be considered a valid surrogate for HRV in all scenarios**, because the peripheral pulse incorporates electromechanical and vascular effects beyond cardiac electrical timing ([Mejía-Mejía et al., 2020, DOI: 10.1088/1361-6579/ab998c](https://doi.org/10.1088/1361-6579/ab998c)).

A 2026 systematic review/meta-analysis found relatively small errors for some PRV-vs-HRV metrics under selected controlled/resting conditions but specifically warned against broad generalization to exercise, stress, sleep and free-living settings ([Xu et al., 2026, DOI: 10.3390/s26165192](https://doi.org/10.3390/s26165192)).

**AUD consequence:** evidence that ECG HRV differs in AUD should **not automatically be transferred unchanged to PPG PRV**.

### Multimodal intoxication — **exploratory**

A 2026 pilot study of **28 healthy participants** used a research wearable measuring HR, skin temperature, EDA and accelerometry. From sober to intoxicated periods:
- HR: **82.54 → 103.24 bpm**, p<0.001
- EDA: **0.34 → 1.44 µS**, p=0.0071
- skin temperature: **29.93 → 31.59 °C**, p<0.001
- accelerometer SD: **0.044 → 0.057 g**, p=0.0064

Multimodal machine-learning accuracy reached **0.80** for an impairment classification task ([Kaczor et al., 2026, PubMed/PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC12805382/)).

This is promising evidence that cardiovascular + EDA + temperature + motion can carry intoxication-related information **in combination**, but it is a small controlled pilot and does not establish a PPG-only alcohol detector or generalize automatically to AUD rehabilitation.

## 5. What it cannot establish by itself

PPG alone cannot establish:

- AUD diagnosis.
- alcohol consumption or BrAC/BAC.
- craving.
- withdrawal.
- relapse risk.
- psychological stress.
- blood pressure from waveform/PAT without a validated model.
- clinical arterial stiffness.
- clinically accurate SpO₂ merely because the MAX30101 supports red/IR acquisition.

## 6. Important confounders / artifacts

A PPG review identifies movement, respiration, low perfusion, ambient light, temperature, skin pigmentation, source-detector alignment, attachment/contact pressure and posture as important influences ([Park et al., 2022, DOI: 10.3389/fphys.2021.808451](https://doi.org/10.3389/fphys.2021.808451)).

Important practical confounders include:

- motion at the sensor-skin interface;
- contact pressure/strap tightness;
- low peripheral perfusion;
- cold-induced vasoconstriction;
- local temperature;
- ambient light leakage;
- skin pigmentation and tissue optical properties;
- placement site;
- posture;
- exercise;
- vascular disease;
- sensor orientation;
- LED current/sample-rate settings;
- clipping/saturation.

For SpO₂ specifically, the FDA additionally highlights poor circulation, pigmentation, skin thickness, skin temperature, tobacco use and nail polish as potential influences, depending on site/device ([FDA](https://www.fda.gov/medical-devices/products-and-medical-procedures/pulse-oximeters)).

## 7. Potential role when combined with the other modalities

### PPG + ECG

1. **Pulse/beat cross-validation:** an ECG R event should precede a physiological PPG pulse; disagreement can expose false/missed PPG beats or poor-quality windows.
2. **PAT:** ECG R-wave to PPG pulse arrival is a cross-sensor timing feature.
3. **PRV-vs-HRV comparison:** divergence itself may reflect peripheral vascular or artifact effects and should not automatically be “corrected away.”

### PPG + IMU

This is one of the strongest measurement-quality combinations in the candidate set. A review of wearable PPG explicitly describes accelerometer/gyroscope reference signals for motion-artifact suppression ([Charlton et al., wearable PPG review](https://pmc.ncbi.nlm.nih.gov/articles/PMC7612541/)). A separate review states that motion artifact is a major challenge for PPG HR tracking ([Ismail et al., 2021](https://link.springer.com/article/10.1186/s13634-020-00714-2)).

The scientifically safe claim is:

**IMU can identify motion-contaminated periods and may support motion-artifact rejection/compensation; it does not guarantee artifact-free PPG.**

### PPG + temperature

Peripheral vasoconstriction/vasodilation and skin temperature influence optical pulse amplitude/perfusion. Temperature can therefore be a useful context channel when PPG amplitude changes.

### PPG + EDA

Concordant peripheral cardiovascular and sympathetic-sudomotor responses may be richer than either alone, especially under low motion. They remain nonspecific with respect to alcohol, stress or craving unless the experimental context supplies that information.

## 8. Evidence gaps / questions still needing investigation

1. Exact SmartElex schematic/layout and optical window geometry.
2. Intended body site and attachment pressure.
3. Which wavelength(s) are best for pulse-quality under the intended placement.
4. Whether pulse rate only, PRV, morphology, or pulse-oximetry is actually in scope.
5. Validation of any SpO₂ algorithm against a reference device if SpO₂ is considered.
6. Whether ECG/PPG clocks are synchronized tightly enough for stable PAT.
7. Whether PAT adds independent AUD-relevant information after HR/EDA/activity are known.
8. Motion-artifact algorithm performance on the exact MAX30101 + MPU6050 physical arrangement.

---

# Modality 3 — EDA / GSR / legacy ProtoCentral tinyGSR

## 1. What it directly measures

Electrodermal activity (EDA) refers to electrical properties of the skin that change with sweat-gland filling/activity. The standard exosomatic approach applies a small electrical excitation and measures conductance/resistance. EDA is closely linked to sympathetic sudomotor activity because eccrine sweat glands are predominantly under **sympathetic cholinergic** control ([Posada-Quintero & Chon, 2020, DOI: 10.3390/s20020479](https://doi.org/10.3390/s20020479); [Boucsein et al., 2012, DOI: 10.1111/j.1469-8986.2012.01384.x](https://doi.org/10.1111/j.1469-8986.2012.01384.x)).

### Exact legacy-board constraint

For the original tinyGSR, ProtoCentral now explicitly states that the front-end gain was set by a hand-adjusted trimmer and that **absolute conductance units cannot truthfully be reconstructed universally from the ADC counts** ([ProtoCentral tinyGSR history](https://protocentral.com/product/protocentral-tinygsr-gsr-eda-digital-output-sensor-board-qwiic-stemma-qt/)).

Therefore, for this exact candidate:

**Direct board output:** digitized relative front-end response associated with skin conductance/resistance.

**Do not document it as:** “absolute EDA in µS” unless board-specific calibration/schematic evidence establishes that capability.

## 2. What can be derived from it

Conceptually, EDA is often decomposed into:

- **Tonic component / skin conductance level (SCL):** slowly changing baseline.
- **Phasic component / skin conductance responses (SCRs):** faster transient responses.
- Number/frequency of nonspecific SCRs.
- SCR amplitude, rise time, recovery time and area.
- Baseline-normalized or within-person change measures.
- Decomposition-derived phasic driver measures.

For this legacy board, the most defensible outputs may be **relative tonic trend and relative phasic response features**, rather than absolute µS amplitudes.

The Society for Psychophysiological Research recommendations define standard EDA terminology, measurement sites and reporting practice ([Boucsein et al., 2012](https://doi.org/10.1111/j.1469-8986.2012.01384.x)).

## 3. Physiological interpretation

EDA is best interpreted as a **sympathetic sudomotor arousal measure**, not a direct “emotion,” “stress,” “craving,” or “lie” measure.

Sweat-gland activation can occur with:
- emotional arousal,
- cognitive effort,
- pain,
- startle,
- heat/thermoregulation,
- movement/physical effort,
- respiratory/speech events,
- medication/drug effects.

Thus:

**skin electrical response → tonic/phasic EDA feature → sympathetic sudomotor association → context-dependent arousal interpretation → possible AUD relevance.**

## 4. Evidence linking it to AUD / alcohol withdrawal / recovery

### Alcohol cue reactivity / craving — **exploratory-to-moderately supported, but weakly specific**

A 2026 exploratory VR cue study in **61 alcohol-dependent patients** found alcohol-associated scenarios significantly increased **nonspecific SCR frequency**, with effect sizes ranging approximately **d=0.31 to d=0.91**, while SCL and HR/HRV did not show the same consistent cue effect. Importantly, correlations between subjective craving and EDA were **weak**: SCL **r=0.20, p=.04** and NS-SCR frequency **r=0.21, p=.03** ([Lütt et al., 2026, DOI: 10.2196/84156](https://doi.org/10.2196/84156)).

That is a strong example of why **physiological arousal ≠ subjective craving**.

An older cue-exposure study in **36 detoxified alcohol-dependent patients** found heterogeneous response patterns: only **22%** showed both subjective and physiological response, **42%** physiological response without subjective response, and **31%** neither ([Drummond et al./cue-reactivity study, PubMed PMID 10943912](https://pubmed.ncbi.nlm.nih.gov/10943912/)).

Another alcohol-cue study reported robust subjective craving but comparatively weak/nonspecific physiological changes in HR, skin conductance and temperature ([PubMed PMID 16604310](https://pubmed.ncbi.nlm.nih.gov/16604310/)).

**Evidence interpretation:** phasic EDA can respond to alcohol cues in some paradigms, but it is not a direct craving meter.

### Recovery and stress monitoring — **exploratory but directly relevant**

A proof-of-concept ambulatory study in **11 adults in AUD recovery** collected EDA and HRV for up to 14 days. It found **87.86%** of EDA samples met their clean-signal criteria and reported associations between physiological features and self-reported stressful events/emotion/pain ([Alinia et al., 2021, DOI: 10.2196/27891](https://doi.org/10.2196/27891)).

This supports feasibility of **ambulatory psychophysiological monitoring in AUD recovery**, not automatic relapse prediction.

### Craving/lapse prediction in daily life — **insufficient for a general predictor**

A 100-day naturalistic study of **10 participants** found strong inter- and intra-individual differences. EDA’s association with craving was generally lower than the cardiovascular association, and the authors did not find a simple one-to-one physiological precursor suitable for imminent lapse detection ([van Lier et al., 2022](https://pmc.ncbi.nlm.nih.gov/articles/PMC9287639/)).

### Acute withdrawal / sweating — **physiologically plausible, direct evidence limited/mixed**

Sweating/diaphoresis is a recognized clinical sign of autonomic hyperactivity during withdrawal. A 24-hour ambulatory study of only **six male withdrawal subjects** found sweating, tremor and locomotor activity elevated versus controls and reported temporal variation in sweating and skin temperature ([White et al., 1994, DOI: 10.1007/BF01828833](https://doi.org/10.1007/BF01828833)).

However, the Bär study of 40 men found **sympathetic skin response** did not show the expected increased sympathetic activity during acute withdrawal ([Bär et al., 2006](https://doi.org/10.1111/j.1530-0277.2006.00191.x)). SSR is not identical to continuous EDA, but the result is an important warning against assuming every sympathetic measure tracks withdrawal severity.

### Intoxication / recent alcohol consumption — **exploratory**

The 2026 multimodal pilot showed EDA increased from **0.34 to 1.44 µS** between sober and intoxicated periods (p=0.0071) in 28 healthy volunteers, but the result was part of a multimodal controlled study and is not specific to ethanol ([Kaczor et al., 2026](https://pmc.ncbi.nlm.nih.gov/articles/PMC12805382/)).

## 5. What it cannot establish by itself

EDA cannot establish:

- AUD diagnosis.
- craving.
- stress cause.
- relapse.
- withdrawal diagnosis/severity.
- recent alcohol use.
- intoxication.
- blood alcohol concentration.

A rise in EDA says **sudomotor/arousal state changed**; it does not identify why.

## 6. Important confounders / artifacts

A 2026 review of EDA artifacts identifies:
- movement,
- respiration,
- speech,
- electrode/system artifacts,
- temperature,
- humidity,
- sound/noise context,
- air pressure/altitude

as relevant sources of variation/artifact, especially in ambulatory use ([Bari et al., 2026, DOI: 10.1088/1361-6579/ae7bae](https://doi.org/10.1088/1361-6579/ae7bae)).

Additional important factors:
- electrode site and sweat-gland density;
- electrode material/gel;
- contact pressure;
- skin hydration;
- individual skin properties;
- handwashing/lotions;
- ambient heat;
- exercise;
- medication/anticholinergic effects;
- nicotine/caffeine;
- acclimation time;
- baseline drift;
- saturation during heavy sweating.

The legacy tinyGSR’s hand-trimmed front end adds a **hardware-specific cross-device comparability problem** on top of physiological confounding.

## 7. Potential role when combined with the other modalities

### EDA + IMU

This is a high-value **interpretability** pairing. A phasic EDA excursion coinciding with large movement is more questionable than a similar excursion during verified low motion. IMU data can support artifact flags and activity context.

### EDA + temperature

Sweat-gland activity and skin temperature both respond to thermoregulatory context. Temperature helps determine whether an EDA baseline shift may plausibly be related to heat/cutaneous state rather than purely psychological arousal.

### EDA + ECG/PPG

Concordant EDA activation and cardiovascular change can indicate broader autonomic arousal, but the combination remains nonspecific. Lack of concordance can also be informative because sympathetic sudomotor and cardiac responses need not move together.

### EDA + longitudinal personal baseline

Given large interindividual differences, within-person change from a stable baseline may be more meaningful than universal thresholds. That is a hypothesis to test, not an established AUD rule.

## 8. Evidence gaps / questions still needing investigation

1. Exact 11/22 legacy board revision and front-end schematic.
2. Whether the trimmer setting can be documented/reproduced on the physical unit.
3. Whether relative features are stable across days without re-trimming.
4. Electrode site and electrode material.
5. Whether phasic detection is robust at the legacy board’s acquisition/filter settings.
6. How movement will be used to mask or grade EDA quality.
7. Whether within-person EDA changes add predictive value beyond self-report, HR and activity.
8. Whether withdrawal sweating produces a sufficiently distinct temporal pattern in the intended population—current evidence is too sparse to assume this.

---

# Modality 4 — Temperature / TMP117

## 1. What it directly measures

TMP117 is a calibrated **local temperature sensor IC**. TI specifies a 16-bit result with 0.0078 °C resolution and up to ±0.1 °C maximum accuracy from −20 to 50 °C ([TI TMP117](https://www.ti.com/product/TMP117)).

But in a breakout-board prototype:

**TMP117 directly measures the temperature of its own sensing die.**

It approximates **skin temperature** only if the mechanical/thermal design couples the sensor strongly to skin and minimizes competing heat paths. TI’s wearable guidance explicitly emphasizes thermal contact and isolation from other heat sources ([TI design guidance](https://www.ti.com/document-viewer/lit/html/SSZT563)).

It does **not** directly measure:
- core body temperature;
- blood temperature;
- autonomic tone;
- fever cause;
- alcohol concentration.

## 2. What can be derived from it

If skin coupling is validated:

- local skin temperature estimate;
- mean/median over a time window;
- deviation from personal baseline;
- rate of change/slope;
- short/long-term temperature trend;
- potential contact/wear-state cue, if empirically calibrated for the prototype;
- contextual covariate for PPG/EDA interpretation.

A single peripheral temperature site should not be silently promoted to “body temperature.”

## 3. Physiological interpretation

Peripheral skin temperature depends on the balance of:
- local blood flow/vasodilation/vasoconstriction;
- core-to-skin heat transfer;
- ambient conditions;
- sweating/evaporation;
- clothing/insulation;
- movement;
- body site;
- circadian rhythm;
- illness/fever.

Cutaneous blood flow is under strong thermoregulatory and sympathetic vascular control. Therefore, skin-temperature change can be **compatible with autonomic/vascular change** without being specific to it.

## 4. Evidence linking it to AUD / alcohol withdrawal / recovery

### Acute alcohol — **moderately supported that effects exist, weak specificity**

An older controlled human study found alcohol infusion rapidly **increased skin temperature in a neutral environment**, but controlled cold exposure drove surface temperature back toward pre-alcohol values, demonstrating strong environmental dependence ([Risbo et al., 1981, DOI: 10.1111/j.1399-6576.1981.tb01639.x](https://doi.org/10.1111/j.1399-6576.1981.tb01639.x)).

A 2024 systematic scoping review of alcohol and heat stress found only **eight eligible studies involving 93 male participants** and concluded that alcohol’s thermoregulatory effects remain poorly characterized ([Morris et al., 2024, DOI: 10.1186/s12940-024-01113-y](https://doi.org/10.1186/s12940-024-01113-y)).

**Interpretation:** alcohol can alter thermoregulation/peripheral temperature, but the literature is too context-dependent to treat skin temperature as a specific alcohol marker.

### Withdrawal — **exploratory/directly relevant symptom context**

Alcohol withdrawal syndromes can include fever/temperature dysregulation as part of autonomic hyperactivity, but temperature is one component of a broader clinical syndrome.

The small 24-hour ambulatory withdrawal study (n=6) found skin temperature and sweating showed temporal patterns and that withdrawal participants had elevated tremor/sweating/activity overall ([White et al., 1994](https://doi.org/10.1007/BF01828833)).

This supports **trend monitoring as a research possibility**, not withdrawal diagnosis from skin temperature.

### Intoxication — **exploratory multimodal evidence**

In the 2026 28-person pilot, wrist skin temperature rose from **29.93 to 31.59 °C** between sober and intoxicated periods (p<0.001), alongside HR, EDA and accelerometry changes ([Kaczor et al., 2026](https://pmc.ncbi.nlm.nih.gov/articles/PMC12805382/)).

Again, the value was multimodal and context-dependent.

### Recovery monitoring — **indirect/exploratory**

Temperature may help characterize sleep/circadian/activity context, peripheral vascular state and wear/contact quality, but there is much less direct evidence for skin temperature as a recovery or relapse-risk variable than for HRV/EDA stress research.

## 5. What it cannot establish by itself

TMP117 skin/local temperature cannot establish:

- alcohol consumption;
- BAC/BrAC;
- intoxication;
- craving;
- relapse;
- autonomic state;
- withdrawal;
- fever etiology;
- core body temperature.

## 6. Important confounders / artifacts

- Ambient temperature and rapid environmental transitions.
- Airflow/fan/AC.
- Clothing/blankets.
- Direct sun/heat sources.
- Skin contact pressure and air gaps.
- Body site.
- Local perfusion and vasoconstriction.
- exercise/activity.
- sweat/evaporative cooling.
- circadian rhythm.
- fever/infection.
- menstrual/hormonal influences where applicable.
- hydration.
- medications.
- board thermal mass.
- heat from the ESP32, regulators, LEDs or battery.
- sensor response time.

## 7. Potential role when combined with the other modalities

### Temperature + PPG

Low peripheral temperature/vasoconstriction can reduce PPG perfusion and amplitude. Temperature can therefore contextualize optical signal quality and pulse morphology.

### Temperature + EDA

Concurrent heat and EDA elevation may reflect a thermoregulatory contribution; an EDA change without thermal/activity change may have a different interpretation. Neither pattern is alcohol-specific.

### Temperature + IMU

Activity generates heat and changes perfusion. IMU helps separate sedentary temperature drift from exercise-related change.

### Temperature + ECG/HR

A rising HR plus rising temperature during movement has a different meaning from a rising HR at low motion and stable environmental conditions. Fusion can reduce ambiguity, but not eliminate it.

## 8. Evidence gaps / questions still needing investigation

1. Exact physical skin-contact design for the SmartElex board.
2. Thermal response time and offset relative to a reference skin thermometer.
3. Degree of thermal contamination from neighboring electronics.
4. Body-site choice.
5. Whether ambient temperature will be separately measured.
6. Whether “wear detection” is reliable for this placement.
7. Whether personal-baseline temperature trends carry independent AUD-related value after activity/environment are modeled.

---

# Modality 5 — IMU / GY-521 MPU-6050

## 1. What it directly measures

The MPU-6050 is a 6-axis inertial sensor:
- **3-axis accelerometer:** specific force along x/y/z; when stationary this includes gravity.
- **3-axis gyroscope:** angular velocity around x/y/z.

TDK specifies selectable ±2/4/8/16 g accelerometer ranges and ±250/500/1000/2000 °/s gyro ranges ([TDK MPU-6050 product center](https://product.tdk.com/en/search/sensor/mortion-inertial/imu/info?part_no=MPU-6050)).

It does **not** directly measure:
- posture category;
- tremor;
- restlessness;
- gait impairment;
- intoxication;
- withdrawal.

Those are derived patterns.

## 2. What can be derived from it

Potential features include:

- acceleration vector magnitude;
- variance/RMS/energy;
- jerk;
- dominant motion frequency;
- activity counts;
- stillness vs movement;
- step/gait-cycle features;
- sway features;
- posture/orientation relative to gravity when stationary or slowly moving;
- angular-motion features;
- **tremor frequency and band-limited energy**;
- restlessness/activity proxies;
- movement flags for physiological signal-quality assessment.

“Posture” should be described cautiously because an accelerometer can estimate orientation relative to gravity, while robust body posture/activity classification depends on placement and a validated algorithm.

## 3. Physiological / behavioral interpretation

IMU data are principally **behavioral/motor context**, not direct autonomic physiology.

Important interpretations:
- Tremor = rhythmic motor oscillation.
- Gait/sway = motor-control/stability behavior.
- Restlessness = repeated or elevated movement pattern, requiring an operational definition.
- Stillness/activity = context for cardiovascular, EDA and temperature changes.
- Motion-artifact risk = measurement-quality context.

## 4. Evidence linking it to AUD / alcohol withdrawal / recovery

### Alcohol withdrawal tremor — **moderately supported for objective tremor quantification**

A 2017 study explicitly analyzed accelerometer-recorded tremor in alcohol withdrawal and found a logarithmic relationship between clinician CIWA tremor score and tremor-signal energy in approximately the **5–15 Hz** range; the model had **RMSE 0.91** relative to physician ratings ([Biomedical Signal Processing and Control, DOI: 10.1016/j.bspc.2016.11.006](https://doi.org/10.1016/j.bspc.2016.11.006)).

Earlier work using 84 recordings from 61 subjects also reported a relationship between tremor energy in roughly **4.4–10 Hz** and expert-rated withdrawal tremor ([EMBC 2014, DOI: 10.1109/EMBC.2014.6944865](https://doi.org/10.1109/EMBC.2014.6944865)).

The 24-hour ambulatory withdrawal study also found tremor and locomotor activity elevated in withdrawal subjects versus controls ([White et al., 1994](https://doi.org/10.1007/BF01828833)).

**Interpretation:** accelerometry can objectively characterize the **tremor component** of withdrawal. It does not diagnose the complete withdrawal syndrome.

### Restlessness / psychomotor agitation — **indirect-to-exploratory**

Withdrawal can involve psychomotor agitation/restlessness, but generic activity counts are nonspecific. Anxiety, exercise, daily chores, insomnia, pain and stimulant use can all increase movement. A validated operational definition is needed before calling an IMU feature “withdrawal restlessness.”

### Intoxication and gait — **exploratory**

A controlled study in **17 participants** used a lumbar smartphone accelerometer during a standardized 20-step walking task. Subject-specific logistic-regression models classified **BrAC >0.08%** with mean accuracy **92.5%**, versus an 88.2% naïve baseline; x-axis amplitude and variance/gait sway were informative ([Suffoletto et al., 2020, PubMed PMID 32800088](https://pubmed.ncbi.nlm.nih.gov/32800088/)).

This is promising but highly task-specific, small, controlled and person-specific. It cannot be generalized to passive free-living GY-521 data without validation.

The 2026 multimodal pilot also found accelerometer SD increased sober→intoxicated, while step counts did not differ significantly, suggesting that **movement quality/variability may matter more than simple activity amount** in that context ([Kaczor et al., 2026](https://pmc.ncbi.nlm.nih.gov/articles/PMC12805382/)).

### Recovery monitoring — **primarily contextual**

Activity/stillness, sleep-adjacent motion and motor symptoms may be useful contextual channels during rehabilitation, but there is much weaker evidence for IMU-only relapse-risk inference than for its roles in context, gait, tremor and artifact detection.

## 5. What it cannot establish by itself

IMU data cannot establish:

- AUD.
- withdrawal.
- craving.
- relapse.
- alcohol consumption.
- BAC/BrAC.
- stress.
- tremor etiology.

A tremor-like band can arise from physiologic tremor, anxiety, caffeine, medications, essential tremor, fatigue or voluntary movement.

## 6. Important confounders / artifacts

- Sensor body location.
- Sensor orientation.
- Loose mounting.
- voluntary movement.
- exercise.
- transport vibration.
- phone/table vibration if not body-mounted.
- physiologic/essential tremor.
- caffeine/nicotine.
- medication.
- anxiety.
- fatigue.
- age and neurologic/musculoskeletal conditions.
- task type.
- sampling rate/filtering.
- saturation/full-scale selection.
- gyro bias/drift.

## 7. Potential role when combined with the other modalities

### IMU + PPG — strong artifact/context rationale

PPG is notably sensitive to motion. Wearable PPG reviews explicitly describe using simultaneous accelerometer/gyroscope data as reference signals for motion-artifact detection/removal ([wearable PPG review](https://pmc.ncbi.nlm.nih.gov/articles/PMC7612541/); [Ismail et al., 2021](https://link.springer.com/article/10.1186/s13634-020-00714-2)).

### IMU + EDA

EDA wearable measurements are also vulnerable to motion/electrode changes. IMU can mark periods in which an EDA transient may have mechanical origin or be confounded by physical activity.

### IMU + ECG

Cable/electrode motion and muscle activation can corrupt single-lead ECG. Simultaneous high motion can lower confidence in R-peak/HRV features.

### IMU + temperature

Activity context is essential for interpreting skin temperature and HR changes.

### IMU + multimodal alcohol-state research

Motor information is qualitatively different from autonomic channels. Therefore, IMU can add an independent behavioral dimension to fusion models. Current evidence supports this as **research value**, not a validated detector.

## 8. Evidence gaps / questions still needing investigation

1. Exact body placement—wrist, torso, hand and pocket produce different tremor/gait signals.
2. Whether one placement can simultaneously serve tremor characterization and artifact reference for the physiological sensors.
3. Sampling rate needed for withdrawal tremor bands without aliasing.
4. Definition/validation of “restlessness.”
5. Free-living gait inference vs standardized walking tasks.
6. GY-521 breakout variation, mounting resonance and noise.
7. Whether motion features improve AUD-related models after controlling for ordinary physical activity.

---

# Modality 6 — Controller / acquisition layer: ESP32 DevKit V1

The ESP32 is included because it is part of the hardware prototype, but it is **not a physiological sensing modality**.

## 1. What it directly measures

Nothing AUD-specific. The ESP-WROOM-32-family module provides MCU, GPIO, ADC and digital interfaces ([Espressif documentation](https://documentation.espressif.com/esp32-wroom-32_datasheet_en.html)).

In this prototype it can receive:
- digital EDA/PPG/temperature/IMU data via sensor interfaces;
- analog ECG output through its ADC, if that architecture is used.

## 2. What can be derived from it

The controller can compute features in software, but those features belong conceptually to the **source sensor modality**, not to ESP32 physiology.

Potential controller-level outputs:
- timestamps;
- synchronized sample frames;
- packet sequence numbers;
- data-quality flags;
- missing-data indicators;
- filtered/aggregated features;
- fusion windows.

## 3. Physiological interpretation

None intrinsic.

Its major scientific contribution is **measurement integrity**: if timestamps, sample rates or channel synchronization are poor, cross-sensor claims may be invalid even when each sensor works independently.

## 4. Evidence linking it to AUD / alcohol withdrawal / recovery

None. ESP32 is not an AUD measurement technology; it is an acquisition platform.

## 5. What it cannot establish by itself

Everything physiological/clinical unless supplied by an actual sensor and validated processing pipeline.

## 6. Important confounders / artifacts

For scientific data quality rather than physiology:
- inconsistent sample scheduling;
- asynchronous sensor clocks;
- ADC nonlinearity/noise;
- packet loss;
- FIFO overrun;
- timestamp jitter;
- Wi‑Fi/Bluetooth activity creating power/RF noise;
- power-supply coupling;
- generic DevKit carrier-board differences.

## 7. Potential role when combined with the other modalities

- common timestamp domain;
- trigger/synchronization logic;
- acquisition buffering;
- data-quality metadata;
- multimodal windows;
- storing raw data before feature extraction;
- cross-sensor beat/motion associations.

**For ECG+PPG PAT especially, synchronization error directly becomes physiological timing error.**

## 8. Evidence gaps / questions still needing investigation

1. Exact 30-pin DevKit carrier manufacturer/revision.
2. ECG ADC effective resolution/noise at the chosen input range.
3. Timestamping architecture and sensor-clock synchronization.
4. Whether acquisition and wireless transmission interfere with each other.
5. Data-loss behavior and quality metadata.
6. Whether raw data can be retained so future algorithms are not constrained by premature feature extraction.

---

# Cross-sensor relationships that are specifically worth separating

## A. ECG ↔ PPG: what is genuinely available?

### 1. Heart rate / pulse rate validation — **strongly defensible**

Under good signal conditions, ECG R-R timing and PPG pulse timing should correspond beat-for-beat, with the peripheral pulse occurring after electrical activation. This provides:
- missed/extra pulse checks;
- optical beat-quality validation;
- detection of pulse deficits or corrupted windows as a research signal-quality issue.

It does **not** make the two waveforms interchangeable.

### 2. HRV vs PRV — **related, not identical**

The ECG RR series reflects cardiac electrical timing. PPG inter-pulse timing includes peripheral vascular and electromechanical timing variation. Reviews explicitly caution against universal PRV=HRV equivalence ([Mejía-Mejía et al., 2020](https://doi.org/10.1088/1361-6579/ab998c)).

### 3. Pulse Arrival Time (PAT) — **technically valid cross-sensor feature**

With synchronized signals:

`PAT = time(PPG fiducial point) – time(ECG R-wave)`

PAT contains:
- pre-ejection/electromechanical delay;
- vascular pulse propagation time.

Therefore ECG+one distal PPG channel does **not** directly provide pure PTT. The distinction is documented in PPG/hemodynamics literature ([Mohammadpoor Faskhodi et al., 2024](https://doi.org/10.1371/journal.pone.0298354); [multi-site PPG review](https://pmc.ncbi.nlm.nih.gov/articles/PMC6912608/)).

### 4. Blood pressure — **claim to avoid at present**

PAT/PTT correlates with blood pressure under many conditions, but cuffless BP requires subject/device-specific calibration and rigorous validation. The candidate prototype cannot simply transform ECG+PPG timing into clinically meaningful BP.

### 5. Possible AUD meaning of PAT — **indirect/speculative**

Alcohol, stress and autonomic changes can alter cardiac contractility, vascular tone and BP, all of which can affect PAT. But direct evidence validating PAT as an AUD, craving, withdrawal or relapse marker is insufficient. It is best categorized as an **exploratory cardiovascular coupling feature**.

---

## B. IMU ↔ ECG/PPG/EDA: can motion information materially improve interpretation?

### PPG — **yes, strongly supported for signal-quality use**

Motion artifact is one of the central limitations of wearable PPG. Reviews describe accelerometer/gyroscope reference signals for detecting or suppressing motion contamination ([Charlton et al., wearable PPG review](https://pmc.ncbi.nlm.nih.gov/articles/PMC7612541/); [PPG motion-artifact review](https://link.springer.com/article/10.1186/s13634-020-00714-2)).

### EDA — **yes, useful but not perfect**

Wearable EDA is affected by movement, contact and pressure changes. A recent review specifically lists movement among major physiological/artifactual influences ([Bari et al., 2026](https://doi.org/10.1088/1361-6579/ae7bae)). IMU can mark high-risk periods, though it cannot identify every electrode artifact.

### ECG — **yes, primarily as a quality/context flag**

Movement and muscle activity can introduce baseline and high-frequency contamination. IMU data can grade confidence in ECG-derived RR/HRV windows, especially in ambulatory use.

### Scientific consequence

A useful architecture concept is not “IMU corrects the physiology,” but:

**IMU supplies an independent motion/context stream that changes confidence in physiological features.**

For example:
- high EDA + high HR + high acceleration → physical activity is a major competing explanation;
- high EDA + HR change + low motion → autonomic arousal becomes more plausible, though still nonspecific;
- PPG HR jump + high motion but stable ECG HR → optical artifact becomes plausible;
- ECG and PPG both show beat-rate change at low motion → increased confidence that the rate change is physiological.

None of these patterns identifies alcohol by itself.

---

# Specificity problem: why multimodal does not automatically mean alcohol-specific

All five physiological/behavioral modalities are **non-specific**:

- HR/HRV respond to exercise, emotion, fever, dehydration, stimulants, sleep and medication.
- EDA responds to arousal, heat, physical effort, pain, startle and electrode/motion effects.
- PPG responds to HR plus vascular tone/perfusion/contact/motion.
- Skin temperature responds to environment, perfusion, activity, circadian state and illness.
- IMU responds to all movement, not just tremor/intoxication/withdrawal.

Multimodal fusion can help because different causes produce different combinations and because some sensors identify artifacts in others. But fusion does **not** create biochemical specificity from nonspecific signals.

For detecting actual alcohol exposure, direct ethanol measures such as breath alcohol, blood alcohol or validated transdermal alcohol sensing are conceptually different: they measure alcohol or a closer chemical proxy. None of the current candidate sensors directly measures ethanol.

Therefore any future “alcohol detected” claim using only these channels would require **independent ground-truth alcohol labels and prospective validation**, not physiological plausibility alone.

---

# Longitudinal / rehabilitation interpretation

The candidate set may be more scientifically defensible for **temporal change and within-person context** than for absolute one-shot classification.

Potential research questions include:
- Does resting HRV change over weeks of abstinence?
- Do cue sessions evoke reproducible phasic EDA responses within the same person?
- Does tremor energy decrease during monitored withdrawal treatment?
- Do motion-adjusted HR/EDA patterns correlate with self-reported stress episodes?
- Do PPG/ECG disagreement rates reveal poor wear conditions?
- Do skin-temperature shifts explain changes in peripheral PPG amplitude?
- Does a person’s own baseline improve interpretation more than a population threshold?

This framing is consistent with the high interindividual variability seen in the 100-day AUD recovery study ([van Lier et al., 2022](https://pmc.ncbi.nlm.nih.gov/articles/PMC9287639/)).

---

# Evidence Map — Not a Design Decision

## Strongly supported physiological measurements

These statements concern **what the modalities can physically/physiologically measure**, not AUD diagnosis.

1. **ECG / AD8232**
   - Single-lead conditioned cardiac biopotential is a valid measurement concept.
   - R peaks → HR and RR intervals are well-established.
   - RR series → standard HRV metrics when acquisition/processing requirements are met.
   - Source: [Analog Devices AD8232](https://www.analog.com/en/products/ad8232.html); [HRV Task Force](https://doi.org/10.1161/01.CIR.93.5.1043).

2. **PPG / MAX30101**
   - Reflected optical signal captures pulsatile peripheral blood-volume dynamics.
   - Pulse rate can be derived under adequate signal quality.
   - PRV is a valid pulse-interval variability measure but is not universally equivalent to ECG HRV.
   - Sources: [Allen 2007](https://doi.org/10.1088/0967-3334/28/3/R01); [MAX30101](https://www.analog.com/en/products/max30101.html); [Mejía-Mejía 2020](https://doi.org/10.1088/1361-6579/ab998c).

3. **EDA / GSR**
   - EDA reflects sympathetic sudomotor activity through sweat-gland-related skin electrical change.
   - Tonic and phasic decomposition is standard.
   - Source: [Boucsein 2012](https://doi.org/10.1111/j.1469-8986.2012.01384.x); [Posada-Quintero & Chon 2020](https://doi.org/10.3390/s20020479).

4. **TMP117**
   - High-accuracy local temperature sensing is well established at the IC.
   - Skin-temperature interpretation depends on thermal coupling and isolation.
   - Source: [TI TMP117](https://www.ti.com/product/TMP117); [TI wearable design guidance](https://www.ti.com/document-viewer/lit/html/SSZT563).

5. **MPU-6050**
   - 3-axis acceleration and 3-axis angular velocity are directly measured.
   - Motion/tremor/activity features can be derived with appropriate placement and processing.
   - Source: [TDK MPU-6050](https://product.tdk.com/en/search/sensor/mortion-inertial/imu/info?part_no=MPU-6050).

## Potentially useful AUD-related measurements

### ECG HRV — **strongest AUD-specific evidence in the candidate set**
- Lower HRV in AUD vs controls has meta-analytic support, although heterogeneity is high.
- Best supported as a **population-level autonomic association / longitudinal research feature**, not an AUD diagnostic.
- Source: [Cheng et al. 2019](https://doi.org/10.1016/j.drugalcdep.2019.05.030).

### Withdrawal tremor quantified by IMU — **moderately supported**
- Accelerometer tremor energy has been linked to clinician-rated withdrawal tremor.
- Measures one motor symptom, not the whole syndrome.
- Source: [DOI 10.1016/j.bspc.2016.11.006](https://doi.org/10.1016/j.bspc.2016.11.006).

### EDA phasic response to alcohol cues — **exploratory-to-moderate**
- Alcohol-associated VR cues increased NS-SCR frequency in a 61-patient study; subjective craving–EDA correlations were only ~0.20–0.21.
- Source: [Lütt et al. 2026](https://doi.org/10.2196/84156).

### HR/EDA/skin-temperature/motion trends during intoxication — **exploratory multimodal**
- A 28-person controlled pilot showed significant sober→intoxicated changes and up to 0.80 multimodal classification accuracy.
- This does not establish generalizable individual alcohol detection.
- Source: [Kaczor et al. 2026](https://pmc.ncbi.nlm.nih.gov/articles/PMC12805382/).

### HRV + EDA in recovery stress monitoring — **exploratory**
- Ambulatory feasibility and associations with self-reported outcomes have been demonstrated in a small AUD-recovery cohort.
- Source: [Alinia et al. 2021](https://doi.org/10.2196/27891).

## Mainly contextual / artifact-rejection measurements

1. **IMU**
   - physical activity context;
   - motion-artifact flag for PPG;
   - useful quality flag for ECG/EDA;
   - posture/orientation context.

2. **Skin temperature**
   - peripheral perfusion/thermal context for PPG;
   - thermoregulatory context for EDA;
   - possible wear/contact context only after validation.

3. **ECG ↔ PPG agreement**
   - cross-validation of pulse timing and quality.

4. **ESP32 timestamps/quality metadata**
   - necessary for valid multimodal analysis but no physiological meaning itself.

## Exploratory relationships needing further validation

- ECG+PPG **PAT** as an AUD/autonomic feature.
- PPG morphology as an alcohol-related vascular marker.
- PRV as a substitute for AUD-relevant ECG HRV in ambulatory motion.
- Skin temperature as an intoxication/withdrawal/recovery marker.
- Free-living gait impairment as an alcohol-state detector.
- “Restlessness” derived from a single IMU.
- Multimodal physiological prediction of craving.
- Multimodal prediction of imminent lapse/relapse.
- Individualized physiological baselines for early-warning intervention.
- Any model trained on one device/population being transferred to these exact breakout boards without revalidation.

## Claims that should currently be avoided

1. **“The prototype diagnoses AUD.”**
2. **“EDA detects craving.”**
3. **“EDA detects stress”** without qualifying that it is a nonspecific sympathetic sudomotor/arousal signal.
4. **“HRV detects relapse”** or reliably predicts imminent relapse in an individual.
5. **“HRV measures sympathetic/parasympathetic balance”** using LF/HF as a simple ratio.
6. **“AD8232 directly measures HRV.”** It measures conditioned biopotential; HRV is derived.
7. **“MAX30101 directly measures heart rate/SpO₂.”** The raw IC output is optical data; HR/SpO₂ require algorithms, and clinical accuracy requires validation.
8. **“PPG PRV is the same as ECG HRV.”**
9. **“ECG + PPG gives PTT.”** With ECG R-wave plus one distal PPG it gives **PAT**, which includes PEP.
10. **“ECG + PPG measures blood pressure.”** Not without validated calibration/modeling.
11. **“TMP117 measures core body temperature.”**
12. **“The legacy tinyGSR reports absolute µS.”** ProtoCentral says absolute units cannot be recovered universally from the original trimmer-set hardware.
13. **“IMU tremor means alcohol withdrawal.”**
14. **“Gait impairment means intoxication.”**
15. **“This sensor set detects recent alcohol consumption.”** None of the candidates directly senses ethanol.
16. **“Multimodal fusion makes the signals alcohol-specific.”**
17. **“A population-level correlation can be used as an individual clinical decision rule.”**

---

# Evidence-strength matrix by proposed AUD relationship

| Candidate feature | AUD generally | Acute withdrawal | Autonomic dysregulation | Craving/stress/arousal | Relapse-risk research | Intoxication/recent alcohol | Recovery monitoring |
|---|---|---|---|---|---|---|---|
| ECG HR | Indirect/moderate | Clinically relevant but nonspecific | Moderate | Indirect | Exploratory | Moderate acute-response evidence | Context/trend |
| ECG HRV (RMSSD/SDNN etc.) | **Well established group association** | **Mixed** | **Moderate-to-strong** | Moderate for stress; exploratory for craving | Exploratory-to-moderate, heterogeneous | Moderate acute alcohol effect | Moderate research value for longitudinal change |
| PPG pulse rate | Indirect/moderate | Nonspecific | Indirect | Indirect | Insufficient alone | Exploratory/moderate physiological response | Context/trend |
| PPG PRV | Indirect; cannot inherit HRV evidence automatically | Insufficient | Moderate as cardiovascular variability, but not HRV-equivalent | Exploratory | Insufficient | Exploratory | Exploratory |
| PPG morphology/perfusion | Insufficient | Insufficient | Physiologically relevant but nonspecific | Indirect | Insufficient | Exploratory | Exploratory |
| EDA tonic/phasic | Indirect | Physiologically plausible; limited direct evidence | Strong sympathetic-sudomotor meaning | **Moderate for arousal; exploratory/moderate for alcohol-cue reactivity** | Insufficient as predictor | Exploratory | Exploratory feasibility |
| Skin temperature trend | Indirect | Exploratory | Indirect/autonomic-vascular | Indirect | Insufficient | Exploratory | Contextual |
| IMU tremor energy | Not AUD-specific | **Moderately supported for tremor component** | Not primary | Indirect | Insufficient | Not primary | Symptom/context trend |
| IMU gait/sway | Not AUD-specific | Indirect | Not primary | Not primary | Insufficient | **Exploratory controlled evidence** | Context |
| General IMU activity | Context | Context/restlessness hypothesis | Context | Major confounder/context | Context | Context | Context |
| ECG+PPG PAT | Indirect | Insufficient | Physiologically plausible | Exploratory | Insufficient | Insufficient | Exploratory |
| Multimodal HR+EDA+temp+IMU | Not diagnostic | Exploratory | Potentially informative | Potentially informative | Exploratory | **Exploratory pilot support** | Exploratory/personalized trends |

---

# Practical scientific interpretation examples

These examples are deliberately phrased as **hypotheses**, not decisions.

### Example 1: HR rises, EDA rises, IMU shows vigorous movement
Most defensible interpretation: **physiological arousal and cardiovascular demand occurred during physical activity.** Alcohol, stress, craving, withdrawal and exercise are not distinguishable from these signals alone.

### Example 2: HR rises, EDA phasic activity rises, IMU remains low, temperature stable
Most defensible interpretation: **a low-motion autonomic/arousal event may have occurred.** Craving is one possible contextual explanation only if contemporaneous cue/self-report data support it.

### Example 3: PPG pulse rate jumps, IMU motion is high, ECG HR remains stable
Most defensible interpretation: **PPG motion artifact or pulse-detection error becomes likely.**

### Example 4: ECG HR and PPG pulse rate rise concordantly, IMU low
Most defensible interpretation: **the rate increase is more likely physiological than an optical artifact.** Cause remains nonspecific.

### Example 5: withdrawal patient shows increasing hand tremor-band energy
Most defensible interpretation: **objective tremor magnitude has increased.** This may contribute to withdrawal assessment but does not establish overall withdrawal severity or cause.

### Example 6: skin temperature rises during a session
Most defensible interpretation: **local thermal/perfusion state changed.** Environmental heat, contact, activity, vasodilation, illness and alcohol are competing explanations.

---

# Key unresolved evidence questions before any later design decision

1. **Exact board identity:** photographs/schematic matching for tinyGSR 11/22 and CJMCU VS82.
2. **Sensor placement:** body site determines PPG, EDA, temperature and IMU meaning.
3. **Common time base:** quantify ECG↔PPG and IMU↔physiology synchronization error.
4. **Ground truth:** define what future studies would use as reference labels—CIWA-Ar item/total, BrAC/BAC, craving EMA, observed lapse, clinician assessment, etc.
5. **Personal baseline:** test whether within-person change outperforms absolute thresholds.
6. **Motion-quality policy:** decide whether high-motion windows are rejected, down-weighted, or modeled.
7. **Legacy EDA comparability:** determine whether only within-session relative features are defensible.
8. **PPG objective:** decide whether the research question is pulse timing, PRV, morphology, or SpO₂; each requires different validation.
9. **Temperature objective:** separate local skin trend from any claim about core temperature.
10. **Tremor placement/protocol:** determine whether the chosen IMU location can capture withdrawal tremor reproducibly.
11. **Confounder capture:** medication, nicotine/caffeine, exercise, respiration, ambient conditions, sleep, hydration and illness may need annotation.
12. **Clinical vs research use:** none of these breakout boards should be assumed to be a certified clinical monitor merely because the underlying IC is used in medical-capable products.

---

# Selected primary and high-priority sources

## Hardware / manufacturer documentation

1. **Analog Devices — AD8232 Single-Lead ECG Front End.**  
   https://www.analog.com/en/products/ad8232.html

2. **Analog Devices — MAX30101 Pulse Oximeter / Heart-Rate Optical Sensor.**  
   https://www.analog.com/en/products/max30101.html  
   Datasheet: https://www.analog.com/media/en/technical-documentation/data-sheets/MAX30101.pdf

3. **Texas Instruments — TMP117 high-accuracy local digital temperature sensor.**  
   https://www.ti.com/product/TMP117

4. **Texas Instruments — wearable temperature design considerations.**  
   https://www.ti.com/document-viewer/lit/html/SSZT563

5. **TDK Product Center — MPU-6050.**  
   https://product.tdk.com/en/search/sensor/mortion-inertial/imu/info?part_no=MPU-6050

6. **Espressif — ESP32-WROOM-32 datasheet.**  
   https://documentation.espressif.com/esp32-wroom-32_datasheet_en.html

7. **ProtoCentral — tinyGSR v3 page including original-board history and absolute-unit limitation.**  
   https://protocentral.com/product/protocentral-tinygsr-gsr-eda-digital-output-sensor-board-qwiic-stemma-qt/

8. **ProtoCentral legacy tinyGSR tutorial.**  
   https://www.hackster.io/protocentral/measuring-emotions-with-gsr-using-tinygsr-arduino-1f038e

9. **SmartElex MAX30101 reseller listing used only for breakout-level details where primary SmartElex documentation was not located.**  
   https://www.theengineerstore.in/products/smartelex-photodetector-breakout-max30101

10. **SmartElex TMP117 reseller listing used only to corroborate exact breakout naming where primary SmartElex documentation was not located.**  
    https://www.theengineerstore.in/collections/sensors/products/smartelex-high-precision-temperature-sensor-tmp117

## Physiological measurement / standards / reviews

11. **Task Force of ESC/NASPE. Heart rate variability: standards of measurement, physiological interpretation and clinical use. Circulation. 1996.**  
    DOI: https://doi.org/10.1161/01.CIR.93.5.1043

12. **Laborde S, Mosley E, Thayer JF. HRV and cardiac vagal tone in psychophysiological research. Front Psychol. 2017.**  
    DOI: https://doi.org/10.3389/fpsyg.2017.00213

13. **Billman GE. The LF/HF ratio does not accurately measure cardiac sympatho-vagal balance. Front Physiol. 2013.**  
    DOI: https://doi.org/10.3389/fphys.2013.00026

14. **Allen J. Photoplethysmography and its application in clinical physiological measurement. Physiol Meas. 2007.**  
    DOI: https://doi.org/10.1088/0967-3334/28/3/R01

15. **Mejía-Mejía E, et al. Pulse rate variability in cardiovascular health: applications and relationship with HRV. Physiol Meas. 2020.**  
    DOI: https://doi.org/10.1088/1361-6579/ab998c

16. **Xu S, et al. Accuracy of PPG-derived PRV vs ECG-derived HRV: systematic review/meta-analysis. Sensors. 2026.**  
    DOI: https://doi.org/10.3390/s26165192

17. **Charlton PH, et al. Assessing hemodynamics from the PPG to gain insights into vascular age. 2022.**  
    DOI: https://doi.org/10.1152/ajpheart.00392.2021

18. **Mohammadpoor Faskhodi M, et al. Pulse arrival time using PPG and ECG. PLoS One. 2024.**  
    DOI: https://doi.org/10.1371/journal.pone.0298354

19. **Boucsein W, et al. Publication recommendations for electrodermal measurements. Psychophysiology. 2012.**  
    DOI: https://doi.org/10.1111/j.1469-8986.2012.01384.x

20. **Posada-Quintero HF, Chon KH. Innovations in EDA data collection/signal processing. Sensors. 2020.**  
    DOI: https://doi.org/10.3390/s20020479

21. **Bari DS, Aldosky HYY, Martinsen ØG. Electrodermal activity measurements: a review of artifacts. Physiol Meas. 2026.**  
    DOI: https://doi.org/10.1088/1361-6579/ae7bae

22. **Wearable PPG cardiovascular monitoring review — motion references including accelerometry/gyroscope.**  
    https://pmc.ncbi.nlm.nih.gov/articles/PMC7612541/

23. **Ismail S, Akram U, Siddiqi I. Heart-rate tracking in PPG affected by motion artifacts: review. 2021.**  
    https://link.springer.com/article/10.1186/s13634-020-00714-2

24. **FDA — Pulse Oximeter Basics / limitations.**  
    https://www.fda.gov/consumers/consumer-updates/pulse-oximeter-basics

## AUD / alcohol / withdrawal / recovery evidence

25. **Cheng YC, Huang YC, Huang WL. Heart rate variability as a potential biomarker for AUD: systematic review/meta-analysis. Drug Alcohol Depend. 2019.**  
    DOI: https://doi.org/10.1016/j.drugalcdep.2019.05.030

26. **Ralevski E, et al. Heart rate variability in alcohol use: a review. Pharmacol Biochem Behav. 2019.**  
    DOI: https://doi.org/10.1016/j.pbb.2018.12.003

27. **Julian TH, et al. Alcohol-induced autonomic dysfunction: systematic review. Clin Auton Res. 2020.**  
    DOI: https://doi.org/10.1007/s10286-019-00618-8

28. **Bär KJ, et al. HRV and sympathetic skin response in acute alcohol withdrawal. Alcohol Clin Exp Res. 2006.**  
    DOI: https://doi.org/10.1111/j.1530-0277.2006.00191.x

29. **White JM, et al. 24-hour monitoring of tremor, sweating, skin temperature and locomotor activity in alcohol withdrawal. Clin Auton Res. 1994.**  
    DOI: https://doi.org/10.1007/BF01828833

30. **Evaluation of alcohol intoxication/withdrawal based on tremor signals. Biomed Signal Process Control. 2017.**  
    DOI: https://doi.org/10.1016/j.bspc.2016.11.006

31. **Time-frequency withdrawal tremor study. EMBC 2014.**  
    DOI: https://doi.org/10.1109/EMBC.2014.6944865

32. **Lütt A, et al. Multimodal psychophysiological assessment of craving during alcohol VR cue exposure. JMIR Serious Games. 2026.**  
    DOI: https://doi.org/10.2196/84156

33. **Cue exposure in alcohol-dependent patients: heterogeneous subjective/physiological responding.**  
    PubMed: https://pubmed.ncbi.nlm.nih.gov/10943912/

34. **Alinia P, et al. Physiological signals in adults in AUD recovery: development/usability study. JMIR Form Res. 2021.**  
    DOI: https://doi.org/10.2196/27891

35. **van Lier HG, et al. Physiology, craving and lapses during 100 days of daily-life monitoring. 2022.**  
    PubMed/PMC: https://pmc.ncbi.nlm.nih.gov/articles/PMC9287639/

36. **Kaczor EE, et al. Detecting ethanol intoxication and impairment using wearable biosensors. 2026.**  
    PubMed/PMC: https://pmc.ncbi.nlm.nih.gov/articles/PMC12805382/

37. **Suffoletto B, et al. Smartphone accelerometers to sense gait impairment due to alcohol intoxication. J Stud Alcohol Drugs. 2020.**  
    PubMed: https://pubmed.ncbi.nlm.nih.gov/32800088/

38. **Risbo A, et al. Human body temperature and controlled cold exposure during experimental alcohol intoxication. 1981.**  
    DOI: https://doi.org/10.1111/j.1399-6576.1981.tb01639.x

39. **Morris NB, Ravanelli N, Chaseling GK. Alcohol and thermoregulatory responses to heat stress: systematic scoping review. Environ Health. 2024.**  
    DOI: https://doi.org/10.1186/s12940-024-01113-y

---

## Bottom-line research interpretation

The evidence supports treating these components as a collection of **complementary, mostly nonspecific physiological and behavioral channels**:

- ECG gives the strongest route to standardized HR/HRV.
- PPG gives peripheral pulse and vascular/perfusion information and enables ECG↔PPG timing/cross-validation.
- EDA gives sympathetic sudomotor arousal, with promising but non-one-to-one alcohol-cue relationships.
- Temperature gives peripheral thermal/perfusion context, with alcohol effects that are real but highly confounded.
- IMU gives motor phenotype and, importantly, motion/artifact context; withdrawal tremor is the most directly AUD-specific IMU use supported by current evidence.
- ESP32 provides acquisition/synchronization rather than physiology.

The literature does **not** justify converting any one of these signals into a direct statement that a person is craving alcohol, withdrawing, intoxicated, about to relapse, or has AUD. The scientifically defensible next stage is to preserve raw measurements, define ground truth and confounders, and test whether **within-person multimodal temporal patterns** add value beyond each channel alone.

**This remains an evidence map, not a design decision.**
