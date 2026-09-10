# AUD Multimodal Hardware — Physical/Form-Factor Exploration

**Status:** exploratory engineering-design input; no final form factor selected  
**Date:** 2026-09-10  
**Primary project context:** `docs/aud_multimodal_hardware_evidence_map.md` and `docs/aud_sensor_role_definition.md`

> **Scope statement:** This document explores physical embodiments of the current sensing system. It does not select a final form factor, body placement, sensor inclusion set, hardware architecture, or patent novelty.

## Evidence language used in this document

- **EVIDENCE** — a published, manufacturer-documented, or project-documented fact.
- **ENGINEERING INFERENCE** — a reasoned consequence of known sensing, electrical, mechanical, or human-factors constraints. It still requires prototype verification.
- **DESIGN HYPOTHESIS** — a candidate embodiment or arrangement proposed for testing. It is not established merely because it is plausible.

Evidence strength is attached to a **claim**, not to a sensor or body site in the abstract. A site may be well supported for one measurement objective and weak for another.

---

## 1. Purpose and Scope

This document explores realistic physical forms for the current candidate sensing stack while preserving, as far as possible, useful physiological signal quality, human usability, and engineering feasibility.

It asks:

> **What physical forms could realistically host these sensing modalities while preserving useful signal quality, human usability, and engineering feasibility?**

It does **not** answer:

> **What final device should be built?**

The form-factor discussion starts from sensing requirements rather than product aesthetics. In particular, it does not assume that every modality should be co-located, that the wrist is the default site, or that a compact single enclosure is preferable to a distributed prototype.

The existing project documents establish important scientific boundaries that are retained here: ECG is the strongest candidate for cardiac timing and ECG-derived HR/HRV research; PPG is a peripheral optical pulse/perfusion channel and PRV is not automatically ECG HRV; the legacy tinyGSR should be treated as relative unless board-specific calibration is proven; TMP117 measures local sensor/die temperature and becomes a skin-temperature measurement only through validated thermal coupling; the MPU-6050 is primarily a motion/context channel with an additional research role for tremor; and multimodal fusion does not create alcohol specificity. The project also explicitly separates IC capability from the exact breakout-board implementation.

### Out of scope

This document does not define patent novelty or claims, final sensor inclusion/exclusion, final PCB, enclosure dimensions, exact pins, exact buses, firmware architecture, exact sample rates, packet formats, cloud systems, ML models, clinical decision algorithms, or medical-grade performance. Where these issues affect physical feasibility they are carried forward only as design questions.

---

## 2. Current Hardware Context

The current candidate hardware is development-oriented rather than wearable-product hardware. That distinction materially changes which form factors are realistic today.

| Candidate | Current project role | Physical/form-factor boundary that matters here |
|---|---|---|
| **ESP32 DevKit V1, 30-pin, ESP-WROOM-32-family** | Acquisition, timestamping, buffering, coordination, transport | “DevKit V1” is not one uniquely controlled carrier design. Exact regulator, ADC routing, RF/power behavior, connectors, dimensions, and pin exposure must be verified on the actual unit before a packaging claim is locked. |
| **ProtoCentral tinyGSR, legacy board, PCB 11/22** | Relative EDA/GSR channel unless exact calibration is proved | Electrodes and skin site matter more than the controller-board position. Exact legacy-board revision/electrode implementation remains unresolved. |
| **CJMCU-8232 / AD8232, PCB VS82** | Conditioned single-lead ECG-like analog waveform | The AD8232 IC is documented, but exact VS82 gain/filter/RLD/passive implementation is not. The electrode geometry and cable/front-end arrangement are therefore part of the prototype, not an incidental accessory. |
| **SmartElex MAX30101 breakout** | Reflective optical PPG acquisition | The MAX30101 IC is documented; the exact SmartElex optical window, shielding, contact surface and mechanical retention are not verified. An IC suitable for reflectance PPG does not make an arbitrary breakout mechanically suitable for every anatomical site. |
| **SmartElex TMP117 breakout** | Local temperature/context channel | The sensor must be thermally coupled to the intended skin site and isolated from competing heat sources. A general breakout board is not automatically a validated skin-temperature probe. |
| **GY-521 / MPU-6050** | Local/gross motion, orientation, tremor and artifact context | IMU interpretation changes with body segment, orientation and mounting stiffness. A controller-mounted IMU is not automatically representative of motion at a remote optical or electrode interface. |

### Three maturity layers must remain separate

**Level 1 — bench / tethered research prototype.** Breakout boards can remain off-body or in a nearby enclosure while sensing contacts/electrodes are placed on the body. This is useful for electrical characterization, sensor validation and placement experiments, but cables can themselves introduce artifact.

**Level 2 — body-worn distributed prototype using breakout boards.** Boards can be placed in pouches, straps, small boxes, a belt/harness or multiple modules, while the actual sensing interfaces contact the appropriate anatomical sites. This is a realistic near-term research layer if wiring, strain relief, hygiene and synchronization are handled deliberately.

**Level 3 — integrated custom wearable / custom PCB.** Optical windows, skin electrodes, thermal islands, compact analog front ends, power distribution and local motion sensing can be designed around the body site. Several attractive wearable configurations become realistic only at this level.

A form factor that is physically plausible at Level 3 should not be described as feasible with the exact current breakouts unless that has been demonstrated.

---

## 3. Why Form Factor Matters for This Sensing Stack

The modalities impose different mechanical interfaces:

- ECG requires a usable electrical lead vector, stable skin-electrode contact and controlled electrode/cable motion.
- PPG requires stable optical coupling, appropriate pressure, control of ambient-light leakage and sufficient local perfusion.
- EDA requires stable electrode contact at a physiologically informative sweat-gland site; the most responsive sites can be inconvenient for daily hand use.
- Local temperature requires a deliberately engineered thermal path to skin and isolation from the controller, battery, regulators, LEDs and ambient airflow.
- IMU data are intrinsically location-specific because the sensor measures the motion of the segment to which it is attached.

This means “put all sensors in one box” is not a neutral packaging decision. It changes what each modality measures, how much artifact it sees, and whether the sensor is observing the same local mechanical/thermal event as the physiological channel it is intended to contextualize.

The current project also contains an analog path: electrode potentials are conditioned by the AD8232 and then sampled downstream. Long or moving electrode cables increase susceptibility to common-mode and motion-related interference; Analog Devices explicitly notes greater interference susceptibility with long cables in biopotential systems. The ESP32 ADC itself is documented as noise-sensitive, and ADC2 use can conflict with Wi-Fi on classic ESP32. Therefore controller/RF placement and analog-path testing are physical design concerns even before firmware is finalized.

Likewise, a body-spanning I²C harness should not be assumed benign. I²C timing is constrained by bus capacitance and rise time; long wires, multiple breakout pull-ups and body motion can create an electrical problem even if the logical device addresses are compatible. A distributed-node architecture can shorten local sensor buses, but then synchronization, batteries and inter-node communication become new constraints.

---

## 4. Sensor-by-Sensor Placement Requirements

### 4.1 ECG / AD8232

#### Evidence-backed placement options

**Chest / torso — strongest general candidate for passive continuous single-lead acquisition.** Ambulatory ECG patches commonly use two closely spaced embedded electrodes on the pectoral/chest region for continuous multi-day recording. A 2019 electrode-location study of 102 single-lead configurations in 19 healthy subjects found the best SNR/QRS-oriented pair in the left chest region above standard V1–V4 locations, with orientation approximately along the cardiac electrical axis. That study was performed under quiet conditions and optimized its own device/objective, so it does not prove one universal placement for this prototype, but it reinforces that torso geometry materially affects signal quality. **Evidence level: strong for torso/chest as an established wearable single-lead site; moderate for any specific pair in this project.** [N1, N2]

**Limb-lead style / hand-to-hand geometry — valid for single-lead spot measurements, less convenient for passive continuous monitoring.** Smartwatch ECG studies commonly obtain an Einthoven-I-like lead by measuring between the wrist and the opposite index finger. This is a real single-lead ECG, but it requires intentional two-point contact and is therefore typically an intermittent measurement rather than a passive continuous wrist stream. **Evidence level: strong for spot acquisition; weak for passive all-day acquisition using this interaction.** [N3]

**Wrist-only or same-wrist continuous ECG — not impossible, but not established for the present hardware.** Research systems have explored wrist electrodes and unconventional geometries, but common clinical/consumer smartwatch ECG uses a second contact on the contralateral hand. Therefore a “single wrist unit with continuous ECG” must be treated as a design hypothesis requiring dedicated electrode-geometry and signal-quality validation, not as a default consequence of using an AD8232. **Evidence level: exploratory for the exact intended configuration.**

#### Electrode spacing and geometry

