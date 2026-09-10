# AUD Multimodal Hardware — Novelty Hypothesis & Prior-Art Map

**Status:** novelty-space exploration / engineering research input  
**Date:** 2026-09-10  
**Repository context reviewed:** `docs/aud_multimodal_hardware_evidence_map.md`, `docs/aud_sensor_role_definition.md`, and `docs/aud_hardware_form_factor_options.md`

## 1. Purpose and Scope

This document explores candidate technical novelty hypotheses and their exposure to existing prior art. It does not establish patentability, select the final invention, define the final hardware architecture, or constitute legal advice.

The goal is to identify where the present AUD-support hardware project appears conventional, where a technically meaningful distinction might still be possible, and what must be specified or tested before a serious novelty assessment can be made.

This document deliberately does **not**:

- declare that any concept is novel;
- provide a patentability or freedom-to-operate opinion;
- select a final sensor set, body placement, form factor, or acquisition topology;
- assume that all modalities must be co-located;
- treat the current breakout-board prototype as a finished wearable;
- draft patent claims, a patent abstract, or an invention disclosure;
- infer alcohol specificity from nonspecific physiology;
- recommend filing a patent.

A negative finding is useful. In particular, this search finds substantial prior-art exposure around multimodal wrist wearables, motion-assisted artifact handling, generic body-sensor synchronization, substance-use/craving wearables, and personalized supervised-to-ambulatory monitoring.

### 1.1 Patent-analysis terminology used here

The following concepts are kept separate:

- **Novelty / anticipation:** whether one earlier disclosure appears to contain all essential elements of a particular technical proposition.
- **Inventive step / non-obviousness:** whether a proposed distinction could still be an obvious combination or modification when several references and common technical knowledge are considered together.
- **Mere aggregation:** known sensors or subsystems placed together without a new technical interaction or effect.
- **New use / intended purpose:** applying known sensing technology to AUD does not automatically create a technical invention.
- **Engineering implementation:** concrete acquisition, synchronization, physical, calibration, signal-quality, or cross-modal mechanisms may matter more than the broad clinical label.

The exposure labels used in this document are evidence summaries, not legal conclusions:

- **VERY HIGH exposure** — very close prior art exists; the broad hypothesis is unlikely to be a useful inventive core without substantial additional distinction.
- **HIGH exposure** — most important components/interactions are already represented in prior art.
- **MODERATE exposure** — relevant art exists, but a narrower technical distinction may remain if it is concretely specified and demonstrated.
- **LOW exposure FOUND IN THIS SEARCH** — no very close disclosure was found in this search; this does **not** mean novel.
- **INDETERMINATE** — the project is too underspecified for meaningful comparison.

---

## 2. Project State Derived from Repository

The repository currently defines an **exploratory multimodal physiological acquisition prototype**, not a validated AUD detector.

### 2.1 Candidate hardware and roles

The present candidate stack is:

1. ESP32 DevKit V1, 30-pin, ESP-WROOM-32-family controller.
2. ProtoCentral tinyGSR legacy GSR/EDA board, PCB marking 11/22.
3. CJMCU-8232 AD8232 single-lead ECG module, PCB marking VS82.
4. SmartElex MAX30101 PPG breakout.
5. SmartElex TMP117 temperature breakout.
6. GY-521 MPU-6050 accelerometer + gyroscope board.

The repository carefully distinguishes **IC capability**, **breakout-board implementation**, and **verified behavior of the exact physical module**. Important board-level questions remain unresolved, including exact ECG filter/gain networks, the legacy tinyGSR transfer function and comparability, the SmartElex optical/thermal constructions, GY-521 board details, ESP32 ADC/timing behavior, and the mechanical coupling that would be required for real body-worn use.

### 2.2 What the channels actually mean

The repository's scientifically conservative interpretation is retained:

- **ECG** supplies cardiac electrical timing; HR and HRV are derived, not directly measured by the AD8232.
- **PPG** supplies peripheral optical pulse/perfusion information; PRV is not automatically equivalent to ECG HRV.
- **EDA** supplies nonspecific sympathetic sudomotor/arousal information; it does not directly measure stress cause, craving, relapse, or alcohol.
- **Temperature** is presently best treated as local/peripheral thermal and perfusion context; TMP117 die accuracy does not imply skin- or core-temperature accuracy in the prototype.
- **IMU** supplies motion, activity, tremor, and artifact context; withdrawal tremor is the strongest AUD-related IMU direction in the repository, but tremor is not AUD-specific.
- **ESP32** is acquisition/timing/transport infrastructure and has no physiological novelty by itself.

None of the candidate sensors directly measures ethanol. Multimodal fusion cannot create biochemical specificity from nonspecific channels.

### 2.3 Multimodal relationships already supported or left open

The existing documents identify several technically meaningful relationships without treating them as established inventions:

- ECG can provide an electrical beat-timing reference against which distal PPG can be checked.
- ECG + distal PPG can support pulse-arrival-time style timing only if the timebase is sufficiently controlled; this is **PAT**, not automatically pure PTT.
- IMU data can identify or grade periods where ECG, PPG, or EDA may be contaminated by motion; it does not automatically repair those signals.
- Local IMU placement may matter because one IMU cannot necessarily represent motion at a remote electrode, optical interface, or EDA contact.
- Temperature may contextualize PPG perfusion/amplitude and EDA state, but the exact technical use is not yet defined.
- Within-person, baseline-relative and longitudinal analysis may be more defensible than universal thresholds, but the baseline protocol is unresolved.

### 2.4 Physical embodiments remain open

The form-factor document deliberately keeps multiple paths alive:

- single wrist;
- wrist + chest;
- wrist + finger/hand + controller;
- chest patch + wrist peripheral module;
- distributed multi-node body-worn research system;
- semi-wearable research harness using the current breakouts;
- session-configurable modular architecture with a lower-burden ambulatory core and temporary higher-fidelity peripheral module;
- exploratory ear + torso + hand/wrist arrangements.

It also distinguishes maturity levels:

- **L1:** bench/tethered breakout prototype;
- **L2:** body-worn/distributed prototype still using development boards/breakouts;
- **L3:** integrated custom wearable/custom PCB.

The novelty analysis must therefore avoid comparing an imaginary finished smartwatch against prior art when the repository has not selected one.

### 2.5 Important unresolved engineering tensions

The repository leaves open:

- physiological fidelity versus comfort and wear burden;
- central versus distributed acquisition;
- long analog leads versus local front-end placement;
- body-spanning I²C versus local buses plus wireless/digital links;
- one global IMU versus sensor-local motion references;
- thermal coupling to skin versus self-heating from electronics;
- signal-validity-first placement versus aesthetic integration;
- controlled-session measurements versus passive ambulatory measurements;
- absolute versus within-person/within-device feature comparability.

These unresolved choices are not defects in the project at this stage; they are exactly the dimensions that must be specified before a narrower novelty hypothesis can be judged.

---

## 3. Methodology

### 3.1 Repository-first decomposition

The search began by reading the complete `docs/` directory and extracting the project's actual measurement roles, open questions, physical archetypes, and cross-modal relationships. Novelty axes were then derived from those documents rather than from a generic idea of an "AUD wearable."

### 3.2 Prior-art search strategy

The search was intentionally decomposed into technical subproblems rather than asking only whether an exact "AUD ECG + PPG + EDA + temperature + IMU device" exists.

Search families included:

- multimodal physiological wearables;
- EDA + PPG + temperature + motion wearables;
- ECG + PPG timing and synchronization;
- distributed wireless body-sensor networks;
- PPG/ECG motion-artifact detection and confidence scoring;
- sensor-local IMUs and local artifact references;
- temperature-assisted PPG/contact compensation;
- personalized calibration and supervised-to-unsupervised monitoring;
- wrist/finger and wrist/chest multi-site systems;
- AUD recovery, craving, relapse, and withdrawal monitoring;
- objective alcohol-withdrawal tremor measurement;
- direct transdermal alcohol measurement and alcohol-monitoring wearables.

Patent terminology, CPC/IPC concepts, family members, backward references, assignees, publication numbers, and priority dates were used where useful.

### 3.3 Sources searched

The investigation used and cross-checked, where accessible:

- Google Patents full records and family/citation data;
- WIPO/PCT publication records and PATENTSCOPE-oriented searches;
- EPO/EP publication data and Espacenet-linked family records;
- USPTO-linked U.S. patent/application records and official publication metadata where accessible;
- Indian patent/publication searches and secondary Indian application records where official indexing was not reliably accessible;
- peer-reviewed literature through PubMed/PMC and publisher pages;
- commercial technical documentation, especially Empatica;
- academic and clinical research systems.

### 3.4 Important limitations

This is a substantial engineering prior-art survey, not a professional patent search.

Specific limitations include:

1. **Patent database coverage and indexing:** synonyms, translations, non-English claims, unpublished applications, and classification differences can hide relevant art.
2. **Legal status:** status fields in aggregator records are informational and may be incomplete; they are not relied on as legal opinions.
3. **Indian application verification:** application **202041020428**, described below, was located through a secondary Indian patent-information source. Its bibliographic/content details should be confirmed directly in official Indian Patent Office records before legal reliance.
4. **Priority versus publication:** priority dates are recorded separately from publication dates. A priority date is not automatically the date on which subject matter became publicly available.
5. **Later publications:** several 2024–2026 publications are useful for landscape and obviousness-risk analysis but may be too late to be prior art relative to any hypothetical earlier project date. No filing date for this project is assumed here.
6. **No claim construction:** patent claims were inspected for technical overlap, but no formal claim interpretation, validity, or infringement analysis was performed.
7. **Repository underspecification:** several hypotheses remain too broad to test against a single reference because the project has not yet defined the relevant mechanism.

---

## 4. Novelty Axes

### Axis NA-01 — Sensor/modal combination

**What appears conventional:** Combining PPG, EDA, temperature and motion in a wrist wearable is well represented by both patent literature and commercial products. ECG can also be combined with PPG and EDA in wearable systems.

**What the repository proposes/leaves open:** The present candidate set contains ECG, PPG, EDA, temperature and IMU but does not require them to be co-located.

**Possible technical distinction:** None is evident from the sensor list alone.

**What must be specified:** A technical interaction that causes one channel to alter acquisition, validation, confidence, calibration, timing, or interpretation of another.

**Current assessment:** The **sensor combination itself should not be treated as the inventive core**.

### Axis NA-02 — Anatomical distribution and physical arrangement

**What appears conventional:** Wrist, finger/ring, chest patch/strap, and multi-node body sensor arrangements are all established.

**Repository state:** Placement is intentionally open and driven by signal validity: chest is attractive for ECG; finger/palm for PPG/EDA quality; wrist for continuous multimodal convenience; local IMUs may be needed.

**Possible technical distinction:** A distribution that is necessary for a measurable technical effect—not merely comfort—could matter.

**What must be specified:** Exact body sites, which node owns each sensor/front end, and what measurable improvement the distribution creates.

### Axis NA-03 — Acquisition topology

**What appears conventional:** Central hubs, distributed nodes, local digitization, wireless body-area networks, and master/slave or peer synchronization are mature concepts.

**Repository state:** Central ESP32 versus distributed nodes is unresolved.

**Possible distinction:** A topology might matter if it simultaneously solves a project-specific analog integrity problem and preserves a quantified cross-node timing requirement.

**What must be specified:** analog lead lengths, ADC locations, local clocks, interfaces, packet structure, buffering, synchronization error budget, and measured noise benefit.

### Axis NA-04 — Synchronization strategy

**What appears conventional:** Timestamp synchronization, master-node schedules, post-hoc signal alignment, heartbeat-derived clock transformation, and shared-clock ECG/PPG acquisition are all represented in prior art.

**Repository state:** synchronization is scientifically necessary for ECG↔PPG timing and for meaningful cross-modal event comparison.

