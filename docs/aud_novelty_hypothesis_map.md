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
- recommend filing a patent;
- equate absence of a close search result with novelty.

A negative result is useful. If a direction is already densely represented in prior art, this document says so rather than forcing a patent narrative around it.

### Patent-analysis vocabulary used here

**Novelty / anticipation** asks whether one earlier disclosure appears to contain all essential elements of a candidate technical concept. This document does not make a legal anticipation finding; it identifies disclosures that may be close enough to threaten such a position.

**Inventive step / non-obviousness** asks whether a skilled person could have arrived at the concept by combining or modifying known teachings. Several hypotheses below may survive one-reference novelty scrutiny yet remain exposed to obviousness because their constituent mechanisms are individually well known.

**Mere aggregation** means known components are placed together without a new technical interaction. The current five-sensor set is especially vulnerable to this problem.

**New use/application** means applying known sensing technology to AUD, withdrawal, recovery, craving, or rehabilitation. A new clinical context does not automatically create a technical invention.

**Engineering implementation** can be more important than the broad use case. A concrete synchronization, contact-quality, distributed acquisition, local artifact-reference, thermal-isolation, or cross-modal gating mechanism could matter more than the phrase “AUD monitoring.”

### Exposure vocabulary

- **VERY HIGH exposure** — very close prior art appears to exist; unlikely to be useful as the inventive core without substantial additional technical distinction.
- **HIGH exposure** — most important elements or interactions appear known, even if no single exact reference was found.
- **MODERATE exposure** — relevant art exists, but a concrete technical distinction may remain if specified and demonstrated.
- **LOW exposure FOUND IN THIS SEARCH** — no close disclosure was located in this search; deeper searching is still required. This is **not** a novelty conclusion.
- **INDETERMINATE** — the project concept is too underspecified for a meaningful assessment.

---

## 2. Project State Derived from Repository

The repository currently describes an **exploratory multimodal physiological research platform** rather than a finalized AUD diagnostic or wearable product.

### Current candidate sensing stack

- **ECG / AD8232-class single-lead front end** — candidate source of cardiac electrical timing, HR, RR/NN intervals, HRV, and beat-quality information. The exact CJMCU-8232 VS82 gain/filter/RLD implementation remains unverified.
- **PPG / MAX30101** — candidate source of peripheral pulse timing, pulse rate, PRV, morphology/perfusion-related information, and ECG↔PPG timing. The exact SmartElex optical/mechanical implementation is unresolved.
- **EDA / legacy ProtoCentral tinyGSR 11/22** — candidate relative electrodermal/sudomotor channel. The repository explicitly does not treat the legacy trimmer-set output as verified absolute µS.
- **Peripheral temperature / TMP117** — candidate local thermal/context channel. It measures local sensor/die temperature and becomes a defensible skin-temperature measurement only through validated thermal coupling.
- **IMU / MPU-6050** — candidate movement, tremor, gait/activity, and signal-quality context channel. Its meaning is site-specific.
- **ESP32 DevKit V1** — acquisition, timestamping, buffering, coordination, and transport. It has no intrinsic AUD physiological meaning.

### Scientific boundary already established by the repository

The repository consistently preserves the chain:

`measurement → derived feature → physiological association → possible AUD relevance → clinical conclusion only if separately validated`

No current sensor directly measures ethanol. The physiological channels are nonspecific. Multimodal fusion may improve context and data-quality interpretation, but it does not create biochemical alcohol specificity.

### Existing cross-modal relationships already supported as engineering directions

The project documents leave room for:

- ECG↔PPG beat agreement and pulse-arrival timing;
- IMU-assisted identification or down-weighting of motion-contaminated PPG/ECG/EDA windows;
- temperature as context for peripheral perfusion and EDA interpretation;
- local IMUs near motion-sensitive interfaces rather than assuming one controller-mounted IMU represents all body sites;
- site-specific sensing instead of forcing all sensors to one location;
- distributed acquisition when long analog or body-spanning digital buses become problematic;
- longitudinal/personal-baseline analysis;
- controlled measurement sessions as distinct from free-living ambulatory operation.

These are **project directions**, not claimed inventions.

### Physical embodiments explicitly left open

The form-factor exploration identifies, without choosing among them:

1. single wrist unit;
2. wrist + chest dual-module system;
3. wrist + finger/hand sensing + central controller;
4. chest patch + wrist peripheral module;
5. distributed multi-node research prototype;
6. semi-wearable research harness;
7. session-configurable modular system with an ambulatory core and temporary high-quality finger/palmar pod;
8. ear PPG + torso ECG + wrist/hand context.

The repository also separates three maturity layers:

- **Level 1:** bench/tethered research prototype;
- **Level 2:** distributed body-worn prototype using current breakout boards;
- **Level 3:** integrated custom wearable/custom PCB.

A Level-3 concept should not be treated as demonstrated by merely strapping Level-1/2 breakouts to the body.

### Important unresolved engineering questions that directly affect novelty

- exact anatomical placement and electrode/contact geometry;
- exact local-vs-central acquisition topology;
- whether analog front ends are local to electrodes;
- synchronization error and required tolerance;
- whether ECG↔PPG PAT is actually in scope;
- how motion quality is represented, rejected, down-weighted, or modeled;
- whether multiple local IMUs are justified;
- whether temperature has a defined technical role beyond generic context;
- whether controlled-session measurements calibrate, validate, or otherwise alter interpretation of ambulatory measurements;
- what each modality must do continuously versus episodically;
- what ground truth is used for withdrawal, craving, alcohol exposure, or recovery outcomes;
- whether personal baselines are simple statistical normalization or part of a more specific acquisition/quality architecture.

These open choices prevent several hypotheses from being assessed more precisely.

---

## 3. Methodology

### 3.1 Repository analysis

The complete current `docs/` directory was reviewed before the novelty analysis:

- `aud_multimodal_hardware_evidence_map.md`;
- `aud_sensor_role_definition.md`;
- `aud_hardware_form_factor_options.md`.

The novelty hypotheses below are derived from engineering tensions and cross-modal relationships already present in those documents. Where the repository preserves uncertainty, this document preserves it.

### 3.2 Novelty decomposition

The system was decomposed into technical axes rather than searched only as “an AUD wearable.” Searches separately addressed:

- sensor combinations;
- ECG/PPG/EDA/temperature/IMU interactions;
- motion-artifact handling;
- sensor-local versus global motion references;
- signal-quality/confidence computation;
- ECG↔PPG timing;
- distributed body sensor networks;
- physiological clock synchronization;
- chest/wrist/finger/hand topologies;
- withdrawal tremor measurement;
- addiction/craving monitoring;
- direct transdermal alcohol sensing versus physiological proxies;
- supervised/personalized training followed by ambulatory monitoring;
- temporary calibration/reference devices paired with daily wearables;
- temperature-driven PPG compensation;
- commercial multimodal wrist systems.

### 3.3 Patent-search approach

Searches used publication numbers, alternate terminology, patent-style descriptions, family members, citations/classifications, and component-independent terms. Sources consulted included:

- Google Patents and patent-family records;
- WIPO/PCT publications and PATENTSCOPE-oriented searches where accessible;
- EPO/EP publications and Espacenet bibliographic routes where accessible;
- USPTO-linked U.S. publication data and Patent Center links exposed by patent records;
- Indian patent/publication searching where accessible;
- CPC/IPC classes and related-family/citation exploration around relevant references.

The search was global. Indian filing context was not treated as limiting prior art to India.

### 3.4 Academic and commercial prior-art search

Peer-reviewed work was searched for:

- AUD recovery physiology;
- craving/cue reactivity;
- alcohol-withdrawal tremor;
- accelerometer-based artifact removal;
- PPG motion compensation;
- multi-site physiological monitoring;
- wearable sensor-quality assessment;
- longitudinal personalized monitoring.

Commercial/research systems were also reviewed where they demonstrate that a physical combination is already technically routine, especially the Empatica platform.

### 3.5 Search limitations

This is a strong engineering-oriented prior-art map, not a professional patent search or legal opinion. Important limitations include:

- patent terminology is broad and families are large;
- legal status shown by aggregators can be incomplete or delayed;
- not all patent offices expose equally searchable full text;
- some Indian bibliographic material was accessible only through secondary patent-information mirrors during this search and is therefore flagged accordingly;
- some very recent publications may not yet be comprehensively indexed;
- an exhaustive claim-by-claim family analysis was not performed;
- non-patent literature can also be novelty-destroying prior art depending on date and circumstances;
- this search cannot prove absence of prior art.

Any hypothesis later chosen for serious IP work requires a professional search with family normalization, legal-date verification, claim analysis, citation chasing, and jurisdiction-specific advice.

---

## 4. Novelty Axes

### Axis A — Sensor/modal combination

**What appears conventional:** multimodal wearables combining optical pulse sensing, EDA/skin impedance, temperature, and inertial sensing are old; adding ECG to multimodal physiological wearables is also well represented.

**What the repository proposes/leaves open:** ECG, PPG, EDA, local temperature, IMU, and a controller may all be present, but inclusion and co-location are not fixed.

**Potential inventive distinction:** not the list itself. A distinction would need to arise from how one channel technically changes acquisition, confidence, validation, timing, or interpretation of another.

**What must be specified:** exact cross-modal interaction, conditions under which channels gate or validate one another, measurable technical effect, and whether the effect exists independently of the AUD label.

**Current exposure:** **VERY HIGH** for the sensor combination itself.

### Axis B — Anatomical distribution / physical arrangement

**What appears conventional:** chest ECG, wrist PPG/EDA/temperature/IMU, finger PPG, palmar EDA, multi-device body sensing, chest+wrist combinations, and multiple wearables operating on one user all have precedent.

**What the repository proposes/leaves open:** modality-specific sites may be selected for signal validity rather than cosmetic integration.

**Potential inventive distinction:** a physical topology could matter if it is tied to a measurable technical problem—for example, local motion reference at each mechanically independent sensor interface, local analog digitization, thermal isolation, or a controlled cross-site reference relationship.

**What must be specified:** which node is where, why the site matters technically, what signal failure occurs without it, and measured improvement.

**Current exposure:** **HIGH** for broad placement concepts; **INDETERMINATE to MODERATE** for narrowly defined site-dependent technical interactions.

### Axis C — Acquisition topology: central versus local digitization

**What appears conventional:** central acquisition, wireless body sensor networks, local sensor nodes, and distributed wearable nodes are all established.

**What the repository proposes/leaves open:** a single ESP32 could acquire all sensors, or analog-sensitive sensors could be digitized locally and synchronized to a master.

**Potential inventive distinction:** possibly a project-specific topology that demonstrably reduces analog-path/cable artifact while preserving cross-node temporal relationships needed for quality arbitration or ECG↔PPG timing.

**What must be specified:** signal path, ADC location, clock domains, transport, timing budget, noise comparison, and why ordinary distributed acquisition is insufficient.

**Current exposure:** **HIGH / INDETERMINATE**.

### Axis D — Synchronization strategy