A single-lead ECG is a voltage difference between two sensing points; it is not “one electrode” and it is not a 12-lead ECG. Lead-vector orientation and separation affect amplitude and morphology. Closely spaced patch electrodes can support useful rhythm monitoring, but may provide lower amplitudes and limited spatial information compared with broader lead geometries. The geometry should therefore be selected against the project’s target output — likely robust R-peak timing/HRV research rather than diagnostic morphology — and then experimentally validated. [N1, N2]

#### Wet versus dry contact

Conventional Ag/AgCl gel electrodes provide well-established contact for short/medium recordings but gels can dry and adhesives can irritate during prolonged wear. Dry/textile electrodes avoid gel replacement but are more sensitive to contact pressure, skin humidity and motion; rigid dry electrodes can show high contact impedance and motion artifact. No electrode technology should be selected here solely because it appears more “wearable.” [N4]

#### Motion and cable implications

**ENGINEERING INFERENCE:** if the current VS82 breakout is used, keeping the AD8232 front end physically close to the ECG electrodes is preferable to routing long high-impedance electrode leads across the body. The conditioned analog output can then travel a more controlled path to the ADC, or a local node can digitize it near the chest. This is not a final architecture decision; it is a reason to test chest-local acquisition against a central-controller harness.

**DESIGN HYPOTHESIS:** a chest ECG module with a local IMU may provide a better artifact reference than an IMU located only on a wrist controller because electrode-interface motion is local.

#### Long-term wear implications

Chest patches can be well tolerated for days, but adhesion, hair, sweat, showering, skin irritation and replacement procedure matter. Wire-based systems add snagging and cable-motion problems. A Level-2 breakout prototype should therefore not pretend to have the wearability of a Level-3 integrated patch.

**Form-factor implication:** ECG does not mathematically force a chest component, but for **passive continuous** single-lead acquisition with the current research objective it strongly biases the architecture toward a torso/chest electrode component or another deliberately spaced electrode geometry.

---

### 4.2 PPG / MAX30101

PPG site choice is a trade-off among perfusion, motion, contact mechanics and wearability. Measurement site significantly changes waveform amplitude and morphology.

#### Finger

Finger PPG is strongly supported for high pulse-wave amplitude and analyzability. In a six-site comparison, finger recordings produced the highest percentage of analyzable waveforms and the highest mean amplitude. A newer reflective-site study likewise identified the finger as the strongest site and wrist as the most challenging among several tested sites. The drawback is practical: fingers are heavily used in ordinary activities, so clips, rings or exposed boards can interfere with dexterity and experience large local motion/pressure changes. **Evidence level: strong for signal potential; moderate/poor for unconstrained continuous practicality depending on fixture.** [N5, N6]

#### Wrist / forearm

Wrist PPG is popular because it is easy to wear continuously, not because it is universally the highest-quality optical site. Ambulatory studies report highly variable wrist signal quality, particularly during daytime movement; contact pressure, posture, local perfusion, skin temperature and strap fit matter. **Evidence level: strong for continuous-wear feasibility; strong for motion/contact limitations.** [N7]

#### Ear / ear canal / auricular sites

Ear PPG is a credible alternative because the ear can offer strong perfusion and a relatively stable sensor location. Hearable reviews report lower movement error at some ear-canal placements, although speaking, chewing and poor fit can still create substantial artifact. **Evidence level: moderate-to-strong as an established PPG site; low for the exact SmartElex breakout in an ear fixture.** [N8]

#### Forehead

Reflectance PPG has been studied on the forehead, including low-perfusion and head-mounted contexts. It can offer a useful vascular bed but requires a stable headband/adhesive fixture, controlled pressure and ambient-light shielding. **Evidence level: moderate for PPG feasibility; context-dependent for ambulatory comfort.** [N9]

#### Chest / torso and upper arm

Reflective PPG has been demonstrated at torso/arm sites in research devices, but waveform quality and optical mechanics are configuration-specific. For the present breakout, these should be treated as **exploratory placement candidates**, not assumed substitutes for finger or wrist.

#### Exact SmartElex breakout constraint

**EVIDENCE:** the project has verified MAX30101 IC capability but not a primary SmartElex schematic, optical window or wearable mechanical interface.

**ENGINEERING INFERENCE:** the rigid breakout will likely require a fixture that controls sensor-to-skin pressure, excludes ambient light and prevents board rocking. A bare PCB taped loosely to skin is not equivalent to a purpose-designed optical wearable.

**DESIGN HYPOTHESIS:** for Level 1/2 controlled sessions, a finger contact fixture could prioritize signal quality; for Level 3 ambulatory use, a custom wrist or ear optical stack may offer better usability.

**Form-factor implication:** finger is a strong research site, wrist is a strong convenience site, and ear is a credible alternative. They should not be treated as interchangeable.

---

### 4.3 EDA / tinyGSR

EDA is especially site-dependent because eccrine sweat-gland density and responsivity vary across the body.

#### Palmar / finger sites

Palm and volar-finger placements are the conventional high-response locations. Comparative work found palm/finger sensors detected more skin conductance responses than wrist sensors, and traditional psychophysiology recommendations emphasize palmar/volar hand sites. **Evidence level: strong for laboratory EDA sensitivity.** [N10, N11]

#### Wrist

Wrist EDA is attractive for wearability and has been used successfully in ambulatory research devices, but it is not physiologically equivalent to palmar EDA. Comparative studies have reported fewer/weaker responses at the wrist and sometimes poor performance for SCR detection relative to palm/finger. Other studies show meaningful wrist EDA in specific settings, so “wrist EDA is useless” would also be too strong. **Evidence level: strong that site changes response; moderate that wrist is feasible; insufficient to claim equivalence to palm/finger.** [N10, N12]

#### Plantar / foot sites

Plantar sites can be electrodermally responsive and in some studies correlate more closely with fingers than other alternative sites. They are generally less attractive for a multi-sensor daily wearable because of footwear, hygiene, pressure, walking artifact and donning burden. **Evidence level: moderate physiological option; weak practical fit for the present first prototype.** [N12]

#### Torso / non-palmar alternatives

Recent work has explored chest/back/torso EDA for integrated wearable systems. Results are promising enough to justify study but weaker and more location/protocol-dependent than hand sites. **Evidence level: exploratory-to-moderate; requires site-specific validation.** [N13]

#### Motion, comfort and long-term contact

Finger/palm electrodes can interfere with hand use and are prone to pressure/motion artifact during manipulation. Wrist electrodes are easier to wear but may sacrifice response sensitivity. Sweat saturation, skin hydration, electrode pressure, temperature and motion affect both.

**Exact tinyGSR constraint:** the legacy 11/22 board remains a relative-output device unless calibration is proven. Physical placement experiments should therefore emphasize within-unit repeatability, stable electrode material/area/contact, and within-person response quality before attempting cross-site amplitude comparisons.

**Form-factor implication:** EDA is one of the strongest reasons not to force every modality onto the wrist. A hand/finger module may be scientifically preferable for controlled sessions even if it is not the long-term ambulatory choice.

---

### 4.4 Peripheral Temperature / TMP117

The TMP117 measures its own local die temperature. Turning that into a useful skin-temperature channel is a **mechanical and thermal design problem**.

#### Candidate anatomical sites

Wrist, hand/finger, chest, upper arm, thigh and other skin sites are all used in contact thermometry research. They do not represent the same physiological quantity: local skin temperature differs by site and responds differently to perfusion, clothing and ambient conditions. A systematic review of contact thermometry found that attachment method, pressure, environment, sensor type and placement can produce practically meaningful bias. **Evidence level: strong that setup matters; no universal “best skin-temperature site” for this project.** [N14]

- **Wrist / finger:** easy to integrate with peripheral sensors and sensitive to peripheral vasoconstriction/vasodilation, but also strongly exposed to ambient temperature and local perfusion changes.
- **Chest / upper arm:** easy to stabilize under clothing or a patch/strap and often used in skin-temperature research, but still measures local skin temperature and can be influenced by clothing, exercise and enclosure heat.
- **Any site:** is only useful if the sensor has a repeatable conductive path to skin and the intended site is documented.

#### Thermal coupling and enclosure effects

TI’s wearable guidance for TMP117 explicitly emphasizes good conductive contact with skin and thermal isolation from other heat sources. It recommends physically separating the sensing element from higher-power electronics and designing the PCB/thermal path so the skin is the dominant heat source. [N15]

**ENGINEERING INFERENCE:** co-locating TMP117 immediately beside an ESP32, regulator, battery, charging circuit or high-duty optical LEDs in a small sealed enclosure can bias the reading toward device temperature. A custom integrated unit should therefore provide a thermally isolated skin-contact island rather than simply placing the temperature IC anywhere on the main board.

**DESIGN HYPOTHESIS:** at Level 2, the TMP117 breakout may need to be physically separated from the controller board and held against skin by a dedicated low-mass contact fixture. This should be validated against a reference under intended ambient and wear conditions.