**Possible distinction:** Only a narrow synchronization method tied to a specific resource/accuracy constraint might remain differentiable.

**What must be specified:** allowable skew, drift, jitter, resynchronization method, clock ownership, and technical consequence of exceeding the bound.

### Axis NA-05 — Local motion reference and signal-quality architecture

**What appears conventional:** Accelerometer/IMU-assisted PPG artifact suppression, confidence scoring, ECG motion-artifact handling, and even an IMU attached to each ECG/EEG electrode are known.

**Repository state:** the form-factor document correctly notes that one controller IMU may not represent motion at a remote sensing interface.

**Possible distinction:** A more specific *site-local quality-state architecture* could remain worth testing if it handles distributed sensors differently from a generic single-device confidence score.

**What must be specified:** number/location of IMUs, mapping between IMU and sensing interface, quality features, gating/down-weighting rules, and measured false-rejection/false-acceptance improvement.

### Axis NA-06 — Cross-modal validation and arbitration

**What appears conventional:** ECG and PPG can be combined for beat detection/timing; confidence engines combining physiological and artifact signals are known.

**Repository state:** ECG↔PPG agreement/disagreement is a plausible way to distinguish a likely optical detection failure from a genuine rate change.

**Possible distinction:** A narrowly defined arbitration mechanism across distributed channels might be differentiable if it has a measurable technical result.

**What must be specified:** what constitutes agreement, which channel is reference under which conditions, how IMU/temp/contact quality modifies confidence, and how missing/noisy modalities are handled.

### Axis NA-07 — Temperature as active context rather than passive feature

**What appears conventional:** Skin temperature is widely co-measured with PPG/EDA, and patents exist that use skin temperature to compensate or control PPG measurement.

**Repository state:** temperature is currently contextual; thermal isolation and self-heating are unresolved.

**Possible distinction:** No distinction exists merely from adding TMP117. A specialized thermal/contact architecture could matter only if it demonstrably improves another channel.

**What must be specified:** thermal model, contact geometry, self-heating measurement, ambient compensation, and the exact PPG/EDA quality or calibration function.

### Axis NA-08 — Controlled-session versus ambulatory architecture

**What appears conventional:** supervised training followed by unsupervised addiction monitoring, and reference-device calibration followed by everyday wearable measurement, are already described in prior art.

**Repository state:** the session-configurable form-factor option intentionally separates a lower-burden ambulatory core from an occasional higher-fidelity peripheral pod.

**Possible distinction:** A project-specific *cross-site measurement-transfer mechanism* may still be investigable, but “use a better sensor during calibration and a convenient sensor later” is not enough.

**What must be specified:** what parameter is transferred, from which site/modal combination to which, how long the transfer remains valid, quality requirements, and error relative to a reference.

### Axis NA-09 — Personalized/baseline-relative longitudinal monitoring

**What appears conventional:** person-specific baseline/training, longitudinal physiological tracking, and individualized models are heavily represented in addiction and wearable literature.

**Repository state:** within-person trends are scientifically attractive because channels are nonspecific and hardware/site effects may limit population thresholds.

**Possible distinction:** None from “personal baseline” alone.

**What must be specified:** a new technical calibration or normalization mechanism rather than a generic personalized classifier.

### Axis NA-10 — Withdrawal-specific motor + autonomic measurement

**What appears conventional:** accelerometer-based objective withdrawal-tremor measurement is decades old, with direct alcohol-withdrawal applications from at least the 1970s/1980s and modern smartphone/CIWA work.

**Repository state:** IMU tremor is one of the best-supported AUD-specific roles, while ECG/EDA/temp remain nonspecific.

**Possible distinction:** A multi-sensor architecture that uses *the same movement phenomenon both as a symptom signal and as a corruption source* might be technically interesting, but the component ideas are known.

**What must be specified:** symptom IMU placement/protocol, artifact IMUs, tremor bands/features, rules separating symptom measurement from corruption handling, and clinical reference labels.

### Axis NA-11 — Breakout-board prototype versus integrated hardware

**What appears conventional:** replacing development boards with a custom PCB, shortening buses, integrating power management, and miniaturizing a wearable are ordinary engineering evolution.

**Repository state:** L1/L2/L3 separation is explicit.

**Possible distinction:** Integration could become inventive only if a concrete electrical/mechanical arrangement creates an unexpected or specifically demonstrated technical effect.

**What must be specified:** actual schematic/layout/mechanical constraints and performance comparison.

### Axis NA-12 — AUD-specific technical adaptation

**What appears conventional:** substance-use, craving, withdrawal, direct alcohol and rehabilitation wearables already exist.

**Repository state:** the current sensors are physiological proxies, not ethanol sensors.

**Possible distinction:** AUD context matters technically only if it creates a sensing problem requiring a distinct architecture—for example, withdrawal tremor interfering with PPG while also being a target measurement.

**What must be specified:** the AUD-specific technical requirement, not merely the clinical intention.

---

## 5. Prior-Art Landscape

The most relevant prior art falls into overlapping categories.

### 5.1 Foundational multimodal physiological wearables

**MIT — US20100268056A1 / US8140143B2, “Washable wearable biosensor.”**  
Priority: 2009-04-16. Publication: 2010-10-21. Assignee: Massachusetts Institute of Technology.

This is a foundational reference because it describes a long-wear physiological sensor system integrating combinations including PPG/heart rate, electrodermal activity/skin conductance, temperature and motion. It also discusses using motion information in relation to PPG quality. This materially weakens any novelty theory based on merely co-locating PPG, EDA, temperature and accelerometry or on the generic idea that motion can be used to judge optical data.

**Google — US20210121136A1, “Screenless Wristband with Virtual Display and Edge Machine Learning.”**  
Priority: 2019-10-28. Publication: 2021-04-29.

This disclosure expressly lists a wrist sensor system containing EDA, PPG, skin temperature and IMU, with ECG as an optional/additional modality. The exact wrist quartet contemplated by the project is therefore not a persuasive novelty anchor.

**Empatica EmbracePlus — commercial platform.**  
The current product documentation lists ventral EDA, multiwavelength PPG, accelerometer + gyroscope, and digital temperature in one wrist-worn platform with continuous raw-data recording. This is highly relevant non-patent prior art/product evidence for the same broad quartet and for longitudinal psychophysiological monitoring.

### 5.2 Wearable signal-quality/confidence systems

**Samsung — US10595786B2 / US20190192080A1, “Confidence indicator for physiological measurements using a wearable sensor platform.”**  
Priority: 2014-03-24.

This family is particularly important. It describes physiological data plus artifact data, motion data from an accelerometer, correlation between motion and PPG/ECG artifacts, confidence indicators, and combining multiple physiological sources such as ECG and PPG with artifact data to determine confidence.

That disclosure substantially exposes broad versions of “use IMU + ECG + PPG to decide whether a cardiovascular signal is trustworthy.”

**US20170164847A1, “Reducing Motion Induced Artifacts in Photoplethysmography (PPG) Signals.”**  
Priority: 2015-12-15. Publication: 2017-06-15.

The reference uses three-axis acceleration as a reference for motion compensation of PPG and weighted combination of motion-compensated signals. Generic local PPG+accelerometer artifact suppression is therefore old.

**Beach et al., 2021 — multichannel IMUs at individual ECG/EEG electrodes.**  
The academic system places an IMU at each individual electrode and uses local movement signals for adaptive artifact removal. This is a strong reference against the broad idea that sensor-local IMUs, rather than one global IMU, are themselves new.

### 5.3 Distributed body-sensor synchronization

**Byteflies — WO2018134380A1, synchronized network of wireless body sensors.**  
Priority: 2017-01-20. Publication: 2018-07-26.

This disclosure addresses synchronization among multiple wireless body sensors and dynamically allocates master-node timing responsibilities. Generic multi-node body synchronization is heavily exposed.

**Onera — US20240366159A1, “Synchronizing sensors using heart rate signals.”**  
Priority: 2021-09-02. Publication: 2024-11-07.

This discloses synchronization of separate body-worn sensors using heartbeat-derived physiological signals and time-domain transformation.

**Philips — WO2024235828A1, “Systems and methods for synchronizing health monitoring devices for accurate processing of shared signals.”**  
Priority: 2023-05-12. Publication: 2024-11-21.

This explicitly discusses synchronizing different wearable devices, including a chest ECG device and wrist PPG device, for joint processing.

**Aikon Technologies — EP4563076A1, synchronized ECG+PPG wearable patch.**  
Priority: 2023-11-28. Publication: 2025-06-04.

This disclosure uses a common device clock to synchronize ECG and PPG and compute PAT. Shared-clock ECG/PPG integration and PAT are therefore not novel concepts.

### 5.4 ECG + PPG + EDA combinations

**Senstream — US10709339B1, biometric wearable.**  
Priority: 2017-07-03. Publication/grant: 2020-07-14.

The device combines PPG and ECG in a finger/ring configuration, and electrodes can also form an EDA/skin-impedance sensor. It also uses ECG↔PPG timing for cardiovascular metrics. This is relevant to finger/hand embodiments and weakens broad “ECG + PPG + EDA” combination theories.

### 5.5 Multi-sensor alcohol/substance-use wearables

**SOBR Safe — WO2022099262A1 and family, “Wearable data collection device with non-invasive sensing.”**  
Priority: 2020-11-03. Publication: 2022-05-12.

This is one of the closest broad alcohol-domain references. A wrist wearable is described with direct alcohol/substance sensing plus optional/combined PPG, ECG, EDA/skin impedance, skin temperature, accelerometer and gyroscope. The presence of direct chemical sensing makes it technically different from this project, but it strongly exposes the broad sensor-aggregation and alcohol-context dimensions.

### 5.6 Direct transdermal alcohol systems

**Giner — US5944661A.**  
Priority: 1997-04-16. Publication: 1999-08-31.

This reference concerns continuous measurement of very low transdermal alcohol using an electrochemical/solid-electrolyte approach.

**Alcohol Monitoring Systems — US7462149B2 / US20040236199A1.**  
Priority: 2003-05-19. The system remotely monitors transdermal alcohol.

**Arborsense — WO2023150198A1.**  
Priority: 2022-02-02. Publication: 2023-08-10.

This is a wrist-worn direct transdermal alcohol monitor using an electrochemical ethanol sensor, with mechanical collection/sealing and tamper/environmental sensing.

These references are highly relevant to the **alcohol-monitoring landscape** but not technically equivalent to the project's nonspecific physiological proxy approach.

---

## 6. AUD-Specific Prior Art

### 6.1 Substance-use craving monitoring: very close to the broad project purpose

**US20200085301A1 / US11375896B2 — “Edge-intelligent IoT-based Wearable Device for Detection of Cravings in Individuals.”**  
Priority: 2017-08-18. Publication: 2020-03-19. Grant publication: 2022-07-05.

This is a major prior-art exposure for broad addiction-recovery concepts. The system describes:

- a wearable for substance-abuse/craving monitoring;
- movement/accelerometer sensing;
- electrodermal response/galvanic skin response;
- temperature;
- heart-rate/pulse-oximetry-related measurement;
- subject-labelled stress/craving inputs;
- supervised collection/training;
- later unsupervised monitoring;
- local/edge processing, remote/cloud/clinician communication and alerts.

Accordingly, the following broad propositions are heavily exposed:

- “wear physiology to infer craving in addiction recovery”;
- “train a personal model during supervision, then monitor after release”;
- “combine motion + EDA + temperature + HR for substance-abuse support”;
- “use edge processing or machine learning to generate alerts.”

The fact that the current project is specifically AUD rather than generic substance use would not, by itself, establish a technical distinction.

### 6.2 Indian alcohol-withdrawal wearable lead

**Indian application 202041020428 — “A Smart Wearable Device For Monitoring Withdrawal Symptoms In A User.”**  
Applicant: Velectron Labs Private Limited. Filing date reported: 2020-05-14. Publication reported: Indian Patent Journal 47/2021 / 2021-11-19. Inventor reported: Abhijit Nair.