**What appears conventional:** synchronization of multiple wireless body sensors is old; using physiological features or heartbeat signals to align independent wearable clocks is also known.

**What the repository proposes/leaves open:** common ESP32 timestamps, multiple synchronized nodes, or post-hoc alignment.

**Potential inventive distinction:** only if synchronization is integrated into another technical mechanism rather than being generic clock alignment—for example, quality-aware synchronization that limits which signals may serve as alignment anchors under motion/contact degradation.

**What must be specified:** absolute/relative timing error, drift correction, anchor signals, failure modes, quality gating, and required physiological timing tolerance.

**Current exposure:** **VERY HIGH** for generic synchronization; narrower extensions **INDETERMINATE**.

### Axis E — Sensor-local motion / artifact architecture

**What appears conventional:** accelerometer-assisted PPG artifact handling is mature; accelerometer-based confidence scoring is patented; research has placed local IMUs at individual electrodes for motion-artifact removal.

**What the repository proposes/leaves open:** the form-factor document asks whether local IMUs should sit with PPG, ECG, and/or EDA rather than only with the controller.

**Potential inventive distinction:** not “place an IMU next to a sensor.” A narrower distinction might involve separate local motion states for mechanically independent sensor interfaces, using those states to prevent a remote IMU from incorrectly validating or invalidating another site.

**What must be specified:** number/sites of IMUs, local coupling, quantitative artifact label, fusion logic, and comparative evidence versus one global IMU.

**Current exposure:** **HIGH**, with narrower architecture **MODERATE–HIGH** at best in this search.

### Axis F — Cross-modal validation / signal-quality arbitration

**What appears conventional:** PPG+accelerometer confidence, physiological+artifact confidence indicators, ECG/PPG cross-checking, and quality-weighted multisensor processing are established themes.

**What the repository proposes/leaves open:** ECG could validate PPG beat timing; PPG could provide peripheral corroboration; IMU can lower confidence; temperature can contextualize peripheral signal degradation.

**Potential inventive distinction:** potentially a precisely defined hierarchy or state machine in which electrical timing, optical pulse, local motion, contact/thermal state, and site-specific quality jointly determine which features are admissible.

**What must be specified:** exact inputs, thresholds/model, output quality states, causal role of each modality, and measurable reduction in false physiological events or unusable windows.

**Current exposure:** **HIGH** for generic confidence fusion; **MODERATE–HIGH** for a narrow architecture until more searching/testing is completed.

### Axis G — Temperature as technical context rather than outcome

**What appears conventional:** skin temperature is used in wearables; temperature compensation/context for PPG and contact conditions is known.

**What the repository proposes/leaves open:** TMP117 may primarily contextualize EDA/PPG and may require a thermally isolated skin-contact region.

**Potential inventive distinction:** only if a specific thermal geometry plus a defined correction/quality mechanism produces a measurable sensing benefit not found in known systems.

**What must be specified:** thermal path, heat sources, reference temperature, time constants, compensation relationship, and validation.

**Current exposure:** **HIGH / INDETERMINATE**.

### Axis H — Controlled-session versus ambulatory operation

**What appears conventional:** wearable systems commonly have calibration/training periods, controlled measurements, and later free-living use. Addiction-craving prior art specifically teaches supervised training followed by unsupervised monitoring.

**What the repository proposes/leaves open:** a session-configurable architecture could temporarily use finger PPG and palmar EDA for high-quality standardized measurements while a lower-burden ambulatory core uses wrist/chest sensors.

**Potential inventive distinction:** not the existence of two modes. A possible distinction would have to specify how high-fidelity session data technically calibrate, qualify, map, or constrain a lower-fidelity ambulatory configuration across different anatomical sites/modalities.

**What must be specified:** what is transferred between modes, whether it is sensor calibration, personal baseline, cross-site mapping, feature normalization, quality model, or something else.

**Current exposure:** **HIGH** for generic train-then-monitor; **MODERATE–HIGH / INDETERMINATE** for a specific cross-site transfer mechanism.

### Axis I — Personalized/longitudinal measurement architecture

**What appears conventional:** personalization, baseline-relative thresholds, subject-specific models, longitudinal monitoring, and supervised training are pervasive.

**What the repository proposes/leaves open:** within-person baselines may be more defensible than universal thresholds because of large interindividual variability.

**Potential inventive distinction:** only if personalization is tied to a concrete hardware/acquisition mechanism rather than generic model training.

**What must be specified:** baseline protocol, drift handling, reference states, quality requirements, re-baselining, and how personalization changes hardware operation or admissible data.

**Current exposure:** **VERY HIGH** for generic personalization.

### Axis J — Withdrawal-specific motor/autonomic interaction

**What appears conventional:** objective alcohol-withdrawal tremor measurement using accelerometers is decades old and has modern clinical studies; multisensor withdrawal wearables also exist.

**What the repository proposes/leaves open:** IMU may have two different roles—quantifying withdrawal tremor and identifying artifact in physiological sensors.

**Potential inventive distinction:** possibly a role-aware architecture that explicitly separates symptom-bearing tremor from motion that invalidates physiological features, potentially using differently placed IMUs or context-specific acquisition states.

**What must be specified:** tremor site, artifact-reference site, frequency features, rules for preserving clinically interesting tremor while rejecting corrupted physiological windows, and validation against clinician/ground-truth labels.

**Current exposure:** **MODERATE–HIGH**.

### Axis K — Breakout prototype to integrated hardware

**What appears conventional:** moving from dev boards to custom PCB/flex/patch integration is ordinary engineering development.

**What the repository proposes/leaves open:** Level-1/2 breakout experiments may later collapse into Level-3 custom nodes.

**Potential inventive distinction:** only an actual custom mechanical/electrical sensing interface could matter—for example, a novel electrode/optical/thermal stack—not the fact that a custom PCB replaces breakouts.

**What must be specified:** actual custom structure and technical effect.

**Current exposure:** **VERY HIGH** for generic integration.

### Axis L — AUD-specific technical adaptation

**What appears conventional:** alcohol-use, craving, intoxication, relapse, and withdrawal monitoring have substantial wearable prior art, including direct transdermal alcohol sensors and physiological-proxy systems.

**What the repository proposes/leaves open:** the present device intentionally does not directly sense ethanol and may support rehabilitation/recovery or withdrawal research using nonspecific physiology.

**Potential inventive distinction:** only if AUD physiology creates a specific hardware requirement or acquisition method that is technically different from generic stress/health wearables.

**What must be specified:** the technical requirement, not merely the patient population or intended use.

**Current exposure:** **HIGH** for broad AUD use; **INDETERMINATE** for a concrete AUD-driven hardware mechanism.

---

## 5. Prior-Art Landscape

The prior-art landscape is broad enough that the project should not start from “does a device with these five sensors exist?” It should assume that many constituent combinations already exist and search for technical interaction.

### A. General multimodal physiological wearables

#### MIT washable wearable biosensor family — US20100268056A1 / US8140143B2 / WO2010120945A1

Priority dates trace to **2009-04-16**. The family describes wearable physiological sensing around the hand/wrist and includes optical pulse sensing, skin conductance, motion/accelerometry, and temperature. It also discusses using motion information in relation to physiological data quality.

**Relevance:** this is strong foundational art against treating PPG + EDA + temperature + motion as a new multimodal sensing concept.

**Difference from this project:** no AUD-specific purpose and no requirement for the present ECG/distributed architecture.

#### Google screenless wristband — US20210121136A1

Priority **2019-10-28**, publication **2021-04-29**. The disclosure includes EDA, PPG, skin temperature, inertial sensing, and optional ECG within a wrist-worn platform.

**Relevance:** further exposes broad wrist multimodality and optional ECG integration.

#### Empatica EmbracePlus / research platform

Commercial documentation describes a purpose-built wrist platform incorporating **EDA, multiwavelength PPG, temperature, accelerometer and gyroscope**.

**Relevance:** this is not merely patent language; it demonstrates commercial/research implementation of almost the exact non-ECG wrist quartet contemplated by the repository.

**Conclusion:** a wrist PPG + EDA + temperature + IMU module is **conventional physical integration**, not a credible inventive core by itself.

### B. Addiction/craving physiological wearables

#### US20200085301A1 / US11375896B2 — edge-intelligent wearable for cravings

Priority **2017-08-18**, application publication **2020-03-19**, later U.S. grant publication **2022-07-05**. The disclosure is directly about cravings in substance-abuse/addiction contexts. It teaches a wearable using motion, electrodermal response, temperature, and heart-rate-related sensing, with a **supervised training period followed by unsupervised daily monitoring** and edge/cloud classification.

**Relevance:** this is one of the most important references in the search. It makes the following broad ideas heavily exposed:

- use physiological wearables for addiction/craving monitoring;
- combine EDA, temperature, movement, and heart-rate-related signals;
- establish subject-specific training/baseline data;
- transition from supervised training to everyday monitoring;
- run classification locally/remotely and produce alerts.

**Difference from this project:** the exact sensor hardware, placement, quality architecture, ECG↔PPG interactions, and form factor can differ. Those differences must be technical, not rhetorical.

### C. Withdrawal-monitoring wearables

#### Indian application 202041020428 — “A Smart Wearable Device For Monitoring Withdrawal Symptoms In A User”

A secondary Indian patent-information source reports filing on **2020-05-14** and publication on **2021-11-19**, with applicant **Velectron Labs Private Limited** and inventor **Abhijit Nair**. The disclosed concept is reported as an arm-worn smart band for non-invasive monitoring of alcohol/drug withdrawal symptoms using multiple physiological sensors, including heart/pulse-related and movement/tremor-related measurements.

**Important source-quality caveat:** the bibliographic/content summary was not independently verified from a stable official Indian Patent Office full-text record during this search. Treat this as a **high-priority Indian search lead**, not a legally verified family record. Before relying on it, retrieve the application and prosecution documents from the official Indian Patent Advanced Search / InPASS system.

**Relevance:** even the narrow “smart wearable for alcohol withdrawal” framing is not empty territory in India.

#### US12290383B2 family — integrated system for withdrawal monitoring/remediation

Priority traces to **2021-11-17**. The family describes a wearable withdrawal-monitoring system using multiple biosensors and behavioral signals. The disclosed wrist-wearable embodiment includes optical oxygen/pulse sensing, temperature, accelerometry, skin impedance/bioelectrical sensing and EMG, and the wider system can include other devices/chest components.

**Relevance:** broad multimodal withdrawal monitoring, symptom fusion, and wrist-based sensing are already heavily represented.

### D. Direct transdermal alcohol monitoring

Direct alcohol sensing is technically different from the current project but is highly relevant to the surrounding landscape.

#### US5944661A — Giner transdermal alcohol sensor

Priority/filing **1997-04-16**, publication **1999-08-31**. It concerns continuous measurement of very low transdermal alcohol levels using an electrochemical/solid-electrolyte mechanism.

#### US7462149B2 / US20040236199A1 — remote blood alcohol monitoring