**Form-factor implication:** temperature can share a general body module with PPG/EDA/IMU, but its *thermal path* may need physical separation inside that module.

---

### 4.5 IMU / MPU-6050

There is no universally best IMU site because the sensor measures the motion of the body segment to which it is attached.

#### Wrist / hand / finger

Useful for upper-limb movement, hand tremor and local motion at wrist PPG/EDA interfaces. Alcohol-withdrawal tremor research has measured hand tremor with handheld/smartphone accelerometry, and bilateral studies show that the two hands are not perfectly interchangeable in their measured tremor frequencies. **Evidence level: moderate for hand-based withdrawal-tremor quantification as a component of clinical assessment; not a diagnosis.** [N16, N17]

#### Forearm

May offer a more stable mounting than the hand while retaining upper-limb motion context. Whether it captures the intended tremor component as well as a hand/finger placement must be tested.

#### Chest / waist / lower back

Central-body placements are common for gross activity, posture, trunk movement and gait-level context. Gait reviews show lower-back and lower-limb placements are common, but also emphasize that optimal location depends on the target parameter. **Evidence level: strong that placement is task-dependent.** [N18]

#### Ankle / shank / foot

Strong candidates for gait-cycle and lower-limb kinematics, but they are poor proxies for wrist optical artifact or hand tremor. They would add another body node and are not automatically justified unless gait becomes a defined endpoint.

#### Local versus global motion context

A single controller-mounted IMU can provide gross activity context, but it is not perfect ground truth for motion at a remote ECG electrode or PPG optical interface. Research has attached IMUs to individual electrophysiology electrodes to obtain local motion references, showing that local motion can be more directly related to local artifact, although artifact-removal performance remains scenario-dependent. [N19]

For wrist PPG, co-located accelerometry is a well-established strategy for identifying motion-contaminated periods. [N20]

**ENGINEERING INFERENCE:** if motion-artifact interpretation is a primary role, the IMU should usually be placed with the artifact-prone sensor or body segment rather than solely with a distant controller. If both hand tremor and chest-ECG artifact matter, one IMU may not satisfy both objectives.

**Form-factor implication:** IMU placement should be defined by its intended role: local optical/electrode artifact, hand tremor, gross activity/posture, or gait. One location should not be assumed to solve all four.

---

### 4.6 ESP32 / controller placement

The ESP32 does not need direct skin contact. This gives the controller more placement freedom than the physiological sensors.

Potential Level-2 locations include a belt/pocket enclosure, chest/torso module, upper-arm/forearm enclosure, or one of several local sensing nodes. Selection should consider:

- distance to the AD8232 analog output and electrode front end;
- digital-bus wire length and capacitance;
- ability to strain-relieve cables;
- RF coexistence with sensitive analog measurements;
- battery/charging access;
- heat transfer into the TMP117 or PPG skin interface;
- wearer comfort and center of mass;
- whether the module’s IMU is meant to represent local sensor motion or merely gross activity.

**EVIDENCE:** Espressif documents classic ESP32 ADC noise sensitivity and ADC2/Wi-Fi coexistence limitations; the NXP I²C specification treats bus capacitance and rise time as design constraints rather than allowing arbitrary wire length. [N21, N22]

**ENGINEERING INFERENCE:** a single central ESP32 may be electrically possible for all modules, but “possible on a schematic” is not enough to establish a clean body-worn harness. Long analog/electrode wires and body-spanning I²C should be compared experimentally against local acquisition or synchronized nodes.

---

## 5. Sensor Co-location Compatibility

“Co-located” can mean either **same body region** or **same physical enclosure**. Those are not equivalent. Two sensors may be on the same wrist while still requiring separate skin-contact zones, optical barriers, thermal isolation or local mechanical fixation.

| Combination | Same body site? | Same enclosure? | Potential benefit | Main conflict / caution | Provisional interpretation |
|---|---|---|---|---|---|
| **ECG + PPG** | Conditional | Conditional/custom | Beat↔pulse cross-check; synchronized distal PPG can support PAT research | Best ECG and PPG sites may differ; chest PPG is less established than finger/wrist/ear; distal separation changes timing interpretation | **Separate sites often scientifically cleaner**: chest ECG + distal PPG. Same-site custom integration remains possible but must be validated. |
| **ECG + IMU** | Yes | Yes | Local motion reference for ECG/electrode movement; posture/activity context | IMU must be rigidly coupled to the relevant module; chest IMU does not measure hand tremor | **Strong co-location candidate** if ECG artifact is an IMU objective. |
| **PPG + IMU** | Yes | Yes | Local motion reference for optical artifact; activity context | Accelerometer is a reference, not perfect artifact ground truth | **Strongest co-location pair in the stack.** |
| **PPG + temperature** | Yes | Yes, with internal separation | Joint perfusion/thermal context; common wear site | PPG LEDs/electronics and enclosure heat can bias temperature; optical and thermal contact pressure requirements differ | **Good custom-wearable pairing** if thermal isolation is engineered. |
| **EDA + temperature** | Yes | Yes, with separate contact geometry | Thermal context for sweat/EDA interpretation | Sweat, electrode pressure and thermal path can interact; temperature must not measure board heat | **Feasible**, especially wrist/hand, but not trivial on exposed breakouts. |
| **EDA + IMU** | Yes | Yes | Local movement/contact context for EDA artifacts | High motion can be physiological activity and mechanical artifact simultaneously | **Strong contextual pairing.** |
| **EDA + PPG** | Yes | Yes/custom | Complementary sudomotor + peripheral pulse signals | Both are contact-sensitive; wrist placement favors convenience but compromises EDA relative to palm/finger; optical window and EDA electrodes require distinct skin interfaces | **Physically feasible, scientifically site-dependent.** |
| **ECG + EDA** | Yes on torso or hand, but conditional | Conditional | Different autonomic-related channels in one module | Chest/torso EDA is less established than palmar; electrode systems may compete for skin area and movement stability | **Possible for integrated research, but separate optimal sites may be preferable.** |
| **ECG + PPG + IMU** | Yes or distributed | Yes/custom | Cardiac timing + pulse + local motion; can improve data-quality interpretation | Same-site PPG may sacrifice optical quality; distal PPG is more useful for PAT but creates distributed topology | **Strong multimodal group when geometry is purpose-specific.** |
| **PPG + EDA + temperature + IMU** | Yes at wrist/hand | Yes at Level 3 | Commercial/research wrist devices demonstrate physical feasibility of this quartet | Wrist EDA is not palmar-equivalent; wrist PPG is motion-sensitive; temperature needs thermal isolation; current breakouts are not equivalent to an integrated wearable | **Strong proof of physical co-existence, not proof of optimal signal validity.** [N23] |

### Co-location conclusions

1. **PPG + IMU** has the clearest local-artifact rationale.
2. **EDA + IMU** and **ECG + IMU** are also sensible when the IMU is physically local to the contact interface being interpreted.
3. **PPG + EDA + temperature + IMU at the wrist** is physically credible at custom-wearable maturity, but it combines two major compromises: wrist EDA sensitivity relative to palm/finger and wrist PPG motion sensitivity.
4. **ECG is the modality most likely to justify a separate torso component** for passive continuous acquisition.
5. **Temperature may be in the same enclosure but should not simply share the same thermal mass.**

---

## 6. Candidate Physical Architectures

No architecture below is selected as the final design.

### 6.1 Single wrist unit

#### Physical description

All continuously worn sensors are concentrated at one wrist. PPG, EDA, peripheral temperature and IMU occupy separate skin-facing regions within one wrist module; ECG would require either an unconventional same-wrist electrode geometry or an intermittent second contact.

```text
       [Single wrist enclosure]
   ┌───────────────────────────┐
   │ PPG optical window       │
   │ EDA electrodes           │
   │ TMP117 thermal island    │
   │ IMU                      │
   │ controller + battery     │
   │ ECG electrodes ?         │
   └───────────────────────────┘
             || skin

ECG option A: same-wrist geometry -> requires validation
ECG option B: opposite-hand touch -> intermittent, not passive continuous
```

#### Strengths

- Lowest donning complexity if a custom device exists.
- Good local IMU reference for wrist PPG and wrist EDA.
- PPG, EDA, temperature and IMU are already known to be physically integrable in research/commercial wrist devices. [N23]
- Strongest path toward a familiar ambulatory form factor.

#### Weaknesses / signal implications

- Wrist EDA is not physiologically equivalent to palmar/finger EDA and can be substantially less responsive in controlled comparisons. [N10, N12]
- Wrist PPG is highly exposed to hand/arm movement and contact-pressure changes. [N7]
- Passive continuous ECG from one wrist should not be assumed from common smartwatch behavior; conventional smartwatch ECG normally uses an opposite-hand contact. [N3]
- A tightly packed controller/battery/optical stack creates a thermal challenge for TMP117.

#### Practical feasibility

