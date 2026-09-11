# Hardware Inventory — AUD/SUD Multimodal Physiological Prototype

**Status:** Stage 0 physical hardware inventory  
**Date:** 2026-09-11  
**Purpose:** Record the exact hardware physically present for the current prototype and separate what is visually verified from what remains unverified.

> This file is the canonical inventory for the hardware currently in hand.  
> When older project documents disagree with this file on board identity, PCB marking, or visible pin/interface information, this file should take precedence.

## 1. Inventory Summary

| ID | Hardware | Exact/visible identity | Primary role | Prior working status | Inventory status |
|---|---|---|---|---|---|
| HW-01 | Controller | ESP32 DEVKITV1, 30-pin, ESP-WROOM-32 module | Acquisition/controller/transport | Previously used successfully | Verified sufficiently for V1 |
| HW-02 | EDA/GSR | ProtoCentral `PC-tinyGSR`, PCB marking **12/22** | Relative EDA/GSR acquisition | Previously used successfully | Verified sufficiently for V1 |
| HW-03 | ECG | `CJMCU-8232`, PCB marking **V502**, AD8232 IC | Single-lead ECG analog front end | Previously used successfully | Verified sufficiently for V1 |
| HW-04 | PPG | SmartElex MAX30101 Photodetector breakout | Optical PPG acquisition | Previously used successfully | Verified sufficiently for V1 |
| HW-05 | Temperature | SmartElex TMP117 Temperature Sensor breakout | Local/peripheral temperature acquisition | Previously used successfully | Verified sufficiently for V1 |
| HW-06 | IMU | GY-521 with MPU-6050 | 6-axis motion / tremor / artifact context | Previously used successfully | Verified sufficiently for V1 |

**Important correction to earlier project notes:**  
The physical tinyGSR board is marked **12/22**, not `11/22`.

**Important correction to earlier project notes:**  
The physical CJMCU-8232 ECG board is marked **V502**, not `VS82`.

---

## 2. Verification Language

This inventory uses three levels:

- **VISUALLY VERIFIED** — directly readable or visible on the supplied photographs.
- **IC-LEVEL FACT** — known property of the identified IC/family, but not automatically proof of every carrier-board implementation detail.
- **UNVERIFIED / TO CHARACTERIZE** — not reliably determinable from the photographs and should not be silently assumed.

The user has stated that the ESP32 and all five sensor boards were previously used and were working. This is recorded as **prior working status**, but it does not replace later signal-quality, calibration, timing, or validation tests.

---

# 3. HW-01 — ESP32 Controller

## 3.1 Identity

**VISUALLY VERIFIED**

- Carrier-board marking: **`ESP32 DEVKITV1`**
- Form: **30-pin development board**
- Main radio/MCU module marking: **`ESP-WROOM-32`**
- Wireless PCB antenna visible
- Micro-USB connector present
- Buttons present:
  - `EN`
  - `BOOT`
- Two 15-pin headers, 30 pins total

## 3.2 Visible Pin Labels

One side visibly includes:

`VIN, GND, D13, D12, D14, D27, D26, D25, D33, D32, D35, D34, VN, VP, EN`

The other side visibly includes:

`3V3, GND, D15, D2, D4, RX2, TX2, D5, D18, D19, D21, RX0, TX0, D22, D23`

## 3.3 Project-Relevant Facts

**IC-/family-level facts**

- Classic ESP32 family, not ESP32-C3/S2/S3.
- 3.3 V logic family.
- Wi-Fi and Bluetooth are available at module level.
- Multiple ADC-capable GPIOs are available.
- GPIO21/GPIO22 are commonly used as SDA/SCL in ESP32 projects, although I²C pins are configurable in software.

## 3.4 Unverified / To Characterize

- Exact carrier-board manufacturer.
- Exact USB-to-UART bridge IC.
- Exact onboard voltage-regulator model.
- Exact regulator current capability.
- Exact ADC behavior/noise on this physical unit.
- Exact board revision beyond the visible `DEVKITV1` marking.