Priority **2003-05-19**; U.S. application publication **2004-11-25**; grant publication **2008-12-09**. Assignee is Alcohol Monitoring Systems, Inc. It covers remote transdermal alcohol monitoring architecture.

#### WO2023150198A1 — Arborsense wearable alcohol monitoring device

Priority **2022-02-02**, publication **2023-08-10**. A wrist-worn electrochemical transdermal alcohol system with collection/amplification chamber and tamper/environmental considerations.

#### WO2022099262A1 — SOBR Safe wearable data collection device

Priority **2020-11-03**, publication **2022-05-12**. The disclosure spans wrist-worn multimodal sensing and substance/alcohol-detection mechanisms, including physiological sensors and inertial sensing.

**Landscape consequence:** “alcohol monitoring wearable” is a mature field. The current project’s physiological proxies are **not equivalent** to direct transdermal ethanol sensing, but the clinical label alone does not create technical novelty.

### E. ECG + PPG systems

#### US10709339B1 — Senstream biometric wearable

Priority **2017-07-03**, publication/grant **2020-07-14**. Describes wearable PPG and ECG sensing, including electrode arrangements that can also be used for EDA/skin impedance, and uses cross-signal timing for cardiovascular measurement.

**Relevance:** ECG + PPG + EDA coexistence and ECG↔PPG timing are not new in themselves.

#### EP4563076A1 — synchronized ECG + PPG wearable patch

Priority/filing **2023-11-28**, publication **2025-06-04**. Describes ECG and PPG acquisition with synchronized timing and pulse-arrival-related computation in a wearable patch.

**Relevance:** common-clock ECG+PPG and PAT-type timing remain crowded even in recent art.

### F. Motion-assisted artifact handling and confidence

#### US10595786B2 — Samsung confidence indicator for wearable physiological measurements

Priority **2014-03-24**; continuation publication **2019-06-27**; grant publication **2020-03-24**. It explicitly combines physiological data with artifact data, including motion, to determine a **confidence indicator**. The disclosure includes wearable multi-sensor platforms and correlation between artifact data and physiological measurements.

**Relevance:** this is the strongest obstacle to a broad “use IMU and other channels to compute confidence in physiological data” novelty hypothesis.

#### US20170164847A1 — reducing motion-induced artifacts in PPG

Priority **2015-12-15**, publication **2017-06-15**. Uses multi-axis accelerometer information to estimate/remove PPG motion artifact.

**Relevance:** PPG+accelerometer artifact compensation is mature.

#### Beach et al., 2021 — multichannel IMU artifact removal for ECG/EEG

Peer-reviewed work demonstrates **local IMUs attached at individual electrode locations** for capturing local movement and adaptive artifact removal.

**Relevance:** even “local IMU near each electrical sensor interface” is not inherently new.

### G. Distributed body-sensor synchronization

#### WO2018134380A1 — Byteflies wireless body-sensor synchronization

Priority **2017-01-20**, publication **2018-07-26**. Describes synchronization among multiple wireless body sensors and master scheduling.

#### US20240366159A1 — Onera heartbeat-based sensor synchronization

Priority **2021-09-02**, publication **2024-11-07**. Synchronizes independent body-worn sensor clocks using heartbeat-related signals and transformations.

#### WO2024235828A1 — Philips multi-device physiological synchronization

Priority **2023-05-12**, publication **2024-11-21**. Assignee **Koninklijke Philips N.V.** It specifically addresses synchronization across separate health-monitoring devices and includes a **chest ECG + wrist PPG** example.

**Relevance:** a chest+wrist system and physiological cross-device synchronization are not new simply because they match the repository’s plausible topology.

### H. Temperature-driven peripheral-signal compensation

Recent wearable disclosures include using skin/contact temperature to compensate or weight PPG and other biosignals. Examples include **WO2024181778A1** and related recent art.

**Relevance:** “use temperature to contextualize PPG” is too broad to carry novelty. A specific thermal/mechanical architecture and demonstrated technical effect would be required.

### I. Controlled/reference calibration paired with everyday wearables

**WO2020119296A1** describes wearable/reference calibration arrangements for physiological measurement and includes finger/ring/wrist configurations in a blood-pressure context.

**Relevance:** the general idea of using a higher-quality/reference configuration to calibrate another wearable is known. A session-configurable AUD architecture would need a more specific cross-site or cross-modality transfer mechanism.

A very recent U.S. application reported as **US20260232248A1** also appears to address paired wrist-wearable/reference PPG comparison for placement calibration. Because only secondary bibliographic access was reliable in this search, it is retained merely as a **recent search lead**, not as a primary basis for any conclusion.

---

## 6. AUD-Specific Prior Art

AUD-specific searching must distinguish direct ethanol measurement from physiological proxy sensing.

### 6.1 Direct ethanol / transdermal alcohol systems

These systems directly or near-directly sense alcohol transdermally. They are relevant because they occupy the alcohol-monitoring application space, but they do **not** technically anticipate a system that only measures ECG/PPG/EDA/temp/motion unless additional overlapping mechanisms are disclosed.

Important examples include:

- US5944661A — electrochemical/solid-electrolyte transdermal alcohol sensing;
- US7462149B2 / US20040236199A1 — remote transdermal alcohol monitoring;
- WO2023150198A1 — wrist electrochemical transdermal alcohol monitor;
- WO2022099262A1 — multimodal wrist device including substance/alcohol sensing concepts.

The distinction should remain explicit in future novelty work:

> **Direct ethanol sensing ≠ nonspecific physiological-response sensing.**

### 6.2 Physiological proxy sensing for addiction/craving/recovery

US20200085301A1 is particularly relevant because it already frames wearable physiological measurements and movement in the context of cravings/substance abuse, and uses supervised training followed by unsupervised monitoring.

Academic work also shows that longitudinal physiological monitoring in AUD recovery is already an active field:

- **Alinia et al. (2021)** used wearable EDA and HRV in adults in AUD recovery for ambulatory stress/emotion/context monitoring.
- **van Lier et al. (2022)** followed participants for roughly 100 days and found large inter- and intra-individual differences between physiology, craving, and lapses.
- **Mei, Emery & Eddie (2026)** report passive-sensing/ECG-HRV work aimed at craving signatures in early AUD recovery.

These studies do not necessarily destroy patent novelty for a concrete hardware architecture, but they make the following **scientifically and conceptually conventional**:

- ambulatory physiology in AUD recovery;
- longitudinal within-person data;
- craving-linked wearable signals;
- person-specific variation;
- coupling wearable physiology to self-report/EMA.

### 6.3 Alcohol withdrawal monitoring

Withdrawal-specific art is also substantial.

#### Tremor

US4306291A, with priority **1979-03-27**, already describes apparatus for measuring tremor and discusses alcohol/drug withdrawal in the specification. Modern studies by Carver/Aarabi/Norouzi and collaborators have quantified alcohol-withdrawal tremor using accelerometers and compared tremor energy to clinician/CIWA ratings.

Therefore:

> **Using an accelerometer to quantify alcohol-withdrawal tremor is heavily prior-art exposed.**

The present project could still investigate how tremor measurement interacts with other sensing roles, but the tremor measurement itself is not a promising novelty foundation.

#### Multimodal withdrawal sensing

The Velectron Indian application and the Rekovar family demonstrate that “wearable + multiple physiological sensors + withdrawal monitoring” is not an untouched architecture.

### 6.4 Craving and relapse

The repository already warns that EDA or HRV should not be treated as direct craving/relapse detectors. The prior-art landscape reinforces this in two ways:

1. patent art already uses multimodal wearables and ML/classification for craving-related monitoring;
2. academic literature shows high individual variability and imperfect physiological specificity.

A claim of technical novelty cannot be rescued simply by stating that known sensors are “for craving” or “for relapse prediction.”

### 6.5 Rehabilitation/recovery monitoring

The stronger engineering question is not “can physiology be monitored during rehabilitation?” It clearly can. The more defensible novelty investigation is whether rehabilitation creates a **specific measurement protocol or hardware state transition** that generic wearables do not already implement.

For example, a future hypothesis might compare:

- high-fidelity controlled-session acquisition at finger/palm/chest;
- lower-burden ambulatory acquisition at wrist/chest;
- a defined technical transfer or validation mechanism between them.

However, supervised→unsupervised addiction-monitoring prior art means this direction must be narrowed well beyond generic personalization.

---

## 7. Multimodal Physiological Wearable Prior Art

### 7.1 The simple five-modality combination

The project’s broad physiological set is:

`ECG + PPG + EDA + temperature + IMU`

This combination should currently be treated as **heavily prior-art exposed**.

Reasons:

- PPG + EDA + temperature + inertial sensing existed in early academic/patent wearable work;
- Empatica commercially implements the non-ECG quartet;
- Google/Samsung and other patent art add optional ECG and broad physiological sensor arrays;
- ECG+PPG coexistence is common;
- ECG/PPG timing and motion compensation are established;
- withdrawal/addiction patents already use overlapping physiological/motion sensor sets.

No evidence found in this search supports treating the mere presence of all five modalities as the inventive core.

### 7.2 ECG + PPG + EDA

Senstream art and other multimodal wearable disclosures show that electrical cardiac, optical pulse, and skin-impedance/conductance sensing can coexist.

Potential differentiation would need to arise from:

- a specific electrode/optical geometry;
- site distribution;
- synchronized timing relationship;
- quality arbitration;
- interaction with local motion;
- another demonstrable technical mechanism.

### 7.3 PPG + EDA + temperature + IMU at the wrist

This is **VERY HIGH exposure**.

Empatica is a decisive practical example. Earlier patent art also covers closely overlapping combinations. The wrist quartet can therefore be considered proof of physical feasibility, not novelty.

### 7.4 ECG on chest + peripheral sensors at wrist

This topology is attractive for this project because it respects ECG placement while retaining a wearable peripheral node. But distributed chest/wrist devices and physiological synchronization already appear in patent art, including the Philips synchronization reference.

Therefore the chest+wrist split is currently best treated as an **engineering choice**, not an inventive distinction, unless the project specifies a technical interaction that depends on the split.

### 7.5 Finger/palmar high-fidelity session sensing

Finger PPG and palmar/finger EDA are scientifically motivated by signal quality, but the underlying anatomical choices are not new.

Potential novelty would not arise from “we moved EDA to the palm” or “we use finger PPG.” It would have to arise from what the system **does with the relationship between those controlled-site measurements and the ambulatory-site measurements**.

---

## 8. Physical/Form-Factor Prior Art

### 8.1 Single wrist unit

**Prior-art state:** heavily crowded. Wrist sensing with PPG, EDA, temperature and motion is commercial and patented. ECG can be added in spot-contact or other configurations in many smartwatch/wearable disclosures.

**Technical effect needed for differentiation:** a genuinely different electrode/optical/thermal/mechanical architecture or acquisition interaction.

**Current exposure:** **VERY HIGH**.

### 8.2 Wrist + chest dual-module