- **Current breakouts:** poor fit for a genuine single wrist unit. The boards may be strapped near the wrist for experiments, but that is not equivalent to a compact wearable and may disturb optical/electrode/thermal contact.
- **Near-term Level 2:** possible only as a bulky experimental wrist assembly and likely not a valid test of final wearability.
- **Level 3:** physically realistic for PPG + EDA + temperature + IMU; ECG remains a separate validation problem.

#### Use-context suitability

- **Structured resting / daily check-in:** moderate if ECG can be intermittent.
- **Cue-reactivity controlled session:** moderate, but hand/palmar EDA may be scientifically preferable.
- **Long-duration ambulatory:** potentially strong after custom integration, with acknowledged EDA/PPG compromises.
- **Movement-rich monitoring:** challenging for PPG; local IMU becomes important.

**Provisional class:** technically feasible but compromised; requires custom hardware integration; ECG aspect exploratory.

---

### 6.2 Wrist + chest dual-module system

#### Physical description

A chest/torso module handles ECG and optionally a chest-local IMU. A wrist module handles PPG, EDA, local temperature and a wrist IMU. Controller placement can be chest, wrist, belt, or split across synchronized nodes; this remains open.

```text
 [Chest ECG electrodes]
        │
 [AD8232 / chest module]
        │
   [local chest IMU]       optional
        │
        ├──── sync/data ───────────────┐
        │                              │
        v                              v
 [controller or node]       [Wrist peripheral module]
                             ├─ PPG
                             ├─ EDA
                             ├─ TMP117
                             └─ wrist IMU
```

#### Strengths

- Allows ECG to use an established torso geometry rather than forcing wrist ECG.
- Keeps a local IMU with motion-sensitive wrist PPG; a second chest IMU can be added only if ECG-local artifact is a defined need.
- Preserves a practical wrist location for continuous peripheral/context sensing.
- Maps naturally toward a future chest patch + wrist wearable architecture.

#### Weaknesses / signal implications

- Two wear locations increase donning, charging and adherence burden.
- Wrist EDA still sacrifices palmar sensitivity.
- Wired Level-2 versions can create cable snagging and motion artifact; wireless/multi-node versions create synchronization and power-management complexity.
- If PAT or beat-to-pulse timing is studied, clock alignment between chest ECG and wrist PPG becomes a measurement requirement rather than a software convenience.

#### Practical feasibility

- **Current breakouts:** relatively realistic as a body-worn distributed prototype, especially for short/medium sessions. The chest ECG breakout can remain in a chest pouch/strap with short electrode leads; wrist boards may be mounted in a larger wrist/forearm fixture.
- **Near-term Level 2:** strong feasibility if cable routing and synchronization are intentionally tested.
- **Level 3:** strong path to a compact dual-wearable system.

#### Use-context suitability

- **Supervised rehabilitation / resting / cue-reactivity:** strong.
- **Early withdrawal under clinical supervision:** strong as a research-sensing topology because chest cardiac timing and wrist/hand motion can coexist, but it does not replace clinical assessment.
- **Ambulatory recovery:** potentially strong after integration.
- **Movement-rich real world:** stronger than a single wrist for ECG, but wrist PPG remains motion-sensitive.

**Provisional class:** strong research-prototype candidate; strong ambulatory candidate after integration.

---

### 6.3 Wrist + finger/hand sensing + central controller

#### Physical description

Signal-quality-sensitive peripheral measurements are moved to the hand: finger PPG and finger/palmar EDA. A wrist/forearm IMU records local limb motion/tremor, and temperature can be placed on wrist/forearm or hand depending the research question. A controller sits on forearm, upper arm, belt or nearby harness. ECG can be added as a separate chest electrode/front-end branch if required by the protocol.

```text
 [Finger PPG]      [Palmar/finger EDA]
       \              /
        \            /
        [Hand interface]
               │
          [Wrist/forearm IMU]
               │
        [TMP117 contact site]
               │
               v
       [Central controller]
       (belt / forearm / bench)

 Optional separate branch:
 [Chest ECG electrodes] -> [AD8232] -> controller/node
```

#### Strengths

- Prioritizes two of the strongest peripheral measurement sites: finger PPG and palmar/finger EDA. [N5, N10]
- Particularly suited to controlled resting, cue-reactivity and structured check-in sessions where hand use can be limited temporarily.
- Wrist/hand IMU can observe tremor and local hand motion.
- Easy to compare finger vs wrist placement experimentally without prematurely committing to ambulatory packaging.

#### Weaknesses / signal implications

- Finger/hand hardware interferes with dexterity and daily tasks.
- Cable/electrode pressure changes can occur whenever the user grasps objects.
- Long-duration wear raises hygiene, snagging and comfort problems.
- If ECG is also included, the system becomes explicitly distributed across hand and chest.

#### Practical feasibility

- **Current breakouts:** strong for controlled sessions using dedicated fixtures/straps and a nearby controller.
- **Near-term Level 2:** feasible for supervised research, less suitable for unconstrained daily life.
- **Level 3:** could evolve into ring/finger/hand modules, but that is a separate product-integration exercise.

#### Use-context suitability

- **Structured rest / cue-reactivity / supervised rehab:** very strong.
- **Daily longitudinal check-in:** strong if used for short measurement windows.
- **Long-duration ambulatory:** weak-to-moderate because of hand burden.
- **Movement-rich free living:** weak for continuous finger instrumentation.

**Provisional class:** strong controlled research-prototype candidate; poor-to-moderate continuous ambulatory fit.

---

### 6.4 Chest patch + wrist peripheral module

#### Physical description

This is the integrated evolution of the chest+wrist concept: a custom chest patch contains the ECG front end/electrodes and potentially a local IMU; a custom wrist device contains PPG, EDA, temperature and IMU.

```text
   [Custom chest patch]
 ┌─────────────────────┐
 │ ECG electrodes      │
 │ ECG analog front end│
 │ optional local IMU  │
 │ local power/node    │
 └─────────────────────┘
          ⇅ synchronized link
 ┌─────────────────────┐
 │ Custom wrist module │
 │ PPG + EDA + Temp    │
 │ wrist IMU           │
 └─────────────────────┘
```

#### Strengths

- Eliminates long ECG lead wires in the eventual integrated form.
- Preserves local motion references at both major sensing locations if needed.
- Offers a plausible path to long-duration monitoring without forcing ECG onto the wrist.

#### Weaknesses / signal implications

- Requires two custom wearables, two skin interfaces and likely two power domains.
- Chest adhesives/patches can create skin irritation and replacement burden over prolonged use. [N24]
- Wrist EDA/PPG limitations remain.
- Synchronization must be measured if cross-modal beat timing is important.

#### Practical feasibility

- **Current breakouts:** a literal chest *patch* is a poor fit; the AD8232 breakout and DevKit should not be described as patch-ready.
- **Near-term Level 2:** emulate the topology with a chest strap/pouch and wrist fixture.
- **Level 3:** strong integrated embodiment candidate.

#### Use-context suitability

- **Controlled research:** strong but may be over-engineered before placement is validated.
- **Ambulatory:** strong potential.
- **Movement-rich:** chest ECG likely retains an advantage over wrist-only ECG, but both modules need artifact handling.

**Provisional class:** requires custom hardware integration; strong ambulatory candidate if evidence remains favorable.

---

### 6.5 Distributed body-worn multi-node research prototype

#### Physical description

Each modality is placed near the anatomical site best suited to the specific research question. Local sensing nodes acquire data close to the sensors, then synchronize or stream to a master/logger.

One possible exploration layout — not a final assignment — is:

```text
 [Chest ECG electrodes]
        │ short leads
 [ECG local node + optional chest IMU]
        │
        ├──────── synchronized data ────────┐
        │                                    │
 [Hand/finger node]                   [Wrist/forearm node]
 ├─ finger PPG                        ├─ TMP117
 ├─ palmar/finger EDA                 └─ IMU / tremor
 └─ optional local IMU                       │
        │                                    │
        └──────────────┬─────────────────────┘
                       v
                [Master/logger]
```

#### Strengths

- Allows each sensor to be evaluated near its strongest candidate site instead of packaging around one enclosure.
- Reduces the need for long high-impedance analog electrode leads if ECG is digitized locally.
- Supports A/B placement studies: finger vs wrist PPG, palmar vs wrist EDA, one vs multiple IMUs, chest vs wrist temperature.
- Cleanest architecture for distinguishing **sensor validity** from **wearable integration** during exploration.

#### Weaknesses / signal implications

- Highest synchronization burden if multiple controllers are used.
- Multiple batteries, chargers, radios or wired links increase operational complexity.
- More modules increase donning errors and placement variability.
- Cross-node timestamp error can invalidate PAT or fine-grained artifact alignment even if each sensor looks individually good.

#### Practical feasibility

- **Current breakouts:** strong, particularly for supervised research, because boards need not be miniaturized into one enclosure.
- **Near-term Level 2:** strong if node synchronization and strain relief are engineered.
- **Level 3:** may later collapse into fewer modules after evidence shows which sites truly need to remain separate.