## 3.5 Canonical Project Name

**ESP32 DEVKITV1, 30-pin, ESP-WROOM-32-family controller board**

---

# 4. HW-02 — ProtoCentral tinyGSR

## 4.1 Identity

**VISUALLY VERIFIED**

- Brand: **ProtoCentral**
- Product marking: **`PC-tinyGSR`**
- PCB date/revision marking: **`12/22`**
- Open-source hardware marking visible
- `Qwiic`-style connectors visible
- 3.5 mm electrode jack visible
- Physical `BASELINE` trimmer present
- Header labels:
  - `GND`
  - `VCC`
  - `SCL`
  - `SDA`
- Board silkscreen states:
  - `VCC: 3.3 to 5V`
  - `IO: 3.3 to 5V`

## 4.2 Visible Electronics

**VISUALLY VERIFIED**

- Main visible analog IC marking is consistent with an **LM324-family** quad op-amp (`L324` marking visible).
- Multiple support passives and digital/interface circuitry are present.
- Two Qwiic-style connectors are populated.
- Baseline adjustment is implemented as a physical trimmer.

## 4.3 Current Project Interpretation

The exact 12/22 board should be treated as a **legacy relative-output tinyGSR implementation** unless a board-specific transfer/calibration function is later proven.

For V1, the useful target is:

**stable and repeatable relative EDA/GSR change**

rather than assuming universal absolute conductance in µS.

## 4.4 Unverified / To Characterize

- Exact ADC/interface IC identity from the current photos.
- Exact I²C address of this specific physical board.
- Exact trimmer transfer function.
- Exact relationship between raw digital reading and conductance.
- Exact analog front-end gain/range.
- Exact electrode transfer/calibration for absolute units.

These are not blockers for using the board as a relative EDA sensor.

## 4.5 Canonical Project Name

**ProtoCentral PC-tinyGSR legacy EDA/GSR board, PCB marking 12/22**

---

# 5. HW-03 — CJMCU-8232 / AD8232 ECG Board

## 5.1 Identity

**VISUALLY VERIFIED**

- PCB marking: **`CJMCU-8232`**
- PCB version/revision marking: **`V502`**
- Front marking: **`AD8232 Heart Monitor`**
- Main IC marking: **AD8232**
- Supply marking: **3.3 V**
- 3.5 mm electrode jack present
- Separate three-pin electrode header present

## 5.2 Visible Signal / Power Pins

**VISUALLY VERIFIED**

Header labels:

- `GND`
- `3.3V`
- `OUTPUT`
- `LO-`
- `LO+`
- `SDN`

Electrode labels:

- `RL`
- `LA`
- `RA`

## 5.3 Project-Relevant Function

**IC-level fact**

The AD8232 is an analog front end for biopotential/ECG-style acquisition.

For this physical board, the intended current V1 path is:

`electrodes -> CJMCU-8232 / AD8232 -> analog OUTPUT -> ESP32 ADC`

## 5.4 Unverified / To Characterize

- Exact passive network values as a complete circuit.
- Exact high-pass and low-pass cutoff frequencies.
- Exact gain.
- Exact RLD/body-bias implementation on this V502 layout.
- Exact lead-off implementation behavior.
- Exact analog output swing on the physical unit under the intended electrode configuration.
- Whether the ESP32 ADC is sufficient for later HRV-quality work.

None of these prevent first-light ECG acquisition.

## 5.5 Canonical Project Name

**CJMCU-8232 AD8232 single-lead ECG/heart-monitor module, PCB marking V502**

---

# 6. HW-04 — SmartElex MAX30101 PPG Board

## 6.1 Identity

**VISUALLY VERIFIED**

- Brand: **SmartElex**
- Board marking: **`MAX30101 Photodetector`**
- Optical sensing package physically present
- I²C-related board markings present
- Board marking visible: **`ADR: 0x52`**
- Two board connectors populated
- Separate header pins populated

## 6.2 Visible Pins

