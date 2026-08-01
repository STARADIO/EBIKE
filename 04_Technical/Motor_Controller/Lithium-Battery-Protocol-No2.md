# Lithium Battery Communication Protocol No.2

---

## Revision History

1. The speed limit value was originally defined as two bytes, but the high byte was always 0 in practice. Therefore, on special instrument panels, the high byte has been redefined as **"Motor Commutation Hall Sensor Magnet Count for Speed Limiting"**.

2. The PAS (Pedal Assist System) magnet disc magnet count originally occupied one byte, but only half a byte is sufficient. The upper nibble is reused for specific parameters:
   - **Bit7**: Reverse gear flag
   - **Bit6**: Auto-cruise flag
   - **Bit5**: Brake flag (brake lever connected to the instrument panel)

3. In the controller transmission section, the gear level sent by the controller is added at Bit1/Bit0 of **"Controller Status 2"** (when a smartphone connects to the controller, the instrument panel only displays the gear level). Additionally, a brake bit is added at Bit5 of **"Controller Status 2"** to distinguish from brake fault (Controller Status 1, Bit1).

4. Added dual-drive functionality: the instrument panel sends Bits 4~5 of **Controller Setting 2** as the drive mode byte.

> **Note**: The brake lever may be connected to either the instrument panel or the controller, so both directions define it. Set to 0 when not in use.

---

## Speed Measurement Methods

1. **Motor Phase Voltage Speed Measurement**: Due to various drawbacks, this protocol does NOT adopt this method. (In rare cases where required, use direct wiring, not the protocol.)

2. **Motor Commutation Hall Speed Measurement**:
   - Magnet count is generally ≥ 30, providing many signals and fast response.
   - **Drawback**: Many high-speed motors have a clutch; when coasting, the commutation Hall cannot detect speed → export vehicles (requiring pedaling without power) generally do not use this method.
   - **Applicable**: Commonly used in scooters and other clutchless motors.

3. **Independent Hall Speed Measurement**: Built-in Hall sensor unaffected by the clutch, with fewer signals (1~6 magnets). Can also use a Hall sensor mounted on the wheel spokes (generally 1 magnet).

4. **Protocol Judgment Rule**:
   - Set parameter **≤ 6** → Independent Hall speed measurement (external speed sensor)
   - Set parameter **> 6** → Motor commutation Hall speed measurement (internal speed sensor)

5. **Three Magnet Definitions**:
   - **① Speed Measurement Magnet** (see 1.4): Used for speed feedback and speed limit calculation. In general, the controller only needs to implement this parameter.
   - **② Motor Commutation Hall Magnet Count for Speed Limiting** (see 1.8): Used in special cases for fine-grained closed-loop speed limit control. Only provided when requested by the controller manufacturer.
   - **③ PAS Magnet Disc Magnet Count**: Used for pedal assist sensor signal detection.

---

## Basic Data Format

### Frame Structure

```
Address(1B) + Length(1B) + Command(1B) + Data(N B) + Checksum(1B)
```

| Field | Size | Description |
|-------|------|-------------|
| Address | 1 byte | Instrument→Controller = `0x01`, Controller→Instrument = `0x02` |
| Length | 1 byte | Total frame length = `4 + N` |
| Command | 1 byte | Distinguishes data type |
| Data | N bytes | Control and configuration data |
| Checksum | 1 byte | XOR checksum (see below) |

### Checksum Algorithm

**XOR Checksum**: XOR all bytes (address, length, command, data) — the result should be 0.

```
Checksum = addr ^ len ^ cmd ^ data[0] ^ data[1] ^ … ^ data[N-1] ^ checksum = 0
```

**Example**:

```
0x01 0x03 0x02 0x00 0x03 0x00 0x00 0x00 0x00 0x81 0x00 0x00 0x00 0x00 0x82

Checksum = 0x01 ^ 0x03 ^ 0x02 ^ 0x00 ^ 0x03 ^ 0x00 ^ 0x00 ^ 0x00 ^ 0x00 ^ 0x81 ^ 0x00 ^ 0x00 ^ 0x00 ^ 0x00 ^ 0x82 = 0 ✓
```

### Frame Delimiter

Frames are separated by **idle time**:
- Recommended receiver idle threshold: **10~20ms**
- Recommended minimum sender idle: **50ms**

### Multi-byte Definition