#### Use-context suitability

- **Supervised rehab / early withdrawal research / cue-reactivity / validation:** very strong.
- **Ambulatory:** moderate at best unless the number of nodes is reduced.
- **Movement-rich free living:** scientifically useful but operationally burdensome.

**Provisional class:** strong research-prototype candidate; exploratory as a long-term user-facing system.

---

### 6.6 Semi-wearable research harness

#### Physical description

The current breakout boards remain exposed or protected in a serviceable central harness/pouch/board carrier. Only the required sensing interfaces are placed on the body, with deliberate strain relief and short local cables where possible.

```text
 [Chest electrodes] ----\
 [Finger/PPG fixture] ----\
 [EDA electrodes] --------> [Harness / belt / tray]
 [TMP117 skin probe] -----/  ├─ AD8232 breakout
 [IMU module] -----------/   ├─ tinyGSR board
                              ├─ MAX30101 board/interface
                              ├─ TMP117 interface
                              ├─ ESP32 DevKit
                              └─ battery / logger
```

A variant can place the AD8232 physically near the chest while the rest of the boards remain central.

#### Strengths

- Highest serviceability and easiest access for debugging, calibration checks and swapping placements.
- Makes current breakout-board identity explicit rather than disguising it as a product wearable.
- Excellent for short controlled studies and bench-to-body transition.
- Allows rapid evaluation of anatomical placement before investing in custom PCBs.

#### Weaknesses / signal implications

- Cable movement can create ECG/EDA artifact and user burden.
- Body-spanning digital buses or raw electrode leads can become electrically fragile.
- A central IMU on the harness is a poor substitute for local motion at wrist/finger/chest sensors.
- Not representative of final ambulatory comfort.

#### Practical feasibility

- **Current breakouts:** strongest immediate fit.
- **Near-term Level 2:** strong for supervised/controlled sessions.
- **Level 3:** not an end-state; primarily a research instrument.

#### Use-context suitability

- **Bench validation / structured rest / cue-reactivity:** excellent.
- **Supervised rehabilitation:** good.
- **Early withdrawal research under supervision:** potentially useful if clinical workflow permits; not a clinical monitor.
- **Ambulatory:** poor.

**Provisional class:** strong immediate research-prototype candidate; poor final ambulatory fit.

---

### 6.7 Additional evidence-backed options

#### 6.7.1 Session-configurable modular system

A core chest/wrist system is worn continuously or during a study, while a finger/hand pod is attached only for high-quality scheduled check-ins or cue-reactivity/resting windows.

```text
 Continuous / longer-window core:
 [Chest ECG] <----sync----> [Wrist PPG + Temp + IMU (+ wrist EDA)]

 Optional high-quality session pod:
                 +----> [Finger PPG]
                 +----> [Palmar/finger EDA]
```

This architecture explicitly accepts that **the best ambulatory site and the best controlled measurement site may differ**. It is a design hypothesis worth testing because it avoids making palmar EDA or finger PPG permanently intrusive while retaining them for standardized sessions.

**Provisional class:** strong exploratory architecture; may reduce the false requirement that every modality be continuous.

#### 6.7.2 Ear PPG + torso ECG + hand/wrist contextual sensing

Ear PPG is evidence-backed as a viable optical site and may be less affected by some gross limb motion than wrist/finger PPG, although jaw motion and fit remain issues. [N8]

```text
 [Ear PPG + optional local IMU]
             │
             ├──── sync ─── [Chest ECG]
             │
             └──── sync ─── [Wrist/hand EDA + Temp + IMU]
```

**Current breakout constraint:** the exact SmartElex MAX30101 breakout is not known to be mechanically appropriate for the ear. A safe ear fixture may require custom packaging or a custom PCB.

**Provisional class:** exploratory / needs validation; likely requires custom hardware integration.

---

## 7. Research Prototype vs Integrated Wearable

| Architecture | Level 1: bench/tethered | Level 2: distributed breakout body-worn | Level 3: custom integrated | Main maturity caveat |
|---|---|---|---|---|
| Single wrist | Limited value | Possible but bulky/atypical | **Yes for PPG+EDA+Temp+IMU; ECG unresolved** | Do not interpret a strapped bundle of boards as evidence that a clean wrist wearable will work. |
| Wrist + chest | Yes | **Strong** | **Strong** | Cross-module synchronization and chest adhesion/strap design remain. |
| Wrist + finger/hand + controller | **Strong** | **Strong for controlled sessions** | Possible | Hand burden limits continuous wear. |
| Chest patch + wrist module | Can emulate topology | Can emulate with strap/pouch | **Requires custom hardware** | Current breakout boards are not patch hardware. |
| Distributed multi-node | **Strong** | **Strong** | May later consolidate | More nodes increase timing/power/donning burden. |
| Semi-wearable harness | **Strongest immediate fit** | **Strong for supervised sessions** | Not intended as final | Cable artifact and comfort are intrinsic limitations. |
| Session-configurable modular | Strong | Strong | Strong potential | Requires protocol-level decision about continuous vs scheduled modalities. |
| Ear hybrid | Can bench-test | Awkward with current breakout | Likely custom | Ear mechanics and local motion need dedicated validation. |

### Important maturity rule

A Level-3 embodiment can use the same **sensing modality** without using the same **breakout board**. The current boards are research vehicles. Final integration may move the IC/front end onto a custom PCB, relocate sensors on flex sections, redesign the optical window/electrodes, or use separate local nodes. That does not retroactively validate the current board at the final site.

---

## 8. Form-Factor Comparison Matrix

Qualitative ratings below are provisional and describe the architecture class, not a final recommendation.

| Option | Sensor placement | Signal-quality potential | Comfort | Wiring complexity | Current-breakout feasibility | Ambulatory suitability | Main strengths | Main risks |
|---|---|---|---|---|---|---|---|---|
| **Single wrist unit** | All peripheral sensors at wrist; ECG same-wrist or touch-contact | **Medium/low overall**; PPG/EDA/ECG compromises | Potentially high at Level 3 | Low externally | **Low** as true single unit | **High potential** after custom integration | Simplicity, single donning location | Wrist-centric scientific compromise; thermal crowding; ECG unresolved |
| **Wrist + chest** | Chest ECG; wrist PPG/EDA/Temp/IMU | **High for ECG, medium for wrist peripheral signals** | Medium | Medium | **High** for Level 2 | **High potential** | Separates ECG from wrist constraints; local wrist motion | Two modules; wrist EDA/PPG limits; synchronization |
| **Wrist + finger/hand + controller** | Finger PPG; palmar/finger EDA; wrist/forearm IMU/temp; optional chest ECG | **High for controlled peripheral signals** | Low/medium | Medium/high | **High** | Low for continuous use; high for check-ins | Strong PPG and EDA sites; tremor-friendly | Hand interference, cable/contact motion, hygiene |
| **Chest patch + wrist module** | Integrated chest ECG(+IMU); integrated wrist quartet | **High potential** | Medium/high if well designed | Low externally | **Low as literal patch** | **High potential** | Clean distributed physiology; fewer exposed wires | Requires custom hardware; adhesives; two power domains |
| **Distributed multi-node** | Site-optimized chest/hand/wrist/other nodes | **Highest exploration potential** | Low/medium | Medium externally, high system complexity | **High** | Medium/low | Preserves modality-specific sites; best for validation | Sync, batteries, donning, operational burden |
| **Semi-wearable harness** | Contacts on body; boards central/exposed | **High in controlled conditions if wiring is managed** | Low | **High** | **Highest immediate** | **Poor** | Debuggable, modular, placement-friendly | Cable artifact, not representative of final product |
| **Session-configurable modular** | Ambulatory core + temporary finger/palm pod | **High during standardized sessions; medium ambulatory** | Medium/high between sessions | Medium | High | High potential | Separates continuous vs high-quality episodic sensing | Protocol complexity; data from different site regimes |
| **Ear hybrid** | Ear PPG; chest ECG; wrist/hand context | Potentially high optical quality | Medium | Medium | Low/medium | Medium/high after custom | Moves PPG away from wrist motion | Ear fixture, jaw artifact, current board unsuited mechanically |

---

## 9. Sensor Placement Decision Matrix