**Prior-art state:** chest ECG + wrist peripheral monitoring is a known combination. Multi-device synchronization is known. Philips specifically discloses chest and wrist devices in a synchronization context.

**Potential project-specific technical space:** site-local motion/quality states, local digitization choices, or another mechanism that depends on the two mechanically independent interfaces.

**Current exposure:** **HIGH**.

### 8.3 Wrist + finger/hand + controller

**Prior-art state:** finger PPG and palmar EDA are old; wearable reference/calibration devices and multi-site sensing are known.

**Potential project-specific technical space:** a defined controlled-session transfer mechanism from high-quality finger/palm measurements to a lower-burden ambulatory node, particularly if it changes data admissibility or sensor operating parameters.

**Current exposure:** **MODERATE–HIGH / INDETERMINATE** depending on mechanism.

### 8.4 Chest patch + wrist peripheral module

**Prior-art state:** chest patches, wrist wearables, multi-device links and synchronization are all known.

**Potential project-specific technical space:** not the two-node topology itself. A special acquisition/quality relationship could remain, but none is currently specified.

**Current exposure:** **HIGH**.

### 8.5 Distributed multi-node research prototype

**Prior-art state:** wireless body sensor networks and synchronized multi-node physiological systems are mature.

**Potential project-specific technical space:** using site-specific nodes as a deliberate measurement-quality architecture rather than generic data collection.

**Current exposure:** **HIGH** for topology; **MODERATE–HIGH** for a narrow quality architecture.

### 8.6 Semi-wearable research harness

**Prior-art state:** conventional research instrumentation. The fact that breakout boards are centrally housed while contacts are distributed is not a promising novelty direction.

**Current exposure:** **VERY HIGH / engineering implementation choice**.

### 8.7 Session-configurable modular system

**Prior-art state:** calibration/reference measurement followed by normal wearable operation is known; addiction/craving art already teaches supervised training followed by everyday monitoring.

**Potential project-specific technical space:** the high-fidelity session pod and ambulatory core use **different anatomical sites and perhaps different modalities**, so a specific cross-site mapping or quality-transfer method might be worth investigating.

**Current exposure:** **MODERATE–HIGH / INDETERMINATE**.

### 8.8 Ear hybrid

**Prior-art state:** ear PPG and multi-site wearables are established.

**Potential project-specific technical space:** none identified merely from using the ear.

**Current exposure:** **HIGH** unless an actual ear-specific optical/mechanical interaction is later designed.

---

## 9. Cross-Modal / Artifact / Synchronization Prior Art

### 9.1 ECG ↔ PPG

Known technical interactions include:

- beat/pulse correspondence;
- pulse-arrival timing;
- synchronized ECG/PPG measurement;
- cardiovascular feature computation;
- cross-signal quality checks.

**Novelty implication:** “compare ECG and PPG” is too broad. “Calculate PAT” is too broad. “Use ECG to verify PPG heart rate” is too broad.

A narrower hypothesis would need a specific failure mode and quality logic.

### 9.2 IMU ↔ PPG

This is one of the most crowded interactions in wearable sensing.

Known art includes:

- accelerometer-referenced artifact filtering;
- frequency-domain motion subtraction;
- correlation of motion and PPG;
- confidence scoring based on artifact signals;
- discarding or reweighting low-quality windows.

**Novelty implication:** “use the IMU to remove PPG motion artifact” is **VERY HIGH exposure**.

### 9.3 IMU ↔ ECG

Motion references for ECG artifact handling are also known, including research with local IMUs at electrode locations.

**Novelty implication:** “put an IMU near ECG electrodes and correct motion” is **HIGH exposure**.

### 9.4 IMU ↔ EDA

Movement/contact artifacts in EDA are well known, and using motion as a contextual/quality signal is a natural extension of wearable signal-quality practice.

No close single reference was identified here for the exact project arrangement, but the concept is **not safely low-exposure** because the functional interaction is predictable and adjacent art is dense.

**Current exposure:** **HIGH / MODERATE–HIGH**, depending on exact logic.

### 9.5 Temperature ↔ PPG

Temperature affects peripheral perfusion and can be used to contextualize or compensate optical sensing. Patent art includes temperature-influenced PPG compensation.

**Novelty implication:** generic temperature correction/context is **HIGH exposure**.

### 9.6 Temperature ↔ EDA

Temperature/humidity/skin state are established EDA influences. Using temperature merely as a covariate is likely an obvious measurement-science choice.

A technical distinction would need a specific hardware thermal/contact mechanism or validated control action.

### 9.7 Local IMU ↔ local sensor interface

The repository correctly notes that a distant controller IMU does not necessarily represent local motion at a PPG window or electrode contact. That insight is scientifically useful, but local-movement sensing has prior art, including local IMUs at individual electrodes.

The potentially narrower question is:

> Can the system maintain **separate site-local quality states** for independently moving contact interfaces and use them to determine which cross-modal relationships remain valid?

This is more specific than “local IMU helps artifact removal,” but it still requires deeper searching.

### 9.8 Multiple physiological channels ↔ signal-quality confidence

Samsung’s confidence-indicator family is particularly relevant. It already teaches deriving confidence from physiological data plus artifact data, including motion, in a wearable platform.

Therefore a generic “multimodal quality score” is **HIGH to VERY HIGH exposure**.

Potential differentiation would require a narrowly defined topology/logic, such as independently quality-scored distributed sites where cross-modal validation is permitted only when both local quality states and inter-node timing satisfy explicit conditions.

### 9.9 Distributed sensors ↔ synchronization

Generic wireless time synchronization and physiological-feature-based synchronization are known.

Potential space may remain in **quality-gated synchronization anchors** or sync requirements tied to a specific cross-modal mechanism, but that is currently only a hypothesis.

---

## 10. Novelty Hypotheses

The hypotheses below are deliberately written as technical propositions rather than patent claims.

### NH-01 — The five-modality AUD physiological wearable

**Hypothesis:** a wearable AUD-support platform combines ECG, PPG, EDA, local temperature, and IMU sensing to monitor physiological state.

**Technical problem addressed:** capture complementary autonomic, cardiovascular, thermal, and motor information.

**Prior-art assessment:** the constituent combinations are extensively represented in general wearables, addiction/craving monitoring, and withdrawal systems.

**Possible differentiator:** none at this level of abstraction.

**Exposure:** **VERY HIGH**.

**Interpretation:** likely a mere aggregation unless a concrete technical interaction is added.

### NH-02 — Wrist peripheral quartet

**Hypothesis:** a wrist module combines PPG, EDA, temperature, and IMU for continuous peripheral sensing.

**Technical problem addressed:** combine common wearable physiological channels in one convenient site.

**Prior-art assessment:** commercially and patent-wise well represented; Empatica is a direct practical example.

**Possible differentiator:** none from the quartet itself.

**Exposure:** **VERY HIGH**.

### NH-03 — Synchronized chest ECG + distal PPG

**Hypothesis:** chest ECG and wrist/finger PPG are synchronized to provide beat correspondence, quality validation, and optionally pulse-arrival timing.

**Technical problem addressed:** preserve robust ECG geometry while using a distal optical pulse signal.

**Prior-art assessment:** ECG+PPG timing, chest/wrist topology, distributed synchronization, and PAT are established.

**Possible differentiator:** a narrowly specified quality-gated timing mechanism might differ; simple synchronization does not.

**Exposure:** **HIGH to VERY HIGH**.

### NH-04 — Local IMU-assisted PPG artifact control

**Hypothesis:** an IMU physically co-located with the optical interface determines whether PPG windows should be rejected, corrected, or down-weighted.

**Technical problem addressed:** optical motion artifact.

**Prior-art assessment:** mature research and patent area.

**Possible differentiator:** none at this broad level.

**Exposure:** **VERY HIGH**.

### NH-05 — Site-local quality states across distributed sensing interfaces

**Hypothesis:** each mechanically independent sensing site maintains a time-aligned local quality state derived from local motion/contact-relevant information; cross-modal features are accepted only when the participating sites independently satisfy defined quality and synchronization criteria.

**Technical problem addressed:** a global IMU or global quality label can misrepresent local interface motion in a distributed body-worn system.

**Project basis:** form-factor document explicitly notes that controller motion may not represent remote PPG/electrode motion and that local IMUs may be useful.

**Closest prior art:** Samsung confidence-indicator art, local-electrode IMU research, multi-device synchronization patents.

**Overlap:** all major building blocks are known.

**Possible differentiator:** the **site-local, mechanically independent quality state + cross-site admissibility rule** rather than generic artifact filtering.

**Exposure:** **MODERATE–HIGH**.

**What must still be specified/tested:** sensors producing local state, contact-quality measurement, clock error limits, rules for allowing ECG↔PPG/EDA features, and comparative performance versus one global motion sensor.

### NH-06 — Separation of withdrawal tremor as a physiological target from motion as a signal-quality contaminant

**Hypothesis:** the system distinguishes motion intended to be preserved as a withdrawal-tremor measurement from motion that invalidates other physiological channels, potentially using different IMU placements and different feature paths.

**Technical problem addressed:** the same motion can be clinically interesting in one channel while simultaneously corrupting ECG/PPG/EDA.

**Project basis:** IMU has both tremor and artifact-context roles in the repository.

**Closest prior art:** old and modern alcohol-withdrawal tremor measurement; general IMU artifact removal; local motion sensing.

**Overlap:** tremor measurement and artifact handling are independently known.

**Possible differentiator:** explicit **role separation and arbitration** so symptom-bearing tremor is retained rather than globally treated as artifact.

**Exposure:** **MODERATE–HIGH**.

**What must still be specified/tested:** tremor location, artifact-reference location, preservation/rejection logic, frequency ranges, ground truth, and whether the architecture provides a measurable benefit beyond ordinary independent feature extraction.

### NH-07 — Cross-modal beat/quality arbitration using ECG + PPG + local motion + peripheral context

**Hypothesis:** ECG serves as electrical beat reference; PPG provides peripheral pulse confirmation; local IMU grades optical/contact motion; peripheral temperature/other contact state can modify optical quality; the system outputs a defined admissibility/confidence state for cardiovascular features.

**Technical problem addressed:** avoid interpreting optical artifacts as physiological changes in ambulatory monitoring.

**Closest prior art:** Samsung confidence indicator, PPG motion-compensation art, ECG+PPG systems, temperature-compensation art.

**Possible differentiator:** only a specific state machine/hierarchy with measurable error reduction, not generic fusion.

**Exposure:** **HIGH** at broad level; **MODERATE–HIGH** if narrowly defined and demonstrably different.

### NH-08 — Thermally explicit peripheral-sensor quality architecture

**Hypothesis:** a skin-facing thermal island is physically isolated from controller/battery/LED heat and its validated local temperature is used to identify peripheral conditions under which PPG/EDA features are not comparable to baseline.

**Technical problem addressed:** self-heating/environment/perfusion can alter both temperature measurement and peripheral signal interpretation.

**Project basis:** form-factor document treats TMP117 thermal isolation as a mechanical design requirement and temperature as contextual rather than a primary AUD marker.

