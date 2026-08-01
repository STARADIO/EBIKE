# KM5S Communication Protocol —   V1.0
---

## 1. Communication Method

**UART**: `9600, n, 8, 1`

## 2. Master-Slave Relationship

- **Master**: Instrument Panel
- **Slave**: Controller

## 3. Communication Interval

- Master sends one frame every **500ms**
- Slave replies after receiving data and **successful checksum verification**

## 4. Power-On Sequence

```
Instrument (Master) ──send S (System Parameter Setup)──► Controller (Slave)
Instrument (Master) ◄──reply ACK ────────────────────── Controller (Slave)
Instrument (Master) ──send R (System Runtime Data)─────► Controller (Slave)
Instrument (Master) ◄──reply status data ────────────── Controller (Slave)
```

## 5. Data Frame Format

```
0x3A + aa + cc + len + dat(1) + dat(2) + … + dat(len) + SUM1 + SUM2 + 0x0D + 0x0A
```

| Field | Meaning | Description |
|-------|---------|-------------|
| `0x3A` | Start Delimiter | Frame start identifier |
| `aa` | Slave Address | Controller address: **0x1A** |
| `cc` | Command | `'S'` = System Parameter Setup, `'R'` = System Runtime Data |
| `len` | Data Length | Number of bytes in `dat` |
| `dat(1…len)` | Data Payload | Actual payload data |
| `SUM1` | Checksum Low Byte | `SUM % 256` |
| `SUM2` | Checksum High Byte | `SUM / 256` |
| `0x0D, 0x0A` | End Delimiter | Frame end identifier (CR+LF) |

### Checksum Calculation

```
SUM = aa + cc + len + dat(1) + dat(2) + … + dat(len)
SUM1 = SUM % 256
SUM2 = SUM / 256
```

---

## 6. Instrument → Controller (Master Transmit)

### 6.1 Command S: System Parameter Setup

- **Data Length**: Fixed `0x07` (7 bytes)

#### Data 1: PAS Sensor Configuration

| Bit | Meaning | Value |
|-----|---------|-------|
| bit7 | Throttle Present | 0 = No throttle, 1 = Has throttle |
| bit6 | PAS Signal Direction | 0 = Forward, 1 = Reverse |
| bit5~0 | PAS Activation Magnet Count | Number of magnets before PAS activates; generally ≥ 2 |

#### Data 2: PAS Speed Following Adjustment

- Range: `0~255`
- Typical values: 6 magnets → `128`, 12 magnets → `64`

#### Data 3: Speed Sensor Magnet Count

| Bit | Meaning | Value |
|-----|---------|-------|
| bit7 | Throttle Gear-Dependent | 0 = Independent, 1 = Gear-dependent |
| bit6 | Throttle 6KM Function | 0 = Normal throttle, 1 = Throttle 6KM walk-assist |
| bit5~4 | Soft Start Parameter | — |
| bit3~0 | Speed Sensor Magnet Count | Pulse count per wheel revolution |

#### Data 4: System Undervoltage Value

| Bit | Meaning | Value |
|-----|---------|-------|
| bit7 | Positive / Negative Offset | 0 = Positive, 1 = Negative |
| bit6~0 | Offset Value | Per unit: **0.1V** |

**Undervoltage Reference Table:**

| System Voltage | Undervoltage Reference |
|----------------|------------------------|
| 24V | 21.0V |
| 36V | 31.5V |
| 48V | 42.0V |

#### Data 5: System Voltage / Current Limit

| Bit | Meaning | Value |
|-----|---------|-------|
| bit7~6 | System Voltage | `01` = 24V, `10` = 36V, `11` = 48V |
| bit5~0 | Current Limit | Per unit: **0.5A** |

#### Data 6: Handshake Byte

- Range: `0~63` random number

#### Data 7: Speed Limit / Wheel Diameter Code

| Bit | Meaning | Value |
|-----|---------|-------|
| bit7~3 | Speed Limit Value | Speed limit **− 10** (unit: Km/h) |
| bit2~0 | Wheel Diameter Code | See table below |

**Wheel Diameter Code Table:**

| Code | Wheel Diameter |
|------|----------------|
| `000` | 16 inch |
| `001` | 18 inch |
| `010` | 20 inch |
| `011` | 22 inch |
| `100` | 24 inch |
| `101` | 26 inch |
| `110` | 700c |
| `111` | 28 inch |

