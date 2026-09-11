# AUD/SUD Multimodal Hardware — Reference Solutions and Replication Map

**Status:** build-first engineering research document  
**Research snapshot:** 2026-09-10  
**Repository reviewed:** `https://github.com/Beastly713/aud-obj-hardware`  
**Target file:** `docs/aud_hardware_reference_solutions.md`  
**Scope:** research prototype / instrumentation baseline, not a medical device and not a patentability analysis

> **Core principle:** reproduce a working measurement chain first. Preserve current hardware where it can be made useful with high confidence; replace or add hardware only when a concrete signal-integrity, timing, safety, reproducibility, logging, or body-interface problem justifies it.

### Reference labels used throughout

- **EXACT REFERENCE** — same IC/module, or an implementation close enough that its electrical/software behavior is directly useful.
- **FUNCTIONAL REFERENCE** — different hardware that solves the same acquisition problem and is useful as an alternative.
- **ARCHITECTURE REFERENCE** — useful for system topology, timing, power, logging, communications, or mechanics.
- **VALIDATION REFERENCE** — useful mainly to establish what a working signal looks like or how to test one.

### Replication-confidence scale

- **R1 — Very high:** public schematic/BOM/code/build instructions plus credible evidence of operation.
- **R2 — High:** manufacturer reference design or peer-reviewed implementation with enough detail to reproduce most of the system.
- **R3 — Moderate:** technically credible implementation with meaningful details missing.
- **R4 — Low:** product/concept information only.
- **R5 — Inspiration only:** insufficient public information for reproduction.

### Additional-hardware confidence scale

- **H1 — Strongly justified now:** needed for a safe or reliable first body-connected baseline, or supported by unusually strong evidence.
- **H2 — Strong candidate:** likely valuable; test against the current path before making it mandatory.
- **H3 — Optional:** useful improvement, but the first working system should not wait for it.
- **H4 — Speculative:** do not buy/build yet without a measured failure that it solves.

---

## 1. Purpose

This document identifies existing working implementations, reference designs, open-source systems, research prototypes and additional hardware options that can be reproduced or adapted to create a reliable multimodal physiological monitoring baseline. Patent novelty is intentionally out of scope.

The immediate target is not an AUD classifier. The immediate target is a **trustworthy synchronized recorder** that can produce real ECG, PPG, EDA, temperature, and inertial data, preserve the raw signals and timing metadata, expose failures rather than hiding them, and be validated against independent references.

The research therefore asks a practical question for every subsystem:

> **What can we buy, reproduce, wire, configure, test, validate, or adapt tomorrow, and what evidence shows that it works?**

The strongest general answer is not one single project. It is a combination of several unusually relevant references:

1. **EmotiBit** is the strongest open implementation reference for the present non-ECG stack: an ESP32-supported system using MAX30101-class optical acquisition together with EDA, temperature, IMU, buffering, wireless streaming, and local SD logging. It also has peer-reviewed validation. [S12–S16]
2. **Analog Devices MAXREFDES100** is the strongest manufacturer reference close to the full stack: MAX30101 PPG + dedicated ECG AFE + body-temperature sensors + accelerometers/gyro + MCU + BLE/USB + direct-to-flash logging, with schematic, BOM, layout, CAD, firmware, GUI, and app source. [S05]
3. **ProtoCentral HealthyPi 5** is one of the strongest open biosignal architecture references: a dedicated acquisition MCU owns the AFEs and microSD while an ESP32-C3 is a separate wireless co-processor. It supplies open hardware and recent firmware. [S08–S11]
4. **ProtoCentral HealthyPi Move** is the closest complete open wearable in sensor scope: ECG, wrist and finger PPG, EDA/GSR, skin temperature, and motion in one shipping nRF5340/Zephyr platform with public hardware and firmware. [S17–S19]
5. **SparkFun AD8232** and the Analog Devices AD8232 evaluation design are the best direct starting references for proving the present AD8232 path. [S23–S25]
6. **ProtoCentral ADS1292R** and **MAX30001/MAX30003** are stronger evidence-backed paths if the ESP32 ADC + AD8232 chain becomes the ECG bottleneck. [S26–S28]
7. **ProtoCentral tinyGSR v3** is the lowest-risk quantitative replacement for the uncertain legacy tinyGSR if relative-output acquisition stops being sufficient. [S33–S35]
8. **TI TIDA-060034** and the skin-temperature flex construction in MAXREFDES104/106 are the strongest practical references for turning a temperature IC into a body-contact measurement rather than merely reading board temperature. [S06, S07, S37]

### Build-first conclusions

| Question | Build-first answer |
|---|---|
| Can the current BOM produce first useful signals? | **Yes, with targeted electrical checks and proper fixtures.** No current component needs to be discarded before first-light testing. |
| Is one classic ESP32 sufficient for V1? | **Yes for a centralized first baseline**, provided ECG uses ADC1, acquisition timing is decoupled from task scheduling, FIFO/buffering are explicit, and local storage is primary. EmotiBit is direct precedent for ESP32 + MAX30101 + EDA + temperature + IMU. |
| Is the ESP32 ADC the best ECG digitizer? | **No.** It is acceptable for first-light waveform/R-peak work, but its noise/reference/linearity limitations create avoidable uncertainty for serious ECG/HRV work. |
| Should AD8232 be discarded now? | **No.** Use it to prove the existing path. Upgrade only after comparing it with a dedicated AFE/ADC. |
| Is legacy tinyGSR usable? | **Yes for relative EDA**, if it is electrically stable. Do not infer universal absolute µS from it. |
| Is the SmartElex MAX30101 worth using? | **Yes for research bring-up**, after checking power/address behavior and building a controlled optical fixture. Its main uncertainty is breakout/mechanics, not whether MAX30101 can measure PPG. |
| Is TMP117 useful? | **Yes, but only as a sensor whose thermal interface must be engineered.** Early skin-contact trends are feasible; a flex/thermal island is the stronger later path. |
| Is MPU-6050 sufficient? | **Yes for V1 motion/tremor/artifact experiments.** A modern FIFO/timestamp-oriented IMU is an optimization, not a prerequisite. |
| What is strongly missing from the present BOM? | **Battery-only body-connected operation, local nonvolatile logging, controlled electrodes/fixtures, and validation tools/procedures.** |
| When should the system become distributed? | Only when measured analog/cable/bus/mechanical problems or local-motion requirements justify the synchronization and power complexity. |

---

## 2. Current Project State Derived from Repository

The repository contains the existing research documents, the canonical physical inventory, and no implementation code at the time of this review:

- `docs/aud_multimodal_hardware_evidence_map.md`
- `docs/aud_sensor_role_definition.md`
- `docs/aud_hardware_form_factor_options.md`
- `docs/aud_novelty_hypothesis_map.md`
- `docs/hardware_inventory.md`

The four research documents were read in full before external implementation research; the physical-board observations below are reconciled against the [canonical hardware inventory](hardware_inventory.md). [S01–S04]

### 2.1 Current candidate hardware

| Current candidate | Intended role | Repository-established boundary |
|---|---|---|
| ESP32 DEVKITV1, 30-pin, ESP-WROOM-32-family controller board | acquisition, timing, buffering, control, transport | The carrier marking, 30-pin format, module family, Micro-USB, EN/BOOT buttons, and standard header labels are verified; exact carrier manufacturer, USB-UART device, regulator, and ADC performance remain unresolved. |
| ProtoCentral PC-tinyGSR legacy EDA/GSR board, PCB marking 12/22 | EDA/GSR | treat as **relative** unless exact legacy transfer/calibration is demonstrated; the `BASELINE` trimmer and `L324` marking are visible, while the ADC/interface, address, and transfer function remain unresolved. |
| CJMCU-8232 AD8232 single-lead ECG/heart-monitor module, PCB marking V502 | conditioned single-lead ECG-like waveform | AD8232 IC and labeled board connections are verified; exact V502 gain/filter/reference/RLD/passive implementation and analog performance remain unresolved. |
| SmartElex MAX30101 PPG/Photodetector breakout board | raw reflective PPG | Board identity and exposed `INT` are verified; silkscreen `ADR: 0x52` is only a physical observation, and the actual address convention, schematic, and optical mechanics remain unresolved. |
| SmartElex TMP117 digital temperature sensor breakout board | local/peripheral temperature | Exposed `INT`, address-selection markings, and a narrowed/cut-out sensor region are verified; the selected address and thermal path remain unresolved, and TMP117 IC performance does not establish skin-temperature accuracy for this breakout. |
| GY-521 MPU-6050 6-axis accelerometer + gyroscope IMU module | motion, activity, tremor, artifact context | Board marking, MPU-6050 IC, and connector pins are verified; exact carrier electronics, genuine-vs-compatible silicon status, and mounting behavior remain unresolved. |

The inventory records basic prior working status for the ESP32 and all five sensor boards. This is evidence of basic functional operation only; signal quality, timing, calibration, and validation remain separate tasks.

### 2.2 Physiological output boundaries retained from the repository

- **ECG:** raw conditioned electrical waveform → R peaks → heart rate/RR → HRV only after signal-quality/timing validation.
- **PPG:** raw red/IR/green optical samples → pulse peaks → pulse rate → PRV/morphology later. PPG-derived variability must not be silently relabeled ECG HRV.
- **EDA:** raw/relative conductance-related output → tonic trend and event-related/phasic response. Legacy tinyGSR should not be presented as universally calibrated absolute skin conductance.
- **Temperature:** sensor temperature → repeatable skin-contact trend only after mechanical/thermal validation.
- **IMU:** raw acceleration/angular velocity → movement magnitude/orientation → tremor spectrum or local artifact context.
- **System:** synchronized, timestamped multimodal raw recording with loss/quality metadata.

None of these sensors directly measures ethanol. A reliable physiological recorder is useful independent of later AUD/SUD interpretation.

### 2.3 Physical assumptions retained

The repository correctly separates:

- **L1:** bench/tethered development,
- **L2:** body-worn/distributed prototype built from development boards,
- **L3:** custom integrated wearable.

For the existing breakouts, the most realistic immediate topology is a **serviceable semi-wearable research system**, not a polished single wrist device. Chest/torso is a strong ECG site; finger is the cleanest first PPG site; palmar/distal fingers are strong EDA sites; temperature requires deliberate thermal contact; and IMU placement depends on whether its purpose is hand tremor, PPG motion, ECG-interface motion, or global activity. [S03]

---

## 3. Reference-System Shortlist — What to Replicate

This table is the fastest route through the report.

| Priority | System | Reference type | Replication confidence | Why it matters to this project | Study/reuse first |
|---|---|---|---|---|---|
| 1 | **EmotiBit** | EXACT + ARCHITECTURE | **R1** | Closest open ESP32 precedent for MAX30101 + EDA + temperature + IMU + buffers + Wi-Fi + SD | firmware scheduling/buffers; MAX30101 path; data packet/SD structure; EDA handling |
| 2 | **MAXREFDES100** | EXACT + FUNCTIONAL + ARCHITECTURE | **R1/R2** | Exact MAX30101 plus dedicated ECG AFE, temperature, motion, local flash; manufacturer-built/tested | complete block diagram, schematics, MAX30101 register workflow, interrupt-driven firmware, ECG cable/power guidance |
| 3 | **HealthyPi 5** | FUNCTIONAL + ARCHITECTURE | **R1** | Open multi-AFE biosignal system with acquisition MCU separated from ESP32-C3 RF MCU | RP2040 sensor ownership, UART RF bridge, microSD, MAX30001/AFe4400 integration, lossless broker |
| 4 | **HealthyPi Move** | FUNCTIONAL + ARCHITECTURE | **R1/R2** | Shipping open wearable already combines ECG + PPG + EDA + temp + IMU | physical partitioning, nRF5340/Zephyr acquisition, local flash, open PCB/enclosure |
| 5 | **SparkFun AD8232** | EXACT | **R1** | Clean open AD8232 board with schematic/PCB/demo and known electrodes | compare V502 board against SparkFun schematic; reproduce first ECG waveform |
| 6 | **ProtoCentral ADS1292R** | FUNCTIONAL | **R1** | 24-bit dedicated ECG/respiration AFE, DRDY/SPI, open HW/SW, ESP32-compatible pattern | replacement benchmark for ECG ADC path |
| 7 | **ProtoCentral tinyGSR v3** | FUNCTIONAL / near-family | **R1** | Open deterministic EDA front end + TLA2022 + calibration reference | quantitative replacement if legacy board is unstable/noncomparable |
| 8 | **TI TIDA-060034** | EXACT (TMP117) | **R2** | Exact TMP117 body-temperature flex reference with dual sensors and design files | flex geometry; system/ambient compensation; thermal separation |
| 9 | **MAXREFDES104 / 106** | FUNCTIONAL + MECHANICAL | **R1/R2** | Ready-to-wear/patch systems with dedicated skin-temperature flex + aluminum interface, flash, ECG/PPG/IMU | physical skin-temperature and body-worn packaging |
| 10 | **Shimmer3R GSR+** | FUNCTIONAL + VALIDATION | **R3** | Professional EDA + PPG + IMU system with raw SD data and published sample recordings | placement, GSR range expectations, real sample data, validation comparator |

---

## 4. Complete Existing Multimodal Systems

### 4.1 EmotiBit — strongest open ESP32 multimodal implementation reference

**Identity**

- Organization: EmotiBit / Connected Future Labs.
- Repositories: `EmotiBit/EmotiBit_FeatherWing`, `EmotiBit/EmotiBit_Docs`, supporting sensor libraries.
- Status: active open-source project; firmware supports Adafruit Feather ESP32.
- URLs: [S12], [S13], [S14].
- **Reference type:** EXACT for MAX30101 + ESP32-family controller; FUNCTIONAL for EDA/temp/IMU; ARCHITECTURE for buffering/logging.
- **Replication confidence:** **R1**.

**Sensors / modalities**

The platform acquires optical PPG, EDA, temperature, and inertial data. The public firmware contains distinct modality buffers and sensor-specific acquisition code. Working-data documentation describes raw streams and SD-recorded data.

**Architecture**

```text
MAX30101 PPG ─────┐
EDA AFE + ADC ────┤
temperature ──────┤
BMI160 IMU ───────┤
                  v
        Feather-class MCU
     (ESP32 supported target)
          │        │
        Wi-Fi    microSD
          │        │
      host/OSC   local raw log
```

**Sampling / buffering**

EmotiBit documentation records typical streams including approximately 25 Hz default PPG (with a 100 Hz special configuration), ~15 Hz EDA, ~7.5 Hz temperature, and ~60 Hz inertial streams. The firmware defines different buffer sizes for the modalities and larger PPG buffering for higher-rate configurations. These exact rates are EmotiBit-specific, not requirements for this project. [S12–S14]

**Communication / logging**

Wi-Fi streaming and local SD recording are first-class parts of the system. The data representation includes timing and packet-level metadata rather than emitting only isolated numeric features.

**Software**

The important study targets are:

- `EmotiBit.cpp` / `EmotiBit.h` — multi-sensor acquisition and buffering.
- `DoubleBufferFloat.*` / buffer utilities — producer/consumer decoupling.
- EDA handling/calibration source.
- EmotiBit’s MAX30101 fork/library.
- stock firmware entry point and platform files for Feather ESP32.
- `EmotiBit_Docs` hardware schematics for multiple board versions.
- working-with-data documentation for local SD data organization.

**What to reuse/adapt**

1. Copy the **architectural pattern**, not the whole product: each sensor writes into a bounded buffer; packetization/logging is decoupled from acquisition.
2. Reuse MAX30101 register/FIFO knowledge as a concrete comparison against the SmartElex board.
3. Reuse the principle that **wireless streaming is not the only copy of the data**.
4. Borrow packet sequence/data-count/protocol metadata ideas so dropped samples are observable.
5. Use EmotiBit’s EDA implementation as a reference for what a calibrated channel looks like, but do not transplant its transfer function to the legacy tinyGSR.

**What cannot be inferred**

- EmotiBit’s PCB optical geometry does not validate the SmartElex breakout mechanically.
- Its EDA calibration does not make the legacy tinyGSR absolute.
- Its physiological feature accuracy does not automatically transfer to this system.

**Validation evidence**

A 2024 peer-reviewed paper validates EmotiBit as a multimodal research device against reference equipment, while later workload validation also reports limitations for some derived measures. That mixed evidence is valuable: the raw platform is credible, but derived HRV/EDA agreement should not be assumed perfect. [S15, S16]

---

### 4.2 Analog Devices MAXREFDES100 — strongest manufacturer match to the desired electronic stack

**Identity**

- Product/reference design: MAXREFDES100 Health Sensor Platform.
- Manufacturer: Analog Devices (originally Maxim Integrated).
- URL: [S05].
- **Reference type:** EXACT for MAX30101; FUNCTIONAL for ECG/temp/IMU; ARCHITECTURE.
- **Replication confidence:** **R1/R2**.

**Sensors / modalities**

- MAX30101 PPG/SpO₂/optical HR sensor.
- MAX30003/MAX30004 biopotential/ECG AFE.
- two MAX30205 body-temperature sensors.
- LIS2DH and LSM6DS3 inertial sensors.
- barometric pressure sensor.

**Architecture**

```text
ECG electrodes → MAX30003/4 AFE ─SPI─┐
MAX30101 optical sensor ─────────────┤
MAX30205 temperature ×2 ─────────────┤
accelerometer/gyro ──────────────────┤
                                     v
                                  MAX32620
                           ┌─────────┼─────────┐
                           USB       BLE      flash
                           GUI       app      offline log
```

Analog Devices explicitly describes the board as designed, built and tested and publishes the schematic, BOM, PCB layout/CAD, fabrication package, PC GUI, driver, Android app, source, binaries, firmware/source links, and direct-to-flash logging. [S05]

**Physical arrangement**

The platform documentation explicitly discusses chest, wrist, ear, finger, head, and arm use cases. For ECG it advises shortening/twisting/shielding leads and moving the electronics closer to the body/electrodes.

**Power and safety lesson**

The reference design can run from USB or a coin cell. Its ECG “best practices” are directly relevant: when streaming over USB, Analog Devices recommends using a laptop running from battery power because isolation is not built into the board, disconnecting other mains-connected devices, and minimizing ECG cable length. This is a strong practical precedent for the body-connected prototype workflow. [S05]

**What to reuse/adapt**

- MAX30101 setup and GUI-controlled register exploration.
- Dedicated ECG AFE rather than relying on a general MCU ADC.
- interrupt-driven firmware structure.
- offline logging and later download.
- third-electrode/body-bias concept in a proven ECG AFE.
- physical noise-control guidance.