| Modality | Candidate sites | Best-supported sites for this exploration | Advantages | Limitations | Evidence level | Implication for form factor |
|---|---|---|---|---|---|---|
| **ECG** | Chest/torso; limb-style hand/wrist; wearable patch; experimental wrist geometries | **Chest/torso for passive continuous single-lead**; limb-style for spot recordings | Stable lead vector; strong QRS potential; mature patch precedent | Electrodes, skin contact, cable/adhesive motion; chest wear burden | Strong for chest continuous; strong for hand-to-hand spot ECG; exploratory for same-wrist continuous | Likely justifies torso component unless ECG is intermittent or an alternate geometry is validated |
| **PPG** | Finger, wrist, ear, forehead, arm, torso | **Finger for controlled signal potential; wrist for convenience; ear credible alternative** | Multiple optical sites; distal pulse complements ECG | Motion, pressure, ambient light, perfusion, temperature, pigmentation/tissue optics | Strong that site matters; strong for finger/wrist; moderate/strong for ear; context-dependent elsewhere | PPG does not have to share ECG site; fixture design is as important as board selection |
| **EDA** | Palm/fingers, wrist, plantar, torso/other | **Palm/fingers for response sensitivity; wrist for wearability** | Directly complementary sudomotor channel | Palmar interferes with hand use; wrist often weaker; site-specific latency/amplitude | Strong for palmar/finger; moderate for wrist feasibility; exploratory for torso alternatives | May justify an optional hand module in controlled sessions |
| **Peripheral temperature** | Wrist, finger/hand, chest, upper arm, thigh, other skin sites | No universal best; choose site based on context and repeatability | Small sensor; useful local thermal/perfusion context | Ambient, airflow, clothing, contact, local perfusion, board heat | Strong that setup/site matter; site-specific physiological meaning | Requires thermal contact island and separation from heat sources; can coexist with other modules only with thermal design |
| **IMU** | Hand/wrist, forearm, chest, waist/lower back, ankle/shank/foot | **Site depends on role**: wrist/hand for tremor/local PPG; chest for ECG/local trunk; waist/lower back for gross gait/activity | No skin electrode/optical contact needed; powerful context channel | Placement/orientation/mounting change meaning; one site cannot represent all local motion | Strong that placement is task-specific | Multiple IMUs may be justified during research even if final system later uses one |
| **ESP32/controller** | Belt/pouch, chest, wrist/forearm, local sensor node | No physiological “best site” | Flexible placement; can centralize logging | ADC/RF noise, bus length, heat, battery, cable routing | Strong for platform constraints; exact DevKit implementation unresolved | Place for electrical/mechanical convenience, not because it is a physiological sensor |

---

## 10. Design Tensions

### Better ECG geometry vs wearer convenience

A chest/torso lead can provide a more natural continuous ECG geometry than a wrist-only scheme, but adds a second wear site, electrodes/adhesive or a strap, and donning burden.

### Palmar EDA quality vs continuous-wear practicality

Palm/finger EDA is more responsive in controlled comparisons, while wrist EDA is substantially easier to wear during ordinary activity. The choice changes the measurement, not just the enclosure.

### Finger PPG quality vs usability

Finger PPG often provides stronger optical waveforms, yet daily hand use makes continuous finger fixtures intrusive. Wrist or ear sites trade some signal/fixture constraints for usability.

### Single compact wearable vs distributed high-quality sensing

A single device simplifies charging and donning. A distributed system lets each modality occupy a more defensible anatomical site. The exploration stage should not resolve this tension in favor of cosmetic simplicity.

### Long sensor leads vs analog noise and cable artifact

Moving wires can contaminate biopotential and EDA measurements, and long biopotential cables increase common-mode pickup. Keeping analog front ends close to electrodes can help, but local nodes add power and synchronization complexity.

### Central I²C simplicity vs body-spanning bus integrity

One ESP32 talking directly to every digital breakout appears simple logically, but long moving wires add capacitance and noise. Short local buses with multiple nodes reduce wiring length but require synchronized clocks and inter-node transport.

### Co-location simplicity vs local motion artifact

Mounting PPG and IMU together improves local motion context. Mounting one IMU at the controller while the PPG is elsewhere may provide only gross movement rather than local interface motion.

### Thermal contact vs enclosure heating

The TMP117 wants close conductive contact with skin while higher-power electronics should be thermally distant. A small sealed enclosure pushes those goals against each other.

### Adhesive stability vs skin tolerance

Adhesive chest patches can provide stable contact and reduce wires, but prolonged wear can cause irritation or require site rotation/replacement. [N24]

### Fixed placement repeatability vs everyday comfort

Research validity benefits from repeatable placement, pressure and orientation. Comfortable wearables tend to allow some variation. A placement guide or mechanical indexing feature may eventually be needed, but this is not yet a final industrial-design decision.

### One IMU vs sensor-local motion awareness

A single IMU minimizes hardware. Multiple local IMUs can better explain motion at different sensor interfaces and body segments. Research may justify temporarily over-instrumenting motion to determine what the final system actually needs.

---

## 11. Attractive but Potentially Misleading Form Factors

### 11.1 “Everything on the wrist because commercial wearables do it”

Custom wrist research devices prove that PPG + EDA + skin temperature + accelerometry can physically coexist. They do **not** prove that wrist EDA is equivalent to palmar EDA, that wrist PPG is optimal during movement, or that the current breakouts can reproduce the same mechanical interfaces. [N10, N23]

### 11.2 “AD8232 on the wrist means continuous wrist ECG”

The AD8232 is a single-lead front end, but the lead still requires two useful electrical sensing points. Common smartwatch ECG obtains a limb-like lead through the opposite finger. A continuous same-wrist configuration is a separate electrode-design problem. [N3]

### 11.3 “Put the TMP117 inside the main enclosure and call it body temperature”

A TMP117 inside a controller enclosure may primarily track its own board/enclosure thermal state unless the skin thermal path dominates. Even a well-coupled reading is local skin temperature, not core temperature. [N15]

### 11.4 “One controller IMU can perfectly correct every artifact”

Local electrode/optical motion can differ from controller motion. Accelerometry is a context/reference channel, not an oracle that separates every artifact from physiology. [N19, N20]

### 11.5 “Finger sensors are always better, therefore put everything on the finger”

Finger PPG and palmar/finger EDA have strong signal advantages, but a finger-centric all-day device may create severe interaction, pressure and motion burdens. Signal strength alone does not determine longitudinal feasibility.

### 11.6 “A chest patch solves all modalities”

Chest ECG is well supported, but torso EDA is less established than hand EDA and chest PPG is not automatically equivalent to finger/wrist/ear PPG. A single chest patch can be an integration target, but not a scientific shortcut. [N13]

### 11.7 “Fewer wires automatically means better data”

Removing wires often improves wearability, but replacing a wired central system with multiple radios/nodes can introduce clock mismatch, power/RF coupling and charging burden. The correct comparison must include signal integrity and synchronization, not enclosure count alone.

---

## 12. Suitability by AUD Monitoring Context

These mappings describe **research-form-factor suitability**, not clinical efficacy for AUD.

| Use context | More suitable form-factor directions | Why | Main cautions |
|---|---|---|---|
| **Supervised rehabilitation-center session** | Semi-wearable harness; distributed multi-node; wrist+finger/hand; wrist+chest | Staff can place sensors reproducibly; signal validity can outweigh convenience; reference equipment can be used | Cable artifact, setup time, need standardized placement |
| **Early withdrawal monitoring under clinical supervision** | Chest ECG + hand/wrist IMU; distributed system; wrist+chest | Supports cardiac timing and hand tremor research simultaneously; EDA/temp can remain contextual | Sensors do not replace clinical withdrawal assessment; motion/tremor is nonspecific |
| **Structured resting measurement** | Finger/palmar + chest configuration; semi-wearable harness | Low motion permits high-quality ECG/PPG/EDA comparisons and placement validation | Results may not generalize to free living |
| **Cue-reactivity / craving research session** | Palmar/finger EDA + ECG + PPG + local IMU; distributed controlled setup | Preserves conventional EDA site and allows multimodal event alignment | Physiological arousal remains nonspecific; hand sensors may affect the task |
| **Ambulatory recovery monitoring** | Wrist+chest; integrated chest patch+wrist; session-configurable modular core | Better balance of passive continuous wear and modality-specific geometry | Long-term adherence, adhesives, wrist EDA/PPG quality, synchronization |
| **Daily longitudinal check-in** | Session-configurable finger/palm pod + chest/wrist core; even a controlled short-session layout | Short measurement window permits higher-quality intrusive sites without all-day burden | Must standardize posture, site, acclimation and environment for comparability |
| **Movement-rich real-world monitoring** | Wrist PPG+local IMU with chest ECG; consider ear PPG research; distributed local motion references | Local motion information is essential when optical/electrode interfaces move | High artifact remains; one global IMU is insufficient as perfect ground truth |

### AUD-specific form-factor implication

AUD support/recovery research spans very different observation contexts. A device optimized for cue-reactivity or early withdrawal research may reasonably use a hand/finger component that would be unacceptable for all-day recovery monitoring. Conversely, a wrist-centered ambulatory device may sacrifice EDA sensitivity or PPG quality that is valuable in a controlled mechanistic study. It is therefore scientifically preferable at this stage to maintain **multiple embodiment classes** rather than forcing one universal wearable.

---

## 13. Provisional Option Classes