---

### 6.2 Command R: System Runtime Data

- **Data Length**: Fixed `0x02` (2 bytes)

#### Data 1: Maximum PWM Value

- Range: `0~255`

#### Data 2: Status

| Bit | Meaning | Value |
|-----|---------|-------|
| bit7 | Headlight | 0 = Off, 1 = On |
| bit6 | Reserved | — |
| bit5 | Battery Low | 0 = Normal, 1 = Low capacity |
| bit4 | Walk Function | 0 = Off, 1 = Walk assist (controller runs at max PWM) |
| bit3 | Reserved | — |
| bit2 | Reserved | — |
| bit1 | Reserved | — |
| bit0 | Overspeed | 0 = Normal, 1 = Overspeed |

---

## 7. Controller → Instrument (Slave Reply)

- **Data Length**: Fixed `0x05` (5 bytes)

### Data 1: Status Code

| Bit | Meaning | Value |
|-----|---------|-------|
| bit7 | Undervoltage | 0 = Not in undervoltage, 1 = Undervoltage |
| bit6~0 | Reserved | — |

### Data 2: Current Information

- **Actual running current / 3**, unit: **A**
- Example: `30` = 10A

### Data 3~4: Speed Information

- Time per wheel revolution (unit: **ms**)
- Data 3 = High byte, Data 4 = Low byte
- Maximum time: 3500ms = `0x0DAC`

### Data 5

#### When Instrument Sends `'S'`: Handshake Lookup

Returns `password[handshake_byte]`. The handshake password table is:

```c
const unsigned char password[64] = {
    137, 159, 134, 249,  88,  11, 250,  61,  33, 150,   3, 193, 118, 141, 209,  94,
    226,  68, 146, 158, 145, 127, 216,  62, 116, 230, 101, 211, 251,  54, 229, 247,
     20, 222,  59,  63,  35, 252, 142, 238,  23, 197,  84,  77, 147, 173, 210,  57,
    142, 223, 157,  97,  36, 160, 229, 237,  75,  80,  37, 113, 154,  88,  23, 120
};
```

#### When Instrument Sends `'R'`: Fault Codes

| Code | Meaning |
|------|---------|
| `0x21` | Current Abnormal |
| `0x22` | Throttle Abnormal |
| `0x23` | Motor Phase Loss |
| `0x24` | Motor Hall Sensor Abnormal |
| `0x25` | Brake Abnormal |

---

## 8. Communication Examples

### 8.1 System Parameter Setup (S)

```
0x3A  Start Delimiter
0x1A  Call Controller (slave address)
0x53  'S' = System Parameter Setup
0x07  Data length = 7 bytes
0x02  Forward PAS, activate after 2 magnets
0x40  PAS ratio = 64
0x01  Speed sensor 1 magnet
0x0A  Undervoltage positive offset +10 (+1.0V), i.e., 32.5V undervoltage
0x8F  36V system, 15A current limit
0x32  Handshake byte = 50
0x7D  Speed limit 25Km/h, wheel diameter 26 inch
0xFF  Checksum low byte (excluding 0x3A)
0x01  Checksum high byte
0x0D  End delimiter 1
0x0A  End delimiter 2
```

### 8.2 System Runtime Data (R)

```
0x3A  Start Delimiter
0x1A  Call Controller (slave address)
0x52  'R' = System Runtime Data
0x02  Data length = 2 bytes
0xAB  PWM max = 171
0x80  Headlight on
0x99  Checksum low byte (excluding 0x3A)
0x01  Checksum high byte
0x0D  End delimiter 1
0x0A  End delimiter 2
```

---

## Frame Structure Quick Reference

| Type | aa | cc | len | Data Description |
|------|-----|-----|-----|------------------|
| S Transmit | `0x1A` | `'S'(0x53)` | `0x07` | PAS + Speed + Undervoltage + Current Limit + Handshake + Speed Limit |
| R Transmit | `0x1A` | `'R'(0x52)` | `0x02` | Max PWM + Status |
| Reply | `0x1A` | `cc`(as-is) | `0x05` | Status + Current + Speed + Password / Fault Code |