**What cannot be inferred**

The design does not include EDA, and its dedicated PCB/PMIC/RF layout is much cleaner than a breadboarded set of breakouts. It therefore proves system feasibility but not noise performance of the current wiring.

---

### 4.3 HealthyPi 5 — strongest open acquisition-MCU / RF-co-processor pattern

**Identity**

- Organization: ProtoCentral.
- Hardware repo: `Protocentral/protocentral_healthypi_5`.
- Current firmware: ProtoCentral HealthyPi 5 firmware/Zephyr repos; wireless bridge `healthybridge-esp32`.
- URLs: [S08–S11].
- **Reference type:** FUNCTIONAL + ARCHITECTURE.
- **Replication confidence:** **R1**.

**Architecture**

```text
MAX30001 ECG/BioZ ─SPI─┐
AFE4400 PPG ───────SPI─┤
temperature ───────I2C─┤
microSD ───────────SPI─┤
                       v
                     RP2040
          acquisition / broker / storage
                       │ UART framed protocol
                       v
                    ESP32-C3
                  BLE / Wi-Fi
```

Current ProtoCentral documentation says the RP2040 owns the sensors, microSD, and display, while the ESP32-C3 owns BLE/Wi-Fi and communicates with the RP2040 over a dedicated UART. [S08–S11]

**Why this matters**

This is a practical answer to the question “what if Wi-Fi/RTOS activity begins to interfere with acquisition?” Do not immediately add a second MCU; first prove one ESP32. If the measurement shows RF/scheduling coupling, HealthyPi demonstrates an evidence-backed next step: **separate the deterministic acquisition/data-broker role from the radio role**.

**Hardware assets**

The main repo exposes Eagle board/schematic files, BOMs, schematic PDFs, multiple main-board revisions, carrier-board designs and enclosure STLs. Current docs also describe isolation/protection on the carrier used for body-connected work.

**Software assets**

The modern firmware is structured around sensor acquisition, data brokerage, SD logging, watchdog behavior, and a separate wireless protocol. `OpenView` is a current cross-platform host visualization path.

**What to reuse/adapt**

- sensor ownership by one acquisition MCU;
- SPI for high-value biosignal AFEs;
- microSD local recording;
- UART-framed link to radio co-processor;
- explicit revision-aware pin maps;
- tutorial-first sensor bring-up before full application;
- watchdog and loss-aware design.

---

### 4.4 HealthyPi Move — closest complete open wearable by modality

**Identity**

- Organization: ProtoCentral.
- Product: HealthyPi Move.
- Current product page and docs: [S17].
- Hardware: `Protocentral/healthypi-move-hw` [S18].
- Firmware: `Protocentral/healthypi-move-fw` [S19].
- Status at research snapshot: product page reports in-stock original Move; firmware/hardware repositories remain active.
- **Reference type:** FUNCTIONAL + ARCHITECTURE.
- **Replication confidence:** **R1/R2**.

**Sensors**

Official product documentation lists:

- MAX30001 ECG/BioZ front end,
- MAX86141 wrist PPG,
- MAX30101 finger PPG,
- biometric sensor hubs,
- EDA/GSR,
- BMI323 IMU,
- MAX30208 skin temperature.

Core electronics use an nRF5340 dual-core MCU, 128 MB QSPI flash, Li-Po battery and PMIC. The watch stores data locally and supports BLE/USB synchronization. [S17]

**Physical arrangement**

The architecture is especially useful because it **does not force every optical measurement into one geometry**: it includes a wrist optical path and a separate finger PPG path. The open hardware repository separates main, sensor, bottom, and finger-sensor PCBs and includes an enclosure directory. [S18]

**What to reuse/adapt**

- system partition between main/sensor/finger boards;
- open PCB/mechanical design as a wearability reference;
- local flash-first data strategy;
- Zephyr-based event/sensor architecture;
- explicit raw-record format and integrity metadata in newer firmware direction;
- separation between continuous low-burden sensors and more deliberate measurements.

**What cannot be inferred**

Its proprietary/algorithm-hub derived metrics should not be transplanted as ground truth. Its warning also makes clear that the platform is not a certified medical diagnostic device.

---

### 4.5 MAXREFDES104 and MAXREFDES106 — mechanical and integrated-body references

**MAXREFDES104**

A ready-to-wear wrist platform that integrates ECG + PPG in the MAX86176 path, a host MCU, sensor/algorithm hub, 64 MB flash, 3-axis accelerometer, mechanical files, and a **temperature flex board whose MAX30208 contacts an aluminum disc for skin temperature**. [S06]

**MAXREFDES106**

A chest-patch-oriented health sensor platform with ECG/PPG/BioZ AFE, low-noise accelerometer, BLE MCU, 256 MB flash, 3D-printed enclosures, and both flexible and rigid temperature boards using a metal interface. [S07]

**Project value**

These designs are more useful than generic “put sensor against skin” advice. They demonstrate a recurring professional pattern:

```text
hot electronics / battery / radio
        ↓ thermally separated from
small temperature PCB/flex → controlled metal skin interface
```

That pattern should guide TMP117 mechanics even though the exact temperature IC differs.

---

### 4.6 Shimmer3R GSR+ — professional EDA/PPG/IMU benchmark

**Identity:** Shimmer3R GSR+ [S20]; sample data [S21].  
**Reference type:** FUNCTIONAL + VALIDATION + ARCHITECTURE.  
**Replication confidence:** **R3** because full internal design is not open to the same degree as EmotiBit/ProtoCentral.

The current platform combines analog GSR, optional optical pulse input and inertial sensing, Bluetooth, an 8 GB microSD, battery operation and a wearable enclosure. Public specifications describe a broad EDA resistance/conductance range, input RF/EMI filtering and current limiting. Shimmer publishes actual sample data where GSR electrodes are on the index/middle fingers and PPG is at the earlobe, including both sitting and walking segments. [S20, S21]

**Project value**

- a realistic quantitative expectation for a research EDA channel;
- a reference for finger EDA + ear PPG in controlled experiments;
- raw sample data as “what working looks like”;
- a candidate reference instrument if available to the lab.

---

### 4.7 BITalino / OpenSignals — accessible modular physiological instrumentation

BITalino provides modular physiological hardware, open firmware/software repositories and board-level files for portions of the ecosystem. [S22] It is less directly matched to the current ICs than the sources above, but it remains a useful **FUNCTIONAL/ARCHITECTURE** reference for:

- modular ECG/EDA acquisition,
- host-side visualization,
- simple reproducible wiring,
- teaching/validation rigs.

**Replication confidence:** **R2/R3**, varying by specific BITalino hardware generation.

---

### 4.8 Hemo-Monitoring-Wearable — academic open ECG + PPG hardware reference

The public repository includes a BLE wearable using ECG, PPG and electrical bioimpedance with nRF52840-class control and public Altium design files / signal examples. [S54]

**Why it matters:** it is a good academic reference for PCB-level separation and low-power integrated physiological acquisition; it is not a replacement for the complete system because EDA and temperature are absent.

**Replication confidence:** **R1/R2**.

---

## 5. ECG Reference Solutions

### 5.1 Current path: CJMCU-8232 V502 AD8232 → ESP32 ADC

**Reference type:** EXACT at AD8232 IC level; R3 for the exact `V502` CJMCU module because its passive network is still uncharacterized.

The first objective is not diagnostic ECG. It is:

```text
electrodes → AD8232-conditioned analog waveform
           → ADC1 samples
           → timestamped raw waveform
           → robust R peaks at rest
           → HR / RR
```

#### Strongest direct implementation reference: SparkFun AD8232

The SparkFun AD8232 repository provides:

- `Hardware/` Eagle `.brd/.sch`;
- `Production Files/`;
- `Software/` demo;
- Fritzing diagrams;
- an established three-electrode hookup guide. [S23, S24]

The common electrode naming is RA, LA and RL. SparkFun’s basic example reads the analog output and leads-off pins. It is useful for **first-light** verification, not as the final timing architecture.

#### Strongest IC/reference-circuit source: Analog Devices AD8232 evaluation design

Analog Devices documents:

- three- or two-electrode configurations;
- REFOUT/mid-supply behavior;
- two-pole high-pass and low-pass signal conditioning;
- RLD/common-mode feedback;
- leads-off modes;
- input current-limiting resistors;
- complete evaluation-board schematic. [S25]

This is critical because the current V502 board’s passive network is uncharacterized. Before interpreting waveform morphology, read/measure the V502 passives and compare their topology with a known AD8232 design; use the inventory photographs as the identity baseline.

### 5.2 Is the classic ESP32 ADC genuinely adequate?

**For first-light ECG and R-peak detection at rest: yes, conditionally.**  
**For the strongest research ECG/HRV baseline: it is not the preferred end state.**

The reasons are concrete:

1. Espressif documents the ADC as a general MCU ADC requiring calibration, not a precision biopotential converter. [S43]
2. ESP32 ADC behavior is noise-sensitive; Espressif recommends input bypassing/multisampling in its ADC guidance. [S47]
3. ADC2 cannot be used while Wi-Fi is active, so the AD8232 output should be on **ADC1**. [S45]
4. The ESP-IDF continuous ADC driver provides DMA/frame-buffered acquisition, but it also exposes a pool-overflow condition if software does not drain the data fast enough. This is a reason to count overflows explicitly. [S44]
5. A FreeRTOS software-timer callback is a weak clock for uniform sample acquisition; ESP Timer or ADC peripheral timing is higher-resolution and less dependent on the low-priority timer task. [S46]

#### Recommended first-light ESP32 implementation

- route AD8232 `OUTPUT` to a verified **ADC1** pin;
- use the ESP-IDF **continuous ADC** driver rather than `analogRead()` inside a loosely timed application loop;
- choose an engineering starting rate of **250–500 samples/s** for raw ECG;
- record raw ADC code and calibrated voltage if ADC calibration is available;
- timestamp the DMA/frame boundary with a monotonic clock and derive sample times from the known sample cadence;
- keep Wi-Fi off during the first noise characterization;
- A/B test RF off/on later;
- log locally rather than making serial/Wi-Fi success part of the definition of acquisition success;
- retain the exposed leads-off state from the V502 board's `LO+`/`LO−` connections.

The proposed 250–500 SPS range is an engineering target for this project, not a claim that AD8232 requires that rate.

### 5.3 AD8232 + better generic ADC

This path can improve ADC resolution/reference behavior while preserving the AD8232 analog conditioning.

**Advantages**
- preserves the current front end;
- isolates the ESP32 on-chip ADC as a variable;
- less firmware change than replacing ECG AFE entirely.

**Disadvantages**
- a generic high-resolution ADC does not add ECG-specific lead-off, RLD, saturation recovery, self-test, or proven electrode protection;
- an I²C ADC can introduce bus/scheduling coupling with other sensors;
- once the project is buying and integrating a dedicated converter, a dedicated ECG AFE/ADC often has better implementation evidence.

**Decision:** **H3**, not the first recommended purchase. Use this only if retaining the AD8232 is important and a specific precision converter can be benchmarked cleanly.

### 5.4 Dedicated ECG AFE/ADC: ProtoCentral ADS1292R

**Reference type:** FUNCTIONAL.  
**Replication confidence:** **R1**.

The ProtoCentral ADS1292R ecosystem provides:

- open breakout/shield hardware;
- Arduino/C++ library;
- examples;
- two ECG electrodes plus driven-right-leg electrode;
- SPI interface;
- explicit `DRDY`, `START`, `RESET`, `CS`;
- a 24-bit dedicated biopotential conversion path;
- test guidance recommending an ECG simulator;
- open hardware/software licenses. [S26, S27]

The exact part to study:

- `protocentral-ads1292r-arduino/examples/`
- `src/`
- `ADS1292rShield_Breakout` board design
- OpenView integration
- the DRDY-driven SPI read sequence.

**Why it is a strong replacement benchmark**

It removes the classic ESP32 ADC from the ECG signal chain and makes the sample clock/data-ready event explicit. It also preserves a familiar breakout-board workflow.

### 5.5 Dedicated single-lead wearable AFE: MAX30001/MAX30003

ProtoCentral’s current MAX30001 breakout documentation specifies single-lead ECG with an 18-bit sigma-delta path, 128/256/512 SPS options, on-chip R-R support, SPI, interrupts and an explicit ESP32 wiring example. [S28] MAXREFDES100 uses a MAX30003/MAX30004-class AFE in a tested multimodal platform. [S05]

**When preferable to ADS1292R**

- if a compact low-power wearable-oriented single-lead path is the goal;
- if on-chip R-R/lead-on/off functions are useful;
- if the project later moves toward a custom integrated PCB.

### 5.6 ECG path comparison

| Path | Complexity | Signal-confidence ceiling | Implementation evidence | HR/RR suitability | HRV research suitability | Main risk | Recommendation |
|---|---|---:|---|---|---|---|---|
| CJMCU AD8232 → ESP32 ADC1 | Lowest | Medium | R3 exact module / R1 IC refs | Good if clean | Conditional | ADC noise + unknown V502 filters | **Use first** |
| AD8232 → generic precision ADC | Medium | Medium-high | depends on ADC | Good | Better if validated | added ADC without ECG-specific benefits | H3 |
| ProtoCentral ADS1292R | Medium | High | **R1** | Strong | Strong | extra SPI/board | **H2 benchmark/upgrade** |
| MAX30001/MAX30003 | Medium | High | **R1/R2** | Strong | Strong | integration learning | **H2 benchmark/upgrade** |

### 5.7 ECG electrode and mechanical baseline

For first body-connected recording:

- use standard disposable Ag/AgCl snap ECG electrodes;
- begin with a documented three-electrode configuration;
- keep electrode cables short and strain-relieved;
- keep the front end close to the electrodes;
- test at rest before motion;
- operate the body-connected side from battery with mains/charging disconnected.

The best first output is a stable raw ECG waveform with reproducible R peaks, not an HRV dashboard.

---

## 6. PPG Reference Solutions

### 6.1 Current SmartElex MAX30101 breakout

**Assessment:** keep it for V1 unless board verification fails.

The MAX30101 IC is not the weak-evidence part of this path. It has unusually strong manufacturer and open-system precedent:

- MAXREFDES100 uses MAX30101 in a complete tested system. [S05]
- EmotiBit uses MAX30101 in a working multimodal design. [S12–S14]
- SparkFun publishes an open MAX30101 breakout with board files and drivers. [S29, S30]
- HealthyPi Move includes a MAX30101 finger optical sensor in a complete wearable ecosystem. [S17]

The SmartElex-specific uncertainties are:

- exact regulator/level interface and schematic;
- address convention behind the physical silkscreen observation `ADR: 0x52` (not yet a verified 7-bit address);
- interrupt behavior and driver semantics, although `INT` is physically exposed;
- PCB optical aperture/guarding;
- mechanical flatness and sensor-to-skin spacing;
- ambient-light shielding;
- how much board rocking/contact-pressure change occurs.

### 6.2 Strongest exact references

#### MAXREFDES100

Use it to study:

- MAX30101 sensor configuration flow;
- register-level tuning;
- simultaneous accelerometer/optical viewing;
- interrupt-driven system architecture;
- optical logging into CSV/flash. [S05]

#### SparkFun MAX30101 Qwiic hardware + MAX3010x library

The SparkFun hardware repo exposes Eagle schematic/board and production files. The Arduino library supports raw FIFO reads, LED amplitudes, modes and device configuration. [S29, S30]

The library is excellent for first-light polling and register exploration. It should not be assumed to be the final interrupt architecture; inspect exactly what its driver supports and adapt the FIFO/INT path as necessary.

#### EmotiBit MAX30101 path

EmotiBit is the most valuable reference for **MAX30101 while other sensors are active**, because it solves the multi-buffer and logging problem rather than just producing a pulse trace.

### 6.3 Recommended PPG first-light fixture

Do not begin at the wrist. Begin with a mechanically controlled finger experiment:

```text
opaque top/side shroud
        ↓
[MAX30101 optical face]
        ↕ fixed light pressure
     finger pad
        ↑
soft backing / adjustable strap
```

Practical requirements:

- sensor window flush or nearly flush to skin;
- opaque foam/printed shroud around the optical area;
- repeatable pressure—not loose contact and not strong vascular compression;
- rigidly support the PCB so the optical package does not rock;
- co-locate the IMU on the fixture if motion-artifact experiments matter;
- record red, IR and green raw channels before relying on any HR algorithm.

### 6.4 Sampling / FIFO starting point

For this project, a **100 SPS raw PPG target** is a defensible engineering starting point because it preserves pulse morphology and is still trivial in bandwidth; EmotiBit has a 100 Hz configuration precedent. Lower rates can be evaluated after raw quality is known. Do not blindly copy another project’s LED current, averaging, pulse width, ADC range or sample averaging: optical geometry and skin/site dominate these settings.

The firmware should:

- configure FIFO;
- read all available FIFO samples on each service event;
- expose overflow or missed-interrupt counters;
- timestamp batches at acquisition, not at host reception;
- reconstruct each sample time from the configured cadence;
- retain LED/current/register configuration in session metadata.

### 6.5 Should the SmartElex breakout be replaced?

**Not initially.** Replace only if one of these is observed:

- pinout/voltage behavior cannot be verified;
- sensor ID/register access is unreliable;
- no practical interrupt/FIFO access;
- power rail is noisy or overheats;
- optical window/PCB geometry makes repeatable contact impossible;
- raw signal remains unstable after a proper fixture is built.

If replacement is needed, the **SparkFun MAX30101 Qwiic breakout** is the strongest like-for-like research option because its schematic and PCB are public. [S30]

### 6.6 Validation

At rest:

1. record raw MAX30101 and simultaneous ECG/reference pulse;
2. show pulse peaks on raw PPG;
3. compare pulse rate with ECG heart rate;
4. create deliberate small pressure/light-leak perturbations and document the failure signature;
5. repeat with hand movement while recording local IMU;
6. retain both “good” and “bad” windows as fixture-validation data.

---

## 7. EDA Reference Solutions

### 7.1 Current ProtoCentral PC-tinyGSR 12/22 — what it can honestly do

**Current decision:** retain for first baseline as a **relative EDA sensor**.

The repository’s caution is correct: the exact legacy PCB 12/22 transfer function is not yet controlled. The physical inventory verifies the `BASELINE` trimmer and `L324` analog-IC marking, but the ADC/interface IC, I²C address, trimmer transfer function, raw-value-to-conductance conversion, and quantitative calibration remain unresolved. ProtoCentral’s current documentation and legacy support context indicate the tinyGSR family uses a TLA2022-class digital ADC path, and the historical board family has revision-dependent analog behavior. [S33–S35]