High byte sent first, low byte sent last (**Big-Endian**).

### UART Parameters

- **Baud Rate**: 9600
- **Data Bits**: 8
- **Stop Bits**: 1
- **Parity**: None

### Compatibility Considerations

```c
// ✅ Recommended: validate using the length provided in the first byte
if (recData[1] != recCount) {
    return;
}

// ❌ Not recommended: hardcoded fixed length
if (recCount != 20) {
    return;
}

// ✅ Consider forward compatibility
if (!((recCount >= 20) && (recCount <= 25))) {
    return;
}
```

---

## Section 1: Instrument → Controller (Transmit Frame)

**20-byte format, Protocol Version 2:**

| Offset | Field | Size | Description |
|--------|-------|------|-------------|
| 0 | Address | 1B | `0x01` |
| 1 | Frame Length | 1B | `20` |
| 2 | Command | 1B | `0x01` |
| 3 | Drive Mode Setting | 1B | `0~2` |
| 4 | PAS Level Setting | 1B | `0~15` (see 1.2) |
| 5 | Controller Control Setting 1 | 1B | Switch/Start/Light/Cruise, etc. |
| 6 | Speed Measurement Magnet Count | 1B | ≤6 external, >6 internal |
| 7~8 | Wheel Diameter | 2B | Unit: 0.1 inch |
| 9 | PAS Sensitivity | 1B | `3~24` |
| 10 | PAS Start Strength | 1B | Upper 4 bits extensible as ABS brake strength |
| 11 | Speed-Limit Hall Magnet Count | 1B | Special function (generally ignored) |
| 12 | Speed Limit Value | 1B | Unit: Km/h |
| 13 | Controller Current Limit | 1B | Unit: 1A |
| 14~15 | Controller Undervoltage Value | 2B | Unit: 0.1V |
| 16~17 | Throttle PWM Duty Cycle | 2B | Unit: 0.1‰ (permil) |
| 18 | Controller Setting 2 + PAS Disc Type | 1B | High nibble + Low nibble |
| 19 | Checksum | 1B | XOR checksum |

---

### 1.1 Drive Mode Setting (Offset 3)

| Value | Mode | Description |
|-------|------|-------------|
| `0` | PAS Drive | Output determined by PAS level; throttle disabled |
| `1` | Electric Drive | Driven by throttle; PAS level disabled |
| `2` | PAS + Electric Coexistence | Electric drive disabled in zero-start state |

- D7~D2: Reserved
- D1~D0: Drive mode

---

### 1.2 PAS Level Setting (Offset 4)

| Instrument Setting | Level Type | Corresponding Send Values Per Gear |
|--------------------|------------|-------------------------------------|
| `0` (or `1`) | 3-level PAS | 5 / 10 / 15 |
| `1` (or `2`) | 5-level PAS | 3 / 6 / 9 / 12 / 15 |
| `2` (or `3`) | 9-level PAS | 1 / 3 / 5 / 7 / 9 / 11 / 13 / 14 / 15 |

> **D7 Bit**: `0` = Normal gear, `1` = **Reverse gear**
>
> Example: `0x81` = Reverse gear 1, `0x82` = Reverse gear 2

---

### 1.3 Controller Control Setting 1 (Offset 5)

| Bit | Meaning | 0 | 1 |
|-----|---------|---|---|
| bit7 | Controller Power State | Not working | Working |
| bit6 | Zero / Non-zero Start | Zero start | Non-zero start |
| bit5 | Light Control | Off | On |
| bit4 | Communication Fault | Communication normal | Fault (cannot receive controller data) |
| bit3 | Cruise Mode Switching | Trigger mode (see bit0) | Long-press mode (see bit1) |
| bit2 | Speed Limit State | Not exceeded | Over speed limit |
| bit1 | Cruise Hold Mode | Not cruising | Cruising (hold to maintain, release to exit = 6KM walk-assist mode) |
| bit0 | Cruise Toggle Mode | No change | Toggle cruise state (invert toggle = fixed-speed cruise) |

> **Cruise Special Note**: When toggling cruise, if the vehicle is stationary → enter 6KM walk-assist cruise; if the vehicle is moving → enter fixed-speed cruise.

---

### 1.4 Speed Measurement Magnet (Offset 6)

- **≤ 6**: External speed measurement mode (independent Hall), most commonly 1 or 6 magnets
- **> 6**: Internal speed measurement mode (motor commutation Hall), e.g., a typical motor with 46 magnet pieces