**Closest prior art:** wearable temperature-isolation design guidance and temperature-driven PPG compensation patents.

**Possible differentiator:** the exact thermal/mechanical structure plus a proven multi-sensor quality rule, if not found in deeper art.

**Exposure:** **INDETERMINATE to HIGH**.

**What must still be specified/tested:** thermal stack, heat path, time constants, correction/admissibility rule, PPG/EDA technical benefit.

### NH-09 — Supervised personal baseline followed by ambulatory AUD monitoring

**Hypothesis:** a participant undergoes controlled baseline/training, after which the wearable evaluates deviations during normal life.

**Technical problem addressed:** large interindividual variation.

**Closest prior art:** US20200085301A1 expressly teaches supervised training followed by unsupervised craving monitoring; personalization is widespread.

**Possible differentiator:** none at this level.

**Exposure:** **VERY HIGH**.

### NH-10 — Temporary high-fidelity finger/palm pod used to qualify a lower-burden ambulatory core

**Hypothesis:** a temporary controlled-session module acquires high-quality finger PPG and palmar/finger EDA while the ambulatory core acquires lower-burden wrist/chest signals; session data are used through a defined transfer mechanism to qualify or normalize ambulatory features.

**Technical problem addressed:** the best physiological site is not always the most wearable site.

**Project basis:** session-configurable modular form-factor option.

**Closest prior art:** generic wearable/reference calibration, supervised-to-unsupervised monitoring, finger/ring/wrist calibration systems.

**Possible differentiator:** a **cross-site/cross-modality transfer function or quality mapping** specifically designed for the change in anatomical sensing regime, rather than simple calibration.

**Exposure:** **MODERATE–HIGH / INDETERMINATE**.

**What must still be specified/tested:** what exactly is transferred, mathematical/physical relationship, same-sensor versus different-sensor mapping, persistence over time, re-calibration triggers, and whether the result improves repeatability.

### NH-11 — Local digitization with bounded synchronization error for distributed physiological acquisition

**Hypothesis:** analog-sensitive channels are digitized near their skin interface, while distributed nodes maintain a bounded synchronization error sufficient for later cross-modal timing/quality processing.

**Technical problem addressed:** long electrode/analog wiring increases noise while distributed clocks threaten cross-modal timing.

**Closest prior art:** wireless body-sensor networks, local sensor nodes, ECG patches, time synchronization, physiological clock alignment.

**Possible differentiator:** none without a specific mechanism or unusually constrained technical tradeoff.

**Exposure:** **HIGH / INDETERMINATE**.

### NH-12 — AUD role-aware measurement states: low-motion autonomic state versus high-motion withdrawal-symptom state

**Hypothesis:** acquisition/feature validity is intentionally state-dependent: low-motion windows permit HRV/EDA/peripheral comparisons, whereas high-motion windows may suppress those interpretations while preserving tremor/activity features as potentially meaningful symptom data.

**Technical problem addressed:** motion is both confounder and desired signal in withdrawal/recovery contexts.

**Project basis:** repository separately recognizes HRV/EDA low-motion needs and IMU withdrawal-tremor relevance.

**Closest prior art:** wearable quality gating, tremor measurement, addiction monitoring, adaptive sensing/processing.

**Possible differentiator:** the domain-specific **measurement-validity state transition** rather than generic activity classification.

**Exposure:** **MODERATE** in this search, with substantial obviousness risk.

**What must still be specified/tested:** states, transitions, sensors active/admissible in each state, whether raw data remain collected, clinical/technical ground truth, and measurable reduction in invalid physiological inference.

---

## 11. Novelty Hypothesis Matrix

| ID | Novelty hypothesis | Technical problem addressed | Supporting project basis | Closest prior art | Overlap | Possible differentiator | Prior-art exposure | What must still be specified/tested |
|---|---|---|---|---|---|---|---|---|
| **NH-01** | Five-modality ECG+PPG+EDA+temp+IMU AUD wearable | Complementary physiological coverage | Entire candidate stack | MIT wearable; Google/Samsung; Empatica; addiction/withdrawal patents | Very broad overlap | None at sensor-list level | **VERY HIGH** | A non-aggregative technical interaction |
| **NH-02** | Wrist PPG+EDA+temp+IMU quartet | Convenient ambulatory sensing | Single-wrist/wrist peripheral options | Empatica; MIT/Google wearables | Near-direct physical overlap | Specific custom mechanism only | **VERY HIGH** | Mechanical/electrical interaction beyond co-location |
| **NH-03** | Synchronized chest ECG + distal PPG | Robust ECG plus peripheral pulse/timing | Chest+wrist, chest patch+wrist | Philips sync; Onera; Aikon; ECG+PPG systems | Core topology/timing known | Narrow quality-gated timing | **HIGH–VERY HIGH** | Sync budget, gating, technical effect |
| **NH-04** | Local IMU-assisted PPG artifact control | Motion artifact | PPG+IMU strong co-location candidate | Samsung confidence; PPG artifact patents/reviews | Very close | None broadly | **VERY HIGH** | Only narrower implementation could differ |
| **NH-05** | Site-local quality states across distributed interfaces | Global IMU misrepresents local sensor motion | Local-IMU and distributed-node questions | Samsung; local-electrode IMUs; body-sensor sync | Building blocks known | Independent local quality + cross-site admissibility | **MODERATE–HIGH** | Quality state definition, local contact sensing, comparison vs global IMU |
| **NH-06** | Separate tremor-as-target from motion-as-artifact | Preserve withdrawal motor signal while rejecting corrupted physiology | IMU has both tremor and artifact roles | US4306291A; tremor studies; motion-artifact art | Both roles individually known | Role-aware separation/arbitration | **MODERATE–HIGH** | Placement, feature paths, preservation/rejection rules |
| **NH-07** | ECG/PPG/IMU/temp quality arbitration | False optical/physiological events | Cross-modal repository relationships | Samsung; PPG compensation; ECG+PPG; temp compensation | Broad confidence fusion known | Specific hierarchy/state machine | **HIGH** | Exact algorithm, interfaces, measurable error reduction |
| **NH-08** | Thermally explicit peripheral quality architecture | Device heat/perfusion confounds temp/PPG/EDA | TMP117 thermal-isolation concern | Temp-compensation patents; thermal wearable guidance | General mechanism known | Specific thermal stack + validated multisensor effect | **INDETERMINATE–HIGH** | Thermal/mechanical design and data |
| **NH-09** | Supervised baseline → ambulatory AUD monitoring | Personal variability | Longitudinal/baseline direction | US20200085301A1; personalized wearable literature | Very close conceptual overlap | None broadly | **VERY HIGH** | Must be much narrower than baseline/personalization |
| **NH-10** | Temporary high-fidelity finger/palm pod qualifies ambulatory core | Best signal site conflicts with daily wearability | Session-configurable modular option | Wearable calibration art; addiction train→monitor | General pattern known | Cross-site/cross-modality transfer | **MODERATE–HIGH / INDETERMINATE** | Transfer function, stability, re-calibration, benefit |
| **NH-11** | Local digitization + bounded sync | Reduce analog path noise while retaining timing | Distributed-node form-factor option | Body-sensor networks; ECG patches; synchronization art | Architecture conventional | Specific constrained mechanism only | **HIGH / INDETERMINATE** | Topology, noise and sync measurements |
| **NH-12** | Low-motion autonomic state vs high-motion symptom state | Motion is both confounder and desired signal | HRV/EDA limitations + withdrawal tremor role | Quality gating + tremor + addiction monitoring | Components known | Role-aware measurement-validity states | **MODERATE** | State definitions, transitions, validation, obviousness search |

### Matrix conclusion

No broad hypothesis in the current project state earned a responsible **LOW exposure found in this search** classification.

That is not a failure of the project. It means the novelty investigation has done useful pruning: the broad wearable architecture is crowded, and any future inventive contribution would likely need to be a **concrete technical mechanism**, not the sensor list or clinical label.

---

## 12. Form Factor × Novelty Interaction

### Single wrist

**Does topology itself create distinction?** No. The wrist quartet is conventional, and wrist ECG concepts are widespread.

**Potential technical effects worth measuring:** thermal isolation, optical/electrode interface geometry, local motion quality.

**Novelty assessment:** topology alone **VERY HIGH exposure**.

### Wrist + chest

**Does topology itself create distinction?** No. Chest ECG plus wrist sensing and multi-device synchronization are known.

**Potential technical effects worth measuring:** independent local quality state at chest and wrist; whether chest-local digitization materially improves ECG under realistic motion; quality-aware cross-node ECG↔PPG validity.

**Novelty assessment:** topology **HIGH exposure**; narrowly defined quality architecture may be worth carrying forward.

### Wrist + finger/hand + central controller

**Does topology itself create distinction?** Mostly no; the sites are established.

**Potential technical effect:** controlled high-quality PPG/EDA could act as a reference for a different ambulatory measurement regime.

**Novelty assessment:** generic topology **HIGH**; a specific cross-site transfer mechanism **INDETERMINATE to MODERATE–HIGH**.

### Chest patch + wrist peripheral module

**Does topology itself create distinction?** No; patches plus wrist wearables and distributed sync are established.

**Potential technical effect:** local digitization and independent quality states could solve real analog/motion issues.

**Novelty assessment:** topology **HIGH**; interaction requires specification.

### Distributed multi-node research system

**Does topology itself create distinction?** No. Body sensor networks are mature.

**Potential technical effect:** separating sensor validity from packaging and retaining site-local quality may provide a useful engineering architecture.

**Novelty assessment:** multi-node organization **HIGH**; specific quality-state mechanism **MODERATE–HIGH**.

### Semi-wearable research harness

**Does topology itself create distinction?** No. It is an appropriate research instrument but not a novelty direction.

**Novelty assessment:** **VERY HIGH exposure / conventional prototyping**.

### Session-configurable modular system

**Does topology itself create distinction?** Two-mode or calibration-mode wearables are known.

**Potential technical effect:** if the temporary pod establishes a reproducible cross-site mapping that enables or invalidates ambulatory features, that mechanism may be worth deeper investigation.

**Novelty assessment:** **MODERATE–HIGH / INDETERMINATE**.

### Ear hybrid

**Does topology itself create distinction?** No. Ear PPG is established.

**Potential technical effect:** only an actual optical/fixture design or ear-specific quality mechanism could matter.

**Novelty assessment:** **HIGH** at current abstraction.

### Form-factor conclusion

The form-factor exploration is valuable for novelty not because it reveals an obviously new shape, but because it reveals **technical discontinuities between sensing sites**:

- motion is local;
- electrical lead geometry is local;
- optical contact/perfusion is local;
- EDA responsivity is local;
- temperature is local and vulnerable to electronics heat;
- clocks can be independent when acquisition is distributed.

If inventive space exists, it is more likely to arise from **how the architecture handles those discontinuities** than from the physical shape itself.

---

## 13. False Novelty Traps

The following ideas may sound innovative in a project discussion but are weak novelty foundations based on the search performed.