The legacy board should be tested as:

```text
electrodes → legacy analog front end → TLA2022 counts
                                  → within-board relative response
```

not:

```text
counts → universal absolute µS across devices/users
```

### 7.2 Exact first tests for the legacy board

Before attaching to a person:

1. run an I²C scan and identify address;
2. read device/config registers if the library supports them;
3. place known precision resistors across the electrode input over a physiologically relevant broad range;
4. record raw counts for each resistor repeatedly;
5. verify monotonicity, stability, saturation limits and noise;
6. repeat after power cycle;
7. document trimmer position/board photographs if a trimmer exists on this revision.

**Pass condition for V1:** the same board gives stable, monotonic, repeatable relative output and visible controlled changes. Absolute conductance calibration is not required for the first instrumentation milestone.

### 7.3 Modern ProtoCentral tinyGSR v3 — strongest low-friction replacement

**Reference type:** FUNCTIONAL / same product family.  
**Replication confidence:** **R1**.  
**Additional-hardware class:** **H2**.

The current open-source tinyGSR provides:

- public hardware and firmware;
- TLA2022 12-bit delta-sigma ADC;
- deterministic front end;
- Qwiic/STEMMA QT interface;
- selectable I²C addresses;
- open hardware/software licensing;
- published build assets and Arduino/C++ source. [S33, S34]

This is the preferred replacement if the research requirement becomes **repeatable quantitative conductance across sessions/boards**, or if the legacy unit is unstable.

### 7.4 EmotiBit EDA — system-integration reference

EmotiBit is valuable not because its exact EDA front end must be copied, but because it shows:

- EDA sampled concurrently with optical, temperature and motion channels;
- calibrated processing structure;
- buffering and local logging;
- real-world wearable use. [S12–S16]

### 7.5 Nervous-EDA — high-end open alternative

**Reference type:** FUNCTIONAL.  
**Replication confidence:** **R1**.  
**Recommendation:** **H3/H4** for this project’s first baseline.

The Nervous EDA project publishes:

- `hardware/` manufacturing ECAD;
- `firmware/` for its analog co-processor and wireless processor;
- `web-app/`;
- release archives with hardware files/binaries;
- a peer-reviewed implementation reference.

It performs multi-frequency impedance spectroscopy rather than simple DC/low-frequency GSR. [S36]

It is technically excellent but over-complex for the immediate goal. Study it if electrode/contact impedance characterization later becomes a core engineering problem.

### 7.6 Shimmer GSR+ — validation/benchmark reference

Shimmer’s published GSR+ specs and example data give a useful sanity check for range, bandwidth, electrodes and placement. Sample data use the index and middle fingers and include an intentionally moving segment. [S20, S21]

### 7.7 Electrode baseline

For controlled research:

- begin with two consistent Ag/AgCl-type skin electrodes or the manufacturer-provided/recommended GSR contacts;
- use distal index/middle finger or another documented palmar site;
- standardize electrode area, separation, pressure, skin preparation and recording duration;
- avoid interpreting absolute level before the legacy board is calibrated;
- log ambient/skin temperature and movement because both can alter EDA context.

---

## 8. Temperature Reference Solutions

### 8.1 Current SmartElex TMP117 — electrically good, mechanically unresolved

The TMP117 IC itself is a high-quality digital temperature sensor. The physical inventory verifies exposed `INT`, address-selection markings for `0x48`–`0x4B`, and a narrowed/cut-out central sensor region. The project’s main risk is **thermal path**, not I²C; the selected address and exact electrical implementation still require verification.

The narrowed/cut-out region appears intended to reduce thermal coupling, but it does not guarantee accurate skin temperature. The complete thermal path, contact bias, self-heating, and quantitative isolation still require validation.

The first question is:

> Does the TMP117 die become dominated by skin temperature in a repeatable time window, or is it mostly measuring the breakout/enclosure/ambient/electronics?

### 8.2 Exact manufacturer reference: TI TIDA-060034

**Reference type:** EXACT for TMP117 body-contact design.  
**Replication confidence:** **R2**.

TI’s TIDA-060034 uses **two TMP117 devices on a small flexible PCB** for hearable body-temperature measurement. One important purpose of the dual-sensor arrangement is compensating system/ambient thermal effects. TI publishes a design guide, assembly drawing, BOM, CAD/CAE assets, Gerbers, PCB layout and schematic. [S37]

This is the strongest exact answer to “how should TMP117 physically become a body sensor?”

**What to adapt**

- small low-thermal-mass flex/daughterboard;
- place the skin-facing sensor away from MCU/regulator/battery heat;
- if precision matters, consider a second ambient/system sensor rather than assuming ambient effects are negligible;
- validate the complete thermal assembly, not only the IC.

### 8.3 Mechanical reference: MAXREFDES104/106 temperature flex

Analog Devices’ wearable references use a separate temperature flex/board connected to an **aluminum disc** for controlled skin contact. [S06, S07]

This suggests an immediately prototypeable pattern for the SmartElex board:

```text
skin
 │
thin thermally conductive interface / small metal contact
 │
TMP117 sensor zone
 │
thermal isolation / air gap / low-conductivity mount
 │
cable
 │
ESP32 + battery + PPG LEDs + regulators kept away
```

Do not add a large metal plate without testing: increasing metal mass can slow response and increase ambient coupling. The goal is repeatable contact, not simply “more metal.”

### 8.4 Electrical bring-up references

SparkFun and Adafruit publish open TMP117 breakout hardware and libraries. These are useful for register/configuration validation when the SmartElex schematic is unavailable, but they do not solve the body-contact mechanics by themselves. [S38]

### 8.5 V1 experiment using the current breakout

1. read TMP117 on the bench and compare against a traceable/contact reference at room temperature;
2. touch/fixture only the sensor end to a controlled warm surface and measure time response;
3. attach to skin using a light, repeatable pressure fixture;
4. place the ESP32/battery at least several centimeters away initially;
5. compare against a co-located contact reference;
6. repeat with Wi-Fi/PPG LEDs on/off and observe self-heating;
7. document equilibration time and residual bias.

**Decision:** keep current board for early trend experiments; move to a small flex/thermal island before claiming robust skin-temperature measurement.

---

## 9. IMU / Tremor Reference Solutions

### 9.1 Current GY-521 / MPU-6050

**Decision:** keep for V1.

The physical inventory verifies the GY-521 marking, MPU-6050 main IC, and exposed `VCC`, `GND`, `SCL`, `SDA`, `XDA`, `XCL`, `AD0`, and `INT` pins. Exact regulator, pull-ups, genuine-vs-compatible silicon status, and measured noise/timing performance remain unresolved. The MPU-6050 is old, but the basic requirements here are modest:

- motion/context;
- hand/wrist tremor spectrum;
- local optical/electrode motion;
- gross activity.

Its established range selection and large software ecosystem are sufficient for these experiments if the verified board is electrically stable; genuine-vs-compatible silicon status and measured performance remain open.

### 9.2 Strongest implementation reference: i2cdevlib

The `jrowberg/i2cdevlib` project contains a mature MPU-6050 device class, raw and DMP examples, calibration/offset examples and interrupt/FIFO patterns. [S40, S41]

Important study locations:

- `Arduino/MPU6050/`
- `examples/MPU6050_raw`
- `examples/MPU6050_DMP6`
- `examples/IMU_Zero`

For this project’s first acquisition, prefer **raw calibrated acceleration/gyro** over making the DMP a dependency. Tremor and artifact analysis require transparent raw timing more than orientation quaternions.

### 9.3 Sampling and configuration

For a tremor-analysis baseline where the repository’s literature focuses on frequencies below roughly 15 Hz, **100–200 SPS** raw IMU is a comfortable engineering starting rate. Configure accelerometer/gyro ranges so normal motion does not clip but resolution remains useful; begin with lower ranges for controlled tremor tests and verify clipping during real activity.

Do not interpret an FFT until:

- sensor rate is measured/controlled;
- dropped FIFO samples are detectable;
- the board is rigidly fixed to the body segment;
- stationary bias/noise is characterized.

### 9.4 Placement by role

| Intended IMU role | Strong first placement |
|---|---|
| hand tremor | hand/wrist/forearm, rigid mounting |
| PPG artifact reference | same fixture as PPG |
| ECG electrode/interface artifact | chest/front-end module |
| gross activity | waist/torso or existing wrist node |
| EDA contact motion | same hand/wrist structure as EDA |

One IMU at the ESP32 enclosure cannot be assumed to solve all of these if the sensors are at different sites.

### 9.5 Modern alternative: Bosch BMI270

**Reference type:** FUNCTIONAL.  
**Replication confidence:** **R2** from official sensor API.  
**Additional hardware:** **H3** unless timing/FIFO/power problems appear.

Bosch’s official API supports wearable-oriented BMI270 variants and a 6 kB FIFO. [S42] A modern IMU becomes materially valuable if the project needs:

- deeper FIFO buffering;
- cleaner interrupt/timestamp behavior;
- lower power;
- better long-term component availability;
- multiple synchronized motion streams.

The current MPU-6050 should not be replaced merely because BMI270 is newer.

### 9.6 IMU validation

- **stationary:** estimate bias and RMS noise;
- **six-face gravity check:** each axis should show approximately ±1 g in the appropriate orientation;
- **repeatable periodic motion:** mechanically oscillate or use a metronome-guided motion and verify the dominant spectral peak;
- **video reference:** record a high-frame-rate video with a visible timing cue for gross-motion event alignment;
- **local artifact experiment:** move the PPG fixture/electrode interface while keeping the rest of the body still, then verify that the local IMU—not only a distant IMU—tracks the perturbation.

---

## 10. Controller / ESP32 Reference Solutions

### 10.1 What one classic ESP32 can realistically handle

The raw data rate of this system is small compared with ESP32 throughput. A plausible first baseline such as:

- ECG 500 SPS × 2 bytes/sample,
- PPG 100 SPS × 3 channels × 4 bytes,
- EDA 32 SPS × 2–4 bytes,
- temperature 4 SPS × 4 bytes,
- IMU 200 SPS × 6 axes × 2 bytes,

is only on the order of a few kilobytes per second before metadata. The hard problem is not bandwidth. It is **deterministic acquisition, buffering, electrical noise, and honest loss detection**.

EmotiBit directly demonstrates that an ESP32-class Feather can run PPG + EDA + temperature + IMU with networking and SD logging. [S12–S14]

### 10.2 ADC rules for ECG

Use:

- **ADC1, not ADC2**, if Wi-Fi may ever be enabled. Espressif explicitly states ADC2 pins cannot be used while Wi-Fi is used. [S45]
- the **continuous ADC driver** with its DMA/frame buffer for ECG rather than ad-hoc single reads. [S44]
- calibration where supported.
- physical bypass/decoupling and measured noise testing rather than assuming nominal bit depth equals useful resolution.

### 10.3 Timing rules

Do not use a general `delay()` loop or a FreeRTOS software timer as the fundamental sample clock. Espressif documents that FreeRTOS software timers are tick-limited and callbacks run from a preemptible low-priority task. [S46]

Preferred order:

1. sensor/AFE’s own conversion clock + `DRDY`/FIFO interrupt;
2. ESP32 ADC peripheral continuous mode;
3. high-resolution `esp_timer` for lower-rate orchestration/timestamping;
4. FreeRTOS task/timer only for non-critical work such as UI/network maintenance.

### 10.4 Task structure

Recommended conceptual task separation:

```text
ISR / DMA callbacks
  ├─ ADC frame ready
  ├─ MAX30101 INT / service flag
  └─ IMU INT / service flag
           │
           v
high-priority acquisition task
  ├─ drains FIFO/ADC frames
  ├─ timestamps
  ├─ writes bounded per-sensor queues
  └─ increments overflow/loss counters
           │
           v
logger task ───────────────→ microSD
           │
           └───────────────→ optional RF preview queue
                               │
                               v
                         BLE/Wi-Fi task
```

No sensor callback should perform long SD writes, formatting, JSON serialization or network transmission.

### 10.5 I²C strategy

The current digital stack is modest, but it uses several breakouts, each potentially carrying its own pull-ups.

For V1:

- keep I²C wiring short and bundled with a clear ground return;
- begin at 100 kHz if wiring is nontrivial;
- inventory all pull-ups and calculate/measure the effective parallel resistance;
- scan addresses at startup and store the result in the session header;
- do not run I²C through long body-spanning cables by assumption;
- separate analog ECG routing from noisy digital/RF wiring.

An I²C multiplexer/buffer is **not required by default**. Add it only for a measured address/capacitance/topology problem.

### 10.6 Local logging

Local nonvolatile storage is strongly justified for body testing because:

- it decouples acquisition integrity from RF;
- it permits battery-only, cable-free operation;
- EmotiBit, HealthyPi 5, HealthyPi Move and multiple Analog Devices platforms use local storage in their reliable architectures. [S05, S08, S12, S17]

The logger should store binary or compact fixed-record data during acquisition if CSV formatting becomes a latency problem; conversion to CSV can happen afterward. At minimum every record/batch needs:

- local monotonic timestamp;
- sensor/channel ID;
- sequence number;
- sample count;
- configured sample rate;
- overflow/error flags;
- payload;
- firmware/config version.

### 10.7 When one ESP32 stops being the right architecture

A second MCU/local node becomes justified if measurement shows:

- RF activity measurably degrades ECG/EDA;
- body-spanning wiring is unreliable;
- the ADC/transport tasks overflow despite correct buffering;
- different body sites require local digitization;
- local sensor-motion references require separate modules;
- synchronized chest/wrist topology becomes a research requirement.

HealthyPi 5 provides the strongest architecture reference for separating an acquisition MCU from an RF MCU. [S08–S11]


## 11. Multimodal Integration Reference Solutions

The integration problem is where otherwise working sensors usually fail. The useful existing systems do not simply wire every device into one loop; they use hardware FIFOs, interrupts, local queues, bounded buffers, independent logging, and explicit transport layers.

### 11.1 Integration problems and proven solution patterns

| Integration problem | Concrete existing solution pattern | Project adaptation |
|---|---|---|
| ADC acquisition blocked by other tasks | ESP-IDF continuous ADC DMA; dedicated ECG AFE with DRDY; EmotiBit sensor buffers | ECG acquisition must never wait on SD/network/JSON |
| PPG FIFO overflow | MAX30101 FIFO service in mature drivers/reference platforms | drain complete FIFO bursts; count overflow/missed service |
| Different sensor rates | EmotiBit per-modality buffers; professional platforms store rate metadata | keep independent rates; do not upsample at acquisition |
| Wi-Fi/analog coupling | MAXREFDES100 recommends battery/noisy-cable precautions; HealthyPi 5 separates RF MCU | characterize RF A/B; split RF only if data justify |
| Network packet loss | local flash/SD in EmotiBit, HSP, HealthyPi | local log is authoritative; wireless is preview/secondary |
| Long analog ECG leads | MAXREFDES100 explicitly recommends short/twisted/shielded leads / PCB near body | place AD8232/dedicated AFE near chest electrodes |
| Body-spanning I²C | professional wearables place sensors on controlled local PCBs | keep buses local; use node split instead of arbitrarily extending I²C |
| One motion reference for multiple sites | local-IMU literature + wrist PPG/IMU systems | temporarily over-instrument with local IMUs if artifact attribution matters |
| Temperature self-heating | TIDA-060034 dual TMP117 flex; MAXREFDES104/106 separate temp flex | create thermal island, keep radio/battery/LEDs remote |
| Board revision uncertainty | HealthyPi 5 uses revision-aware pin definitions; EmotiBit prints HW version and publishes versioned schematics | build a board-inventory file and never infer from product family alone |

### 11.2 Current likely I²C topology

The exact physical boards must be scanned rather than assumed, but the likely devices are sufficiently address-diverse for a single short V1 bus. Important caveat: a **modern tinyGSR v3 and TMP117 can both occupy common low I²C address regions depending on strap configuration**, so address straps must be checked during any replacement.

A safe startup procedure is:

```text
power digital sensors only
→ I²C scan at 100 kHz
→ compare discovered addresses against board inventory
→ initialize one device at a time
→ run all sensors together
→ monitor NACK/retry/error counters
→ raise bus speed only if needed and verified
```

### 11.3 Pull-up conflicts

Four breakouts may each carry SDA/SCL pull-ups. Parallel pull-ups reduce effective resistance. Too-strong pull-up increases current and can violate device sink capability; too-weak pull-up plus cable capacitance slows edges.

**Action:** physically inspect/measure resistance from SDA/SCL to the active rail with power removed. Record which board supplies each pull-up. Remove/disable redundant pull-ups only when the exact PCB is known.

### 11.4 Interrupt allocation

A robust centralized build should reserve GPIOs for:

- MAX30101 interrupt, if the exact breakout exposes it;
- IMU interrupt;
- ECG leads-off pins for AD8232, or `DRDY` for a dedicated ECG AFE;
- microSD chip select;
- optional shared sync/test output.

Do not require every low-rate device to have an interrupt. TMP117 and relative EDA can be serviced by a lower-rate scheduler if that does not block higher-rate channels.

### 11.5 Shared power rails

For L1/L2:

- use one clean battery-derived 3.3 V rail where board compatibility allows;
- verify which breakout has onboard regulators and level shifters before applying 5 V;
- decouple locally at every board;
- keep PPG LED current return and radio current paths physically away from the ECG analog reference path;
- route grounds deliberately rather than through long daisy chains;
- characterize PPG LED switching and radio transmit effects in ECG/EDA spectra.

### 11.6 Cable strategy

**Good first harness**

- short chest electrode leads into ECG AFE;
- conditioned analog ECG from AFE to nearby central board, or preferably digitize locally if chest distance is large;
- short I²C within a hand/wrist pod;
- digital link from pod to central logger if distances become body-scale;
- strain relief at every skin-facing cable.

**Avoid**

- several tens of centimeters of loose unshielded raw ECG electrode wire running beside Wi-Fi antenna/PPG LED wiring;
- long moving I²C ribbon as a final body topology;
- unsupported sensor boards hanging from electrode/optical leads.

### 11.7 Recommended data model

Store samples as independent sensor streams sharing one timebase.

Example conceptual record:

```text
session_id
firmware_hash
board_inventory_hash
sensor_id
local_timestamp_us
sample_period_us
first_sample_sequence
sample_count
status_flags        # fifo_overflow, leads_off, i2c_retry, saturation...
configuration_hash  # register settings
samples[]
```

A later merged table can interpolate/align for plotting. The acquisition firmware should not destroy native timing to make a visually convenient single-row CSV.

---

## 12. Centralized Acquisition Architectures