A secondary Indian patent-information source describes an arm-worn smart band for non-invasive alcohol/drug withdrawal monitoring, including pulse oximetry/heart rate, blood-pressure-related optical sensing, an NIR alcohol-related sensor, EMG for muscle/neuropathy/tremor-related information, server-side comparison with thresholds, and alerts.

This is materially relevant because it is directly framed around alcohol withdrawal and a compact wearable. However, because the present search did not obtain a reliable official Indian Patent Office full-record page, **the bibliographic details and procedural status must be re-verified through official IPO records before legal reliance**.

Even without treating it as a legal conclusion, this is a strong search lead showing that “wearable + multiple biomarkers + alcohol withdrawal + remote alerting” is not an unexplored concept.

### 6.3 Recent withdrawal-monitoring patent family

**Rekovar — US20230355177A1 / US12290383B2, “Integrated artificial intelligence based system for monitoring and remediating withdrawal symptoms.”**  
Priority: 2021-11-17. U.S. application publication: 2023-11-09. Grant publication: 2025-05-06.

The family addresses withdrawal monitoring/remediation with wearable physiological sensing, including movement, temperature, impedance/conductance and bioelectrical modalities among a broader set. It is recent, but it further increases the exposure of generic “multimodal wearable withdrawal monitoring.”

### 6.4 Academic AUD recovery monitoring

**Alinia et al., 2021.**  
Adults in AUD recovery were monitored with wearable physiological sensors in an ambulatory proof-of-concept setting. EDA and HRV features were compared with self-reported stress/emotion-related outcomes.

This is relevant because it shows that ambulatory EDA + cardiovascular variability in AUD recovery is established research territory.

**van Lier et al., 2022.**  
A 100-day intensive idiographic study examined physiology, alcohol craving and lapses. It reported substantial intra- and inter-individual heterogeneity.

This directly weakens simplistic universal-threshold novelty narratives and supports the repository's cautious baseline-relative framing—but personalized longitudinal physiology itself is not new.

**Mei, Emery & Eddie, 2026.**  
A very recent study used ambulatory ECG/HRV plus ecological momentary assessment in early AUD recovery to study craving biosignatures. It is important current landscape evidence that cardiac physiological monitoring in AUD recovery continues to be actively researched.

### 6.5 Alcohol-withdrawal tremor

Tremor is unusually important because it is both:

1. a potentially relevant withdrawal symptom component; and
2. a possible contaminant of optical/electrical wearable signals.

But the first role is not new.

**US4306291A, “Tremor measurement device.”**  
Priority: 1979-03-27. Publication: 1981-12-15.

The reference explicitly discusses abnormal postural tremor during withdrawal from alcohol or drugs and uses an accelerometer attached to the hand/finger to quantify tremor.

**Carver et al., 2014; Aarabi et al., 2015; Norouzi et al., 2017.**  
These works use smartphone/iOS accelerometry and signal processing to quantify the tremor component of alcohol-withdrawal assessment and compare it with CIWA-Ar/clinician ratings. Norouzi et al. reported a relationship between withdrawal tremor energy in approximately the 5–15 Hz range and clinical tremor ratings.

Therefore, “use an accelerometer to quantify withdrawal tremor” is **heavily prior-art exposed**.

### 6.6 Direct alcohol measurement versus proxy physiology

The project must keep two technical families separate:

- **Direct or near-direct alcohol sensing:** transdermal electrochemical alcohol, optical/analyte sensing, breath, blood, etc.
- **Nonspecific physiological proxy sensing:** ECG/HRV, PPG, EDA, temperature, motion/tremor.

Direct ethanol systems are relevant prior art because they occupy the same overall AUD/alcohol-monitoring application space, but they do not anticipate every physiological-proxy architecture. Conversely, choosing nonspecific proxies rather than ethanol sensing does not automatically create novelty.

---

## 7. Multimodal Physiological Wearable Prior Art

### 7.1 The simple five-sensor combination is not a strong novelty basis

The candidate collection:

> ECG + PPG + EDA + temperature + IMU

should **not** presently be treated as inventive merely because all five appear in one project.

Reasons:

1. PPG + EDA + temperature + motion has foundational wearable prior art from MIT with 2009 priority.
2. A later Google wristband disclosure explicitly lists EDA + PPG + skin temperature + IMU, with ECG as another possible modality.
3. Empatica commercially deploys PPG + EDA + temperature + accelerometer/gyroscope in one wrist device.
4. SOBR Safe's alcohol/substance-use patent family explicitly contemplates a wearable containing PPG, ECG, EDA/skin impedance, skin temperature, accelerometer/gyroscope and alcohol/substance sensors.
5. Senstream combines PPG, ECG and EDA/skin impedance in a compact finger/ring architecture.
6. Generic multimodal confidence/fusion and physiological classification are heavily represented.

**Conclusion:** the five-sensor combination itself appears **VERY HIGH exposure** and is likely to be a mere aggregation unless the project later defines a specific technical interaction with measurable effect.

### 7.2 Wrist multimodal monitoring

A single wrist module containing PPG + EDA + temperature + IMU is almost exactly the sensor profile of current research/medical wearables such as EmbracePlus and is explicitly disclosed in patent literature.

**Exposure:** VERY HIGH.

### 7.3 EDA + cardiovascular sensing

EDA + HR/HRV for stress/arousal research is common. In AUD recovery, Alinia et al. already investigated EDA and HRV in ambulatory use.

**Exposure:** HIGH for the broad combination and use.

### 7.4 ECG + PPG

ECG + PPG combinations, synchronized acquisition, pulse-arrival timing, and combined cardiovascular calculations are extensively represented.

**Exposure:** VERY HIGH for broad ECG↔PPG timing or PAT as the inventive concept.

### 7.5 Generic multimodal fusion

“Fuse multiple sensors,” “use AI,” “use ML to predict stress/craving/withdrawal,” or “assign weights to modalities” are not credible novelty statements at this level. Prior art includes addiction-specific ML and wearable confidence/fusion systems.

**Exposure:** VERY HIGH.

---

## 8. Physical/Form-Factor Prior Art

The form-factor document is especially useful because it prevents the search from collapsing into a wrist-watch assumption.

### 8.1 Single wrist

**Repository rationale:** low burden; PPG/EDA/temp/IMU are plausible; continuous ECG is unresolved.

**Prior-art condition:** The PPG+EDA+temp+IMU wrist architecture is explicitly commercial and patented.

**Novelty implication:** Physical convenience does not create a technical distinction. Any novelty would need to arise from a specific interface, measurement, quality, or thermal mechanism.

**Exposure:** VERY HIGH for the broad form.

### 8.2 Wrist + chest

**Repository rationale:** chest for ECG quality, wrist for PPG/EDA/temp/motion; attractive for ambulatory research.

**Prior-art condition:** chest ECG + wrist PPG and cross-device synchronization are known. Chest straps/patches plus wrist wearables are common in body-sensor systems.

**Novelty implication:** The topology itself is not a safe inventive core. A quantified method of using the topology may still be investigated.

**Exposure:** HIGH to VERY HIGH.

### 8.3 Wrist + finger/hand + controller

**Repository rationale:** stronger finger PPG and palmar/finger EDA during controlled sessions; wrist/forearm for motion/temp; optional chest ECG.

**Prior-art condition:** finger/ring PPG, finger EDA, wrist/finger paired systems, and multi-device calibration are known.

**Novelty implication:** “Put the high-quality sensors on the finger” is an implementation choice unless the temporary module performs a defined cross-site calibration/transfer function.

**Exposure:** HIGH broad; potentially MODERATE only for a sufficiently narrow, measurable transfer mechanism.

### 8.4 Chest patch + wrist peripheral module

**Repository rationale:** future integrated ambulatory architecture.

**Prior-art condition:** patch + wrist multi-device monitoring, distributed synchronization, ECG/PPG combinations, and wrist physiological platforms are established.

**Exposure:** HIGH.

### 8.5 Distributed multi-node research prototype

**Repository rationale:** place each modality at a scientifically favorable site and accept more wiring/synchronization burden.

**Prior-art condition:** wireless body-area sensor networks and distributed physiological systems are mature; synchronization itself is also heavily patented.

**Novelty implication:** Excellent research architecture does not imply patent novelty. Its value may be experimental validity.

**Exposure:** HIGH.

### 8.6 Semi-wearable research harness

**Repository rationale:** current breakouts remain serviceable and scientifically inspectable.

**Prior-art condition:** research harnesses and body sensor networks are routine.

**Novelty implication:** likely an engineering fixture rather than an inventive core unless a specific wiring/interface architecture solves a nontrivial measurable problem.

**Exposure:** HIGH / INDETERMINATE for narrower implementation details.

### 8.7 Session-configurable modular architecture

**Repository rationale:** an ambulatory core is supplemented by a temporary higher-fidelity palm/finger module during standardized sessions.

**Prior-art condition:** calibration modes, reference devices, wrist/finger paired systems, and supervised-training-to-unsupervised monitoring exist. A 2026 U.S. application also describes a comfortable everyday wrist device compared against a separately positioned calibration PPG device.

**Novelty implication:** the broad idea is not enough. A narrower cross-site, cross-modality measurement-transfer protocol could still be worth engineering investigation.

**Exposure:** HIGH for generic “calibration pod + daily wearable”; MODERATE/INDETERMINATE for an exact transfer mechanism not yet defined.

### 8.8 Ear hybrid

**Repository rationale:** ear PPG may provide an alternative optical site; torso ECG and hand/wrist context remain separate.

**Prior-art condition:** ear PPG/hearables plus body-worn sensing are well established in general.

**Novelty implication:** no current repository mechanism makes this a strong novelty direction.

**Exposure:** INDETERMINATE to HIGH; low project maturity.

---

## 9. Cross-Modal / Artifact / Synchronization Prior Art

### 9.1 ECG ↔ PPG

Known art includes:

- combined ECG/PPG wearable systems;
- shared-clock synchronization;
- cross-device alignment;
- PAT computation;
- use of both ECG and PPG in physiological confidence estimation.

Therefore:

- “use ECG to validate PPG heart rate” is broadly exposed;
- “synchronize ECG and PPG” is broadly exposed;
- “calculate PAT from ECG and distal PPG” is broadly exposed.

A project-specific distinction would need to be more concrete than those statements.

### 9.2 IMU ↔ PPG

This is one of the most heavily exposed interactions.

Known art includes:

- motion gating of PPG;
- accelerometer-referenced motion compensation;
- frequency-domain correlation between PPG and accelerometry;
- signal-quality/confidence metrics incorporating accelerometry;
- exclusion of PPG pulse estimates when tremor/motion frequency overlaps the optical pulse signal.

The repository is scientifically right to use local motion as context, but that alone is not an inventive concept.

### 9.3 IMU ↔ ECG

Known art includes accelerometer-correlated ECG confidence and local IMUs attached at ECG electrodes for adaptive artifact removal.

Thus:

- “put an IMU near ECG to remove motion artifacts” is highly exposed;
- “use one IMU per electrode/local interface” also has direct academic precedent.

### 9.4 IMU ↔ EDA

Motion/contact artifact handling in EDA is well known at the measurement-science level. The patent search found less exact EDA+local-IMU claim language than for PPG, but generic wearable artifact/context use remains crowded.

A narrow EDA-specific local contact-state implementation would need its own targeted follow-up search.

### 9.5 Temperature ↔ PPG / EDA

Known art includes:

- co-measurement of temperature with PPG/EDA;
- using skin temperature to compensate PPG-related measurements;
- using temperature/contact information to adjust PPG;
- explicit concern with internal self-heating versus skin-facing temperature.

Therefore, “temperature contextualizes PPG” is not enough.

A potentially meaningful distinction would need an exact thermal architecture and a demonstrated improvement in signal quality or calibration, and it would remain vulnerable to multiple-reference obviousness arguments.