1. **“We combine ECG, PPG, EDA, temperature and IMU.”**  
   **Assessment:** **VERY HIGH exposure.** Multimodal wearables with overlapping combinations are old.

2. **“PPG + EDA + temperature + IMU are all in one wrist device.”**  
   **Assessment:** **VERY HIGH exposure.** Commercially demonstrated by Empatica and covered in older wearable art.

3. **“We use an ESP32.”**  
   **Assessment:** ordinary controller choice; no meaningful novelty.

4. **“The device communicates over Wi-Fi/BLE.”**  
   **Assessment:** ordinary implementation.

5. **“We calculate HRV.”**  
   **Assessment:** established physiological processing.

6. **“We use EDA to infer stress/craving.”**  
   **Assessment:** scientifically nonspecific and already explored in addiction/wearable art.

7. **“An IMU next to PPG removes motion artifact.”**  
   **Assessment:** **VERY HIGH exposure**.

8. **“An IMU sits next to ECG electrodes.”**  
   **Assessment:** local-electrode inertial references already appear in research.

9. **“ECG and PPG are synchronized to compute PAT.”**  
   **Assessment:** **VERY HIGH exposure**.

10. **“Multiple body-worn sensor nodes are synchronized.”**  
    **Assessment:** mature body-sensor-network problem with patent art.

11. **“ECG and PPG validate each other.”**  
    **Assessment:** broad cross-modal confidence/correlation is known.

12. **“Temperature helps interpret PPG.”**  
    **Assessment:** temperature-driven compensation/context exists in prior art.

13. **“EDA is measured at the palm/fingers instead of the wrist.”**  
    **Assessment:** established measurement science, not novelty by itself.

14. **“Chest ECG and wrist peripheral sensors.”**  
    **Assessment:** established topology.

15. **“We use personal baselines.”**  
    **Assessment:** **VERY HIGH exposure** as a generic concept.

16. **“The patient is trained/recorded in rehab and then monitored at home.”**  
    **Assessment:** addiction/craving prior art already teaches supervised training followed by unsupervised monitoring.

17. **“A temporary calibration device is used with a daily wearable.”**  
    **Assessment:** reference/calibration arrangements are established in physiological wearables.

18. **“Generic multimodal fusion / AI predicts withdrawal or relapse.”**  
    **Assessment:** crowded and technically vague; intent/application does not supply novelty.

19. **“We move from breakout boards to a custom PCB.”**  
    **Assessment:** ordinary product development.

20. **“We split sensors into multiple nodes to shorten I²C/analog wires.”**  
    **Assessment:** a reasonable engineering choice but distributed/local acquisition is old unless a specific new mechanism is present.

21. **“Accelerometry quantifies alcohol-withdrawal tremor.”**  
    **Assessment:** heavily exposed by decades of art.

22. **“The same known wearable is novel because it is for AUD rehabilitation.”**  
    **Assessment:** new intended use alone is a weak technical novelty foundation.

---

## 14. Potentially Promising Novelty Directions

These are **hypotheses worth carrying forward**, not statements of novelty.

### PD-01 — Site-local quality-state architecture for distributed physiological sensing

**Technical problem:** one global motion/quality signal can incorrectly characterize a remote sensor interface.

**Proposed technical distinction:** each mechanically independent sensing site derives its own quality state from local motion/contact/context, and cross-modal features are only admitted when all participating sites meet specified quality and synchronization criteria.

**Closest known art:** Samsung confidence indicators, local-electrode IMU research, distributed synchronization systems.

**Why it may differ:** the candidate distinction is not generic artifact filtering but **distributed interface-specific quality and cross-site admissibility**.

**Current exposure:** **MODERATE–HIGH**.

**Engineering work needed:**

- define local quality variables for chest ECG, wrist/finger PPG, EDA, and thermal contact;
- compare local versus single-global IMU prediction of corrupted windows;
- define confidence/admissibility states;
- measure false acceptance/rejection of physiological events;
- define synchronization tolerance when cross-site features are enabled.

**Further search needed:** patents on distributed sensor-quality flags, local contact sensing, per-node confidence, quality-aware fusion, and quality-gated cross-device synchronization.

### PD-02 — Dual-role movement architecture: symptom signal versus artifact signal

**Technical problem:** withdrawal tremor may be important information, yet motion also corrupts cardiovascular/EDA signals.

**Proposed technical distinction:** separate motion streams or processing roles preserve tremor as a target measurement while independently grading artifact at physiological interfaces.

**Closest known art:** withdrawal tremor patents/studies; local motion-artifact work; general confidence systems.

**Why it may differ:** the system is not trying to “remove all motion.” It distinguishes **motion that is itself the phenotype** from **motion that invalidates another modality**.

**Current exposure:** **MODERATE–HIGH**.

**Engineering work needed:** site/orientation experiments, tremor band definition, clinician/reference labels, local interface motion measurement, state arbitration.

**Further search needed:** patents on tremor-aware PPG/ECG validation, simultaneous tremor and autonomic monitoring, motion-preserving artifact gating, withdrawal-specific adaptive sensing.

### PD-03 — Controlled-session-to-ambulatory measurement transfer across different body sites

**Technical problem:** palm/finger may provide better EDA/PPG data during controlled sessions while wrist/chest are more practical for daily monitoring.

**Proposed technical distinction:** a temporary high-fidelity pod establishes a concrete mapping/quality model used to qualify or normalize a different ambulatory measurement regime.

**Closest known art:** wearable calibration/reference systems; supervised→unsupervised addiction monitoring.

**Why it may differ:** the candidate technical space is specifically the **cross-anatomical/cross-modality transfer**, not simply “calibrate the device.”

**Current exposure:** **MODERATE–HIGH / INDETERMINATE**.

**Engineering work needed:** define transferred parameter, stability over days/weeks, re-calibration triggers, within-person benefit, missingness behavior.

**Further search needed:** cross-site PPG calibration, wrist↔finger transfer, palm↔wrist EDA mapping, personalized sensor-domain adaptation, multimodal wearable calibration patents.

### PD-04 — Role-aware measurement-validity states for AUD withdrawal/recovery research

**Technical problem:** autonomic features often require low-motion windows while high-motion/tremor windows may be clinically interesting rather than disposable.

**Proposed technical distinction:** explicit acquisition/feature-validity states in which the system changes what it considers interpretable based on movement and sensor quality, while retaining raw data.

**Closest known art:** adaptive sensing, activity-dependent quality gating, withdrawal tremor measurement, addiction monitoring.

**Why it may differ:** the proposed distinction ties motion state to **which physiological relationships remain valid**, not merely to activity classification.

**Current exposure:** **MODERATE** in this search, with substantial inventive-step risk.

**Engineering work needed:** define states/transitions, prove they reduce invalid features, ensure tremor is not discarded, establish ground truth.

**Further search needed:** state-machine/adaptive wearable patents combining tremor, HRV/EDA validity, and multi-channel acquisition policies.

### PD-05 — Thermally verified peripheral context only if it produces a measurable sensing benefit

**Technical problem:** local electronics heat and peripheral thermal state can bias temperature and optical/electrodermal interpretation.

**Proposed technical distinction:** a purpose-designed skin thermal path is used not as an outcome sensor but as part of an empirically validated peripheral-signal quality decision.

**Closest known art:** TMP117-type wearable thermal design and temperature-driven PPG compensation.

**Why it may differ:** only a specific physical thermal structure + multisensor quality effect could distinguish it.

**Current exposure:** **INDETERMINATE–HIGH**.

**Engineering work needed:** thermal simulation/measurement, heat-isolation prototype, skin-reference validation, PPG/EDA quality study.

**Further search needed:** thermal-island wearable patents, PPG temperature compensation, skin-contact detection, sensor self-heating compensation.

### Promising-direction caution

None of these directions should currently be called “the novelty.” Every one relies on further technical definition and additional prior-art searching. PD-01 to PD-04 are worth carrying forward primarily because they emerge naturally from real project constraints and are narrower than simple sensor aggregation.

---

## 15. Heavily Prior-Art-Exposed Directions

The following directions should **not** be treated as likely inventive cores unless a materially narrower mechanism is later identified:

- five-sensor multimodal wearable itself;
- wrist PPG + EDA + temperature + IMU;
- ECG + PPG coexistence;
- ECG↔PPG beat comparison or PAT;
- accelerometer-assisted PPG artifact reduction;
- generic sensor-confidence scoring;
- local IMU beside an electrode or optical sensor;
- synchronized multiple wearable nodes;
- chest ECG + wrist PPG architecture;
- generic temperature compensation/context for PPG;
- generic personal baselines;
- generic supervised training followed by ambulatory monitoring;
- generic wearable addiction/craving classification;
- generic alcohol-withdrawal wearable;
- accelerometer measurement of withdrawal tremor;
- generic calibration/reference device paired with everyday wearable;
- generic modularity;
- generic custom-PCB integration;
- using AUD rehabilitation as the only differentiating feature.

### Strongest negative finding

> **The current ECG + PPG + EDA + temperature + IMU combination itself appears heavily exposed to prior art and should probably not be treated as the inventive core.**

That conclusion is supported independently by foundational multimodal wearable art, commercial wrist systems, ECG+PPG systems, motion-quality patents, addiction/craving monitoring art, and withdrawal-monitoring art.

---

## 16. Insufficiently Defined Areas Blocking Assessment

### 16.1 Exact sensing topology

It is not yet known whether the final architecture is single-node, chest+wrist, chest+hand, session-configurable, or another arrangement. Broad topology art is crowded; a narrower novelty assessment requires the exact relationship among nodes.

### 16.2 Exact anatomical placement

Chest, wrist, finger/palm and ear have different signal and artifact behavior. Placement must be connected to a technical effect rather than convenience.

### 16.3 Synchronization requirement

The project has not yet specified whether it needs:

- approximate multimodal windows;
- beat-level alignment;
- PAT-level timing;
- sub-sample/post-hoc drift correction;
- one common hardware clock;
- independent local clocks.

Without a timing budget, a synchronization novelty hypothesis is too vague.

### 16.4 Local versus central acquisition

The exact analog path, ADC location, bus length, node count, and RF architecture are open. These choices are central to any claim of noise reduction or local sensor integrity.

### 16.5 Artifact policy

The repository asks whether high-motion data should be rejected, down-weighted, or modeled. The novelty analysis needs the actual policy and technical implementation.

### 16.6 Contact-quality sensing

Motion alone does not fully represent optical/electrode contact. It is unknown whether the design will explicitly measure contact pressure, electrode impedance, optical DC level, skin separation, local mechanical motion, or another quality variable.

### 16.7 Cross-modal interaction

It remains unclear which relationships are essential versus exploratory:

- ECG validates PPG beats?
- PPG validates ECG timing?
- IMU gates PPG only or all channels?
- EDA is simply analyzed independently or changes system state?
- temperature only annotates environment or actively changes quality decisions?
- tremor is a standalone feature or changes acquisition policy?

### 16.8 Baseline/reference strategy