### 12.1 Centralized option CEN-1 — current hardware, one ESP32

```text
Chest ECG electrodes
      │
   CJMCU-8232 V502 AD8232 ──analog──> ESP32 ADC1
                         │
SmartElex MAX30101 ─I²C─┤
PC-tinyGSR 12/22 ──I²C──┤
SmartElex TMP117 ──I²C──┤
GY-521 MPU-6050 ───I²C──┤
                         ├──SPI── microSD
                         └──BLE/Wi-Fi preview
```

**Advantages**

- minimum added electronics;
- single monotonic clock;
- easiest firmware/debug;
- no inter-node drift;
- directly tests the present BOM.

**Weaknesses**

- ADC uncertainty on ECG;
- physical harness may make I²C/analog routing awkward;
- one IMU may be remote from some sensing interfaces;
- radio and shared rails can couple noise.

**Replication confidence:** **R2** when built using EmotiBit-like buffering and Espressif ADC rules.

### 12.2 Centralized option CEN-2 — one MCU + dedicated ECG AFE

```text
ECG electrodes → ADS1292R/MAX30001 ─SPI/DRDY─┐
 SmartElex MAX30101 ─I²C/FIFO──────────────────┤
 PC-tinyGSR 12/22 ───I²C───────────────────────┤
 SmartElex TMP117 ───I²C───────────────────────┤
 GY-521 MPU-6050 ────I²C/FIFO──────────────────┤
                                              v
                                      acquisition MCU
                                          │      │
                                         SD     BLE/Wi-Fi
```

This is the preferred **controlled body-worn** centralized architecture because it removes the noisiest uncertain conversion path without changing the rest of the BOM.

**Replication confidence:** **R1/R2** by analogy to MAXREFDES100 and HealthyPi architectures.

### 12.3 Practical wire-length rule

There is no universal “maximum body cable length” that can be stated honestly without cable capacitance, pull-ups, routing and speed. Therefore:

- keep analog ECG path as short as practical;
- keep I²C local to a module rather than treating it as a body-area bus;
- if a body-spanning cable is needed, prefer a robust digital point-to-point link after local sampling/digitization.

### 12.4 When centralized should win

Use centralized V1 if:

- all sensors can reach the controller with a controlled harness;
- the goal is synchronized proof rather than ambulatory comfort;
- one central battery/logger is operationally simpler;
- measured I²C and ECG noise are acceptable.

Do not split nodes only because a distributed architecture sounds sophisticated.

---

## 13. Distributed Acquisition Architectures

### 13.1 Distributed option DIST-1 — chest ECG node + peripheral multimodal node

```text
        CHEST NODE
ECG electrodes
     │
dedicated ECG AFE
     │
 local MCU ─ local flash
     │
     ├── sync pulse / clock-sync messages
     │
     v
        PERIPHERAL NODE
MAX30101 + local IMU
EDA + local IMU if needed
TMP117 thermal island
     │
 local MCU ─ local flash
     │
     v
      host merge / analysis
```

This matches the physiological reality better than an all-wrist assumption: chest ECG can remain at a strong electrical site while PPG/EDA/temperature/motion occupy an appropriate distal site.

### 13.2 Distributed option DIST-2 — acquisition nodes + radio hub

A second pattern is not “every sensor has Wi-Fi.” Instead:

```text
local sensor nodes ─wired digital/UART/SPI-like link─> central acquisition hub
                                                   │
                                                   └─ radio/storage
```

This can reduce analog lead length without multiplying radios.

### 13.3 Existing references

- body-sensor-network systems and prior art in the repository demonstrate that distributed synchronization is not novel; that is useful here because it means the pattern is mature. [S04]
- HealthyPi 5 demonstrates deterministic acquisition ownership separated from wireless ownership. [S08–S11]
- Shimmer demonstrates multi-device wearable data logging and synchronization tooling at a commercial research-platform level. [S20, S52]
- OpenEarable and SensWear demonstrate open modular body-worn local-sensor designs with modern BLE/IMU/temperature/PPG patterns. [S55, S56]

### 13.4 Costs of distribution

Every additional node adds:

- battery and charging;
- clock drift;
- firmware versioning;
- startup/session pairing;
- data-loss modes;
- radio coexistence or wired-link complexity;
- donning errors.

Therefore distribution is a **measurement-driven escalation**, not a default.

### 13.5 Trigger conditions for splitting V1

Split only after one or more are measured:

1. ECG quality improves materially when the AFE/ADC is moved to the chest.
2. Central I²C wiring produces errors or unacceptable cable burden.
3. PPG requires a local IMU that is not physically representative of the central module.
4. EDA contact motion needs its own local reference.
5. radio activity cannot be sufficiently isolated from analog acquisition.
6. the form factor becomes unusable even though the electrical system works.

---

## 14. Synchronization Solutions

### 14.1 The synchronization requirement depends on the output

Different analyses need different timing precision.

| Output | Timing requirement |
|---|---|
| independent HR, EDA trend, temperature, gross activity | tens to hundreds of ms may be scientifically usable |
| event-aligned cue/behavior response | typically much tighter than event duration, but not sample-level ECG/PPG |
| ECG ↔ PPG beat pairing | millisecond-class alignment is desirable |
| pulse-arrival-time research | tight, characterized millisecond/sub-millisecond error budget depending on claimed resolution |
| local IMU ↔ PPG artifact timing | sample-level alignment relative to the motion/PPG rates is desirable |

Do not engineer sub-millisecond multi-node sync unless a cross-modal feature actually needs it.

### 14.2 One-controller synchronization

This is the reason Blueprint A starts centralized.

Rules:

1. use one monotonic microsecond-scale clock;
2. timestamp **when data become available from the sensor/peripheral**, not when a host receives a packet;
3. for FIFO sensors, store the timestamp of a known edge/read and reconstruct earlier sample times using the configured sample period;
4. maintain independent sample sequence numbers;
5. record configuration changes as time-stamped events;
6. do not align by arrival order alone.

### 14.3 Dedicated AFE timestamping

For ADS1292R/MAX3000x:

```text
AFE conversion clock
   ↓
DRDY interrupt at known cadence
   ↓
MCU timestamp
   ↓
SPI burst read
```

This is stronger than polling an analog channel from a general task because the data-ready edge provides a concrete timing event.

### 14.4 Multi-node V1: shared hardware sync pulse

If two body nodes can tolerate a thin wire during research, the simplest high-confidence scheme is:

```text
master GPIO ───────────────┬──> node A interrupt/timestamp
                           └──> node B interrupt/timestamp
```

Emit a pulse:

- at session start;
- periodically during long recordings if practical;
- at session end.

Each node stores its **own local sample timestamps** plus the observed pulse timestamps. Post-processing fits clock offset and drift.

This is generally easier to validate with a logic analyzer than a purely wireless sync algorithm.

### 14.5 Multi-node wireless sync

If wiring is unacceptable:

- retain monotonic local clocks on every node;
- exchange periodic timestamp messages;
- estimate offset and drift, not only a one-time offset;
- reject high-latency/asymmetric exchanges where possible;
- store the raw sync exchanges so post-processing can be improved;
- never replace sensor timestamps with host receive timestamps.

### 14.6 Physiological/post-hoc alignment

Heartbeat-based or signal-feature-based alignment can be a useful diagnostic fallback, and the prior-art map already identifies published/patented examples. It should **not be the only synchronization mechanism** when the hardware can provide explicit timestamps.

### 14.7 RTC role

An external RTC is useful for wall-clock/session start time and long-term calendar alignment. It does **not** by itself solve sample-level synchronization. Therefore an RTC is H3/H4 for the first baseline, not a substitute for monotonic acquisition timing.

### 14.8 How to validate synchronization

Connect a logic analyzer/oscilloscope to:

- shared sync GPIO;
- ECG DRDY or ADC frame-ready debug pin;
- MAX30101 INT;
- IMU INT;
- optional “sample accepted” debug pin.

Measure:

- ISR/service latency distribution;
- maximum interval deviation;
- missed events;
- clock drift between nodes;
- drift before/after RF/SD stress.

---

## 15. Power / Isolation / Human-Connected Safety

This section is for **safe research prototyping**, not certification.

### 15.1 Default human-connected rule

> **When ECG or EDA electrodes are connected to a person, run the entire body-side prototype from battery, unplug USB, unplug charging, and avoid a mains-referenced oscilloscope/programmer connection.**

This is not merely conservative preference. Analog Devices’ MAXREFDES100 ECG guidance says isolation is not built into that platform and recommends a laptop running on battery when ECG is streamed by USB, while minimizing other connected equipment. ProtoCentral’s ADS1292R hardware documentation similarly advises disconnecting mains when electrodes are on the body. [S05, S27]

### 15.2 Safe prototype workflow

**Phase A — no human**

- bench supply or USB allowed;
- fixed resistors for EDA;
- ECG simulator/function source through appropriate safe test interface;
- optical target/finger substitute where useful;
- logic analyzer and oscilloscope freely connected.

**Phase B — first human body connection**

- battery-only body-side electronics;
- charging physically disconnected;
- local SD/flash logging;
- wireless preview only if needed;
- no mains-powered oscilloscope ground connected to the body-side circuit.

**Phase C — supervised instrumentation/debug**

If a wired USB/debug link is genuinely necessary, use a specifically engineered isolation path and independently verify its ratings/topology. Do not assume a cheap generic “USB isolator” makes the entire prototype medically safe.

### 15.3 USB isolation reference

Analog Devices’ ADuM4160 family and CN0419 reference design are examples of a purpose-built isolated USB data path. CN0419 publishes a complete isolated USB reference circuit with isolation components and design files. [S49, S50]

**Important boundary:** an isolated USB data IC/evaluation board is not equivalent to certifying the entire patient-connected device. Leakage paths can still exist through chargers, oscilloscopes, other cables, shields, programmers and electrode circuits.

### 15.4 Electrode-domain protection

Copy proven AFE input practices rather than inventing electrode protection:

- input current-limiting resistors;
- RF/EMI filters designed without destroying CMRR;
- appropriate RLD/body-bias networks;
- lead-off circuits;
- ESD/fault handling.

Analog Devices’ AD8232 evaluation design explicitly includes current-limiting input resistors and warns that they do not eliminate the need for isolation from the power line. [S25]

### 15.5 Charging while worn

For this research baseline: **do not charge while worn with electrodes attached**. A future certified/integrated product would need a deliberate power/charging safety architecture; that is outside the current prototype claim.

### 15.6 Battery hardware

A small protected Li-Po/other battery solution is justified, but the exact pack/charger should be selected after the total rail/current requirements and each breakout regulator are measured. The critical H1 requirement is not a particular battery brand; it is **electrically untethered body operation**.

---

## 16. Mechanical / Body-Interface Solutions

### 16.1 ECG interface

**Replicate first**

- disposable Ag/AgCl snap electrodes;
- documented three-electrode geometry;
- short, twisted/organized lead set;
- strain relief at the AFE enclosure;
- adhesive/strap that prevents the breakout mass from tugging on electrodes.

**Study:** SparkFun AD8232 hookup, AD8232 eval design, MAXREFDES100 ECG best practices. [S23–S25, S05]

### 16.2 PPG interface

The PPG fixture must control:

1. optical contact;
2. ambient light;
3. pressure;
4. relative motion.

**Minimum reproducible finger fixture**

- 3D-printed or foam cradle around PCB;
- dark/opaque optical gasket;
- adjustable hook-and-loop/elastic strap;
- compliant backing;
- witness marks so placement is repeated.

The fixture should be treated as part of the sensor, not as packaging.

### 16.3 EDA interface

For early experiments:

- same electrode material each session;
- same fingers/palmar sites;
- same electrode spacing/contact area;
- cable strain relief;
- avoid a heavy board hanging from electrode wires;
- log time since placement because hydration/contact equilibrate.

### 16.4 Temperature interface

Strong reference pattern:

- small skin-facing sensor island/flex;
- metal or thermally conductive interface;
- low thermal mass;
- insulation from processor/regulator/battery;
- mechanical pressure sufficient for repeatable contact but not vascular compression.

**Study first:** TI TIDA-060034 and MAXREFDES104/106 temperature subassemblies. [S37, S06, S07]

### 16.5 IMU interface

A loose IMU gives the movement of the board, not necessarily the body segment.

Use:

- rigid mount;
- documented axes;
- orientation marker;
- strap or adhesive that does not wobble;
- local placement if the purpose is artifact reference.

### 16.6 Recommended L2 physical arrangement

```text
CHEST
[Ag/AgCl ECG electrodes]
      │ short leads
[AD8232 or dedicated ECG AFE]
      │

HAND / WRIST POD
[MAX30101 + local IMU]
[EDA electrode connector]
[TMP117 thermal contact island]
      │

WAIST / ARM / HARNESS
[ESP32/logger + battery + microSD]
```

The exact boards do not need to fit inside one enclosure to prove the sensing architecture.

---

## 17. Validation Equipment and Methods

The acceptance principle is:

> **Every channel must first be verified electrically, then physiologically, and then under multimodal interference.**

### 17.1 ECG

**Validation references/equipment**

- ECG simulator / biomedical signal simulator;
- simultaneous Polar H10 or other raw-ECG chest reference for human rest recordings;
- oscilloscope only for non-human or properly isolated bench tests.

Polar’s official BLE SDK exposes H10 ECG samples at 130 Hz and RR/HR data, making it a practical independent reference for beat timing even though it is not a full diagnostic ECG. [S51]

**Tests**

1. simulator waveform at several heart rates;
2. measure clipping, baseline recovery and leads-off;
3. human rest recording against reference ECG;
4. posture/motion challenge;
5. RF off/on test.

**Pass for first baseline**

- clear repeatable QRS/R peaks at rest;
- no unexplained clipping;
- beat count/HR agrees closely with the reference over a controlled window;
- sample loss is zero or explicitly flagged;
- waveform degradation under RF/motion is measured, not hidden.

### 17.2 PPG

**Validation equipment**

- simultaneous ECG (best pulse timing reference);
- validated commercial pulse oximeter if SpO₂ is later studied;
- Shimmer/EmotiBit sample traces as waveform-quality references. [S14, S21]

**Tests**

- rest finger recording;
- ambient-light challenge;
- strap-pressure sweep;
- local motion challenge;
- compare optical pulse intervals with ECG R-R trends without claiming equivalence.

### 17.3 EDA

**Electrical validation**

- precision resistor set or resistance decade box;
- repeated readings across range;
- warm-up/power-cycle repeatability.

**Physiological validation**

- controlled rest;
- standardized brief arousal/stimulus protocol appropriate to the research setting;
- simultaneous Shimmer/BIOPAC/other research EDA if available.

For legacy tinyGSR, pass/fail concerns **relative monotonicity and repeatability**, not absolute µS.

### 17.4 Temperature

**Validation equipment**

- calibrated contact thermometer/reference probe;
- controlled temperature block/bath with the electronic board kept dry;
- second reference temperature sensor.

**Tests**

- room-temperature offset;
- warm-surface step response;
- skin co-location;
- ambient airflow step;
- controller/radio/PPG LED self-heating A/B.

**Pass**

A repeatable skin-contact trend with a characterized equilibration time and bias against the chosen reference.

### 17.5 IMU / tremor

**Validation equipment**

- gravity;
- rigid jig;
- known-frequency oscillation or shaker if available;
- video with timing marker;
- second reference IMU if available.

**Tests**

- six orientations;
- stationary noise;
- fixed-rate sinusoidal/periodic movement;
- known hand-tremor-like movement;
- FFT frequency recovery.

### 17.6 Timing

**Equipment**

- logic analyzer or oscilloscope;
- shared GPIO pulse source;
- optional signal generator fed into multiple acquisition paths.

**Tests**

- timestamped GPIO edges;
- ADC/AFE data-ready interval distribution;
- FIFO service latency;
- node-to-node drift over 30–60 min;
- SD stress;
- RF stress.

### 17.7 Validation table

| Channel | Electrical proof | Physiological proof | Interference proof |
|---|---|---|---|
| ECG | simulator waveform / ADC range | R peaks vs raw ECG reference | RF, cable, motion |
| PPG | register/FIFO + optical target | pulse peaks vs ECG | light, pressure, motion |
| EDA | resistor transfer | tonic/phasic relative response | motion, temp, electrode drying |
| temp | calibrated thermal reference | repeatable skin trend | self-heating, ambient |
| IMU | gravity / known motion | body movement/tremor task | mount looseness |
| system timing | shared pulses | ECG↔PPG beat alignment | RF/SD/load drift |

---

## 18. High-Value Open-Source Repositories

### 18.1 `EmotiBit/EmotiBit_FeatherWing`

- **URL:** [S12]
- **License:** MIT for the main firmware repository; inspect dependency-specific licenses.
- **Hardware files:** in separate EmotiBit Docs/hardware repositories.
- **Firmware:** yes.
- **Host software:** related EmotiBit repositories/tools.
- **Why study it:** direct ESP32 + MAX30101 + EDA + temperature + IMU precedent.
- **Exact parts:** `EmotiBit.cpp`, `EmotiBit.h`, buffer classes, stock firmware, platform configurations.
- **Replication confidence:** **R1**.

### 18.2 `EmotiBit/EmotiBit_Docs`

- **URL:** [S13]
- **Hardware:** schematics for multiple EmotiBit revisions.
- **Exact part:** `hardware_files/README.md` and versioned schematics; data docs.
- **Value:** board-version discipline and system wiring reference.
- **R1**.

### 18.3 `Protocentral/protocentral_healthypi_5`

- **URL:** [S08]
- **Hardware:** `hardware/` Eagle `.sch/.brd`, BOMs, schematic PDFs, enclosure STLs.
- **Firmware links:** current firmware and HealthyBridge are linked from README.
- **Exact part:** hardware revisions 5.3–5.7 and carrier/isolation architecture.
- **R1**.

### 18.4 HealthyPi 5 firmware / HealthyBridge

- **URLs:** [S09], [S10]
- **Firmware:** acquisition/broker/SD on RP2040; wireless on ESP32-C3.
- **Exact part to study:** sensor tutorials, full applications, board pin map, wireless protocol, watchdog/logging.
- **Value:** strongest reference for acquisition MCU separated from RF.
- **R1**.

### 18.5 `Protocentral/healthypi-move-hw`

- **URL:** [S18]
- **License:** repository currently exposes an MIT license file; product documentation also identifies open-hardware licensing for the platform—verify individual fabrication files before reuse.
- **Hardware:** separate `main board`, `sensor board`, `bottom board`, `finger sensor board`, plus `enclosure/`.
- **Value:** closest open physical partition to a full ECG+PPG+EDA+temp+motion wearable.
- **R1/R2**.