### 9.6 Multiple channels ↔ quality confidence

Samsung's confidence-indicator family is especially problematic for broad cross-modal quality hypotheses: it describes combining PPG, ECG and artifact data, including accelerometer information, to derive confidence.

Any project hypothesis in this area must therefore be narrower—for example, tied to distributed **site-local** quality states, a specific rule for symptom motion versus interface motion, or an experimentally defined failure mode.

### 9.7 Distributed sensors ↔ synchronization

Synchronization of body sensors is not an open field. Prior art covers:

- rotating/master schedules;
- wireless body sensor clocks;
- physiological-feature-based alignment;
- shared-clock integrated ECG/PPG;
- chest/wrist ECG+PPG synchronization.

A generic “timestamp all sensors with ESP32” or “synchronize multiple nodes” proposition is highly exposed.

---

## 10. Novelty Hypotheses

The hypotheses below are deliberately phrased as engineering propositions, not patent claims.

### NH-01 — Five-modality AUD physiological wearable

**Proposition:** A body-worn AUD-support system acquires ECG, PPG, EDA, peripheral temperature and inertial data for longitudinal physiological monitoring.

**Technical problem addressed:** richer measurement of nonspecific cardiovascular, sudomotor, thermal and motor state.

**Assessment:** This is mostly a sensor aggregation. Closely overlapping multimodal wearables and alcohol/substance-use systems already exist.

**Exposure:** **VERY HIGH**.

**Carry forward?** As a research prototype architecture, yes. As the inventive core, probably not.

---

### NH-02 — Wrist PPG + EDA + temperature + IMU core

**Proposition:** A wrist module continuously captures optical pulse, EDA, peripheral temperature and local inertial data as the ambulatory core.

**Technical problem addressed:** lower-burden longitudinal monitoring.

**Assessment:** The exact quartet is present in commercial and patent prior art.

**Exposure:** **VERY HIGH**.

**Possible differentiator:** none from the sensor set or wrist placement alone.

---

### NH-03 — Chest ECG + distal PPG synchronized cardiovascular reference

**Proposition:** Chest ECG provides beat timing while a wrist/finger PPG provides distal pulse timing; synchronized data are used for beat agreement and/or pulse-arrival timing.

**Technical problem addressed:** obtain a more reliable electrical timing reference and peripheral pulse measurement from physiologically suitable sites.

**Assessment:** ECG+PPG timing, PAT, shared-clock acquisition and chest↔wrist synchronization are well represented.

**Exposure:** **VERY HIGH** for broad timing/PAT; **HIGH** for generic beat cross-checking.

**Possible differentiator:** would require a more specific quality-arbitration mechanism or system constraint.

---

### NH-04 — Local IMU-assisted PPG artifact control

**Proposition:** An IMU physically co-located with the PPG interface is used to reject, down-weight, compensate, or grade PPG windows affected by local movement.

**Technical problem addressed:** wrist/finger optical motion artifact.

**Assessment:** Directly and repeatedly represented in patent and academic art.

**Exposure:** **VERY HIGH**.

**Carry forward?** Yes as sound engineering practice; no as a broad novelty thesis.

---

### NH-05 — Distributed site-local quality states

**Proposition:** Each mechanically distinct sensing interface/node has a local motion/contact quality state, rather than relying on one central IMU; those local states govern per-channel retention/down-weighting before multimodal interpretation.

**Technical problem addressed:** motion at a remote electrode/optical/EDA interface may differ from motion at the controller.

**Closest art:** per-electrode IMUs for ECG/EEG artifact removal; PPG/ECG confidence systems using accelerometry; wearable signal-quality systems.

**Overlap:** The components and general quality idea are known.

**Possible differentiator:** a distributed architecture in which each sensor interface emits a time-aligned local quality state and the system uses those states to prevent a clean channel from being discarded because another body site is moving.

**Exposure:** **MODERATE to HIGH**.

**What must be specified/tested:** exact local quality feature definitions; physical IMU-to-sensor mapping; cross-node clock error; arbitration rules; comparison against one-global-IMU and no-quality baselines.

---

### NH-06 — Withdrawal tremor as both target signal and corruption source

**Proposition:** A hand/wrist inertial channel quantifies withdrawal-relevant tremor while separate local motion references at optical/electrical interfaces determine whether the same or related movement corrupts PPG/ECG/EDA; the system preserves tremor as a symptom feature while independently suppressing its effect on physiological signal confidence.

**Technical problem addressed:** in withdrawal monitoring, motion can be clinically informative and simultaneously invalidate physiological measurements; a naive “reject all high-motion windows” policy may discard the phenomenon of interest.

**Closest art:** decades of alcohol-withdrawal tremor measurement; PPG tremor/motion artifact rejection; sensor-local IMUs; multimodal confidence systems.

**Overlap:** Every major ingredient is known independently, and tremor-induced PPG artifact is known in other movement-disorder contexts.

**Possible differentiator:** a precise dual-role architecture that distinguishes **symptom-motion representation** from **interface-artifact representation**, with separate local sensors and a defined quality policy.

**Exposure:** **MODERATE to HIGH**.

**What must be specified/tested:** body sites, tremor feature bands/protocol, which channels remain interpretable during tremor, local artifact thresholds, and evidence that the architecture retains clinically useful tremor while reducing false cardiovascular/EDA changes.

---

### NH-07 — Cross-modal beat and quality arbitration

**Proposition:** ECG beat timing, PPG pulse detection, local IMU state and peripheral temperature/contact context are combined to assign a per-window cardiovascular measurement-confidence state; disagreement is used to identify likely optical failure rather than being fused into an AUD score.

**Technical problem addressed:** distinguish genuine rate changes from PPG artifact/perfusion/contact failures.

**Closest art:** Samsung confidence-indicator family; ECG+PPG multi-sensor confidence/fusion; PPG motion quality systems; temperature-assisted PPG compensation.

**Overlap:** broad multi-source confidence is already known.

**Possible differentiator:** a narrowly specified distributed rule set tied to the project's separate anatomical sites and measured failure modes.

**Exposure:** **HIGH** broad; potentially **MODERATE** only after substantial narrowing.

**What must be specified/tested:** confidence-state machine, temporal alignment, perfusion/temperature conditions, failure labels, reference ECG, and quantified improvement over existing single-channel SQIs.

---

### NH-08 — Thermally explicit peripheral quality architecture

**Proposition:** A skin-facing temperature element is mechanically/thermally isolated from controller/battery heat and used not as an AUD feature alone but to identify thermal/contact states that alter PPG/EDA reliability.

**Technical problem addressed:** distinguish true peripheral physiological change from device self-heating, ambient/contact transitions and perfusion-dependent PPG/EDA changes.

**Closest art:** wearable skin-temperature/PPG compensation patents, wearable self-heating/temperature separation, multimodal temperature co-measurement.

**Overlap:** temperature compensation and self-heating management are known.

**Possible differentiator:** exact geometry/model and a cross-channel quality effect specific to the eventual hardware.

**Exposure:** **INDETERMINATE to HIGH**.

**What must be specified/tested:** thermal resistance/path, self-heating experiment, skin/reference thermometer comparison, ambient response, and measurable effect on PPG/EDA error.

---

### NH-09 — Supervised personal baseline followed by ambulatory monitoring

**Proposition:** Establish a person's physiological baseline in a supervised/controlled period and use it during unsupervised recovery monitoring.

**Technical problem addressed:** high inter-person variability.

**Assessment:** Very close addiction-specific prior art exists, especially US20200085301A1.

**Exposure:** **VERY HIGH**.

**Carry forward?** Scientifically plausible; not a good novelty core.

---

### NH-10 — Temporary high-fidelity peripheral pod calibrates a lower-burden ambulatory core

**Proposition:** During standardized sessions, a temporary finger/palm module captures higher-quality PPG and/or EDA while the ambulatory wrist core records simultaneously; a defined mapping/quality reference derived from the paired session is later used to interpret the wrist signals when the pod is absent.

**Technical problem addressed:** the best ambulatory site and the best measurement site may differ.

**Closest art:** generic reference-device calibration, wrist/finger paired sensing, PPG calibration modes, a 2026 U.S. application comparing an everyday wrist wearable against a calibration device, and addiction-specific supervised-to-unsupervised training.

**Overlap:** the broad reference-device/calibration concept is known.

**Possible differentiator:** only a concrete cross-site, cross-modality transfer mechanism—especially if it addresses a reproducible wrist-versus-palmar EDA/PPG measurement problem rather than merely training a classifier.

**Exposure:** **MODERATE to HIGH / INDETERMINATE** until the transferred quantity is specified.

**What must be specified/tested:** exact pod sensors, simultaneous acquisition requirement, transfer function or calibration object, validity duration, re-calibration trigger, and reduction in error/uncertainty.

---

### NH-11 — Distributed local acquisition to jointly reduce analog-path vulnerability and retain cross-modal timing

**Proposition:** Analog-sensitive front ends remain close to their sensing interfaces, are digitized locally, and send time-referenced digital data to a central controller while meeting a defined synchronization bound.

**Technical problem addressed:** long body-spanning analog leads can increase noise, while fully distributed devices create clock/synchronization error.

**Closest art:** local wearable front ends, wireless body sensor networks, distributed synchronization, shared-clock and post-hoc alignment methods.

**Overlap:** architecture is largely conventional.

**Possible differentiator:** only a highly specific circuit/timing implementation with measured joint improvement under the constraints of this prototype.

**Exposure:** **HIGH / INDETERMINATE**.

**Carry forward?** Important engineering architecture; weak novelty thesis at current abstraction.

---

### NH-12 — AUD-specific quality policy for low-motion autonomic events versus high-motion symptom events

**Proposition:** The system uses distinct acquisition/interpretation policies for (a) low-motion windows where autonomic channels are evaluated and (b) high-motion/tremor windows where motor symptoms remain analyzable while motion-sensitive autonomic channels are marked with lower confidence, without claiming that either state is alcohol-specific.

**Technical problem addressed:** a single global “motion rejection” policy may erase withdrawal-relevant motor information or contaminate autonomic interpretation.

**Closest art:** motion-conditioned wearable quality, tremor monitoring, addiction physiological monitoring.

**Overlap:** motion gating and symptom monitoring are known, but the exact two-regime measurement policy was not found as a single close disclosure in this search.

**Possible differentiator:** a rigorously defined measurement-state architecture tied to different local sensor roles.

**Exposure:** **MODERATE** for this narrow hypothesis, with significant inventive-step risk from combining known references.

**What must be specified/tested:** state definitions, transitions, local quality indicators, what data remain valid in each state, false state-switch rate, and effect on usable-data yield.

---

## 11. Novelty Hypothesis Matrix