“Personal baseline” is too generic. Novelty cannot be evaluated until the system defines:

- how baseline is acquired;
- at what site;
- under what motion/environmental state;
- whether it is sensor-specific or multimodal;
- how it drifts;
- when it is recalibrated;
- whether it changes hardware operation or only downstream statistics.

### 16.9 Controlled-session versus ambulatory relationship

The session-configurable form factor is potentially interesting, but it is unknown what technical information passes from one regime to another.

### 16.10 Withdrawal-tremor role

It is unknown whether tremor is:

- a primary research endpoint;
- a contextual feature;
- a motion-confounder label;
- all three with separate processing paths.

### 16.11 Temperature mechanism

A thermally isolated skin-facing design has not yet been specified. Without geometry and validation, temperature novelty is indeterminate.

### 16.12 Exact breakout and custom-hardware boundary

The project correctly distinguishes IC capability from exact board behavior. Any later inventive mechanical/electrical structure will likely belong to Level-3 custom integration and cannot be inferred from current breakouts.

### 16.13 Ground truth

Novel technical effects must be evaluated against a defined reference:

- ECG/PPG reference equipment;
- artifact annotations;
- contact-loss ground truth;
- clinician tremor/CIWA scoring;
- BrAC/BAC/transdermal alcohol ground truth;
- craving EMA;
- observed lapses;
- environmental reference measurements.

Without this, “better interpretation” is not measurable.

---

## 17. Targeted Questions for the Next Engineering Stage

1. **What is the single most important measurement failure the architecture should prevent?**  
   Example categories: false PPG heart-rate jumps, invalid HRV windows, EDA contact transients, thermal bias, cross-node timing error.

2. **Which sensing interfaces move independently enough to need their own local quality reference?**  
   Compare wrist, chest, finger/palm and any ear site.

3. **Can one global IMU predict corrupted windows at every remote sensor site as well as local IMUs can?**  
   This should be experimentally measured rather than assumed.

4. **When motion is high, which data should be invalidated and which should be preserved because movement itself is the target?**

5. **If withdrawal tremor is measured, is its optimal IMU placement the same as the optimal PPG/EDA artifact-reference placement?**

6. **Does chest-local ECG digitization measurably outperform long electrode/analog wiring in the exact Level-2 prototype?**

7. **What synchronization error is acceptable for each intended cross-modal feature?**  
   HR windows and PAT have very different requirements.

8. **Does ECG↔PPG beat agreement materially reduce false optical events beyond PPG+IMU alone?**

9. **Does temperature add measurable PPG/EDA quality information after local motion, contact, ambient temperature and activity are already known?**

10. **Can the TMP117 be made into a repeatable skin-facing thermal measurement with the current breakout, or is a custom thermal island required?**

11. **What exactly does a controlled finger/palm session teach the ambulatory wrist/chest system?**  
    A scalar baseline, distribution, cross-site transfer function, quality model, sensor-placement model, or something else?

12. **How stable is that transfer across hours, days and weeks?**

13. **What event triggers re-baselining or re-calibration?**

14. **Can the architecture demonstrate a technical benefit without using an AUD label?**  
    If not, the supposed novelty may be only intended use.

15. **Which project feature remains technically distinctive if the words alcohol, AUD, craving, withdrawal and rehabilitation are removed?**  
    This is a useful stress test for technical novelty.

16. **What raw data and quality metadata must be retained so that future algorithms are not locked to today's hypotheses?**

17. **Which form factor is needed only for research flexibility and which physical distinction is expected to survive into integrated hardware?**

18. **Would a two-node quality-aware system still provide a measurable advantage if all sensors were tested in a non-AUD population?**

19. **What failure case specifically defeats a conventional Empatica-like wrist quartet or a conventional chest ECG + wrist PPG system?**

20. **Can that failure case be reproduced and the proposed architecture shown to solve it quantitatively?**

These questions should be answered before attempting to select a patentable inventive core.

---

## 18. Recommended Next Prior-Art Searches

### 18.1 Distributed site-local quality

Suggested concepts:

- `wearable distributed sensors local signal quality accelerometer physiological`
- `body sensor network per-node signal quality confidence`
- `local motion sensor electrode contact quality wearable`
- `physiological sensor interface motion confidence local accelerometer`
- `cross-device quality gated physiological fusion`
- `distributed wearable sensor quality flags synchronization`
- `multi-node physiological sensing contact quality arbitration`

Useful CPC/IPC neighborhoods to expand from relevant families:

- **A61B5/00** — measuring for diagnostic purposes;
- **A61B5/024 / A61B5/0245** — pulse/heart-rate related optical/electrical sensing families;
- **A61B5/0402 / A61B5/33** — electrocardiography related classes;
- **A61B5/053 / A61B5/0537** — impedance/conductance-related measurements;
- **A61B5/11** — movement-related measurement;
- **A61B5/6801** and neighboring wearable/body-attached arrangements;
- **G16H** classes for health-data processing only when tied to technical sensing mechanisms;
- body-area-network synchronization classes exposed by WO2018134380A1.

The exact subgroup numbers should be rechecked from the live classification tree before relying on them as exhaustive search classes.

### 18.2 Tremor-as-signal versus motion-as-artifact

Queries:

- `alcohol withdrawal tremor wearable PPG artifact`
- `tremor aware physiological signal quality wearable`
- `accelerometer tremor preserve artifact rejection ECG PPG`
- `motion phenotype artifact physiological wearable tremor`
- `withdrawal tremor multimodal wearable heart rate electrodermal`
- `tremor gated HRV EDA wearable`

Citation-chain starting points:

- US4306291A;
- Norouzi et al. 2017;
- Carver/Aarabi withdrawal-tremor papers;
- Samsung confidence-indicator family;
- local-electrode IMU artifact-removal literature.

### 18.3 Controlled-to-ambulatory cross-site transfer

Queries:

- `wrist finger PPG calibration wearable cross-site`
- `palm wrist electrodermal calibration transfer wearable`
- `reference sensor calibrate wearable different body site`
- `temporary reference wearable personal calibration physiological`
- `session calibration ambulatory physiological monitoring wearable`
- `domain adaptation wearable sensor body location calibration`
- `cross-placement physiological normalization PPG EDA`

Search both patents and literature. This area is likely to use terms such as **calibration**, **reference sensor**, **domain adaptation**, **transfer calibration**, **cross-site mapping**, **placement normalization**, and **personalization** rather than “controlled session.”

### 18.4 Quality-aware synchronization

Queries:

- `wearable physiological synchronization signal quality anchor`
- `ECG PPG synchronization quality gated`
- `body sensor network clock synchronization signal confidence`
- `physiological feature clock alignment corrupted signal`
- `heartbeat synchronization wearable quality index`
- `cross-device physiological alignment motion artifact`

Start from:

- WO2018134380A1;
- US20240366159A1;
- WO2024235828A1;
- EP4563076A1.

Then inspect cited/citing families and classifications.

### 18.5 Thermal/contact quality

Queries:

- `skin temperature PPG signal quality compensation wearable patent`
- `thermal isolation skin temperature wearable PPG`
- `sensor self heating skin temperature contact quality wearable`
- `temperature compensated electrodermal activity wearable`
- `PPG perfusion temperature quality index`
- `thermal island wearable skin sensor patent`

Start from WO2024181778A1 and related temperature-compensation citations.

### 18.6 AUD-specific technology

Queries:

- `alcohol use disorder wearable physiological patent`
- `alcohol craving wearable electrodermal heart rate patent`
- `alcohol withdrawal wearable tremor ECG EDA patent`
- `relapse prediction physiological wearable alcohol patent`
- `rehabilitation wearable alcohol autonomic monitoring`
- `CIWA accelerometer wearable`
- `alcohol withdrawal body sensor network`
- `alcohol recovery personal baseline wearable`

Expand beyond alcohol-specific terms into:

- addiction;
- substance use disorder;
- drug craving;
- withdrawal syndrome;
- relapse/lapse;
- autonomic monitoring;
- behavioral health wearables.

### 18.7 Indian follow-up

Use official Indian Patent Office/InPASS searching for:

- application **202041020428**;
- applicant/assignee **Velectron Labs Private Limited**;
- inventor **Abhijit Nair**;
- title phrase “Smart Wearable Device For Monitoring Withdrawal Symptoms”; and
- CPC/IPC families returned by the official record.

Download and review at minimum:

- complete specification;
- claims;
- drawings;
- publication bibliographic sheet;
- examination report/search strategy;
- amendments and current status.

The secondary record suggests prosecution documents exist, including a First Examination Report, but those should be verified against the official registry before legal reliance.

---

## 19. Source / Prior-Art Ledger

Dates and assignees below are recorded from the cited patent/publication records where available. Patent status shown by Google or secondary aggregators is not treated as a legal opinion.

### P-01 — Foundational multimodal wrist/hand biosensor

- **Title:** Washable wearable biosensor
- **Type:** U.S./PCT patent family
- **Publications:** US20100268056A1; US8140143B2; WO2010120945A1
- **Applicant/assignee:** Massachusetts Institute of Technology
- **Priority date:** 2009-04-16
- **U.S. application publication:** 2010-10-21
- **Jurisdiction:** US / WO
- **Relevant features:** wearable physiological sensing including optical pulse/PPG-related sensing, skin conductance, temperature and accelerometry/motion; motion can affect/qualify physiological interpretation.
- **Project overlap:** broad wrist/peripheral multimodality; motion-aware sensing.
- **Important difference:** not AUD-specific; exact project ECG/distributed topology differs.
- **Why it matters:** strong foundational exposure for the wrist quartet and simple multimodal aggregation.
- **Stable URL:** https://patents.google.com/patent/US20100268056A1/en

### P-02 — Google multimodal wristband

- **Title:** Screenless Wristband with Virtual Display and Edge Machine Learning
- **Type:** U.S. patent application
- **Publication:** US20210121136A1
- **Applicant/assignee:** Google LLC
- **Priority date:** 2019-10-28
- **Publication date:** 2021-04-29
- **Jurisdiction:** US
- **Relevant features:** wrist wearable with EDA, PPG, skin temperature, inertial sensing and optional ECG among broad sensors.
- **Project overlap:** almost the entire modality set at wrist level.
- **Important difference:** general wearable/HMI context.
- **Why it matters:** simple co-location is not new.
- **Stable URL:** https://patents.google.com/patent/US20210121136A1/en

### P-03 — Addiction/craving wearable

- **Title:** Edge-intelligent IoT-based Wearable Device For Detection of Cravings in Individuals
- **Type:** U.S. patent family
- **Publications:** US20200085301A1; US11375896B2
- **Inventors:** Megan Reinhardt; Nicole Gilbertson; Premananda Indic; Prabha Sundaravadivel
- **Priority date:** 2017-08-18
- **Filing date:** 2019-11-19
- **Application publication:** 2020-03-19
- **Grant publication:** 2022-07-05
- **Jurisdiction:** US
- **Relevant features:** addiction/craving monitoring; wearable motion, EDR/EDA, temperature, heart-rate-related data; supervised training followed by unsupervised monitoring; edge/cloud classification.
- **Project overlap:** AUD/craving context, multimodal autonomic/motion sensing, personal training/baseline, ambulatory phase.
- **Important difference:** exact hardware/placement/quality architecture differs.
- **Why it matters:** one of the closest conceptual references to broad rehabilitation/craving monitoring.
- **Stable URL:** https://patents.google.com/patent/US20200085301A1/en