### 18.6 `Protocentral/healthypi-move-fw`

- **URL:** [S19]
- **Stack:** Zephyr / nRF Connect SDK.
- **Value:** modern embedded wearable architecture; local storage; sensor/health services.
- **Caveat:** active issues show the project is evolving. Pin behavior and branch/version must be pinned before reproduction.
- **R1/R2**.

### 18.7 `sparkfun/AD8232_Heart_Rate_Monitor`

- **URL:** [S23]
- **Hardware files:** `Hardware/` Eagle `.brd/.sch`, production files.
- **Firmware/examples:** `Software/`.
- **Value:** known AD8232 passive network and first waveform.
- **R1**.

### 18.8 `Protocentral/protocentral-ads1292r-arduino`

- **URL:** [S26]
- **Directories:** `examples/`, `src/`.
- **Hardware:** separate board repository [S27].
- **License:** MIT software; open hardware under ProtoCentral’s published hardware license.
- **Value:** DRDY/SPI 24-bit ECG reference.
- **R1**.

### 18.9 `sparkfun/SparkFun_MAX3010x_Sensor_Library`

- **URL:** [S29]
- **Directories:** `src/`, `examples/`.
- **Value:** raw FIFO/register bring-up and broad MAX3010x support.
- **Caveat:** confirm MAX30101-specific behavior and interrupt support in the exact branch; do not treat a family-generic example as validation of every sensor revision.
- **R1 for software reference / R2 for exact application**.

### 18.10 `sparkfun/SparkFun_Photodetector_Breakout_MAX30101_Qwiic`

- **URL:** [S30]
- **Hardware:** `Hardware/` schematic/board/production assets.
- **Value:** like-for-like open MAX30101 breakout if SmartElex electrical or mechanical uncertainty blocks progress.
- **R1**.

### 18.11 `Protocentral/protocentral_tinygsr`

- **URL:** [S34]
- **Directories:** `hardware/`, `firmware/`, `assets/`.
- **License:** CERN-OHL-P v2 hardware; MIT software; CC BY-SA documentation.
- **Value:** deterministic open EDA replacement.
- **R1**.

### 18.12 `sensors-inl/Nervous-EDA`

- **URL:** [S36]
- **Directories:** `hardware/`, `firmware/`, `web-app/`.
- **License:** MIT according to repository.
- **Releases:** manufacturing ECAD archive, firmware and application binaries.
- **Value:** high-end open EDA measurement and electrode-impedance architecture.
- **R1**.

### 18.13 `jrowberg/i2cdevlib`

- **URL:** [S41]
- **Exact path:** `Arduino/MPU6050/` and examples.
- **License:** MPU-6050 device code uses MIT.
- **Value:** mature raw, FIFO/DMP, calibration examples.
- **Caveat:** very old ecosystem with historical issue reports; use raw transparent paths and test on ESP32 rather than assuming examples are production-safe.
- **R2**.

### 18.14 `boschsensortec/BMI270_SensorAPI`

- **URL:** [S42]
- **Official vendor repository.**
- **Value:** modern replacement path with FIFO and official API.
- **R2**.

### 18.15 Other useful repositories

- BITalino organization and firmware: [S22].
- OpenEarable open ear-worn sensor platform: [S55].
- SensWear modular wearable: [S56].
- Hemo-Monitoring-Wearable: [S54].
- Polar BLE SDK for H10 validation: [S51].

---

## 19. Manufacturer Reference Designs

### 19.1 Analog Devices MAXREFDES100

**Use/reference this exact design because:** it proves MAX30101 can coexist with a dedicated ECG AFE, temperature sensors, multiple motion sensors, local logging, BLE and USB on one tested platform with full design assets. [S05]

### 19.2 Analog Devices MAXREFDES104

**Use/reference this design because:** it supplies the strongest open manufacturer wrist mechanics for combined ECG/PPG plus a separate skin-temperature flex/contact structure and local flash. [S06]

### 19.3 Analog Devices MAXREFDES106

**Use/reference this design because:** it is a body-patch architecture with ECG/PPG/BioZ, motion, local flash, BLE and dedicated skin/ambient temperature assemblies. [S07]

### 19.4 TI TIDA-060034

**Use/reference this design because:** it answers the exact TMP117 body-contact question with two TMP117s, a flex strip and complete design files. [S37]

### 19.5 Analog Devices AD8232 evaluation design

**Use/reference this design because:** it exposes the passive/filter/RLD/current-limiting choices hidden by the generic `AD8232 module` label. Compare the V502 board against it. [S25]

### 19.6 Espressif ESP-IDF ADC/timer drivers

These are not complete biomedical reference designs but are authoritative implementation references:

- ADC driver/calibration [S43]
- continuous ADC/DMA [S44]
- ADC2/Wi-Fi constraint [S45]
- high-resolution ESP Timer vs FreeRTOS software timers [S46]
- ADC noise guidance [S47]

### 19.7 USB isolation reference

- ADuM4160 product/evaluation material [S49]
- CN0419 isolated USB reference design [S50]

Use as an electrical architecture reference only; do not claim patient-safety compliance for a prototype built from it.

---

## 20. Academic Hardware Prototypes

### 20.1 EmotiBit validation

The 2024 Measurement: Sensors paper evaluates EmotiBit’s multimodal physiological signals against reference instrumentation and is one of the most directly useful academic validations because the hardware itself is open and close to the current non-ECG stack. [S15]

A later validation study is useful as a caution: derived PPG/HRV/EDA agreement can become context-dependent under workload. [S16]

**Project lesson:** validation must cover the exact feature and use condition, not only “the sensor produced a plausible-looking graph.”

### 20.2 Nervous EDA

The Nervous EDA project is linked to a peer-reviewed IEEE Sensors Journal implementation of continuous multifrequency skin-impedance spectroscopy. [S36]

**Project lesson:** if DC/relative GSR proves inadequate, there are open, scientifically documented alternatives with much richer contact/impedance information—but they carry substantially more complexity.

### 20.3 Local IMUs for physiological artifact

The repository’s prior research identifies peer-reviewed work attaching local IMUs to physiological electrodes for motion-artifact measurement/removal. [S58]

**Project lesson:** a local motion sensor is a credible engineering tool, but it must be co-located with the relevant mechanical interface and validated; it is not a magic correction signal.

### 20.4 Alcohol-withdrawal tremor studies

The repository’s evidence/prior-art documents identify accelerometer-based alcohol-withdrawal tremor studies including Norouzi et al. and earlier work. [S57]

**Project lesson:** the MPU-6050 experiment should reproduce the **measurement method first**—rigid hand/wrist accelerometry + frequency-domain output—without claiming that tremor alone diagnoses withdrawal.

### 20.5 Open ECG + PPG wearable hardware

The Hemo-Monitoring-Wearable repository is useful as a reproducible academic PCB reference combining ECG, PPG and another electrical modality with BLE and public board files. [S54]

---

## 21. Commercial / Research Platforms

These systems are architecture/validation references. Their undocumented internal details are **not** inferred.

### 21.1 Shimmer3R

Relevant products include GSR+ and ECG/EMG units. Public documentation describes:

- EDA/GSR;
- optical pulse;
- IMU;
- microSD;
- battery;
- Bluetooth;
- sample recordings;
- ECG platforms based on dedicated AFEs in relevant units. [S20, S21, S52]

**Value:** strong research reference for raw-signal collection, electrodes, placement and multi-device workflows.

### 21.2 Empatica EmbracePlus / research platform

The repository already used Empatica as a physical-integration reference for PPG + EDA + temperature + motion on the wrist. [S60]

**Value:** demonstrates that this quartet can coexist in a professional wrist device.  
**Cannot infer:** optical layout, EDA electrode circuit, proprietary algorithms, or clinical accuracy.

### 21.3 Polar H10

Polar’s official SDK exposes raw ECG at 130 Hz, HR/RR and accelerometer data. [S51]

**Value:** inexpensive independent beat-timing/HR reference for chest recordings.  
**Limit:** not a substitute for a laboratory ECG simulator or clinical ECG when validating morphology/amplitude.

### 21.4 BITalino

Useful as a modular low-cost research/teaching system with open portions of hardware/firmware. [S22]

### 21.5 HealthyPi Move

Although open-source, it is also a commercial research/developer reference: a working wearable with nearly the full target modality set. [S17]

### 21.6 Professional version of this class of system

The common professional design pattern is:

```text
skin/electrode/optical interface
   ↓
dedicated sensor/AFE close to body
   ↓
digital conversion at the sensor/front end
   ↓
interrupt/FIFO based acquisition
   ↓
local monotonic timestamps
   ↓
buffer
   ├─ local flash/SD (authoritative)
   └─ BLE/USB/Wi-Fi (transport/preview)
   ↓
host software with raw-data export
```

The present prototype should converge toward this pattern rather than a single blocking Arduino loop that sends every reading directly to a laptop.


## 22. Candidate Additional / Replacement Hardware

The table intentionally distinguishes **required-to-build safely/reliably** from “nice newer component.”

| Component | Problem it solves | Existing component affected | Evidence/reference implementation | Benefit | Added complexity | Confidence | Required now or later |
|---|---|---|---|---|---|---|---|
| **Local microSD / nonvolatile logger** | wireless/USB loss; need battery-only body recording | ESP32 system | EmotiBit, HealthyPi 5, HSP platforms all log locally [S05,S08,S12] | authoritative raw copy; no RF dependency | SPI + filesystem + write buffering | **H1** | **Now**, before serious body sessions |
| **Battery-only body-side power arrangement** | mains/leakage path during ECG/EDA use | whole system | MAXREFDES100 + ProtoCentral ECG safety guidance [S05,S27] | safer research workflow; lower mains noise | charging/power management discipline | **H1** | **Now** |
| **Disposable Ag/AgCl ECG electrodes + proper snap cable** | unstable electrical contact; cable motion | AD8232/ECG | SparkFun/ADI/ProtoCentral ECG systems [S23–S27] | repeatable electrode interface | consumables | **H1** | **Now** |
| **Standardized EDA electrodes/contact fixture** | unknown/variable skin contact | tinyGSR | ProtoCentral, Shimmer [S33,S20] | repeatability | fixture/protocol | **H1** | **Now** |
| **Opaque pressure-controlled PPG finger fixture** | ambient light, pressure, motion | SmartElex MAX30101 | MAXREFDES/EmotiBit/professional PPG practice [S05,S12] | makes raw PPG reproducible | simple mechanical work | **H1** | **Now** |
| **TMP117 skin-contact thermal fixture** | die may measure board/ambient heat | SmartElex TMP117 | TI TIDA-060034; MAXREFDES104/106 [S37,S06,S07] | repeatable thermal path | mechanical iteration | **H1** | **Now for body temp experiments** |
| **ProtoCentral ADS1292R breakout** | ESP32 ADC noise/reference/linearity; explicit sample timing | AD8232+ESP32 ADC | open HW/SW/DRDY/SPI [S26,S27] | 24-bit dedicated ECG conversion; deterministic timing | extra SPI board/firmware | **H2** | Benchmark after AD8232 first-light |
| **ProtoCentral MAX30001/MAX30003 breakout** | same ECG bottleneck, wearable-oriented AFE | AD8232+ESP32 ADC | ProtoCentral + MAXREFDES100 [S28,S05] | integrated ECG ADC/R-R/interrupts; compact | extra board/firmware | **H2** | Alternative to ADS1292R |
| **Modern tinyGSR v3** | legacy board cannot supply reproducible absolute conductance; unknown trimmer transfer | legacy tinyGSR | open deterministic hardware/software [S33,S34] | reproducible/calibratable EDA | purchase + re-baseline | **H2** | When quantitative/cross-session EDA is required |
| **SparkFun MAX30101 Qwiic breakout** | SmartElex schematic/address/optical PCB uncertainty | SmartElex MAX30101 | open schematic/PCB/library [S30] | known electrical design | replacement/fixture change | **H2** | Only if SmartElex fails verification |
| **Second/local IMU** | central IMU does not represent PPG/ECG interface motion | MPU-6050 topology | local-IMU research; multimodal refs [S58,S05] | channel-local artifact labels | extra I²C/address/mount | **H3** | Add for artifact experiment, not by default |
| **BMI270 breakout** | deeper FIFO, modern availability/power/timing | MPU-6050 | Bosch official API [S42] | modern motion acquisition | new driver/calibration | **H3** | Later if current IMU timing/availability fails |
| **RP2040 acquisition MCU + ESP32-C3 RF split** | RF/task coupling; acquisition determinism | one ESP32 | HealthyPi 5 [S08–S11] | isolates sensing from networking | second MCU/protocol | **H3 initially; H2 if measured issue** | Escalation |
| **USB digital isolation based on a vetted design** | wired debug path while body-connected | USB/mains path | ADuM4160/CN0419 [S49,S50] | safer isolated data path | power/isolation design; still not certification | **H2** | Only if live wired debug is necessary |
| **I²C mux (e.g. TCA9548A-class)** | true address collision | I²C devices | standard bus solution | isolates addresses/branches | another IC; sync/scheduling | **H4** | Do not buy unless conflict exists |
| **I²C buffer/extender** | excessive cable capacitance | body-spanning I²C | standard bus solution | longer wiring | more parts/topology | **H4** | Prefer local nodes first |
| **Generic precision external ADC for AD8232** | ESP32 ADC quality | AD8232 | common instrumentation pattern | preserves current AFE | extra ADC; fewer ECG-specific benefits | **H3** | Benchmark only if preserving AD8232 matters |
| **External RTC** | wall-clock/calendar time | system clock | general embedded pattern | absolute date/time | extra part | **H4 for sync** | Later; does not solve sample timing |
| **Large custom PCB** | size/integration | all breakouts | every reference platform | cleaner final device | highest design risk | **H4 now** | After signal baseline |

### 22.1 What should actually be bought/assembled first

The smallest evidence-backed procurement/fixture increment is:

1. a 3.3 V-compatible microSD logging path;
2. protected battery power appropriate to the verified board rails;
3. known ECG snap electrodes/cable;
4. EDA electrodes;
5. simple PPG light/pressure fixture materials;
6. simple TMP117 skin-contact/thermal-isolation fixture materials;
7. a logic analyzer if the lab does not already have one;
8. an ECG simulator or access to one if serious ECG validation is planned.

Do **not** initially buy:

- a second ESP32;
- an I²C mux;
- multiple new IMUs;
- an RTC;
- an exotic EDA spectroscopy board;
- a custom PCB.

Those become justified only by measured evidence.

---

## 23. Replication Blueprint A — Minimum Viable Synchronized Bench System

### Goal

Get **all current sensors producing timestamped raw data together** with the least new signal-chain hardware.

### 23.1 Block diagram

```text
                         ┌────────────────────────────┐
Ag/AgCl electrodes ─────>│ CJMCU-8232 AD8232 (V502) │
                         └──────────┬─────────────────┘
                                    │ analog VOUT
                                    v
                              ESP32 ADC1
                                    │
SmartElex MAX30101 (finger fixture) ─ I²C/FIFO┤
PC-tinyGSR 12/22 ───────────── I²C ───────────┤
SmartElex TMP117 ───────────── I²C ───────────┤
GY-521 MPU-6050 ────────────── I²C ───────────┤
                                    │
                             ESP32 DEVKITV1
                       acquisition + timestamps
                          │                 │
                    SPI microSD       BLE/Wi-Fi
                    primary log       preview only
```

### 23.2 Components

**Existing**

- ESP32 DEVKITV1, 30-pin, ESP-WROOM-32-family controller board.
- CJMCU-8232 AD8232 single-lead ECG/heart-monitor module, PCB marking V502.
- SmartElex MAX30101 PPG/Photodetector breakout board.
- ProtoCentral PC-tinyGSR legacy EDA/GSR board, PCB marking 12/22.
- SmartElex TMP117 digital temperature sensor breakout board.
- GY-521 MPU-6050 6-axis accelerometer + gyroscope IMU module.

**Add**

- microSD logger;
- battery-only supply for body tests;
- electrodes/cables;
- PPG fixture;
- temperature fixture;
- mechanical mounting/strain relief.

### 23.3 Proposed acquisition settings

These are **starting engineering settings**, not medical standards:

| Channel | Starting rate | Acquisition method |
|---|---:|---|
| ECG | 250–500 SPS | ESP-IDF ADC continuous on ADC1 |
| PPG | 100 SPS, three wavelengths if stable | MAX30101 FIFO |
| EDA | 16–32 SPS | TLA2022/board API, raw/relative |
| temperature | 1–4 SPS | TMP117 I²C |
| IMU | 100–200 SPS | raw accel+gyro, FIFO/INT if stable |

### 23.4 Body placement for first human session

- ECG: chest/torso documented three-electrode placement.
- PPG: index/finger pad in controlled fixture.
- EDA: palmar/distal index + middle fingers, preferably opposite hand if PPG fixture interferes.
- TMP117: forearm/wrist skin-contact fixture away from main electronics.
- IMU: on the PPG/hand fixture for the first artifact/tremor experiment.

### 23.5 Synchronization

One ESP32 monotonic timestamp domain. ECG conversion is peripheral-timed; FIFO batches retain sample cadence. Low-rate sensor reads are timestamped locally.

### 23.6 Communication/storage

- microSD = authoritative data;
- serial only during non-body bench development;
- BLE/Wi-Fi preview after acquisition works without it;
- record overflow/error counters.

### 23.7 Power

- bench electrical tests: USB acceptable with no person;
- body-connected: battery-only, USB/charger unplugged.

### 23.8 Directly adapted from

- EmotiBit: ESP32 + multiple sensor buffers + SD + wireless separation [S12–S14].
- MAXREFDES100: MAX30101 coexistence, interrupt-driven acquisition, local logging, ECG wiring/noise practice [S05].
- SparkFun AD8232: first waveform and electrode topology [S23,S24].
- Espressif: continuous ADC, ADC1/Wi-Fi and timing guidance [S43–S46].

### 23.9 Expected strengths

- fastest route to a five-modality file;
- one timebase;
- no premature component replacement;
- reveals the actual weak links.

### 23.10 Expected problems

- ECG ADC noise and V502 filter uncertainty;
- EDA only relative;
- PPG fixture sensitivity;
- TMP117 thermal lag/bias;
- wiring burden;
- SD write stalls if not buffered.

### 23.11 Replication confidence

**R2 overall.** Individual IC paths are highly documented, but the exact generic breakouts and combined wiring are not.

### 23.12 Blueprint A pass criteria

A 30-minute bench/dummy-source run and then a short controlled human run must produce:

- complete files that parse;
- no silent sample loss;
- explicit error/overflow flags;
- stable raw ECG with R peaks at rest;
- repeatable pulse waveform at the finger;
- monotonic resistor response and a physiological relative EDA trace;
- repeatable skin-contact temperature trend;
- calibrated raw IMU;
- one merged timeline.

---

## 24. Replication Blueprint B — Strong Controlled Body-Worn Research System

### Goal

Improve the largest signal-quality uncertainty while retaining most current hardware.

### 24.1 Key change

Replace **AD8232 → ESP32 ADC** with a dedicated ECG AFE/ADC.

Preferred first benchmark:

- **ProtoCentral ADS1292R**, because hardware/software are open, it exposes DRDY and SPI, and it is explicitly designed for ECG/respiration.
- **MAX30001/MAX30003** is an equally credible wearable-oriented alternative.

### 24.2 Block diagram

```text
CHEST
Ag/AgCl electrodes
    │
ADS1292R or MAX30001/3
    │ SPI + DRDY
    └───────────────┐

HAND/WRIST
SmartElex MAX30101 + GY-521 MPU-6050 ─┼─ I²C
PC-tinyGSR 12/22 ─────────────────────┤
SmartElex TMP117 thermal pod ─────────┤
                    v
             ESP32 controller
         high-res local timestamps
           │               │
        microSD         BLE preview
           │
         battery
```

### 24.3 Why this is a better research baseline

The ADC uncertainty is removed from ECG while the project continues testing the current PPG/EDA/temp/IMU boards. This is a clean experiment: only one important signal chain changes.

### 24.4 ECG benchmark protocol

Record simultaneous conditions with:

1. current AD8232 + ESP32 ADC;
2. dedicated AFE path;
3. same subject/electrodes/reference where possible.

Compare:

- baseline noise;
- 50 Hz contamination;
- clipping/recovery;
- R-peak detection yield;
- RR timing consistency;
- motion sensitivity;
- RF on/off effect.

If AD8232 performs equivalently for the project’s intended outputs, it can remain. If not, the dedicated AFE becomes H1 for the next stage.

### 24.5 Physical design

- chest AFE physically close to electrodes;
- digital connection back to controller;
- finger PPG for controlled sessions;
- finger/palmar EDA;
- TMP117 on a thermally isolated small fixture;
- local IMU at PPG/hand;
- main ESP32/battery/logger on waist/arm where it does not mechanically load sensors.

### 24.6 Replication confidence

**R2 to R1/R2**, because the most sensitive electrical channel now follows a dedicated open AFE path.

---

## 25. Replication Blueprint C — Higher-Confidence Distributed System

### Goal

Digitize near the body interface, minimize vulnerable analog/bus wiring, and retain measurable synchronization.

### 25.1 Topology

```text
CHEST NODE
  ECG electrodes
      ↓
  MAX30001/ADS1292R
      ↓
  local MCU
      ├─ local flash
      └─ sync I/O / digital link

PERIPHERAL NODE
  MAX30101 + local IMU
  quantitative tinyGSR v3 (or validated legacy board)
  TMP117 thermal island
      ↓
  local MCU
      ├─ local flash
      └─ sync I/O / BLE

              ↓
      session merge tool
    clock-offset + drift model
```

### 25.2 Node MCU choice

Do not default to “two ESP32s.” Suitable implementations include:

- ESP32 on both nodes for code reuse;
- RP2040 acquisition node + ESP32-C3 radio hub following HealthyPi 5;
- nRF52/nRF53-class BLE nodes if low-power wireless becomes the main constraint.

The MCU choice is less important than preserving local sample clocks and data.

### 25.3 Synchronization choice

**Research-first option:** a shared GPIO sync pulse between nodes even if data transfer is wireless. This isolates the synchronization problem from RF packet latency.

For untethered later work:

- periodic clock exchanges;
- offset + drift fit;
- optional physiological cross-check;
- local storage on each node.

### 25.4 Power

Each body node is independently battery powered. Charging occurs off-body.

### 25.5 When Blueprint C is justified

Only after Blueprint B shows at least one of:

- chest analog path cannot be kept clean centrally;
- peripheral I²C harness is mechanically/electrically poor;
- local movement differs enough that multiple motion references are valuable;
- user burden is lower with modular nodes;
- research requires true site-local data quality.

### 25.6 Expected strengths

- shorter analog path;
- local sensor mechanics;
- independent fault isolation;
- scalable placement.

### 25.7 Expected problems

- clock drift;
- multiple batteries;
- start/stop coordination;
- duplicated storage;
- data merge complexity;
- RF coexistence.

### 25.8 Replication confidence

**R2.** Every pattern is proven separately, but the exact project-specific distributed combination still needs integration work.

---

## 26. Replication Blueprint D — Best Evidence-Backed System if Current Hardware Limitations Are Ignored

### Goal

Answer: **what would be built if the sole optimization target were implementation confidence, signal quality and reproducibility rather than preserving the current BOM?**

### 26.1 Architecture

```text
                      CONTROLLED BODY INTERFACES
      ┌─────────────────┬────────────────┬──────────────────┐
      │                 │                │                  │
chest ECG           distal PPG       palm/finger EDA   skin temp flex
      │                 │                │                  │
MAX30001/          MAX30101/          tinyGSR v3 /      TMP117 flex
MAX30003 or        MAX8614x-class     validated open     dual-sensor
ADS1292R           optical AFE        quantitative AFE   thermal design
      │                 │                │                  │
      └────── digital/FIFO/DRDY ────────┴──────────────────┤
                                                           │
                                                   modern FIFO IMU(s)
                                                           │
                                                           v
                                                acquisition MCU
                                         (RP2040/nRF53/MAX32666 class)
                                                  │           │
                                          flash/microSD     watchdog
                                                  │
                                           framed digital link
                                                  │
                                                RF MCU
                                             BLE / Wi-Fi
                                                  │
                                                 host
```

### 26.2 Evidence basis

This architecture deliberately combines patterns from:

- MAXREFDES100: complete multisensor reference and MAX30101 + ECG AFE [S05];
- MAXREFDES104/106: integrated wearable/patch, local flash, temperature flex/contact [S06,S07];
- HealthyPi 5: acquisition MCU separated from ESP32 radio [S08–S11];
- HealthyPi Move: complete modality set, local flash and physical board partition [S17–S19];
- tinyGSR v3/Nervous EDA: reproducible EDA [S34,S36];
- Bosch BMI270: modern FIFO motion path [S42].

### 26.3 Preferred hardware

| Function | Preferred evidence-backed path |
|---|---|
| ECG | MAX30001/MAX30003 or ADS1292R |
| PPG | MAX30101 if raw research continuity desired; newer MAX8614x/MAX86176 class if redesigning optics |
| EDA | modern tinyGSR v3 topology or another calibrated open EDA AFE |
| temperature | TMP117 flex/dual-sensor architecture patterned on TIDA-060034 |
| IMU | BMI270/BMI323/ADXL-class modern FIFO IMU appropriate to placement |
| acquisition | dedicated MCU that owns all sample clocks and local storage |
| radio | separate BLE/Wi-Fi processor if Wi-Fi is required |
| storage | raw local flash/microSD |
| power | battery + controlled PMIC; no body-connected charging |
| synchronization | shared acquisition clock where co-located; local clocks + explicit sync protocol where distributed |

### 26.4 Why not simply copy HealthyPi Move?

HealthyPi Move is close, but the project should still control:

- raw signal access;
- exact sampling and timing;
- body-site choices;
- EDA electrode geometry;
- temperature thermal path;
- whether ECG is continuous chest ECG or an interaction-based wrist ECG;
- whether PPG is finger, wrist or both.

The strongest design is therefore a **reference-derived research instrument**, not a clone of a commercial wearable.

### 26.5 Replication confidence

**R2/R1 at subsystem level; R2 overall.**

---

## 27. Recommended Replication Path

The sequence below is deliberately designed to prevent “integration theater,” where all sensors appear in one UI before any channel is known to be trustworthy.

### Stage 0 — Freeze and reconcile the physical board inventory

**Status:** **Physical identification substantially complete.** The direct photographic observations are recorded in the [canonical hardware inventory](hardware_inventory.md), which is the source of truth for the current physical BOM.

**Verified current baseline**

- `ESP32 DEVKITV1`, 30-pin, ESP-WROOM-32-family controller board, with Micro-USB, EN/BOOT buttons, and visible standard header labels.
- ProtoCentral `PC-tinyGSR` legacy EDA/GSR board, PCB marking `12/22`, with a physical `BASELINE` trimmer.
- `CJMCU-8232` AD8232 single-lead ECG/heart-monitor module, PCB marking `V502`.
- SmartElex MAX30101 Photodetector board with physically exposed `INT`; its `ADR: 0x52` text remains a silkscreen observation rather than a verified 7-bit address.
- SmartElex TMP117 board with exposed `INT`, address-selection markings for `0x48`–`0x4B`, and a narrowed/cut-out central sensor region.
- GY-521 MPU-6050 6-axis accelerometer + gyroscope IMU module with the photographed connector pin set.

The ESP32 and all five sensor boards also have prior basic working status. This confirms previous basic operation, not signal quality, timing, calibration, or validation.

**Remaining on-demand Stage-0 characterization**

The inventory resolves the board identities and visible features but not every electrical property. Before the corresponding signal-validation stage, characterize only what that stage needs: exact ESP32 carrier/USB-UART/regulator/ADC behavior; PC-tinyGSR ADC/interface, address, trimmer transfer and calibration; V502 passive/filter/gain/RLD network; MAX30101 power/address convention/interrupt behavior and optical implementation; TMP117 selected address/thermal path; and GY-521 regulator/pull-ups/silicon status/performance.

**Actions**

- preserve the inventory photographs, silkscreen/IC markings, and verified connector maps as the identity baseline;
- measure supply/logic levels and identify onboard regulators/level shifters where needed for the next signal path;
- run an I²C scan and record pull-up resistance before integration;
- use exposed interrupt pins in the relevant signal-validation stages;
- read/measure the V502 AD8232 passive values and characterize the PC-tinyGSR transfer behavior when those stages require it.

**Expected output**

`hardware_inventory.md` remains the canonical physical inventory; targeted electrical notes or a machine-readable board/config table may be added as on-demand characterization outputs.

**Pass**

Every wire in later stages can be traced to a verified board, pin, and voltage, while unresolved electrical properties remain explicitly labeled as unresolved.

**Likely remaining failures**

board-specific regulator/pull-up behavior; a physical address differing from a silkscreen or listing convention; uncharacterized passive networks; optical/thermal mechanics; clone-compatible silicon; and measured signal-quality or timing limits.

---

### Stage 1A — Prove AD8232 independently

**Reference**

SparkFun AD8232 + Analog Devices eval design. [S23–S25]

**Hardware**

V502 board, ECG simulator or battery-only human setup, ESP32.

**Software**

Minimal ESP-IDF ADC-continuous logger; no Wi-Fi.

**Output**

raw ADC codes/voltage, leads-off state if available.

**Pass**

stable baseline and repeatable QRS/R peaks at rest or correct simulator waveform; no clipping.

**Failure modes**

wrong electrode mapping, board gain/filter mismatch, ADC pin on ADC2, noise, mains coupling.

---

### Stage 1B — Prove MAX30101 independently

**Reference**

MAXREFDES100 + SparkFun MAX30101 + EmotiBit. [S05,S29,S30,S12]

**Hardware**

SmartElex board, finger fixture, ESP32.

**Software**

read part/register identity; raw FIFO logger.

**Output**

red/IR/green raw traces plus register configuration.

**Pass**

clear periodic pulse waveform at rest for at least several minutes without FIFO overflow.

**Failure modes**

ambient light, poor pressure, wrong LED settings, board voltage issue, polling too slowly.

---

### Stage 1C — Prove ProtoCentral PC-tinyGSR 12/22 independently

**Reference**

ProtoCentral tinyGSR docs/library and TLA2022 driver, applied to the verified PC-tinyGSR 12/22 board. [S33–S35]

**Hardware**

ProtoCentral PC-tinyGSR 12/22, precision resistors/decade box, later two electrodes.

**Output**

raw counts vs resistance; relative human EDA trace.

**Pass**

monotonic and repeatable electrical response; no unexplained saturation/drift; visible physiological modulation.

**Failure modes**

unknown trimmer position, bad electrodes, cable motion, address/config mismatch.

---

### Stage 1D — Prove TMP117 independently

**Reference**

TI TIDA-060034 + open TMP117 libraries. [S37,S38]

**Hardware**

SmartElex TMP117 breakout board, calibrated thermometer/reference, simple thermal fixture.

**Output**

temperature vs time under room/warm-surface/skin-contact steps.

**Pass**

repeatable trend, stable register reads, characterized lag/bias.

**Failure modes**

board self-heating, poor contact, airflow, main electronics too close.

---

### Stage 1E — Prove MPU-6050 independently

**Reference**

i2cdevlib raw/calibration examples. [S41]

**Hardware**

GY-521 MPU-6050 module, rigid mount.

**Output**

raw calibrated accel/gyro at measured sample cadence.

**Pass**

correct gravity orientation; stationary noise characterized; known periodic motion recovered in FFT.

**Failure modes**

clone/bad sensor, loose mounting, bus timing, FIFO overflow, incorrect scale factors.

---

### Stage 2 — Validate each modality against a reference

**Hardware**

ECG simulator/Polar H10; reference pulse; resistance box/research EDA if available; calibrated thermometer; motion jig/video; logic analyzer.

**Output**

per-modality validation report with plots and error/failure examples.

**Pass**

each channel has at least one independent electrical or physical truth source.

**Failure modes**

comparison clocks not aligned; reference itself not raw/validated; comparing derived metrics rather than raw signals.

---

### Stage 3 — Build one-controller raw recorder

**Reference**

EmotiBit + Espressif continuous ADC. [S12–S14,S44]

**Hardware**

all sensors + microSD + battery.

**Software**

per-sensor producer queues, logger task, session header, sequence/error counters.

**Output**

single session directory containing all raw channels.

**Pass**

30–60 min bench run with expected sample counts and no silent loss.

**Failure modes**

SD stalls, I²C contention, dynamic memory fragmentation, task starvation.

---

### Stage 4 — Timing and noise stress test

**Test matrix**

- Wi-Fi off/on;
- BLE off/on;
- SD off/on;
- USB powered vs battery on non-human test load;
- PPG LEDs off/on;
- stationary vs cable motion.

**Output**

jitter, overflow, spectral-noise and sample-loss report.

**Pass**

the effect of each subsystem on ECG/EDA/PPG is quantified.

**Failure modes**

unrepeatable setup; host timestamps mistaken for acquisition timestamps.

---

### Stage 5 — Move each sensor to a body-valid fixture

**Hardware**

chest ECG mount, finger PPG fixture, standardized EDA contacts, TMP117 thermal fixture, rigid IMU mount.

**Output**

controlled resting human recording.

**Pass**

all channels remain usable when physically worn and the fixtures do not mechanically corrupt each other.

**Failure modes**

cable tug, optical pressure, electrode drying, temperature self-heating, motion of loose boards.

---

### Stage 6 — Upgrade only measured bottlenecks

Decision rules:

- **ECG:** if AD8232+ESP32 ADC has excess noise/timing/clipping relative to a dedicated AFE, move to ADS1292R/MAX30001.
- **EDA:** if relative legacy output is inadequate for cross-session quantitative analysis, move to tinyGSR v3.
- **PPG:** if SmartElex breakout cannot be mechanically/electrically stabilized, move to an open MAX30101 board.
- **IMU:** if FIFO/timing/availability is limiting, move to BMI270-class.
- **bus/topology:** if body-spanning wiring is poor, split nodes.
- **RF:** if acquisition quality degrades under RF despite scheduling/power work, separate acquisition and wireless MCUs.

---

### Stage 7 — Build the first synchronized multimodal human recording

**Protocol**

- seated rest;
- controlled deep breaths / benign standardized arousal protocol as ethically appropriate;
- short posture change;
- small controlled hand motion;
- brief tremor-like movement task;
- annotate all events.

**Output**

one plot with native-resolution channels aligned to a shared time axis plus event markers.

**Pass**

the artifact seen in one channel can be explained using raw signal + motion/contact/timing context rather than guessed.

---

### Stage 8 — Produce the first interpretable features

Only after raw acceptance:

- ECG R peaks, HR, RR;
- PPG pulse peaks and pulse rate;
- EDA tonic/phasic relative measures;
- temperature trend;
- IMU magnitude and spectrum;
- signal-quality flags.

Do not build an AUD prediction model at this stage.

---

### Stage 9 — Decide central vs distributed using data

Compare:

- usable-data yield;
- timing error;
- noise;
- donning burden;
- cable failures;
- battery/runtime;
- firmware complexity.

Choose distribution only if it wins on a measured requirement.

---

## 28. Expected First Working Outputs

### 28.1 ECG

```text
raw conditioned waveform
        ↓
R-peak markers
        ↓
heart rate + RR intervals
        ↓
HRV only after RR/timing quality is validated
```

**First proof:** reproducible R peaks at rest.

**Do not accept as proof:** only a BPM number with no raw waveform.

### 28.2 PPG

```text
raw red / IR / green streams
        ↓
pulse peaks
        ↓
pulse rate
        ↓
PRV / morphology / perfusion analyses later
```

**First proof:** periodic pulse morphology whose rate agrees with ECG/reference at rest.

### 28.3 EDA

```text
legacy raw counts / relative conductance signal
        ↓
stable tonic baseline
        +
visible phasic changes
```

**First proof:** known resistors produce monotonic output and human recording shows plausible slow/phasic changes.

### 28.4 Temperature

```text
TMP117 raw °C
   ↓
contact step response
   ↓
repeatable skin-contact trend
```

**First proof:** known thermal input/reference comparison and repeatable on-skin equilibration.

### 28.5 IMU

```text
raw accel xyz + gyro xyz
      ↓
calibrated axes
      ↓
movement magnitude
      ↓
PSD / tremor-band energy
```

**First proof:** known mechanical frequency appears at the correct spectral frequency.

### 28.6 System

The first complete recording should look like a **research data package**, not one giant mixed serial string:

```text
session_YYYYMMDD_HHMMSS/
  session.json
  ecg.bin/csv
  ppg.bin/csv
  eda.bin/csv
  temperature.csv
  imu.bin/csv
  events.csv
  sync.csv
  errors.csv
```

`session.json` should record:

- exact board IDs/revisions/photos;
- firmware Git commit;
- sampling configuration;
- sensor registers;
- electrode/fixture placement;
- battery state;
- RF state;
- participant/session pseudonymous ID if human data are collected under an approved protocol;
- start wall-clock and monotonic base;
- calibration/reference devices.

### 28.7 Proposed V1 engineering acceptance targets

These are **project targets for falsifiable testing**, not medical-device performance claims.

| Metric | Initial target |
|---|---|
| silent sample loss | **0**; any loss must be flagged |
| 30–60 min bench continuity | complete parseable recording |
| ECG | visible stable R peaks at rest; HR/beat count closely matches independent reference |
| PPG | stable finger pulse waveform; pulse rate closely matches ECG at rest |
| EDA | monotonic fixed-resistance response + repeatable relative physiological trend |
| temperature | repeatable contact response and characterized bias/lag against reference |
| IMU | correct scale/orientation + known-frequency recovery |
| general multimodal alignment | event/motion changes appear in correct temporal order |
| ECG↔PPG experiment | characterize millisecond-scale alignment before PAT-like analysis |
| overflow/error metadata | present and tested by intentionally stressing buffers |

A later validation plan can define numeric error limits based on the exact research question and reference instrument.

---

## 29. Major Known Failure Modes and Existing Solutions

| Failure mode | What it looks like | Existing solution pattern | V1 action |
|---|---|---|---|
| mains-coupled ECG noise | strong 50 Hz / unstable baseline | battery operation, short leads, body bias/RLD, shielding [S05,S25] | battery-only human session; shorten leads |
| ESP32 ADC variability | baseline/scale variation, noise | calibration + ADC1 + dedicated AFE benchmark [S43–S47] | use ADC continuous; compare ADS1292R |
| ADC2/Wi-Fi conflict | failed/invalid reads with Wi-Fi | Espressif: use ADC1 [S45] | reserve ADC1 for ECG |
| FreeRTOS timer jitter | nonuniform sample spacing | sensor clocks, DMA/DRDY, ESP Timer [S44,S46] | do not time ECG with low-priority software timer |
| ADC buffer overflow | missing ECG frames | explicit continuous-driver pool handling [S44] | count overflow; drain in high-priority task |
| PPG FIFO overflow | discontinuities / repeated samples | FIFO interrupt/service + bounded queue | expose FIFO overflow counter |
| PPG ambient light | saturated/DC-shift trace | opaque optical shroud | controlled finger fixture |
| PPG pressure change | amplitude/morphology changes | fixed compliant pressure | adjustable repeatable strap |
| PPG motion | pulse-like artifacts | local IMU + signal-quality labeling | co-locate IMU and retain raw motion |
| EDA absolute ambiguity | arbitrary between-unit scale | deterministic calibrated AFE | keep legacy relative; tinyGSR v3 if needed |
| EDA electrode motion | abrupt conductance jumps | strain relief + local motion context | secure cables; mark artifact |
| temperature self-heating | gradual rise when electronics active | separate flex/island + ambient reference [S37,S06] | move TMP117 away from main board |
| loose IMU | false vibration/tremor | rigid mounting | mount mechanically before FFT |
| I²C address conflict | one sensor disappears | configurable address/mux | inventory/strap first; mux only if real |
| excessive I²C pull-ups | distorted edges/high current | inspect/disable redundant pull-ups | measure effective resistance |
| long I²C wiring | NACKs, edge distortion | local buses/local nodes | keep short; split later |
| SD write blocking | gaps in acquisition | double/ring buffers [S12] | dedicated logger task; preallocated buffers |
| Wi-Fi blocking/latency | dropouts/jitter | local logging; RF co-processor [S08,S12] | wireless secondary |
| host timestamps | apparent variable timing | local acquisition timestamps | never use receive time as sample time |
| distributed clock drift | channels slowly misalign | shared pulse + offset/drift fit | periodic sync events |
| unversioned firmware | irreproducible sessions | config + Git hash metadata | store hash in every session |
| unknown clone board | internet wiring “works sometimes” | exact inventory and schematic comparison | Stage 0 mandatory |
| deriving HRV from poor PPG | plausible but invalid feature | validate beat intervals vs ECG [S16] | ECG is primary timing reference |
| interpreting relative EDA as µS | false quantitative precision | fixed-resistor calibration / new AFE | label units honestly |
| charging while worn | hazardous leakage path | off-body charging | prohibit in protocol |

### 29.1 Failure-injection tests worth doing deliberately

A good logger should survive or report known bad conditions. Intentionally create:

- PPG light leak;
- one ECG lead detached;
- full/near-full PPG FIFO by delaying service in a test build;
- SD write stress;
- I²C sensor unplug/replug on bench;
- Wi-Fi burst traffic;
- known buffer overflow;
- timestamp discontinuity/reboot.

The system passes when it **records the failure explicitly** rather than quietly generating plausible-looking numbers.

---

## 30. Remaining Unknowns

### 30.1 Hardware unknowns that block exact wiring

1. Exact ESP32 DevKit carrier/revision/regulator/ADC pin availability.
2. Exact CJMCU V502 passive values and RLD/reference/filter topology.
3. Exact PC-tinyGSR 12/22 ADC/interface, I²C address, trimmer transfer/calibration state, and quantitative conversion.
4. Exact SmartElex MAX30101 schematic, supply regulation, address convention, interrupt behavior, and optical implementation; physical `INT` exposure is verified.
5. Exact SmartElex TMP117 selected address, PCB thermal path, regulator, and pull-ups.
6. Exact GY-521 regulator/pull-ups, performance, and whether the MPU-6050 is genuine or compatible silicon.

### 30.2 Measurement unknowns

1. Required ECG bandwidth and whether morphology beyond R peaks matters.
2. Whether HRV is a primary research endpoint or secondary.
3. Whether ECG↔PPG pulse-arrival timing is actually needed.
4. Whether PPG is ultimately finger-session, wrist-continuous, or both.
5. Whether EDA needs absolute µS/cross-session comparability.
6. Which temperature quantity matters: raw local skin trend, peripheral context, or calibrated skin temperature.
7. Whether withdrawal tremor is passive ambulatory or standardized task-based.
8. Which local motion references materially improve usable-data yield.

### 30.3 System unknowns

1. Longest intended recording.
2. Whether raw data must stream in real time or can be uploaded later.
3. exact battery/runtime target.
4. maximum physical cable length.
5. required synchronization error budget.
6. environmental range.
7. human-subject/IRB protocol boundaries.
8. reference equipment actually available to the team.

### 30.4 Direct answers to the 22 final questions

**1. Can every current sensor be made to produce a useful real signal?**  
**Yes, with conditions.** AD8232 should produce a conditioned ECG-like waveform; MAX30101 can produce raw PPG; legacy tinyGSR can produce useful relative EDA if stable; TMP117 can produce a meaningful local/skin-contact trend if mechanically coupled; MPU-6050 can produce useful motion/tremor data. The unresolved part is the exact generic breakout implementation, not the basic modality feasibility.

**2. What is the strongest existing implementation for each one?**  
ECG current path: SparkFun AD8232 + ADI AD8232 eval design. PPG: MAXREFDES100 + EmotiBit + SparkFun MAX30101. EDA legacy/current family: ProtoCentral tinyGSR docs and modern v3; broader EDA benchmark: EmotiBit/Shimmer/Nervous EDA. Temperature: TI TIDA-060034 plus MAXREFDES104/106 mechanics. IMU: i2cdevlib for MPU-6050; Bosch BMI270 for modern replacement. Controller/integration: EmotiBit and HealthyPi 5.

**3. What exact code/schematic/reference design should be studied first?**  
Study, in order: SparkFun AD8232 `Hardware/` + `Software/`; Analog Devices AD8232 eval circuit; MAXREFDES100 design package; EmotiBit `EmotiBit.cpp/.h` and buffer/data docs; SparkFun MAX3010x `src/` + MAX30101 hardware repo; ProtoCentral tinyGSR `hardware/firmware`; TI TIDA-060034 package; HealthyPi 5 hardware/firmware/wireless split.

**4. Which current hardware components are good enough for V1?**  
All six are good enough to **test** in V1. The strongest caveats are ECG ADC quality, legacy EDA calibration and TMP117 mechanics.

**5. Which current components are likely bottlenecks?**  
First: ESP32 on-chip ADC as the ECG digitizer. Second: exact V502 AD8232 passive topology. Third: legacy tinyGSR quantitative reproducibility. Fourth: SmartElex PPG/temp mechanics. The ESP32 CPU itself is not the likely bottleneck.

**6. What extra components are strongly justified?**  
Local nonvolatile storage, battery-only body-side power, proper ECG/EDA electrodes, PPG fixture and temperature thermal fixture are H1. A dedicated ECG AFE and modern tinyGSR are H2 benchmark/replacement candidates.

**7. Is the ESP32 enough?**  
**Yes for centralized V1.** There is direct precedent for ESP32 running MAX30101 + EDA + temp + IMU with SD/Wi-Fi, and the data rate is small. Use deterministic peripheral/FIFO timing and buffers.

**8. Is the ESP32 ADC enough for ECG?**  
**Enough to prove ECG/R peaks at rest; not the strongest research-quality endpoint.** Benchmark it against a dedicated ECG AFE before relying on it for HRV/timing-sensitive work.

**9. Should ECG remain AD8232-based?**  
Keep it through first-light and comparative testing. For a stronger controlled body-worn baseline, dedicated ADS1292R/MAX30001/MAX30003-class acquisition is better supported.

**10. Is the legacy tinyGSR usable enough for relative EDA?**  
**Yes, if fixed-resistor testing shows stable monotonic response.** Keep its output explicitly relative; do not promise universal absolute µS.

**11. Is the SmartElex MAX30101 breakout a practical PPG prototype?**  
**Yes.** Verify power/address behavior and build a controlled optical fixture. Replace with an open SparkFun MAX30101 board only if the SmartElex board itself is the measured problem.

**12. How should TMP117 physically contact skin?**  
Use a small, low-thermal-mass skin-facing contact area/thermal island, mechanically pressed reproducibly against skin and thermally isolated from MCU/regulator/battery/PPG heat. TIDA-060034’s flex architecture and MAXREFDES104/106 metal contact are the strongest references.

**13. Is MPU6050 sufficient for tremor and artifact experiments?**  
**Yes for V1.** Rigidly mount it, use transparent raw data, characterize sample timing and calibration, and move to a modern FIFO IMU only if needed.

**14. Can one MCU acquire everything?**  
**Yes.** One ESP32 is the correct first integration experiment.

**15. When would multiple local nodes become justified?**  
When actual measurements show long analog/I²C wiring, local sensor motion, RF coupling, placement or user-burden problems that cannot be solved cleanly in the centralized harness.

**16. How should the sensors be synchronized?**  
One local monotonic timebase in centralized V1; hardware/FIFO/DRDY event timestamps. For multiple nodes, retain local timestamps and use shared sync pulses during research or periodic offset/drift estimation wirelessly.

**17. How should data be logged without loss?**  
Bounded producer queues + dedicated logger task + local SD/flash + sequence numbers + FIFO/queue overflow flags. Wireless is not the authoritative copy.

**18. How should body-connected operation be powered safely?**  
Battery-only body-side operation with USB/charging physically disconnected for ECG/EDA sessions. Use engineered isolation only if a live wired link is genuinely needed; do not claim certification.

**19. What existing complete systems are closest?**  
HealthyPi Move for sensor-set similarity; EmotiBit for current non-ECG components/ESP32 pattern; MAXREFDES100 for MAX30101 + ECG AFE + temp + motion manufacturer reference; HealthyPi 5 for robust acquisition/storage/RF partitioning.

**20. What should we reproduce first?**  
Independent raw channels using the strongest exact references, then the one-ESP32 local-SD recorder. Do not begin with a custom PCB or distributed nodes.

**21. What should the first complete multimodal recording look like?**  
Native-rate raw ECG, three-wavelength PPG, relative EDA, TMP117 temperature and six-axis IMU sharing a traceable timebase, plus events/configuration/error metadata.

**22. What measurable criteria tell us V1 works?**  
No silent data loss; repeatable reference-validated signals for every modality; quantified timing/jitter; raw waveform availability; known failure modes detected; repeatable body fixtures; and a 30–60 minute integrated run that remains parseable under controlled load.

---

## 31. Source / Implementation Ledger

### 31.1 Core source ledger

All URLs below were opened/verified during this research or were already verified in the repository research and re-used as implementation references. Where a commercial platform does not publish circuitry, the ledger explicitly limits the claim to architecture/validation.