**VISUALLY VERIFIED**

- `1.8V`
- `INT`
- `SCL`
- `SDA`
- `3.3V`
- `GND`

The presence of a physically exposed **INT** pin is useful for later FIFO/interrupt-driven acquisition.

## 6.3 Project-Relevant Function

**IC-level fact**

MAX30101-family devices are optical pulse/PPG sensors with internal LED drivers, photodetection, ADC, FIFO, and I²C control.

For V1, the project should retain raw optical data rather than relying only on derived BPM output.

## 6.4 Address Note

The board physically prints **`ADR: 0x52`**.

This should be preserved as a **silkscreen observation only** until the exact SmartElex documentation or a direct bus check establishes what convention the board uses for that printed value.

Do **not** silently rewrite it as a verified 7-bit I²C address.

## 6.5 Unverified / To Characterize

- Exact SmartElex schematic.
- Exact regulator and level-shifting implementation.
- Exact role of the exposed `1.8V` pin.
- Exact optical window/guard geometry.
- Exact interrupt wiring/behavior.
- Exact 7-bit I²C address on the physical unit.
- Contact-pressure and ambient-light behavior in the eventual fixture.

## 6.6 Canonical Project Name

**SmartElex MAX30101 PPG/Photodetector breakout board**

---

# 7. HW-05 — SmartElex TMP117 Temperature Board

## 7.1 Identity

**VISUALLY VERIFIED**

- Brand: **SmartElex**
- Board marking: **`TMP117 Temperature sensor`**
- Central temperature-sensor IC physically present
- Sensor IC is positioned within a mechanically narrowed/cut-out PCB region
- I²C and interrupt markings present
- Two board connectors populated

## 7.2 Visible Pins

**VISUALLY VERIFIED**

- `GND`
- `3V3`
- `SDA`
- `SCL`
- `INT`

## 7.3 Visible Address Configuration

**VISUALLY VERIFIED**

ADDR configuration silkscreen shows selectable markings including:

- `0x48`
- `0x49`
- `0x4A`
- `0x4B`

The exact selected address should be confirmed from the actual board configuration when required.

## 7.4 Project-Relevant Function

**IC-level facts**

TMP117 is a 16-bit digital temperature sensor. It supports multiple selectable I²C addresses and an alert/data-ready-capable output at IC level.

For this project, the relevant measurement is not automatically "body temperature" or "core temperature."

The board must first demonstrate a repeatable **skin-contact/local temperature trend** with a controlled thermal interface.

## 7.5 Important Mechanical Observation

The physical board places the sensing IC inside a narrowed/cut-out central region.

This is consistent with an attempt to reduce thermal coupling from the rest of the PCB, making this board more suitable for contact-temperature experiments than a completely unisolated general-purpose breakout.

It still does not by itself prove skin-temperature accuracy.

## 7.6 Unverified / To Characterize

- Exact selected I²C address.
- Exact thermal resistance between sensor island and main PCB.
- Exact self-heating contribution.
- Effect of attached wires/connectors on thermal response.
- Skin-contact bias and equilibration time.
- Exact regulator/pull-up implementation.

## 7.7 Canonical Project Name

**SmartElex TMP117 digital temperature sensor breakout board**

---

# 8. HW-06 — GY-521 / MPU-6050 IMU

## 8.1 Identity

**VISUALLY VERIFIED**

- PCB marking: **`GY-521`**
- Main IC marking: **InvenSense MPU-6050**
- Blue GY-521-style carrier board
- Onboard power/support circuitry visible

## 8.2 Visible Pins

**VISUALLY VERIFIED**

- `VCC`
- `GND`
- `SCL`
- `SDA`
- `XDA`
- `XCL`
- `AD0`
- `INT`

## 8.3 Project-Relevant Function

**IC-level facts**

MPU-6050 provides:

- 3-axis accelerometer
- 3-axis gyroscope
- programmable accelerometer full-scale ranges
- programmable gyroscope full-scale ranges
- FIFO support
- interrupt support
- auxiliary I²C interface