| ID | Novelty hypothesis | Technical problem addressed | Supporting project basis | Closest prior art | Overlap | Possible differentiator | Prior-art exposure | What must still be specified/tested |
|---|---|---|---|---|---|---|---|---|
| NH-01 | Five-modality AUD physiological wearable | Multidimensional monitoring | Current ECG/PPG/EDA/temp/IMU candidates | MIT wearable; Google wristband; SOBR alcohol wearable; Empatica | Very broad overlap | None from list alone | **VERY HIGH** | Actual technical interaction |
| NH-02 | Wrist PPG+EDA+temp+IMU ambulatory core | Low-burden continuous sensing | Single-wrist/form-factor option | Empatica EmbracePlus; US20210121136A1 | Near-direct quartet | None from wrist placement alone | **VERY HIGH** | A distinct interface/mechanism |
| NH-03 | Chest ECG + distal PPG synchronized timing | Reliable electrical + peripheral timing | Wrist+chest option; PAT/cross-check interest | EP4563076A1; WO2024235828A1; US10709339B1 | ECG/PPG timing and sync known | Narrow quality mechanism only | **VERY HIGH / HIGH** | Timing bound, arbitration rule |
| NH-04 | Local IMU-assisted PPG artifact control | Optical motion artifact | Strong repository rationale for local IMU | US20170164847A1; US10595786B2; MIT | Directly known | None broad | **VERY HIGH** | Use as engineering requirement |
| NH-05 | Distributed site-local quality states | Remote-site motion differs from controller motion | Form-factor/local-IMU concern | Beach 2021; Samsung confidence patent | Local IMU + confidence known | Per-interface quality states across distributed nodes | **MODERATE–HIGH** | Node mapping, quality state, benchmark |
| NH-06 | Withdrawal tremor as target + corruption source | Preserve symptom motion while protecting physiology | Withdrawal-tremor role + artifact concerns | US4306291A; 2014–2017 tremor work; PPG artifact art | Ingredients known separately | Explicit dual-role separation and local quality architecture | **MODERATE–HIGH** | Sites, policies, validation |
| NH-07 | ECG/PPG/IMU/temp quality arbitration | Separate optical failure from physiological change | ECG↔PPG and temp context in repo | US10595786B2; PPG/temp compensation art | Broad confidence fusion known | Distributed, failure-specific arbitration | **HIGH; possibly MODERATE if narrowed** | State machine, ground-truth failures |
| NH-08 | Thermally explicit peripheral quality architecture | Self-heating/contact/perfusion confounding | TMP117 context + thermal isolation concerns | PPG temperature-compensation and wearable thermal patents | Temperature compensation known | Exact thermal geometry linked to PPG/EDA error | **INDETERMINATE–HIGH** | Thermal design and measured benefit |
| NH-09 | Supervised personal baseline → ambulatory monitoring | Inter-person variability | Repo baseline question | US20200085301A1; longitudinal AUD literature | Very close addiction-specific art | None broad | **VERY HIGH** | Scientific protocol only |
| NH-10 | Temporary high-fidelity pod calibrates ambulatory core | Best research site differs from best daily site | Session-configurable modular option | WO2020119296A1; wrist/finger calibration art; US20260232248A1; craving patent | Calibration concept known | Exact cross-site/cross-modality transfer | **MODERATE–HIGH / INDETERMINATE** | Transferred quantity, validity, benefit |
| NH-11 | Local digitization + bounded sync | Analog noise vs distributed clocks | Central/distributed tension | body-sensor sync art; shared-clock ECG/PPG | Architecture known | Only exact circuit/timing solution | **HIGH / INDETERMINATE** | Schematic, clocks, noise/sync data |
| NH-12 | Low-motion autonomic vs high-motion symptom measurement states | Motion rejection can erase target symptom | Repo artifact policy + tremor role | Motion-quality art + tremor art | Components known | Explicit role-aware state policy | **MODERATE** | State definitions and measured usable-data benefit |

### Matrix interpretation

The matrix does **not** reveal a currently obvious “low-exposure invention.” That is an important result.

The broad project is located in a crowded technical area. The hypotheses worth carrying forward are narrower mechanisms where the repository's real engineering tensions may eventually produce a demonstrable technical distinction. Even those are vulnerable to inventive-step arguments based on combining existing references.

---

## 12. Form Factor × Novelty Interaction

| Candidate embodiment | Does topology itself appear distinctive? | Technical effect that could matter | Prior-art concern | Current novelty implication |
|---|---|---|---|---|
| Single wrist | No | local multi-sensor convenience; common clock | exact sensor quartet commercially/patent-known | **Not a novelty core** |
| Wrist + chest | No | chest ECG quality + distal pulse/context | chest/wrist and ECG/PPG sync known | only narrower quality/sync mechanisms worth testing |
| Wrist + finger/hand + controller | Not by itself | higher-quality palm/finger EDA/PPG during sessions | finger sensing and paired calibration known | possible only if a defined cross-site transfer mechanism exists |
| Chest patch + wrist | No | distributed site-appropriate sensing | common body-network topology; synchronization patents | **Highly exposed** |
| Distributed multi-node | No | each modality at preferred site; local motion reference | mature body-sensor networks | research value high; novelty value low unless interaction is specific |
| Semi-wearable harness | Usually no | shorter serviceable paths; controlled research setup | conventional research engineering | likely validation platform, not inventive core |
| Session-configurable modular | Broad idea no | high-fidelity session can potentially anchor lower-burden daily sensing | calibration/reference-device art + supervised addiction monitoring | narrower transfer mechanism worth investigating |
| Ear + torso + hand/wrist | No evidence yet | alternative PPG site | hearables/multi-site systems already known | **INDETERMINATE** |

### 12.1 Local IMUs and form factor

The form-factor document's concern that one IMU may not represent motion at every sensor is technically sound. However:

- per-electrode/local IMU measurement is already published;
- PPG+accelerometer artifact confidence is heavily patented;
- therefore the novelty question is not “should we add local IMUs?”

A more useful question is:

> Can a distributed node expose a **local, synchronized quality state** that changes how another modality is trusted, while preserving motion itself when motion is a clinically relevant target?

That is a narrower engineering question and remains worth testing.

### 12.2 Thermal isolation and form factor

Separating a skin-facing temperature sensor from processor/regulator/battery heat is good wearable design. Prior art also recognizes self-heating and temperature compensation.

A technical distinction would require more than physical separation. It would need, for example:

- a defined two-node or two-thermal-path measurement;
- a model that estimates whether the measured temperature is skin-dominant;
- an explicit PPG/EDA quality action;
- measured improvement against a reference.

### 12.3 Long analog ECG leads versus local front end

Placing analog front ends near electrodes and transporting digital data is conventional in many biomedical systems. This design choice may be essential to make the prototype work but is not presently a strong novelty direction.

If later measurements reveal a project-specific topology that meets a tight ECG quality target and ECG↔PPG timing target with unusual resource constraints, that exact implementation could be re-searched.

---

## 13. False Novelty Traps

The following ideas may sound innovative in a project discussion but are weak novelty foundations in light of the present search.

1. **“ECG + PPG + EDA + temperature + IMU in one system.”**  
   Strongly exposed by general multimodal wearables and alcohol/substance-use wearable art.

2. **“PPG + EDA + temperature + IMU on the wrist.”**  
   Direct commercial and patent precedent exists.

3. **“Use an ESP32 as the controller.”**  
   A standard microcontroller choice, not an inventive mechanism.

4. **“Send the data over Wi-Fi or BLE.”**  
   Routine wearable transport.

5. **“Measure HRV for AUD.”**  
   HRV is well-established measurement science and already studied in AUD recovery.

6. **“Measure EDA for craving/stress.”**  
   EDA is well established, nonspecific, and already used in addiction/craving research and patents.

7. **“Use an IMU beside PPG to remove motion artifacts.”**  
   Very heavily exposed.

8. **“Use local IMUs instead of one global IMU.”**  
   Per-electrode/local-IMU academic precedent exists; the broad idea is insufficient.

9. **“Calculate PAT from ECG and PPG.”**  
   Established and patented in many cardiovascular systems.

10. **“Synchronize multiple wearable nodes.”**  
    Mature body-sensor-network prior art exists.

11. **“Use ECG and PPG together to improve confidence.”**  
    Multi-source confidence systems already exist.

12. **“Use temperature to improve PPG.”**  
    Temperature/contact compensation of PPG has direct patent art.

13. **“Put EDA on the palm/finger because it is a better site.”**  
    Choosing an established measurement site is not inherently inventive.

14. **“Use a chest ECG and wrist sensors.”**  
    Common distributed physiological topology.

15. **“Personalize to the patient.”**  
    Generic person-specific modeling/baselines are heavily represented.

16. **“Train during rehab, monitor later at home.”**  
    Very close addiction-specific patent art expressly teaches supervised training and later unsupervised monitoring.

17. **“Use a temporary calibration device and then a convenient wearable.”**  
    Calibration/reference-device wearable art exists.

18. **“Use AI to fuse multimodal data and predict relapse/craving.”**  
    Broad, crowded and scientifically underdefined; an AUD label does not make generic AI fusion technically novel.

19. **“Move from breakout boards to one custom PCB.”**  
    Normal product integration.

20. **“Shorten I²C wires by splitting the system into nodes.”**  
    Good engineering but generally conventional unless a highly specific architecture produces a non-obvious measurable effect.

21. **“Measure withdrawal tremor with an accelerometer.”**  
    Directly known for decades.

22. **“Call a known physiological wearable AUD-specific.”**  
    Intended use alone is a weak technical distinction.

---

## 14. Potentially Promising Novelty Directions

“Promising” here means **worth carrying forward for engineering definition and deeper searching**, not “novel.”

No direction below currently has a clean low-exposure finding.

### PD-01 — Site-local quality-state architecture for distributed sensing

**Technical problem:** A central IMU does not necessarily represent the movement of a remote ECG electrode, PPG optical window, or EDA contact. Treating all channels as equally contaminated can either discard useful data or retain corrupted data.

**Possible technical distinction:** Each mechanically independent sensing interface emits a local, time-aligned quality state. Cross-modal processing consumes those quality states rather than one global motion flag.

**Closest known art:** per-electrode IMUs; PPG/ECG motion confidence; generic multimodal confidence engines.

**Why it may still differ:** The project could potentially define a distributed *interface-specific* quality protocol rather than a single-device signal quality indicator.

**Current exposure:** MODERATE–HIGH.

**Engineering work required:**

- define a mechanical node/interface;
- measure local-versus-central IMU divergence;
- create per-channel quality labels using reference sensors/video/contact perturbation;
- quantify whether local quality states improve retained clean data or reduce false physiological events;
- define timestamp accuracy needed between quality state and raw channel.

**Further search required:**

- “distributed wearable local signal quality state”;
- “sensor node quality metadata body area network”;
- “per-electrode IMU physiological confidence”;
- “distributed PPG ECG local accelerometer confidence”;
- citations of US10595786B2 and Beach et al. 2021.

### PD-02 — Dual-role motion architecture: symptom versus artifact

**Technical problem:** In alcohol withdrawal, tremor can be a target phenomenon while motion simultaneously corrupts PPG/ECG/EDA. A generic high-motion rejection policy risks throwing away the symptom signal while an unqualified fusion model may mistake tremor-induced artifact for physiological change.

**Possible technical distinction:** Separate:
- a symptom-oriented hand/wrist tremor measurement;
- sensor-interface-local artifact references;
- a policy that preserves tremor as an output while reducing confidence only in the affected physiological channels.

**Closest known art:** alcohol-withdrawal tremor measurement, Parkinson/tremor-related PPG artifact exclusion, per-sensor IMUs, PPG/ECG confidence patents.

**Why it may still differ:** The exact *role separation* and data-validity state machine may not be contained in a single close reference found here.

**Current exposure:** MODERATE–HIGH; inventive-step risk is substantial.

**Engineering work required:**

- define tremor placement and standardized/ambulatory protocol;
- characterize tremor frequency leakage into PPG/ECG/EDA;
- establish local-vs-symptom IMU geometry;
- build a state machine distinguishing “symptom motion present” from “this channel unusable”;
- test against clinician-rated tremor and reference cardiovascular signals.

**Further search required:**

- tremor-monitoring patents that also discuss PPG/ECG corruption;
- Parkinson/essential-tremor wearable patents with cardiac optical sensing;
- withdrawal-monitoring patents containing accelerometer + PPG quality logic.

### PD-03 — Controlled-session-to-ambulatory measurement transfer

**Technical problem:** Palm/finger sites may give stronger EDA/PPG measurements but are burdensome for continuous wear; the wrist is more practical but physiologically and mechanically different.

**Possible technical distinction:** Simultaneous standardized acquisition from a temporary high-fidelity peripheral pod and the ambulatory core generates an explicit mapping/quality reference later used when only the ambulatory module is present.

**Closest known art:** supervised addiction training, reference-device calibration, wrist/finger PPG calibration, cross-device calibration.