### P-04 — Samsung physiological confidence indicator

- **Title:** Confidence indicator for physiological measurements using a wearable sensor platform
- **Type:** U.S. patent family
- **Publication:** US10595786B2; related US20190192080A1
- **Assignee:** Samsung Electronics Co., Ltd.
- **Priority date:** 2014-03-24
- **Continuation filing:** 2019-03-01
- **Application publication:** 2019-06-27
- **Grant publication:** 2020-03-24
- **Jurisdiction:** US
- **Relevant features:** physiological data + artifact data; accelerometer/motion; correlation and confidence indicators; modular wearable sensor platform.
- **Project overlap:** cross-modal quality/confidence and IMU-based artifact handling.
- **Important difference:** general wearable, not AUD-specific.
- **Why it matters:** strongly exposes generic “multimodal confidence score” hypotheses.
- **Stable URL:** https://patents.google.com/patent/US10595786B2/en

### P-05 — PPG motion-artifact reduction

- **Title:** Reducing Motion Induced Artifacts in PPG Signals
- **Type:** U.S. patent application
- **Publication:** US20170164847A1
- **Priority date:** 2015-12-15
- **Publication date:** 2017-06-15
- **Jurisdiction:** US
- **Relevant features:** 3-axis accelerometer reference used to reduce/estimate optical motion artifact.
- **Project overlap:** PPG + local IMU.
- **Important difference:** no AUD context.
- **Why it matters:** makes generic PPG+IMU correction very high exposure.
- **Stable URL:** https://patents.google.com/patent/US20170164847A1/en

### P-06 — Heartbeat-based independent-sensor synchronization

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
  - Espacenet bibliographic route: https://worldwide.espacenet.com/publicationDetails/biblio?FT=D&locale=en_EP&CC=EP&NR=4563076A1&

### P-09 — Wireless body-sensor synchronization

- **Title:** Method for providing synchronization between plurality of wireless body sensors
- **Type:** PCT publication
- **Publication:** WO2018134380A1
- **Assignee:** Byteflies N.V.
- **Priority date:** 2017-01-20
- **Filing date:** 2018-01-19
- **Publication date:** 2018-07-26
- **Jurisdiction:** WO/PCT
- **Relevant features:** synchronization/master timing among multiple body-worn wireless sensing nodes.
- **Project overlap:** distributed multi-node prototype.
- **Important difference:** generic body sensor network.
- **Why it matters:** multi-node synchronization itself is conventional.
- **Stable URL:** https://patents.google.com/patent/WO2018134380A1/en

### P-10 — ECG+PPG+EDA wearable interaction

- **Title:** Biometric wearable for continuous heart rate and blood pressure monitoring
- **Type:** U.S. patent
- **Publication:** US10709339B1
- **Assignee:** Senstream, Inc.
- **Priority date:** 2017-07-03
- **Filing date:** 2018-07-03
- **Publication/grant date:** 2020-07-14
- **Jurisdiction:** US
- **Relevant features:** wearable ECG electrodes and PPG; electrodes can be used for EDA/skin impedance; cross-signal timing.
- **Project overlap:** ECG+PPG+EDA and timing interaction.
- **Important difference:** cardiovascular/BP purpose and different physical implementation.
- **Why it matters:** this multimodal combination and timing interaction are already known.
- **Stable URL:** https://patents.google.com/patent/US10709339B1/en

### P-11 — Indian withdrawal wearable search lead

- **Title:** A Smart Wearable Device For Monitoring Withdrawal Symptoms In A User
- **Type:** Indian patent application — **secondary bibliographic/search lead requiring official verification**
- **Application number:** 202041020428
- **Applicant reported:** Velectron Labs Private Limited
- **Inventor reported:** Abhijit Nair
- **Filing date reported:** 2020-05-14
- **Publication date reported:** 2021-11-19
- **Jurisdiction:** IN
- **Relevant features reported:** arm/smart-band withdrawal monitoring with multiple physiological sensors including pulse/heart-related, optical, alcohol-related and movement/tremor measurements.
- **Project overlap:** alcohol withdrawal wearable and multimodal sensing.
- **Important difference:** reported sensor set includes direct/near-direct alcohol-oriented measurement concepts and EMG; exact claims must be read officially.
- **Why it matters:** high-priority Indian art to retrieve through InPASS.
- **Status note:** do not rely on secondary status labels; official prosecution/status must be checked.
- **Search-lead URL:** https://www.quickcompany.in/patents/a-smart-wearable-device-for-monitoring-withdrawal-symptoms-in-a-user

### P-12 — Multimodal withdrawal monitoring/remediation

- **Title:** Integrated artificial intelligence based system for monitoring and remediating withdrawal symptoms
- **Type:** U.S. patent family
- **Publication:** US20230355177A1 / US12290383B2 and related family members
- **Assignee:** Rekovar, Inc. (family record)
- **Priority date:** 2021-11-17
- **Continuation filing:** 2023-04-27
- **Application publication:** 2023-11-09
- **Grant publication:** 2025-05-06
- **Jurisdiction:** US
- **Relevant features:** wearable withdrawal monitoring; optical pulse/oxygen sensing, temperature, accelerometer, skin impedance/bioelectrical sensing, EMG and broader behavioral sensing; may work with other wearable/chest components.
- **Project overlap:** multimodal withdrawal hardware and movement/physiology fusion.
- **Important difference:** broad withdrawal/remediation system and different sensor set/use.
- **Why it matters:** “wearable multimodal withdrawal system” is already crowded.
- **Stable URL:** https://patents.google.com/patent/US12290383B2/en

### P-13 — SOBR Safe multimodal alcohol-related wrist device

- **Title:** Wearable data collection device with non-invasive sensing
- **Type:** PCT family
- **Publication:** WO2022099262A1; related US20240008812A1 / EP4240229A1 and others
- **Assignee:** SOBR Safe, Inc. / family records
- **Priority date:** 2020-11-03
- **Filing date:** 2021-11-03
- **Publication date:** 2022-05-12
- **Jurisdiction:** WO/PCT
- **Relevant features:** wrist-worn multimodal physiological sensing including PPG, ECG/biopotential, EDA/skin impedance, temperature and inertial sensing, with substance/alcohol-oriented sensing concepts.
- **Project overlap:** broad modality set + alcohol context + wrist embodiment.
- **Important difference:** includes alcohol/substance sensing mechanisms not present in current project.
- **Why it matters:** very close broad landscape reference.
- **Stable URL:** https://patents.google.com/patent/WO2022099262A1/en

### P-14 — Early transdermal alcohol sensor

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
- **Authors:** Beach et al.
- **Publication:** Healthcare Technology Letters, 2021
- **DOI:** 10.1049/htl2.12016
- **Relevant features:** multiple local IMUs associated with individual electrophysiology interfaces; adaptive motion-artifact removal.
- **Project overlap:** site-local IMU idea for ECG/electrode motion.
- **Important difference:** ECG/EEG instrumentation, not AUD.
- **Why it matters:** local IMUs are not novel by themselves.
- **Stable URL:** https://doi.org/10.1049/htl2.12016

### L-02 — AUD recovery ambulatory physiology

- **Title:** Associations Between Physiological Signals Captured Using Wearable Sensors and Self-reported Outcomes Among Adults in Alcohol Use Disorder Recovery
- **Type:** Peer-reviewed paper
- **Authors:** Alinia et al.
- **Publication:** JMIR Formative Research, 2021
- **DOI:** 10.2196/27891
- **Relevant features:** ambulatory EDA/HRV and self-reported stress/emotion/context in AUD recovery.
- **Project overlap:** rehabilitation/recovery physiology and longitudinal sensing.
- **Important difference:** research study, not the current hardware architecture.
- **Why it matters:** AUD-recovery wearable physiology is not a new application concept.
- **Stable URL:** https://doi.org/10.2196/27891

### L-03 — 100-day physiology/craving/lapse study

- **Title:** An ideographic study into physiology, alcohol craving and lapses during one hundred days of daily life monitoring
- **Type:** Peer-reviewed paper
- **Authors:** van Lier et al.
- **Publication:** Addictive Behaviors Reports, 2022
- **DOI:** 10.1016/j.abrep.2022.100443
- **Relevant features:** long-duration within-person monitoring; large individual variability; craving/lapse relationships.
- **Project overlap:** personalized longitudinal recovery monitoring.
- **Important difference:** research protocol rather than hardware novelty.
- **Why it matters:** supports the repository’s scientific rationale for within-person analysis but also shows the concept is established.
- **Stable URL:** https://doi.org/10.1016/j.abrep.2022.100443

### L-04 — Recent craving biosignature work

- **Title:** Using Passive Sensing to Isolate a Biosignature for Craving Among Individuals in Early Alcohol Use Disorder Recovery
- **Type:** Peer-reviewed paper
- **Authors:** Mei, Emery & Eddie
- **Publication:** Addiction Biology, 2026
- **DOI:** 10.1111/adb.70182
- **Publication date:** 2026-08-10
- **Relevant features:** passive physiological sensing including ECG/HRV with ecological craving assessment in early AUD recovery.
- **Project overlap:** current landscape for physiological craving monitoring.
- **Important difference:** study method/device differs; does not establish the present hardware architecture.
- **Why it matters:** shows that physiological craving-sensing research is active and recent.
- **Stable URL:** https://doi.org/10.1111/adb.70182

### L-05 — Modern accelerometer withdrawal-tremor quantification

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

Similarly, generic personalization, generic addiction monitoring, generic withdrawal monitoring, accelerometer-based tremor measurement, generic PPG motion correction, generic distributed synchronization, and generic chest+wrist topology are all heavily exposed.

The directions most worth carrying forward are narrower:

1. **site-local quality states with cross-site admissibility in a distributed sensing system;**
2. **separating withdrawal tremor as a desired physiological/motor signal from motion as an artifact source for other sensors;**
3. **a precisely defined controlled-session-to-ambulatory transfer mechanism across different anatomical sensing sites;**
4. **role-aware measurement-validity states that preserve symptom motion while restricting invalid autonomic interpretations;**
5. **a thermally explicit peripheral quality mechanism only if a specific physical design produces measurable PPG/EDA benefit.**

These are not established as novel. They are the hypotheses that survived this search as being technically specific enough to justify deeper engineering definition and professional prior-art searching.

The immediate next step should therefore be **engineering specification of the mechanism**, not claim drafting: decide which failure mode is being solved, define the topology/state/quality variables quantitatively, build a comparative experiment, and then repeat the prior-art search around that narrowed mechanism.