The `AD0` pin is used to select between the two MPU-6050 I²C addresses at IC level.

For the project, the board is sufficient for first-stage:

- raw motion
- activity context
- local motion artifact experiments
- tremor-frequency / spectral experiments

## 8.4 Unverified / To Characterize

- Exact onboard regulator model.
- Exact effective I²C pull-up configuration.
- Genuine-vs-compatible-clone status of the MPU-6050 silicon.
- Actual noise floor of the physical board.
- Actual FIFO/timing behavior in the final ESP32 firmware.
- Mounting-induced mechanical resonance.

These are not blockers for V1 motion/tremor work.

## 8.5 Canonical Project Name

**GY-521 MPU-6050 6-axis accelerometer + gyroscope IMU module**

---

# 9. Corrections Required in Existing Documentation

The following corrections should be propagated wherever the older identifiers appear.

| Existing/old wording | Correct wording from physical inventory |
|---|---|
| `ProtoCentral tinyGSR ... PCB marking 11/22` | **ProtoCentral PC-tinyGSR ... PCB marking 12/22** |
| `CJMCU-8232 ... PCB marking VS82` | **CJMCU-8232 ... PCB marking V502** |

The correction should be applied as an **identity correction only**.

Do not use the correction as a reason to silently alter unrelated research conclusions unless those conclusions depended specifically on the incorrect revision identifier.

---

# 10. Current Hardware Baseline

The exact current baseline is:

1. **ESP32 DEVKITV1, 30-pin, ESP-WROOM-32-family controller board**
2. **ProtoCentral PC-tinyGSR legacy EDA/GSR board, PCB marking 12/22**
3. **CJMCU-8232 AD8232 single-lead ECG/heart-monitor module, PCB marking V502**
4. **SmartElex MAX30101 PPG/Photodetector breakout board**
5. **SmartElex TMP117 digital temperature sensor breakout board**
6. **GY-521 MPU-6050 6-axis accelerometer + gyroscope IMU module**

This list should be treated as the canonical physical BOM until hardware is intentionally added or replaced.

---

# 11. Prior Working Status

The user reports that:

- the ESP32 controller has previously been used successfully;
- all five sensor boards have previously been used successfully;
- the sensor set and controller have previously been operated as working hardware.

Therefore, basic "does the board power up at all?" testing is not the current research bottleneck.

Future tests should focus on:

- exact signal quality;
- repeatability;
- calibration where required;
- body-interface quality;
- timing;
- sample loss;
- cross-sensor integration;
- electrical noise;
- validation against references.

---

# 12. Remaining Stage-0 Unknowns

The following are intentionally **not blockers** to moving into implementation:

### ESP32
- exact USB-UART bridge
- exact regulator model
- exact ADC noise behavior

### tinyGSR
- exact digital/ADC IC identity from the current photographs
- exact I²C address
- exact transfer function to conductance
- baseline-trimmer calibration

### AD8232
- complete V502 passive/filter network
- exact gain and cutoff frequencies

### MAX30101
- exact SmartElex schematic
- exact regulator/level-shifter details
- exact address convention behind printed `ADR: 0x52`

### TMP117
- exact selected address
- quantitative thermal behavior of the sensor island

### MPU-6050
- exact regulator/pull-up implementation
- genuine-vs-compatible silicon status
- measured noise/timing characteristics

These unknowns should be resolved only when they become relevant to a concrete integration or validation step.

---

# 13. Stage-0 Conclusion

**Physical hardware identification is complete enough to proceed.**

The project now has a canonical inventory for the six boards actually present.

The next engineering phase should not repeat basic board-identification work. It should move to:

1. preserve/correct the existing documentation using this inventory;
2. define the implementation repository structure;
3. bring up and record each sensor using known-good reference implementations;
4. build the first centralized multimodal raw recorder;
5. validate signal quality and timing;
6. add/replace hardware only when measured evidence justifies it.

Patent novelty is not part of this inventory.