**Why it may still differ:** A very specific cross-site, cross-modality transfer—especially one addressing known EDA/PPG site non-equivalence—might differ from generic calibration, but this has not yet been defined.

**Current exposure:** MODERATE–HIGH / INDETERMINATE.

**Engineering work required:**

- choose what is transferred: amplitude normalization, feature mapping, confidence envelope, contact-state model, or baseline relationship;
- prove repeatability across days and re-donning;
- define re-calibration trigger;
- compare to no-calibration and within-site baseline strategies.

**Further search required:**

- “palmar wrist electrodermal calibration wearable”;
- “cross-site EDA calibration palm wrist”;
- “finger wrist PPG transfer calibration”;
- wearable reference-sensor paired calibration patents;
- human-factors patents using temporary reference modules.

### PD-04 — Role-aware measurement-state architecture

**Technical problem:** Different project goals require different validity conditions: HRV may require low-motion standardized windows; tremor requires the presence of movement; EDA and PPG may have different contact/motion sensitivities.

**Possible technical distinction:** The device reports explicit acquisition states in which different modalities remain valid rather than computing one generic “multimodal score.”

**Closest known art:** signal-quality gating, activity-context wearables, adaptive measurement systems.

**Why it may still differ:** The project could define states around the **measurement roles** derived from AUD/withdrawal physiology rather than generic activity classification.

**Current exposure:** MODERATE, with obviousness risk.

**Engineering work required:** specify state transitions, channel-validity matrix, required sensors, quality metadata, and demonstrate higher valid-data yield or lower false event rate.

### PD-05 — Thermally verified peripheral context only if it drives a measurable quality improvement

**Technical problem:** a TMP117 breakout may measure board/self-heating/environment rather than skin; PPG/EDA depend on peripheral thermal/contact conditions.

**Possible technical distinction:** a hardware thermal path plus algorithm that proves the temperature reading is skin-dominant enough to alter PPG/EDA quality decisions.

**Closest known art:** PPG temperature compensation, contact-temperature systems, self-heating management.

**Current exposure:** INDETERMINATE–HIGH.

**Reason to carry forward:** not because temperature is novel, but because the repository has a real unresolved thermal engineering problem. If experimentation reveals a nontrivial solution, that implementation can be searched again.

---

## 15. Heavily Prior-Art-Exposed Directions

The following directions should presently be treated primarily as **engineering/scientific choices**, not likely novelty anchors:

- the five-sensor combination itself;
- the PPG+EDA+temperature+IMU wrist quartet;
- generic wrist wearables;
- generic chest+wrist topologies;
- ECG+PPG synchronization;
- ECG+PPG PAT;
- accelerometer-assisted PPG artifact rejection;
- generic physiological signal confidence scoring;
- one or more local IMUs for motion artifact;
- generic wireless body sensor networks;
- BLE/Wi-Fi transport;
- generic timestamp synchronization;
- generic distributed acquisition;
- person-specific baselines;
- supervised training followed by ambulatory addiction monitoring;
- generic multimodal/AI prediction of craving, relapse, intoxication, or withdrawal;
- accelerometer measurement of alcohol-withdrawal tremor;
- adding “AUD rehabilitation” as the intended use of a conventional wearable;
- generic direct alcohol monitoring—an old and crowded separate field;
- generic calibration from a reference wearable to a daily wearable;
- generic use of temperature to compensate PPG;
- custom-PCB integration of known breakout functions.

---

## 16. Insufficiently Defined Areas Blocking Assessment

A serious novelty analysis cannot proceed much further on the most interesting hypotheses until the following are defined.

### 16.1 Exact sensing topology

- Which modalities are continuous versus session-only?
- Which sensors share a node?
- Which body sites are used?
- Which signals are truly simultaneous?

### 16.2 Local versus central acquisition

- Where is each analog front end?
- Where does ADC occur?
- Are there multiple MCUs?
- Which buses stay local?
- What crosses the body as analog, wired digital, or wireless data?

### 16.3 Synchronization

- Required maximum clock error for each cross-modal interaction.
- Clock source and timestamp location.
- Handling of drift, buffering and packet loss.
- Whether synchronization is hardware, protocol, physiological-post-hoc, or hybrid.

### 16.4 Artifact and quality policy

- reject, down-weight, repair, or retain?
- what counts as local motion?
- what counts as contact loss?
- whether a local IMU is attached to the exact sensing interface;
- whether symptom motion must remain analyzable.

### 16.5 ECG↔PPG interaction

- simple pulse/beat cross-check?
- PAT?
- PPG artifact adjudication?
- reference heart-rate recovery?
- what synchronization precision is required?

### 16.6 Temperature interaction

- passive contextual feature?
- PPG/EDA confidence modifier?
- contact-state detector?
- thermal compensation input?
- what prevents board heat from dominating?

### 16.7 EDA comparability

- whether the legacy tinyGSR is used only for relative within-session features;
- whether board-specific calibration is possible;
- what later integrated EDA circuit would replace it;
- palm/finger versus wrist interpretation.

### 16.8 Personal baseline/reference strategy

- what is baseline?
- controlled abstinent session, morning rest, rehab-supervised window, or rolling baseline?
- which variables are normalized?
- how does baseline drift?
- how are medications, caffeine/nicotine, sleep, illness and activity annotated?

### 16.9 Controlled-session versus ambulatory transfer

- what does a temporary pod teach/calibrate?
- is there simultaneous reference acquisition?
- what is the transfer error?
- how long is the calibration valid?

### 16.10 Withdrawal-specific protocol

- exact tremor placement;
- rest/postural/task protocol;
- CIWA-Ar item versus total score or another clinical reference;
- whether passive tremor and standardized tremor are treated separately.

### 16.11 Ground truth and intended output

The project has not selected whether the technical endpoint is:

- signal quality;
- tremor magnitude;
- autonomic state trend;
- cue-reactivity research;
- withdrawal symptom component;
- craving correlation;
- relapse-risk research;
- recent alcohol/intoxication research;
- general recovery monitoring.

Novelty analysis changes materially depending on the output.

### 16.12 Future integrated hardware

A custom integrated device cannot be evaluated by assuming the current ESP32 + breakout geometry will simply shrink. The eventual optical window, electrodes, thermal path, analog front ends, mechanical pressure, clocks and power system may create different technical questions.

---

## 17. Targeted Questions for the Next Engineering Stage

1. What is the **first technical endpoint** we want to optimize: usable signal yield, tremor quantification, cross-modal timing, or an AUD research outcome?
2. Which modalities need to be **physically simultaneous**, and why?
3. For each modality, what is the **best measurement site** and the **acceptable ambulatory site**?
4. Does any modality need an **interface-local IMU**, and can experiments prove that a central IMU is insufficient?
5. Can we define a **per-channel signal-quality label** with independent ground truth?
6. During tremor, which channels remain valid and which become unreliable?
7. Can withdrawal-relevant motion be retained as a feature while optical/electrical artifact is independently flagged?
8. What maximum ECG↔PPG synchronization error is acceptable for the intended cross-modal feature?
9. If PAT is not an endpoint, do we need tight ECG↔PPG timing at all?
10. Is temperature merely logged, or does it change a specific acquisition/quality decision?
11. How will TMP117 be thermally isolated from the ESP32/regulator/battery in L2 and L3 prototypes?
12. Can we experimentally distinguish **skin temperature**, **contact transition**, and **board self-heating**?
13. What exact problem does a session-only finger/palm pod solve?
14. What mathematical/physical quantity would transfer from a controlled session to ambulatory wrist use?
15. Does that transfer remain valid after re-donning, exercise, temperature changes or days of drift?
16. Is the legacy tinyGSR adequate for any cross-session calibration hypothesis?
17. What raw data and quality metadata must be retained so later algorithms do not erase potentially useful failure information?
18. Which reference devices will validate ECG, PPG, EDA, skin temperature and tremor separately?
19. What is the explicit “do not interpret” condition for each channel?
20. Which technical features remain after removing the words **AUD**, **craving**, **withdrawal**, **rehab**, and **AI** from the description? Those remaining mechanisms are the stronger candidates for future novelty analysis.

---

## 18. Recommended Next Prior-Art Searches

The next search should be hypothesis-specific rather than broader.

### 18.1 Distributed local quality metadata

Suggested concepts:

- `"distributed wearable" "local signal quality" sensor node`
- `"body sensor network" quality metadata physiological`
- `"per sensor" accelerometer ECG PPG quality`
- `"local motion" physiological sensor confidence wearable`
- `"interface motion" PPG electrode quality IMU`

Useful classes/areas to combine:

- `A61B5/68` — sensor arrangements relative to the patient/body;
- `A61B5/6801` / `A61B5/681` — worn/body-surface and wrist-type arrangements;
- `A61B5/72` — physiological signal processing;
- `A61B5/7264` / related classifier subclasses;
- `H04J3/06` and descendants — synchronization;
- body-area/wireless sensor-network subclasses associated with cited synchronization families.

### 18.2 Symptom motion versus artifact motion

Suggested concepts:

- `"tremor" PPG artifact accelerometer wearable`
- `"withdrawal tremor" PPG ECG wearable`
- `"tremor monitoring" physiological signal quality`
- `"Parkinson" PPG accelerometer motion artifact patent`
- `"essential tremor" ECG PPG wearable motion artifact`
- `"alcohol withdrawal" accelerometer pulse oximeter artifact`

Backward-search:

- citations to US4306291A;
- citations to US10595786B2;
- citations to recent movement-disorder PPG quality papers;
- patents citing CIWA/tremor measurement work.

### 18.3 Controlled-session ↔ ambulatory transfer

Suggested concepts:

- `"cross-site calibration" wrist finger PPG wearable`
- `"palm wrist" EDA calibration wearable`
- `"electrodermal" palm wrist normalization`
- `"reference wearable" calibration "wrist" physiological`
- `"temporary sensor" calibration wearable daily monitoring`
- `"paired sensor" calibration ambulatory physiological`
- `"personal calibration" EDA PPG wearable`

Search patent families around:

- WO2020119296A1;
- US20260232248A1;
- wrist/finger blood-pressure/PPG calibration families;
- substance-use supervised/unsupervised training patents.

### 18.4 Thermal quality architecture

Suggested concepts:

- `"skin temperature" PPG signal quality compensation wearable`
- `"thermal isolation" skin temperature wearable PPG`
- `"self heating" wearable skin temperature sensor`
- `"temperature contact" PPG wearable`
- `"skin temperature" electrodermal signal quality`
- `"EDA" temperature compensation wearable`

### 18.5 AUD/withdrawal-specific technology

Suggested concepts:

- `"alcohol withdrawal" wearable physiological patent`
- `"alcohol withdrawal" accelerometer heart rate electrodermal`
- `"CIWA" wearable sensor patent`
- `"craving" wearable EDA heart rate patent`
- `"alcohol relapse" wearable physiological sensor`
- `"AUD recovery" ECG wearable`
- `"alcohol use disorder" "body sensor"`
- `"substance use" wearable physiological "baseline"`

Indian follow-up:

- re-run **202041020428** in the official Indian Patent Advanced Search / InPASS system;
- retrieve the complete specification, publication journal entry, FER/hearing record if public, and applicant/inventor bibliographic sheet;
- search Indian publications by the same applicant/inventor and IPC/CPC neighbors.

### 18.6 Exact patent-family and citation work

For the most consequential references, the next professional-style search should:

1. inspect independent claims and all family members;
2. identify earliest priority documents and whether the relevant feature is supported there;
3. inspect backward and forward citations;
4. search assignee portfolios;
5. search examiner-cited non-patent literature;
6. search CPC/IPC neighbors, not just keywords;
7. map which features occur in **one reference** versus only across multiple references.

---

## 19. Source / Prior-Art Ledger

> **Status note:** legal-status fields below are informational snapshots from the consulted databases and should not be treated as legal opinions. Priority and publication dates are kept separate.