> Note: Some earlier versions mistakenly documented this as pole pair count.

---

### 1.5 Wheel Diameter (Offset 7~8)

- Unit: **0.1 inch**
- Example: Send `305` → wheel diameter 30.5 inches

---

### 1.6 PAS Sensitivity (Offset 9)

- Range: `3~24`
- Meaning: The number of Hall pulses detected from standstill before outputting drive signal

---

### 1.7 PAS Start Strength + ABS Brake Strength (Offset 10)

| Bit | Function |
|-----|----------|
| bit7~4 | ABS brake strength (high 4 bits, extended for specific cases) |
| bit3~0 | PAS start strength: `0~5` (0 = weakest, 5 = strongest) |

> The absolute PWM value for PAS is defined by the controller manufacturer.

---

### 1.8 Speed-Limit Hall Magnet Count + Speed Limit Value (Offset 11~12)

- **Offset 11**: Speed-limit dedicated motor commutation Hall magnet count (replaces the original high byte of the speed limit value)
- **Offset 12**: Speed limit value (unit: **Km/h**)

> Newly matched controller firmware should only read the low byte (discard the high byte). This field is for specific instrument panels; older controllers are unlikely to be directly mass-production matched with such panels.

---

### 1.9 Controller Current Limit (Offset 13)

- Unit: **1A**
- Default: `12`

---

### 1.10 Controller Undervoltage Value (Offset 14~15, 2 bytes)

- Unit: **0.1V**
- High byte bits 1~0 + low byte 8 bits = **10 bits total**, max undervoltage 102.3V

**High byte bits 7~2 special functions:**

| Bit | Function | 0 | 1 |
|-----|----------|---|---|
| bit7 | Overspeed Auto Alert | Off | On (commonly triggered at 15KM) |
| bit6 | Horn Flag | Horn off | Horn active |
| bit5 | High/Low Beam Select | Low beam | High beam |
| bit4 | Reserved | — | — |
| bit3 | Left Turn Signal | Off | On |
| bit2 | Right Turn Signal | Off | On |
| bit1~0 | Undervoltage value (high 2 bits) | — | — |

---

### 1.11 Throttle PWM Duty Cycle (Offset 16~17)

- Unit: **0.1‰** (permil)
- Maximum value: **1000** (i.e., 100%)

> ⚠️ **Safety Recommendation**: If the controller continuously fails to receive valid communication data for a certain period (e.g., 2s), it should gradually brake until stopped. The instrument panel transmission interval is approximately 100~150ms.

---

### 1.12 Controller Setting 2 + PAS Disc Magnet Count (Offset 18)

**High 4 bits (Controller Setting 2):**

| Bit | Function | Value / Description |
|-----|----------|---------------------|
| bit7 | Reverse Flag | 0 = Inactive, 1 = Reverse |
| bit6 | Auto-Cruise Flag | 0 = No auto-cruise, 1 = Enabled (duration determined by controller) |
| bit5~4 | Drive Mode Selection | `0` = Single rear drive, `1` = Single front drive, `2` = Dual drive |

**Low 4 bits (PAS Disc Magnet Count):**

| Value | Magnet Count |
|-------|-------------|
| `5` | 5 magnets |
| `8` | 8 magnets |
| `12` | 12 magnets |

> Compatibility Tip: When reading the PAS magnet count, use `& 0x0F` to filter out the high bits for full backward compatibility.

---

## Section 2: Controller → Instrument (Reply Frame)

**14-byte format, Command 1 data frame:**

| Offset | Field | Size | Description |
|--------|-------|------|-------------|
| 0 | Address | 1B | `0x02` |
| 1 | Frame Length | 1B | `14` |
| 2 | Command | 1B | `0x01` |
| 3 | Controller Status 1 | 1B | Fault / Status bits |
| 4 | Controller Status 2 | 1B | Extended status / Gear |
| 5~6 | Running Current | 2B | See 2.3 |
| 7 | Current Ratio | 1B | 1~100% |
| 8~9 | Speed Feedback | 2B | See 2.5 |
| 10 | Battery Capacity | 1B | 0~100 (percentage) |
| 11 | Controller Status 3 | 1B | LOCK / P-gear / Turn signals, etc. |
| 12 | Remaining Range | 1B | Unit: Km |
| 13 | Checksum | 1B | XOR checksum |