These are classes, not a ranking or final design selection.

### Strong research-prototype candidates

- **Distributed multi-node research prototype** — strongest ability to place modalities where evidence supports them and to compare alternatives before integration.
- **Semi-wearable research harness** — strongest immediate fit for the exact development boards, especially for short controlled studies.
- **Wrist + finger/hand + controller** — strong for structured sessions emphasizing PPG/EDA signal quality and hand tremor.
- **Wrist + chest dual-module** — strong bridge between research validity and future ambulatory integration.

### Strong ambulatory candidates

- **Wrist + chest dual-module**, especially after custom integration.
- **Chest patch + wrist peripheral module**, at Level 3.
- **Session-configurable modular system**, if future protocols show that some modalities need only standardized episodic acquisition rather than continuous measurement.

### Technically feasible but compromised

- **Single wrist unit containing every modality**, particularly if it claims passive continuous ECG and treats wrist EDA as equivalent to palmar EDA.
- **Central harness with long uncontrolled sensor wiring**, if cable motion and bus integrity are not characterized.

### Requires custom hardware integration

- True compact **single wrist** PPG+EDA+temperature+IMU integration.
- True **chest ECG patch** rather than a strapped breakout.
- **Ear PPG** using a stable, comfortable optical fixture.
- Any design requiring a thermally isolated skin-temperature island inside a compact enclosure.

### Exploratory / needs validation

- Same-wrist continuous ECG geometry.
- Torso EDA as a replacement for palmar/finger EDA.
- Chest PPG as the main optical site for this project.
- One IMU serving simultaneously as hand-tremor sensor and artifact reference for remote chest/wrist sensors.

### Poor fit for current hardware

- A cosmetically compact all-in-one wrist wearable built directly from the exact current breakout boards.
- A literal adhesive chest patch containing the current ESP32 DevKit + breakout stack.
- An in-ear/earbud embodiment using the current MAX30101 breakout without a dedicated mechanical/optical redesign.

---

## 14. What Remains Unresolved

### Anatomical-placement validation

1. Which single-lead ECG electrode pair gives sufficiently stable R-peak timing with the exact VS82 board under rest, posture changes and normal movement?
2. How much signal-quality loss occurs when PPG moves from finger to wrist or ear using the exact optical hardware/fixture?
3. How much EDA response sensitivity is lost when moving from palm/finger to wrist with the exact legacy tinyGSR electrode configuration?
4. Which local temperature site best serves the intended *contextual* question, and can the TMP117 breakout be thermally coupled repeatably there?
5. Which IMU site is necessary for withdrawal-tremor research, and which site is necessary for artifact context? Are these the same?

### Mechanical-interface validation

6. What electrode material/adhesive/contact pressure is appropriate for ECG and EDA at the intended wear duration?
7. What mechanical fixture gives the MAX30101 stable optical pressure without excessive compression or ambient-light leakage?
8. How will sensor modules be strain-relieved so cable motion does not dominate ECG/EDA signals?
9. How repeatable is donning location and orientation between sessions/users?
10. How are sweat, cleaning, skin preparation, hair, hygiene and site rotation handled?

### Electrical/topology validation

11. What is the measured noise of the exact ESP32 ADC path for the conditioned AD8232 output during Wi-Fi/Bluetooth activity?
12. How long can each actual I²C connection be before rise-time/noise/reliability problems appear with the specific breakout pull-ups and cable type?
13. Is a single central controller cleaner than local nodes once analog path length, cable motion and synchronization are measured rather than assumed?
14. If multiple nodes are used, what synchronization error is achieved and is it adequate for beat/pulse alignment or PAT research?
15. Does RF transmission create measurable artifacts in ECG/EDA/PPG or local heating near the temperature sensor?

### Human-factors validation

16. What wear duration is acceptable for chest electrodes/adhesives, wrist straps and hand/finger fixtures?
17. Which module is most likely to be removed, repositioned or worn loosely in real use?
18. Is a two-module system more acceptable than a larger single wrist unit?
19. For daily check-ins, is temporary high-quality finger/palmar sensing preferable to lower-burden continuous wrist sensing?

### Scientific-use validation

20. Which modalities actually need continuous acquisition for each AUD research question?
21. Does a lower-burden site preserve enough feature reliability to answer that question?
22. Which sensors are valuable primarily as quality/context channels rather than outcome channels?
23. Does adding a second local IMU materially improve artifact interpretation enough to justify another sensor?

---

## 15. Questions Carried Forward to Architecture Design

The following answers are **provisional evidence-based directions**, not final decisions.

### Which modalities genuinely require different anatomical sites?

**Current direction:** at least some likely do if signal validity is prioritized. Continuous ECG strongly favors a deliberate torso/chest lead geometry; high-response EDA favors palm/fingers; PPG has strong finger/ear options as well as convenient wrist use; IMU placement depends on the motion target. A single site is therefore not scientifically neutral.

### Can PPG, EDA, temperature and IMU realistically coexist at one site?

**Yes physically, especially at the wrist with custom hardware.** Commercial/research devices demonstrate this combination. But co-existence is not equivalent to each modality being at its strongest site, and temperature requires internal thermal isolation. Current breakout boards do not reproduce the mechanical integration of purpose-built devices. [N23]

### Does ECG practically force a chest/torso component?

**Not absolutely, but it strongly pushes the design that way for passive continuous acquisition.** Limb-style smartwatch ECG is valid for spot checks, while chest patches are an established continuous configuration. Same-wrist passive ECG remains a separate validation problem. [N1, N2, N3]

### Is palmar/finger EDA important enough to justify a hand/finger component?

**The evidence supports higher EDA response sensitivity at palm/finger than wrist, but whether that advantage justifies a hand module depends on the research context.** It is especially compelling for controlled cue-reactivity/resting experiments; less obviously justified for all-day wear. [N10, N12]

### Is finger PPG scientifically stronger but practically worse than wrist PPG?

**Generally yes for waveform quality under controlled comparison, with important context.** Finger has high amplitude/analyzability, whereas wrist is easier to wear continuously but highly motion/contact sensitive. Ear is a meaningful third option rather than a binary choice. [N5, N6, N7, N8]

### Should the IMU sit with the most artifact-prone sensor rather than only at the controller?

**If artifact interpretation is the goal, local placement is better justified.** PPG+local IMU has strong precedent, and local-motion research in electrophysiology shows why one distant motion sensor may miss interface-specific motion. A separate controller IMU can still provide gross activity context. [N19, N20]

### Can one ESP32 centrally acquire all modules without problematic analog lead lengths?

**Electrically plausible, not yet established physically.** The answer depends on where the AD8232 is placed, whether the central link is raw electrode, conditioned analog or digital, actual I²C cable capacitance/pull-ups, ADC noise, grounding, RF activity and strain relief. This requires bench/body-worn comparison rather than intuition. [N21, N22]

### Would multiple synchronized sensing nodes be physically cleaner?

**Potentially.** Local nodes can shorten analog and digital paths and reduce body-spanning wiring. The cost is measurable synchronization error, more power domains, radios/connectors and operational burden. Multi-node should therefore be tested as a research topology, not assumed superior.

### Which options are realistic using the exact current breakout boards?

**Most realistic now:** bench/tethered, semi-wearable harness, distributed body-worn research system, wrist+finger/hand controlled setup, and a non-miniaturized chest+wrist prototype. **Poor fit now:** true compact single wrist, literal adhesive chest patch, and earbud-like PPG.

### Which options only become realistic after custom PCB integration?

A polished single-wrist quartet, a genuine chest ECG patch, compact ear PPG, and any tightly integrated enclosure requiring a deliberate thermal island/optical window/electrode geometry are primarily Level-3 configurations.

### Which form factors support controlled sessions?

Distributed, harness, chest+hand/finger and chest+wrist arrangements are all strong candidates. Controlled sessions can tolerate additional sensors and cables in exchange for stronger placement validity.

### Which support long-duration ambulatory use?

Chest+wrist, integrated chest-patch+wrist and session-configurable modular systems have the clearest path. A single custom wrist device may be highly wearable but carries the largest physiological-placement compromises.

### Which introduce large signal-quality compromises merely for cosmetic simplicity?

The highest-risk case is an all-on-wrist design that treats wrist EDA as palmar-equivalent, assumes wrist PPG will remain reliable during movement, embeds TMP117 beside heat-generating electronics without a thermal path, and assumes AD8232 enables continuous same-wrist ECG without validating a lead geometry.

---

## 16. Source / Evidence Ledger

### 16.1 Primary project sources

| ID | Source | Role in this document |
|---|---|---|
| **P0** | `docs/aud_multimodal_hardware_evidence_map.md` | Primary scientific and hardware-capability context; measurement→feature→interpretation boundary; existing AUD evidence. |
| **P1** | `docs/aud_sensor_role_definition.md` | Primary role-definition context; IC-vs-breakout boundary; cross-modal relationships, confounders, open questions and claims to avoid. |