| ID | System/source | Type | Hardware | What it demonstrates | Reproducible assets | Confidence | Exact value to project | URL |
|---|---|---|---|---|---|---|---|---|
| S01 | Project evidence map | repository context | current six boards | exact known/unknown component boundaries | Markdown | — | authoritative current evidence | https://github.com/Beastly713/aud-obj-hardware/blob/main/docs/aud_multimodal_hardware_evidence_map.md |
| S02 | Project sensor-role definition | repository context | all modalities | physiological roles and non-claims | Markdown | — | prevents over-interpretation | https://github.com/Beastly713/aud-obj-hardware/blob/main/docs/aud_sensor_role_definition.md |
| S03 | Project form-factor options | repository context | all modalities | body-site / L1-L3 topology questions | Markdown | — | physical constraints | https://github.com/Beastly713/aud-obj-hardware/blob/main/docs/aud_hardware_form_factor_options.md |
| S04 | Project novelty map | architecture/prior-art context | multimodal | existing architecture precedents | Markdown | — | implementation leads only | https://github.com/Beastly713/aud-obj-hardware/blob/main/docs/aud_novelty_hypothesis_map.md |
| S05 | Analog Devices MAXREFDES100 | EXACT + ARCHITECTURE | MAX30101, MAX30003/4, MAX30205, IMUs, MAX32620 | complete tested multimodal acquisition, flash, BLE/USB, ECG best practices | schematic, BOM, PCB CAD/layout, FAB, firmware, GUI/app source | **R1/R2** | strongest exact MAX30101 + ECG system ref | https://www.analog.com/en/resources/reference-designs/maxrefdes100.html |
| S06 | Analog Devices MAXREFDES104 | ARCHITECTURE + MECHANICAL | ECG/PPG AFE, MAX30208 flex, accel, MCU, flash | wearable integration; temp flex + aluminum skin interface | board/mechanical/design files, software | **R1/R2** | skin temperature + wrist mechanics | https://www.analog.com/en/resources/reference-designs/maxrefdes104.html |
| S07 | Analog Devices MAXREFDES106 | ARCHITECTURE + MECHANICAL | ECG/PPG/BioZ, temp flex/rigid, ADXL367, MCU, flash | chest patch, skin/ambient temp, local flash | design/mechanical files, firmware/software | **R1/R2** | patch/body interface | https://www.analog.com/en/resources/reference-designs/maxrefdes106.html |
| S08 | ProtoCentral HealthyPi 5 hardware | FUNCTIONAL + ARCHITECTURE | RP2040, ESP32-C3, MAX30001, AFE4400, temp, microSD | acquisition MCU + RF MCU, open board | `.sch/.brd`, BOM, PDFs, STL | **R1** | strongest controller partition reference | https://github.com/Protocentral/protocentral_healthypi_5 |
| S09 | HealthyPi 5 firmware | ARCHITECTURE | RP2040 biosignal acquisition | sensor tutorials/full app, data broker, SD, watchdog | source/build docs | **R1** | loss-aware acquisition firmware | https://github.com/Protocentral/protocentral_healthypi_5_firmware |
| S10 | HealthyBridge ESP32 | ARCHITECTURE | ESP32-C3 | BLE/Wi-Fi co-processor + framed MCU link | source/docs | **R1** | radio separation | https://github.com/Protocentral/healthybridge-esp32 |
| S11 | ProtoCentral OpenView | VALIDATION + HOST | multiple ProtoCentral sensors | host visualization/logging over USB/BLE/Wi-Fi | source | **R1** | quick raw-signal inspection | https://github.com/Protocentral/protocentral_openview |
| S12 | EmotiBit FeatherWing firmware | EXACT + ARCHITECTURE | MAX30101, EDA, temp, IMU, ESP32-supported | real multimodal acquisition, buffers, Wi-Fi, SD | firmware/source | **R1** | closest current non-ECG stack | https://github.com/EmotiBit/EmotiBit_FeatherWing |
| S13 | EmotiBit hardware docs | EXACT + ARCHITECTURE | EmotiBit V4–V7 | versioned schematics and mechanical info | schematics/docs | **R1** | compare real multimodal PCB | https://github.com/EmotiBit/EmotiBit_Docs/tree/master/hardware_files |
| S14 | EmotiBit working-data documentation | VALIDATION | all EmotiBit streams | real data format/rates/metadata/examples | docs/data guidance | **R1** | logging schema and expected raw output | https://github.com/EmotiBit/EmotiBit_Docs |
| S15 | Montgomery et al. 2024 EmotiBit validation | VALIDATION | PPG, EDA, temp, IMU | comparison with gold-standard research devices | paper | **R2** | research-grade validation context | https://doi.org/10.1016/j.measen.2024.101075 |
| S16 | Vorreuther et al. 2025 EmotiBit validation | VALIDATION | PPG/EDA vs reference ECG/EDA | HR agreement but HRV/EDA limitations under workload | paper | **R2** | prevents overclaiming derived features | https://doi.org/10.3389/fnrgo.2025.1585469 |
| S17 | HealthyPi Move official | FUNCTIONAL + ARCHITECTURE | MAX30001, wrist/finger PPG, EDA, BMI323, MAX30208, nRF5340 | complete open wearable with near-exact modality set | links to HW/FW/docs | **R1/R2** | closest full-system reference | https://protocentral.com/product/healthypi-move/ |
| S18 | HealthyPi Move hardware | ARCHITECTURE | main/sensor/bottom/finger boards | PCB partition and enclosure | PCB + enclosure source | **R1/R2** | physical module split | https://github.com/Protocentral/healthypi-move-hw |
| S19 | HealthyPi Move firmware | ARCHITECTURE | nRF5340/Zephyr | modern wearable FW and data services | source/build docs | **R1/R2** | modern RTOS/reference | https://github.com/Protocentral/healthypi-move-fw |
| S20 | Shimmer3R GSR+ | FUNCTIONAL + VALIDATION | GSR, PPG accessory, IMU, microSD, STM32U5 | research EDA/PPG wearable | product docs/sample data; not full open schematic | **R3** | EDA range/placement/reference | https://www.shimmersensing.com/product/shimmer3-gsr-unit/ |
| S21 | Shimmer sample data | VALIDATION | GSR, PPG, ECG/respiration examples | real raw traces, sampling/placement examples | downloadable sample data | **R2** | “working signal” examples | https://www.shimmersensing.com/support/sample-data/ |
| S22 | BITalino GitHub organization | FUNCTIONAL + ARCHITECTURE | modular biosignals | open firmware/APIs/hardware portions | source/repositories | **R2/R3** | low-cost modular reference | https://github.com/BITalinoWorld |
| S23 | SparkFun AD8232 Heart Rate Monitor | EXACT | AD8232 | known working board and analog topology | Eagle schematic/PCB, production, demo | **R1** | compare current V502 + first ECG | https://github.com/sparkfun/AD8232_Heart_Rate_Monitor |
| S24 | SparkFun AD8232 Hookup Guide | EXACT | AD8232 board/electrodes | practical RA/LA/RL placement and first signal | guide/code | **R1** | first-light wiring | https://learn.sparkfun.com/tutorials/ad8232-heart-rate-monitor-hookup-guide |
| S25 | Analog Devices AD8232 evaluation guide | EXACT | AD8232 | REFOUT, RLD, filtering, input protection, two/three electrode | schematic + circuit explanation | **R2** | understand hidden V502 passives | https://wiki.analog.com/resources/eval/ad8232-evaluation-guide/a03321a |
| S26 | ProtoCentral ADS1292R Arduino library | FUNCTIONAL | ADS1292R | DRDY/SPI ECG/respiration acquisition | `src/`, `examples/` | **R1** | ECG upgrade firmware | https://github.com/Protocentral/protocentral-ads1292r-arduino |
| S27 | ProtoCentral ADS1292R hardware | FUNCTIONAL | ADS1292R breakout/shield | open ECG AFE board, electrode/power guidance | hardware files/docs | **R1** | ECG upgrade hardware | https://github.com/Protocentral/ADS1292rShield_Breakout |
| S28 | ProtoCentral MAX30001 getting started | FUNCTIONAL | MAX30001 | 18-bit ECG, 128/256/512 SPS, SPI/INT, ESP32 wiring | docs + linked HW/SW | **R1/R2** | wearable ECG alternative | https://protocentral.com/product/protocentral-max30001/docs/getting-started/ |
| S29 | SparkFun MAX3010x library | EXACT-ish | MAX3010x incl. MAX30101 support | FIFO/register/LED raw acquisition | source/examples | **R1/R2** | PPG driver reference | https://github.com/sparkfun/SparkFun_MAX3010x_Sensor_Library |
| S30 | SparkFun MAX30101 Qwiic hardware | EXACT | MAX30101 | documented breakout electrical design | Eagle schematic/PCB/production | **R1** | replacement if SmartElex uncertain | https://github.com/sparkfun/SparkFun_Photodetector_Breakout_MAX30101_Qwiic |
| S31 | EmotiBit MAX30101 library | EXACT | MAX30101 family | library fork used in multimodal system | source | **R2** | compare practical MAX30101 changes | https://github.com/EmotiBit/EmotiBit_MAX30101 |
| S32 | Analog Devices MAX30101 product | EXACT | MAX30101 | primary IC behavior/specifications | datasheet/eval resources | **R2** | definitive register/electrical source | https://www.analog.com/en/products/max30101.html |
| S33 | ProtoCentral tinyGSR official | EXACT/near-family | TLA2022 + GSR AFE | current board specs, docs, electrode workflow | schematic/library links | **R1/R2** | legacy-family support + replacement context | https://protocentral.com/product/protocentral-tinygsr-gsr-eda-digital-output-sensor-board-qwiic-stemma-qt/ |
| S34 | ProtoCentral tinyGSR repository | FUNCTIONAL | modern tinyGSR | open deterministic EDA hardware/software | `hardware/`, `firmware/` | **R1** | best low-friction quantitative EDA path | https://github.com/Protocentral/protocentral_tinygsr |
| S35 | ProtoCentral TLA20xx library | EXACT | TLA2022 family | ADC driver/configuration | source/examples | **R1/R2** | legacy tinyGSR low-level ADC reference | https://github.com/Protocentral/protocentral_tla20XX_arduino |
| S36 | Nervous-EDA | FUNCTIONAL | PSoC analog + nRF52840 | open wearable impedance-spectroscopy EDA | ECAD, FW, app, binaries | **R1** | advanced EDA alternative | https://github.com/sensors-inl/Nervous-EDA |
| S37 | TI TIDA-060034 | EXACT | TMP117 ×2 flex | body-temperature flex, ambient/system compensation | guide, BOM, schematic, Gerber, layout | **R2** | strongest TMP117 body-mechanics ref | https://www.ti.com/tool/TIDA-060034 |
| S38 | SparkFun TMP117 library/hardware | EXACT | TMP117 | open electrical bring-up | library + board files | **R1** | verify SmartElex register behavior | https://github.com/sparkfun/SparkFun_TMP117_Arduino_Library |
| S39 | Adafruit TMP117 PCB | EXACT | TMP117 | alternate open breakout | schematic/board | **R1** | board comparison | https://github.com/adafruit/Adafruit-TMP117-PCB |
| S40 | MPU-6050 primary device documentation | EXACT | MPU-6050 | ranges/register/FIFO/interrupt basis | manufacturer docs | **R2** | definitive IMU config | https://invensense.tdk.com/products/motion-tracking/6-axis/mpu-6050/ |
| S41 | jrowberg i2cdevlib | EXACT | MPU-6050 | mature raw/DMP/calibration examples | source/examples | **R2** | rapid IMU bring-up | https://github.com/jrowberg/i2cdevlib |
| S42 | Bosch BMI270 Sensor API | FUNCTIONAL | BMI270 | official driver and FIFO architecture | source/examples | **R2** | modern IMU alternative | https://github.com/boschsensortec/BMI270_SensorAPI |
| S43 | ESP-IDF ADC overview | EXACT | classic ESP32 ADC | official ADC architecture/calibration | driver docs/examples | **R2** | controller ADC rules | https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/adc/index.html |
| S44 | ESP-IDF ADC continuous mode | EXACT | classic ESP32 ADC/DMA | sample frequency, DMA/buffer/overflow behavior | driver docs/examples | **R2** | ECG sampling implementation | https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/adc/adc_continuous.html |
| S45 | ESP-IDF GPIO / ADC2-Wi-Fi note | EXACT | classic ESP32 | ADC2 cannot be used with Wi-Fi | official docs | **R2** | reserve ADC1 | https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/gpio.html |
| S46 | ESP-IDF ESP Timer | EXACT | ESP32 | high-res timer vs FreeRTOS timer behavior | official docs | **R2** | local timestamping | https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/system/esp_timer.html |
| S47 | ESP-IDF legacy ESP32 ADC guidance | EXACT | classic ESP32 ADC | noise/multisampling/Vref guidance | official docs | **R2** | ADC uncertainty | https://docs.espressif.com/projects/esp-idf/en/v4.4.2/esp32/api-reference/peripherals/adc.html |
| S48 | NXP I²C specification | FUNCTIONAL | I²C bus | bus capacitance/pull-up fundamentals | standard | **R2** | bus calculations | https://www.nxp.com/docs/en/user-guide/UM10204.pdf |
| S49 | Analog Devices ADuM4160 | FUNCTIONAL | USB isolation | purpose-built USB isolator | product/eval docs | **R2** | isolated-debug reference | https://www.analog.com/en/products/adum4160.html |
| S50 | Analog Devices CN0419 | FUNCTIONAL + ARCHITECTURE | isolated USB | complete isolated USB reference circuit | schematic/BOM/layout | **R2** | wired-isolation architecture | https://www.analog.com/en/resources/reference-designs/circuits-from-the-lab/cn0419.html |
| S51 | Polar BLE SDK — H10 | VALIDATION | Polar H10 | raw ECG 130 Hz, RR/HR, accelerometer | official SDK/examples | **R2** | independent beat/reference recording | https://github.com/polarofficial/polar-ble-sdk/blob/master/documentation/products/PolarH10.md |
| S52 | Shimmer documentation hub | ARCHITECTURE | Shimmer3 family | firmware/manual/sync/logging docs | downloadable docs | **R3** | multi-device research workflow | https://www.shimmersensing.com/support/wireless-sensor-networks-documentation/ |
| S53 | Shimmer sample data | VALIDATION | ECG/GSR/PPG/IMU | downloadable real raw examples | datasets | **R2** | expected waveform/data checks | https://www.shimmersensing.com/support/sample-data/ |
| S54 | Hemo-Monitoring-Wearable | FUNCTIONAL + ARCHITECTURE | ECG, PPG, EBI, nRF52840 | fabricated open multimodal wearable with signal plots | Altium HW + embedded code | **R1/R2** | PCB/signal-integrity reference | https://github.com/JermYeWorm/Hemo-Monitoring-Wearable |
| S55 | OpenEarable | ARCHITECTURE | ear PPG/temp/IMU/storage | fully open ear-worn sensor platform | HW/FW/mechanics | **R1/R2** | future ear PPG/mechanics | https://open-earable.teco.edu/ |
| S56 | SensWear | ARCHITECTURE | modular PPG/temp/motion/touch | modular sensor daughterboards, synchronized FW/logging | Altium/manufacturing + FW/apps | **R1/R2** | multi-board wearable design | https://github.com/Sens-Wear |
| S57 | Norouzi et al. 2017 | VALIDATION | accelerometer | alcohol-withdrawal tremor energy analysis | paper | **R2** | tremor-analysis target | https://doi.org/10.1016/j.bspc.2016.11.006 |
| S58 | Beach et al. 2021 | FUNCTIONAL + VALIDATION | local IMUs + ECG/EEG | local sensor motion references for artifact removal | paper | **R2** | local IMU rationale | https://doi.org/10.1049/htl2.12016 |
| S59 | MIT washable wearable patent/reference | ARCHITECTURE | PPG, EDA, temp, motion | early multimodal wearable pattern | patent disclosure | **R3** | architecture precedent | https://patents.google.com/patent/US20100268056A1/en |
| S60 | Empatica EmbracePlus | ARCHITECTURE | PPG, EDA, temp, motion | professional wrist quartet | product/technical docs | **R4** for reproduction | professional form-factor benchmark | https://www.empatica.com/embraceplus/ |

### 31.2 GitHub repository implementation ledger

| Repository | Relevant directories/files | License | Hardware files? | Firmware? | Host software? | Activity note | Exact part worth studying |
|---|---|---|---|---|---|---|---|
| `EmotiBit/EmotiBit_FeatherWing` | `EmotiBit.cpp`, `EmotiBit.h`, `DoubleBufferFloat.*`, stock firmware, board config | MIT main repo; dependency licenses vary | separate docs repo | **Yes** | related repos | active into 2026 | multi-rate buffers, SD/Wi-Fi coexistence, MAX30101 integration |
| `EmotiBit/EmotiBit_Docs` | `hardware_files/`, working-data docs | see repo | **Yes, versioned schematics** | links | docs | active | hardware-revision discipline and data format |
| `Protocentral/protocentral_healthypi_5` | `hardware/`, enclosure, README | CERN/open HW per repo files | **Yes** | links | archived + OpenView link | current docs/revisions in 2026 | RP2040 + ESP32-C3 partition and board revisions |
| HealthyPi 5 firmware | tutorials/apps, board/pin docs, broker/storage | open source | n/a | **Yes** | OpenView | current 2026 rewrite | acquisition ownership, lossless broker, SD |
| `Protocentral/healthybridge-esp32` | protocol/BLE/Wi-Fi code/docs | MIT/open source | n/a | **Yes** | web/host pieces | current | RF co-processor boundary |
| `Protocentral/healthypi-move-hw` | `pcbs/main board`, `sensor board`, `bottom board`, `finger sensor board`, `enclosure` | repository license + product open-HW notice; verify file-specific terms | **Yes** | no | no | active issues in 2026 | physically separate wrist/finger boards |
| `Protocentral/healthypi-move-fw` | Zephyr app/sensor/data code | open source; verify current branch file | n/a | **Yes** | companion app separate | active 2026 | wearable data store, raw capture, sensor services |
| `sparkfun/AD8232_Heart_Rate_Monitor` | `Hardware/`, `Production Files/`, `Software/`, `Fritzing/` | see `LICENSE.md` | **Yes** | demo | Processing example | mature/stable | exact passive network and first-light waveform |
| `Protocentral/protocentral-ads1292r-arduino` | `src/`, `examples/` | MIT software | separate HW repo | **Yes** | OpenView | maintained ecosystem | DRDY/SPI read and ECG processing |
| `Protocentral/ADS1292rShield_Breakout` | board design/docs | CERN-OHL-P v2 current notice | **Yes** | linked | OpenView | maintained ecosystem | dedicated ECG hardware reference |
| `sparkfun/SparkFun_MAX3010x_Sensor_Library` | `src/`, `examples/` | mixed SparkFun/Maxim/BSD notices; inspect files | no | driver | examples | mature | raw FIFO/register control |
| `sparkfun/SparkFun_Photodetector_Breakout_MAX30101_Qwiic` | `Hardware/`, `Documents/`, production | see `LICENSE.md` | **Yes** | library linked | examples | stable | fully documented MAX30101 breakout |
| `Protocentral/protocentral_tinygsr` | `hardware/`, `firmware/`, `assets/` | CERN-OHL-P v2 HW; MIT SW | **Yes** | **Yes** | compatible OpenView/workflows | current product | deterministic EDA replacement |
| `sensors-inl/Nervous-EDA` | `hardware/`, `firmware/`, `web-app/`, releases | MIT | **Yes** | **Yes** | **Yes** | recent academic project | full reproducible high-end EDA chain |
| `jrowberg/i2cdevlib` | `Arduino/MPU6050/`, raw/DMP/IMU_Zero examples | MIT for relevant device code | no | driver/examples | no | mature legacy | raw MPU-6050 register/FIFO/calibration code |
| `boschsensortec/BMI270_SensorAPI` | driver/examples/FIFO support | BSD-style official repo | no | **Yes** | no | maintained vendor API | modern FIFO IMU path |
| `polarofficial/polar-ble-sdk` | `documentation/products/PolarH10.md`, ECG demos | Polar SDK license | proprietary device | SDK | **Yes** | current 2026 | reference ECG acquisition |
| `JermYeWorm/Hemo-Monitoring-Wearable` | `hardware/`, `embedded/`, paper/slides | MIT | **Yes** | **Yes** | limited | research repo | integrated ECG+PPG PCB and signal plots |
| `Sens-Wear/Hardware` + stack | main/PPG/temp/debug boards, manufacturing outputs | repository license | **Yes** | separate repo | mobile/Python separate | active | modular daughterboard/mechanical architecture |

### 31.3 Real-data / “what working looks like” links

Use these before creating project-specific plots:

1. **Shimmer sample data:** raw GSR/PPG/ECG examples with sampling/placement notes — https://www.shimmersensing.com/support/sample-data/
2. **EmotiBit data documentation:** format and example-data guidance — https://github.com/EmotiBit/EmotiBit_Docs
3. **MAXREFDES100 GUI examples:** live raw optical/accelerometer and ECG plots/configuration — https://www.analog.com/en/resources/reference-designs/maxrefdes100.html
4. **SparkFun AD8232 hookup:** recognizable first-light ECG waveform and wiring — https://learn.sparkfun.com/tutorials/ad8232-heart-rate-monitor-hookup-guide
5. **HealthyPi/OpenView:** host visualization for raw ECG/PPG and compatible ProtoCentral devices — https://github.com/Protocentral/protocentral_openview
6. **Hemo-Monitoring-Wearable:** fabricated-board ECG/EBI result plots — https://github.com/JermYeWorm/Hemo-Monitoring-Wearable
7. **Norouzi et al. withdrawal tremor:** acceleration-derived tremor energy in a 5–15 Hz analysis range — https://doi.org/10.1016/j.bspc.2016.11.006

### 31.4 The files to clone/read first

If work starts tomorrow, the most efficient initial reference checkout is:

```bash
# Complete multimodal references
git clone https://github.com/EmotiBit/EmotiBit_FeatherWing.git
git clone https://github.com/EmotiBit/EmotiBit_Docs.git
git clone https://github.com/Protocentral/protocentral_healthypi_5.git

# Current-component and replacement references
git clone https://github.com/sparkfun/AD8232_Heart_Rate_Monitor.git
git clone https://github.com/sparkfun/SparkFun_MAX3010x_Sensor_Library.git
git clone https://github.com/sparkfun/SparkFun_Photodetector_Breakout_MAX30101_Qwiic.git
git clone https://github.com/Protocentral/protocentral_tinygsr.git
git clone https://github.com/Protocentral/protocentral-ads1292r-arduino.git
git clone https://github.com/Protocentral/ADS1292rShield_Breakout.git
git clone https://github.com/jrowberg/i2cdevlib.git
```

Then download/reference the manufacturer packages:

- MAXREFDES100 design package [S05]
- TI TIDA-060034 design files [S37]
- AD8232 evaluation circuit [S25]

### 31.5 Final implementation recommendation

The highest-value sequence is:

> **Do not redesign the platform first. Build Blueprint A with local logging and safe battery operation; use it to measure the limitations of the current BOM. Upgrade ECG to ADS1292R/MAX3000x only if the AD8232+ESP32 ADC path fails a side-by-side signal/timing benchmark; replace legacy tinyGSR only when quantitative EDA becomes necessary; replace the SmartElex MAX30101 only if its actual board/mechanics fail; retain MPU-6050 until FIFO/timing/availability becomes a measured problem; and do not distribute acquisition until wiring/site-local measurements justify the cost.**

This approach maximizes the probability of obtaining a real synchronized multimodal recording quickly while preserving a clean route to stronger instrumentation.

---

## Overall build decision

A credible **V1 can be built from the current parts**. The project does not need to wait for patent novelty, a custom PCB, multiple MCUs, or wholesale sensor replacement.

The practical architecture to reproduce first is:

```text
current sensors
→ canonical inventory plus targeted electrical characterization
→ individual raw-signal proof
→ battery-only body interfaces
→ one ESP32 acquisition timebase
→ ADC1 continuous ECG
→ FIFO/interrupt-aware PPG + IMU
→ relative legacy EDA
→ thermally controlled TMP117
→ buffered local microSD
→ explicit sequence/error metadata
→ independent validation
```

The first likely upgrade is **not a faster MCU**. It is a **dedicated ECG AFE/ADC** if the AD8232 + classic ESP32 ADC cannot meet the project’s measured R-peak/RR/noise requirements.

The second likely upgrade is **modern quantitative EDA** if relative legacy tinyGSR data cannot support the chosen cross-session research question.

Everything else should be earned by a failure measurement.