---

### 2.1 Controller Status 1 (Offset 3)

| Bit | Meaning | 0 | 1 |
|-----|---------|---|---|
| bit7 | 6KM Walk-Assist Cruise State | Not in this state | In 6KM cruise |
| bit6 | Hall Sensor Fault | Normal | Fault |
| bit5 | Throttle Fault | Normal | Fault |
| bit4 | Controller Fault | Normal | Fault |
| bit3 | Undervoltage Protection | Not protecting | Protecting |
| bit2 | Cruise State | Not cruising | Cruising |
| bit1 | Brake Lever Fault | No fault | Fault |
| bit0 | Motor Phase Loss | Normal | Phase loss fault |

---

### 2.2 Controller Status 2 (Offset 4)

| Bit | Meaning | 0 | 1 |
|-----|---------|---|---|
| bit7 | Vehicle Level State | Not level | Level (always send 1 if not implemented) |
| bit6 | PAS Sensor State | Normal | Fault (always send 1 if not supported) |
| bit5 | Brake Lever (Braking) | Not engaged | Engaged |
| bit4 | Communication Fault | Normal | Fault (cannot receive instrument data) |
| bit3 | Charging State | Not charging | Charging |
| bit2 | Speed Limit State | Not limited | Limiting |
| bit1~0 | Gear (Controller→Instrument) | `00`/`01`/`10`/`11` = Gear 0/1/2/3 |

> Difference between Status 1 bit1 (brake fault) and Status 2 bit5 (brake state): The former is fault detection, the latter is state detection.

---

### 2.3 Running Current (Offset 5~6, 2 bytes)

- Earlier unit: **1A**
- **Current**: High byte bit6 determines precision

| High Byte bit6 | Unit | Example |
|----------------|------|---------|
| `0` | 1A | `0x0010` = 16A |
| `1` | 0.1A | `0x4010` = 1.6A |

- bit7 reserved for sign (positive/negative), currently kept 0

---

### 2.4 Current Ratio (Offset 7)

- Range: **1~100%**
- 100% corresponds to the current limit value

---

### 2.5 Speed Feedback (Offset 8~9, 2 bytes)

- Content: Time duration for one wheel revolution, unit: **1ms**
- Speed magnet count **≤ 6**: One Hall pulse = one revolution
- Speed magnet count **> 6**: Half the Hall pulses = one revolution (e.g., setting 46 → 23 pulses = 1 revolution)
- **Stop timeout**: 3.5s (3500ms)

---

### 2.6 Controller Status 3 (Offset 11)

| Bit | Meaning | 0 | 1 |
|-----|---------|---|---|
| bit7 | LOCK (Anti-theft Lock) | Unlocked | Locked |
| bit6 | P-Gear | Not P-gear | P-gear |
| bit5 | — | Reserved | — |
| bit4 | Left Turn Signal | Off | On |
| bit3 | Right Turn Signal | Off | On |
| bit2 | Helmet Status | Not worn | Worn |
| bit1~0 | Bluetooth Status | `00` = Disconnected, `01` = Connected, `10`/`11` = Reserved |

---

### 2.7 Battery Capacity & Remaining Range (Offset 10, 12)

- Implemented by **BMS**; send `0` when no BMS is present

---

## Frame Structure Quick Reference

### Instrument → Controller (20 bytes)

```
Offset: 0    1   2    3    4    5    6    7-8  9   10   11  12   13   14-15  16-17  18   19
Field:  ADDR LEN CMD  DRV  AST  CTL1 MAG  DIA  SEN STR  HSP SPD  ILMT UVAL   PWM    CTL2 XOR
Value:  0x01 20  0x01 0-2  0-15 bit  ≤6/  0.1" 3-24 0-5  —  Km/h 1A   0.1V   0.1%   H+L  XOR
                                        >6     inch
```

### Controller → Instrument (14 bytes)

```
Offset: 0    1   2    3     4     5-6   7    8-9   10   11    12   13
Field:  ADDR LEN CMD  STA1  STA2  CURR  RAT  SPEED CAP  STA3  RNG  XOR
Value:  0x02 14  0x01 bit   bit   cf.2.3 %    cf.2.5 %    bit   Km   XOR
```

---

*STARADIO EBIKE — 2026. Confidential & Proprietary.*