### 16.2 Major external sources newly used for physical/form-factor analysis

| ID | Source | Type | Major form-factor claim supported |
|---|---|---|---|
| **N1** | Zhu H, Pan Y, Wu F, Huan R. *Optimized Electrode Locations for Wearable Single-Lead ECG Monitoring Devices* (Sensors, 2019), DOI **10.3390/s19204458** | Peer-reviewed instrumentation study | Single-lead ECG signal quality depends on torso electrode geometry; left-chest diagonal pair performed best in the tested configuration. |
| **N2** | Steinberg JS et al. *2017 ISHNE-HRS expert consensus statement on ambulatory ECG and external cardiac monitoring/telemetry*, DOI **10.1016/j.hrthm.2017.03.038** / **10.1111/anec.12447** | Expert consensus | Continuous chest patch ECG with embedded electrodes is an established ambulatory form; wires/adhesives have distinct wearability trade-offs. |
| **N3** | Samol A et al. *Single-Lead ECG Recordings Including Einthoven and Wilson Leads by a Smartwatch* (2019), PMID **31658713** | Peer-reviewed feasibility study | Common smartwatch single-lead ECG uses wrist/other body contact plus an opposite finger, illustrating two-point geometry and spot-check behavior. |
| **N4** | *Wearable Smart Textiles for Long-Term Electrocardiography Monitoring—A Review* (Sensors, 2021), PMCID **PMC8234162** | Peer-reviewed review | Wet/dry electrode trade-offs; dry/textile contact pressure and motion artifact; long-term electrode comfort. |
| **N5** | Liu J et al. *Quantitative Comparison of Photoplethysmographic Waveform Characteristics: Effect of Measurement Site* (2019), PMID **30890959** | Peer-reviewed site comparison | Finger had highest analyzability/amplitude among finger, wrist, arm, earlobe and forehead in the tested protocol. |
| **N6** | *Signal Quality of Reflective-Mode Photoplethysmograms Across Anatomical Sites* (2026), PMID **42197799** | Peer-reviewed site comparison | Reflective PPG quality is strongly site-dependent; finger strongest and wrist most challenging in the reported comparison. |
| **N7** | *Evaluation of the signal quality of wrist-based photoplethysmography* (2019), PMID **31100748**, DOI **10.1088/1361-6579/ab225a** | Peer-reviewed ambulatory study | Wrist PPG quality is highly variable and poorer during daytime movement than night/rest in the studied cohort. |
| **N8** | *The Principles of Hearable Photoplethysmography Analysis and Applications in Physiological Monitoring—A Review* (Sensors, 2023), PMCID **PMC10384007** | Peer-reviewed review | Ear/ear-canal PPG is a credible site with motion advantages in some contexts, but fit, chewing and speaking remain artifact sources. |
| **N9** | *A review on wearable photoplethysmography sensors and their potential future applications in health care* (2019), PMCID **PMC6426305** | Peer-reviewed review | PPG can be measured at wrist, forehead, ear, torso and other sites; pressure and site-specific optical design are critical. |
| **N10** | *Framework for Selecting and Benchmarking Mobile Devices in Psychophysiological Research* (2021), PMCID **PMC7854837** | Peer-reviewed comparative study/framework | Palm/finger EDA detected SCRs better than wrist placements in the benchmark; wrist convenience should not be equated with physiological equivalence. |
| **N11** | Boucsein W et al. *Publication recommendations for electrodermal measurements* (Psychophysiology, 2012), DOI **10.1111/j.1469-8986.2012.01384.x** | Consensus/recommendations | Standard EDA terminology and conventional palmar/volar measurement practice. |
| **N12** | *Bilateral comparison of traditional and alternate electrodermal measurement sites* (2020), PMID **32931044**; and *Lapses in skin conductance responding across anatomical sites* (2016), PMID **27015847** | Peer-reviewed site studies | Fingers/plantar sites are generally more reliable/responsive; wrist responses differ in frequency/latency/amplitude. |
| **N13** | *Optimizing Sensor Locations for Electrodermal Activity Monitoring Using a Wearable Belt System* (2025), DOI **10.3390/jsan14020031**; *Electrodermal Activity Analysis at Different Body Locations* (2025), PMID **40292889** | Peer-reviewed exploratory placement studies | Torso EDA is being explored for multimodal integration but remains weaker/more context-dependent than conventional hand sites. |
| **N14** | MacRae BA et al. *Skin Temperature Measurement Using Contact Thermometry: A Systematic Review of Setup Variables and Their Effects on Measured Values* (2018), PMID **29441024** | Systematic review | Attachment, pressure, environment, sensor type and site materially affect contact skin-temperature readings. |
| **N15** | Texas Instruments. *Design Considerations for a Wearable Temperature Monitoring System* / TMP117 wearable thermal guidance (2018) | Manufacturer application guidance | Good skin thermal contact and isolation from other heat sources are required; sensor location on PCB/enclosure is a measurement issue. |
| **N16** | Norouzi N et al. *Evaluation of alcohol intoxication and withdrawal syndromes based on analysis of tremor signals* (2017), DOI **10.1016/j.bspc.2016.11.006** | Peer-reviewed clinical/instrumentation study | Handheld accelerometry can quantify an alcohol-withdrawal tremor component; does not establish full withdrawal diagnosis. |
| **N17** | Aarabi P et al. *A quantitative evaluation of alcohol withdrawal tremors* (EMBC 2015), DOI **10.1109/EMBC.2015.7319812** | Peer-reviewed conference study | Left/right hand tremor signals are not identical; hand-local measurement matters for tremor characterization. |
| **N18** | Prisco G et al. *Validity of Wearable Inertial Sensors for Gait Analysis: A Systematic Review* (2024), PMID **39795564**; plus 2026 gait-location review | Systematic reviews | IMU placement is task-specific; lower back/lower limb/foot/wrist/chest configurations answer different movement questions. |
| **N19** | *Motion artefact removal in electroencephalography and electrocardiography by using multichannel inertial measurement units and adaptive filtering* (2021), PMCID **PMC8450177** | Peer-reviewed instrumentation study | Local IMUs attached to individual electrodes can capture local motion not represented by one global IMU; artifact correction remains conditional. |
| **N20** | *Characterization and reduction of motion artifacts in photoplethysmographic signals from a wrist-worn device* (2016), PMID **26737695** | Peer-reviewed instrumentation study | Co-located accelerometry provides useful motion reference for wrist PPG artifact analysis. |
| **N21** | Espressif Systems. ESP32 ADC documentation / ESP-IDF Programming Guide | Manufacturer documentation | Classic ESP32 ADC is noise-sensitive; ADC2 has Wi-Fi coexistence restrictions; analog acquisition must be characterized in-system. |
| **N22** | NXP Semiconductors. *I²C-bus specification and user manual*, UM10204 | Manufacturer/interface specification | I²C is constrained by bus capacitance/rise time; long body-spanning wiring cannot be assumed reliable without electrical characterization. |
| **N23** | Empatica E4 / EmbracePlus official research documentation | Commercial/research wearable example | A purpose-built wrist device can physically co-locate PPG, EDA, skin temperature and motion sensing; this is an integration precedent, not proof of optimal site equivalence or breakout-board suitability. |
| **N24** | *A user-friendly integrated monitor-adhesive patch for long-term ambulatory ECG monitoring* (2012), PMID **22153334**; related long-term patch human-factors studies | Peer-reviewed human-factors/monitoring studies | Multi-day chest patch wear is feasible, but comfort, skin irritation and adhesive management remain real design constraints. |
| **N25** | Analog Devices. AD8232 product documentation and right-leg-drive technical guidance | Manufacturer documentation | AD8232 supports single-lead biopotential conditioning; long cables increase susceptibility to common-mode interference, making physical front-end/cable layout relevant. |

### Source-use boundary

Commercial devices are cited only as **engineering examples of physical integration**, not as evidence that their algorithms, clinical performance, electrode construction, optical geometry or regulatory status transfer to the current prototype.

Manufacturer IC documentation is used only for the relevant IC/system constraint. It does not establish the exact performance of the named breakout board.

---

## Final Quality Check

- Multiple realistic form-factor classes are explored; no final design is selected.
- Placement analysis starts from sensing requirements rather than aesthetics.
- Anatomical-site claims are separated into evidence, engineering inference and design hypothesis.
- Current breakout feasibility is separated from future custom-PCB feasibility.
- Wrist-centered assumptions are explicitly challenged.
- Motion, skin contact, optical coupling, thermal coupling, electrode geometry, wiring and synchronization are included.
- Controlled and ambulatory use cases are treated separately.
- Scientifically attractive-but-weak shortcuts are identified.
- Patent novelty and final sensor inclusion are not defined.
- Uncertainties and validation questions are carried forward rather than silently resolved.

**No final form factor is selected by this document.**