### P-01 — MIT multimodal wearable

- **Title:** Washable wearable biosensor
- **Type:** U.S. patent application / patent family
- **Publication:** US20100268056A1; related US8140143B2; WO2010120945A1
- **Applicant/assignee:** Massachusetts Institute of Technology
- **Priority date:** 2009-04-16
- **Publication date:** 2010-10-21 (US A1)
- **Jurisdiction:** US / PCT family
- **Relevant features:** wearable PPG/HR, skin conductance/EDA, temperature, motion; long-term wearable design; motion information related to optical signal use.
- **Project overlap:** PPG + EDA + temperature + motion combination; motion-aware PPG quality.
- **Important difference:** not AUD-specific; does not define this project's eventual distributed topology.
- **Why it matters:** foundational evidence that the broad multimodal quartet and motion-aware optical sensing are old.
- **Stable URL:** https://patents.google.com/patent/US20100268056A1/en

### P-02 — Google wrist multimodal system

- **Title:** Screenless Wristband with Virtual Display and Edge Machine Learning
- **Type:** U.S. patent application
- **Publication:** US20210121136A1
- **Assignee:** Google LLC
- **Priority date:** 2019-10-28
- **Filing date:** 2020-10-28
- **Publication date:** 2021-04-29
- **Jurisdiction:** US
- **Database status consulted:** abandoned
- **Relevant features:** wrist sensor system expressly includes EDA, PPG, skin temperature and IMU; ECG also contemplated.
- **Project overlap:** near-direct match to the proposed wrist quartet.
- **Important difference:** broader wearable/computing context rather than AUD.
- **Why it matters:** makes the wrist quartet a poor novelty anchor.
- **Stable URL:** https://patents.google.com/patent/US20210121136A1/en

### P-03 — Samsung multimodal confidence system

- **Title:** Confidence indicator for physiological measurements using a wearable sensor platform
- **Type:** U.S. patent
- **Publication:** US10595786B2; related US20190192080A1
- **Assignee:** Samsung Electronics Co., Ltd.
- **Priority date:** 2014-03-24
- **A1 publication date:** 2019-06-27
- **Grant publication date:** 2020-03-24
- **Jurisdiction:** US
- **Relevant features:** physiological + artifact data; accelerometer correlation with PPG and ECG; confidence indicator; multiple physiological sources can be combined with artifact data.
- **Project overlap:** IMU-assisted PPG/ECG confidence and cross-modal quality architecture.
- **Important difference:** not AUD-specific and not necessarily distributed across the project's proposed sites.
- **Why it matters:** one of the strongest references against broad cross-modal signal-quality novelty.
- **Stable URL:** https://patents.google.com/patent/US10595786B2/en

### P-04 — Accelerometer-referenced PPG artifact reduction

- **Title:** Reducing Motion Induced Artifacts in Photoplethysmography (PPG) Signals
- **Type:** U.S. patent application
- **Publication:** US20170164847A1
- **Priority date:** 2015-12-15
- **Publication date:** 2017-06-15
- **Jurisdiction:** US
- **Relevant features:** three-axis acceleration used to generate motion-compensated PPG signals.
- **Project overlap:** local IMU + PPG artifact handling.
- **Important difference:** narrower optical heart-rate use.
- **Why it matters:** direct anticipation/exposure of generic PPG+accelerometer artifact correction.
- **Stable URL:** https://patents.google.com/patent/US20170164847A1/en

### P-05 — Wireless body-sensor synchronization

- **Title:** Method for providing synchronization between a plurality of wireless body sensors and method for operating a synchronized network of wireless body sensors
- **Type:** PCT publication
- **Publication:** WO2018134380A1
- **Assignee:** Byteflies NV
- **Priority date:** 2017-01-20
- **Filing date:** 2018-01-19
- **Publication date:** 2018-07-26
- **Jurisdiction:** WO/PCT
- **Relevant features:** multiple wireless body sensors; synchronization signals; scheduled master-node roles; energy/performance-aware synchronization.
- **Project overlap:** distributed multi-node synchronized wearable topology.
- **Important difference:** generic body network rather than the specific physiology.
- **Why it matters:** generic distributed synchronization is crowded.
- **Stable URL:** https://patents.google.com/patent/WO2018134380A1/en

### P-06 — Physiological-signal-based sensor synchronization

- **Title:** Synchronizing sensors using heart rate signals
- **Type:** U.S. patent application
- **Publication:** US20240366159A1
- **Assignee:** Onera Technologies B.V.
- **Priority date:** 2021-09-02
- **Filing date:** 2022-08-17
- **Publication date:** 2024-11-07
- **Jurisdiction:** US
- **Relevant features:** synchronization of independent body-worn sensor clocks using heartbeat-derived sequences and time-domain transformation.
- **Project overlap:** distributed physiological signal alignment.
- **Important difference:** not AUD-specific.
- **Why it matters:** even post-hoc physiological alignment is established.
- **Stable URL:** https://patents.google.com/patent/US20240366159A1/en

### P-07 — Chest/wrist multi-device physiological synchronization

- **Title:** Systems and methods for synchronizing health monitoring devices for accurate processing of shared signals
- **Type:** PCT publication
- **Publication:** WO2024235828A1
- **Assignee:** Koninklijke Philips N.V.
- **Priority date:** 2023-05-12
- **Publication date:** 2024-11-21
- **Jurisdiction:** WO/PCT
- **Relevant features:** synchronization of separate health-monitoring devices; example includes chest ECG and wrist PPG.
- **Project overlap:** wrist+chest form factor and ECG↔PPG alignment.
- **Important difference:** later art; cardiovascular synchronization focus.
- **Why it matters:** directly exposes broad chest/wrist sync concepts.
- **Stable URL:** https://patents.google.com/patent/WO2024235828A1/en

### P-08 — Shared-clock ECG+PPG patch

- **Title:** A wearable patch for collecting health-related data, and a method of collecting synchronized data with said patch
- **Type:** European patent application
- **Publication:** EP4563076A1; related WO2025116725A1
- **Assignee:** Aikon Technologies B.V.
- **Priority date:** 2023-11-28
- **Filing date:** 2023-11-28
- **Publication date:** 2025-06-04
- **Jurisdiction:** EP
- **Relevant features:** ECG + PPG in one patch, shared clock/synchronized timestamps, PAT calculation.
- **Project overlap:** ECG↔PPG synchronization and PAT.
- **Important difference:** integrated patch and cardiovascular purpose.
- **Why it matters:** common-clock ECG/PPG and PAT are not new concepts.
- **Stable URLs:**  
  - Google Patents: https://patents.google.com/patent/EP4563076A1/en  
  - Espacenet bibliographic route: https://worldwide.espacenet.com/publicationDetails/biblio?FT=D&locale=en_EP&CC=EP&NR=4563076A1&KC=A1

### P-09 — PPG + ECG + EDA ring/finger wearable

- **Title:** Biometric wearable for continuous heart rate and blood pressure monitoring
- **Type:** U.S. patent
- **Publication:** US10709339B1
- **Assignee:** Senstream Inc.
- **Priority date:** 2017-07-03
- **Filing date:** 2018-07-03
- **Publication/grant date:** 2020-07-14
- **Jurisdiction:** US
- **Relevant features:** PPG, ECG electrodes, ECG↔PPG timing, electrodes also usable for EDA/skin impedance; finger/ring physical arrangement.
- **Project overlap:** ECG+PPG+EDA combination; finger/hand archetype.
- **Important difference:** cardiovascular/BP objective.
- **Why it matters:** compact multi-electrode optical/electrodermal combination is already known.
- **Stable URL:** https://patents.google.com/patent/US10709339B1/en

### P-10 — Addiction craving wearable

- **Title:** Edge-intelligent IoT-based Wearable Device for Detection of Cravings in Individuals
- **Type:** U.S. patent application / granted family
- **Publication:** US20200085301A1; US11375896B2
- **Inventors:** Megan Reinhardt, Nicole Gilbertson, Premananda Indic, Prabha Sundaravadivel
- **Priority date:** 2017-08-18
- **Filing date:** 2019-11-19
- **Publication date:** 2020-03-19
- **Grant publication:** 2022-07-05
- **Jurisdiction:** US
- **Relevant features:** substance-use/craving wearable; movement, EDR/EDA/GSR, temperature, pulse/heart-rate-related sensing; supervised training and unsupervised monitoring; edge/cloud processing and alerts.
- **Project overlap:** AUD-recovery purpose, multimodal proxy physiology, personal supervised baseline/training, ambulatory phase.
- **Important difference:** generic substance abuse and broad craving classifier rather than this project's exact sensing architecture.
- **Why it matters:** perhaps the most important reference against broad AUD-specific personalization and supervised→ambulatory novelty.
- **Stable URL:** https://patents.google.com/patent/US20200085301A1/en

### P-11 — Indian alcohol-withdrawal wearable lead

- **Title:** A Smart Wearable Device For Monitoring Withdrawal Symptoms In A User
- **Type:** Indian patent application/publication lead
- **Application:** 202041020428
- **Applicant:** Velectron Labs Private Limited
- **Inventor:** Abhijit Nair
- **Reported filing date:** 2020-05-14
- **Reported publication:** 2021-11-19 / Journal 47/2021
- **Jurisdiction:** India
- **Relevant features reported:** smart arm band; alcohol/drug withdrawal; pulse oximeter/heart rate, optical BP, NIR alcohol-related sensing, EMG/muscle/tremor-related sensing; server thresholds and alerts.
- **Project overlap:** direct alcohol-withdrawal purpose and multi-biomarker wearable.
- **Important difference:** sensor set and mechanisms differ; includes alcohol-related NIR and EMG.
- **Verification warning:** this search obtained the record through a secondary Indian patent-information source, not a reliable official IPO full-record page. Verify official bibliographic data, publication, complete specification and status in InPASS/IPO before legal reliance.
- **Stable secondary URL:** https://www.quickcompany.in/patents/a-smart-wearable-device-for-monitoring-withdrawal-symptoms-in-a-user

### P-12 — Recent withdrawal-monitoring family

- **Title:** Integrated artificial intelligence based system for monitoring and remediating withdrawal symptoms
- **Type:** U.S. application / patent family
- **Publication:** US20230355177A1; US12290383B2
- **Assignee:** Rekovar Inc.
- **Priority date:** 2021-11-17
- **Filing date (continuation record):** 2023-04-27
- **A1 publication date:** 2023-11-09
- **Grant publication date:** 2025-05-06
- **Jurisdiction:** US
- **Relevant features:** wearable multimodal physiological monitoring for withdrawal with movement, temperature, impedance/bioelectrical and other channels in a broader remediation system.
- **Project overlap:** withdrawal-specific multimodal wearable.
- **Important difference:** broader treatment/remediation system and different modalities.
- **Why it matters:** reinforces heavy exposure of generic withdrawal-wearable framing.
- **Stable URL:** https://patents.google.com/patent/US12290383B2/en

### P-13 — Alcohol/substance wearable with broad sensor stack

- **Title:** Wearable data collection device with non-invasive sensing
- **Type:** PCT publication and multinational family
- **Publication:** WO2022099262A1; EP4240229A1/A4; US20240008812A1
- **Assignee:** SOBR Safe Inc.
- **Priority date:** 2020-11-03
- **Filing date:** 2021-11-03
- **Publication date:** 2022-05-12 (WO)
- **Jurisdiction:** WO/PCT; US/EP/CA/MX family
- **Relevant features:** wrist wearable; alcohol/substance sensing; PPG; ECG; EDA/skin impedance; skin temperature; accelerometer+gyroscope; remote processing.
- **Project overlap:** alcohol context plus nearly the entire physiological candidate family.
- **Important difference:** includes direct/near-direct analyte sensing; project currently does not measure ethanol.
- **Why it matters:** strong evidence against sensor aggregation as novelty in an alcohol context.
- **Stable URL:** https://patents.google.com/patent/WO2022099262A1/en

### P-14 — Foundational transdermal alcohol sensor

- **Title:** Potential and diffusion controlled solid electrolyte sensor for continuous measurement of very low levels of transdermal alcohol
- **Type:** U.S. patent
- **Publication:** US5944661A
- **Original assignee:** Giner Inc.
- **Priority/filing date:** 1997-04-16
- **Publication date:** 1999-08-31
- **Jurisdiction:** US
- **Relevant features:** direct transdermal ethanol sensing.
- **Project overlap:** alcohol monitoring purpose only.
- **Important difference:** biochemical alcohol measurement versus nonspecific physiological proxies.
- **Why it matters:** establishes that direct wearable alcohol monitoring is old and technically distinct.
- **Stable URL:** https://patents.google.com/patent/US5944661A/en

### P-15 — Remote transdermal alcohol monitoring

- **Title:** Method and apparatus for remote blood alcohol monitoring
- **Type:** U.S. patent family
- **Publication:** US7462149B2; US20040236199A1
- **Assignee:** Alcohol Monitoring Systems, Inc.
- **Priority date:** 2003-05-19
- **A1 publication:** 2004-11-25
- **Grant publication:** 2008-12-09
- **Jurisdiction:** US
- **Relevant features:** remote transdermal alcohol monitoring.
- **Project overlap:** longitudinal alcohol-monitoring application.
- **Important difference:** directly measures transdermal alcohol.
- **Why it matters:** separates direct ethanol art from physiological-proxy art.
- **Stable URL:** https://patents.google.com/patent/US7462149B2/en

### P-16 — Recent wrist transdermal alcohol monitor

- **Title:** Wearable alcohol monitoring device
- **Type:** PCT publication
- **Publication:** WO2023150198A1; related US20250160738A1 / CA family
- **Assignee:** Arborsense Inc.
- **Priority date:** 2022-02-02
- **Filing date:** 2023-02-02
- **Publication date:** 2023-08-10
- **Jurisdiction:** WO/PCT
- **Relevant features:** wrist-worn electrochemical ethanol sensor; collection/amplification chamber; tamper/environmental sensors.
- **Project overlap:** continuous alcohol-support/monitoring context.
- **Important difference:** direct transdermal ethanol mechanism.
- **Why it matters:** shows technically concrete novelty in this field often lies in the sensing/mechanical mechanism rather than merely the clinical label.
- **Stable URL:** https://patents.google.com/patent/WO2023150198A1/en

### P-17 — Wearable calibration reference art

- **Title:** A method for calibrating a blood pressure monitor, and a wearable device thereof
- **Type:** PCT publication
- **Publication:** WO2020119296A1
- **Relevant features:** calibration mode, stabilized PPG measurement, one wearable/reference arrangement calibrating another; finger/ring and wrist variants discussed.
- **Project overlap:** session/reference-device idea and wrist/finger pairing.
- **Important difference:** blood-pressure calibration rather than AUD psychophysiology.
- **Why it matters:** broad “use a reference configuration to calibrate everyday wearable measurements” is established.
- **Stable URL:** https://patents.google.com/patent/WO2020119296A1/en

### P-18 — Recent paired calibration-device search lead (not relied on as primary evidence)

- **Title:** Techniques for recommending a wrist-wearable device position for physiological measurements based on photoplethysmography (PPG) data and systems of use thereof
- **Type:** U.S. patent-application search lead; current metadata was available through a secondary patent portal during this search, not yet through a sufficiently reliable primary/full-record source.
- **Reported publication:** US20260232248A1
- **Reported assignee:** Meta Platforms Technologies LLC
- **Reported priority:** 2025-02-12
- **Reported filing:** 2026-02-12
- **Reported publication:** 2026-08-13
- **Jurisdiction:** US
- **Reported relevant features:** wrist wearable used together with a separate calibration device; simultaneous PPG comparison at different wearable positions/tightnesses.
- **Project overlap:** reference/calibration device plus an everyday wearable.
- **Important difference:** placement recommendation and PPG-specific calibration, not session-to-ambulatory AUD measurement transfer.
- **Why it matters:** useful as a recent search lead showing that paired wearable/reference calibration remains an active area, but it is **not relied on for a novelty conclusion in this document**. The older primary PCT calibration reference P-17 carries the substantive exposure analysis.
- **Verification note:** confirm bibliographic record and full disclosure through USPTO Patent Center or another authoritative source before legal reliance.
- **Search-lead URL:** https://patents.justia.com/patent/20260232248

### P-19 — Temperature-driven PPG compensation example

- **Title:** Wearable device for monitoring body water, electronic device, and operation method therefor
- **Type:** PCT publication
- **Publication:** WO2024181778A1
- **Relevant features:** skin temperature used to change weights/compensate PPG-related data and other biosignals.
- **Project overlap:** temperature as active context for optical sensing.
- **Important difference:** different health endpoint and implementation.
- **Why it matters:** weakens broad “use temperature to contextualize/correct PPG” novelty.
- **Stable URL:** https://patents.google.com/patent/WO2024181778A1/en

### L-01 — Sensor-local IMUs for artifact removal

- **Title:** Motion artefact removal in electroencephalography and electrocardiography by using multichannel inertial measurement units and adaptive filtering
- **Type:** Peer-reviewed paper
- **Authors:** Christopher Beach, Mingjie Li, Ertan Balaban, Alexander J. Casson
- **Publication:** Healthcare Technology Letters, 2021
- **DOI:** 10.1049/htl2.12016
- **Relevant features:** IMUs attached to individual ECG/EEG electrodes; local movement used for adaptive artifact removal.
- **Project overlap:** local IMU rather than one central IMU.
- **Important difference:** not AUD-specific; electrode-focused.
- **Why it matters:** local motion reference is not new by itself.
- **Stable URL:** https://pubmed.ncbi.nlm.nih.gov/34584747/

### L-02 — AUD recovery wearable physiology

- **Title:** Associations Between Physiological Signals Captured Using Wearable Sensors and Self-reported Outcomes Among Adults in Alcohol Use Disorder Recovery
- **Type:** Peer-reviewed paper
- **Authors:** Alinia et al.
- **Publication:** JMIR Formative Research, 2021
- **DOI:** 10.2196/27891
- **Relevant features:** ambulatory wearable EDA and HRV in AUD recovery; signal quality and self-reported outcomes.
- **Project overlap:** AUD recovery, longitudinal physiology, EDA + cardiovascular variability.
- **Important difference:** research study, not the same hardware architecture.
- **Why it matters:** AUD-specific ambulatory physiology is established research territory.
- **Stable URL:** https://pubmed.ncbi.nlm.nih.gov/34287205/

### L-03 — 100-day idiographic AUD monitoring

- **Title:** An ideographic study into physiology, alcohol craving and lapses during one hundred days of daily life monitoring
- **Type:** Peer-reviewed paper
- **Publication:** Addictive Behaviors Reports, 2022, 16:100443
- **DOI:** 10.1016/j.abrep.2022.100443
- **Relevant features:** intensive longitudinal physiology/craving/lapse monitoring; strong individual heterogeneity.
- **Project overlap:** baseline-relative and longitudinal AUD monitoring.
- **Important difference:** not a hardware novelty disclosure.
- **Why it matters:** personalization and longitudinal comparison are scientifically known.
- **Stable URL:** https://doi.org/10.1016/j.abrep.2022.100443

### L-04 — Recent passive ECG/HRV craving study

- **Title:** Using Passive Sensing to Isolate a Biosignature for Craving Among Individuals in Early Alcohol Use Disorder Recovery
- **Type:** Peer-reviewed paper
- **Authors:** Sara Mei, Noah N. Emery, David Eddie
- **Publication:** Addiction Biology, 2026
- **DOI:** 10.1111/adb.70182
- **Relevant features:** ambulatory ECG/HRV plus EMA in early AUD recovery.
- **Project overlap:** ECG/HRV and craving/recovery context.
- **Important difference:** does not disclose this multimodal hardware.
- **Why it matters:** shows active recent research around AUD craving biosensing.
- **Stable URL:** https://pubmed.ncbi.nlm.nih.gov/42575852/

### L-05 — Alcohol-withdrawal tremor quantification

- **Title:** Evaluation of alcohol intoxication and withdrawal syndromes based on analysis of tremor signals
- **Type:** Peer-reviewed paper
- **Authors:** Norouzi et al.
- **Publication:** Biomedical Signal Processing and Control, 2017, 33:83–87
- **DOI:** 10.1016/j.bspc.2016.11.006
- **Relevant features:** accelerometer-recorded withdrawal tremor; relationship between tremor energy and clinician rating.
- **Project overlap:** IMU withdrawal-tremor role.
- **Important difference:** focused on tremor scoring rather than multimodal wearable quality.
- **Why it matters:** withdrawal tremor measurement is established.
- **Stable URL:** https://doi.org/10.1016/j.bspc.2016.11.006

### P-20 — Early alcohol-withdrawal tremor device

- **Title:** Tremor measurement device
- **Type:** U.S. patent
- **Publication:** US4306291A
- **Assignee:** Alcoholism and Drug Addiction Research Foundation
- **Priority date:** 1979-03-27
- **Filing date:** 1979-12-14
- **Publication date:** 1981-12-15
- **Jurisdiction:** US
- **Relevant features:** accelerometer-based tremor measurement; specification explicitly discusses abnormal postural tremor during withdrawal from alcohol/drugs.
- **Project overlap:** objective withdrawal tremor.
- **Important difference:** old dedicated tremor apparatus.
- **Why it matters:** strongly defeats any broad proposition that inertial withdrawal-tremor measurement is new.
- **Stable URL:** https://patents.google.com/patent/US4306291A/en

### C-01 — Empatica EmbracePlus

- **Title/product:** EmbracePlus / Empatica Health Monitoring Platform
- **Type:** Commercial/research wearable technical documentation
- **Manufacturer:** Empatica
- **Current documentation consulted:** 2026
- **Relevant features:** ventral EDA, multiwavelength PPG, 3D accelerometer+gyroscope, digital temperature, continuous data recording.
- **Project overlap:** exact broad wrist quartet and longitudinal physiological monitoring.
- **Important difference:** no ECG in the wrist sensor set and not AUD-specific.
- **Why it matters:** commercially demonstrates that the quartet is conventional.
- **Stable URL:** https://www.empatica.com/embraceplus

---

## Overall conclusion

The current project occupies a **crowded multimodal wearable-sensing landscape**.

The strongest negative conclusion is:

> **The current ECG + PPG + EDA + temperature + IMU combination, including a wrist PPG/EDA/temp/IMU module, appears heavily exposed to prior art and should probably not be treated as the inventive core.**

Likewise, broad versions of:

- motion-assisted artifact handling,
- ECG↔PPG synchronization/PAT,
- body-sensor synchronization,
- personal baseline learning,
- supervised-to-ambulatory addiction monitoring,
- withdrawal-tremor quantification,
- generic AI/multimodal fusion,

are already strongly represented.

The most defensible next step is **not to pick a novelty now**. It is to convert a few repository-grounded engineering tensions into precise, measurable mechanisms and then search those mechanisms again.

The hypotheses most worth carrying forward are:

1. **distributed site-local quality states**, if they demonstrably outperform one global motion/context signal;
2. **dual-role movement handling**, where withdrawal-relevant tremor is preserved as a target signal while its corruption of local physiological sensors is separately identified;
3. **controlled-session-to-ambulatory measurement transfer**, but only if the project defines an explicit cross-site/cross-modality calibration object rather than generic personalization;
4. **role-aware measurement states**, where each modality has a defined validity policy instead of generic fusion;
5. **thermally verified peripheral context**, only if a concrete thermal design measurably improves PPG/EDA reliability.

Each remains exposed to prior art and obviousness arguments. None should presently be described as “the novelty.”
