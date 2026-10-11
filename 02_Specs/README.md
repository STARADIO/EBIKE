# Specification Matrix

All seven models at a glance. Per-model detail lives in each model's folder.

---

## Drive, Power and Speed

| Model | Drive | Motor | Controller | Top Speed |
|-------|-------|-------|------------|-----------|
| S26-PRO-D | AWD | 500 W F + 500 W R | 58.8 V, 2 x 20 A, ISO 13849 | 45 km/h US · 25 km/h EU |
| S8-D | AWD | 500 W F + 500 W R | Dual-drive | 45 km/h US · 25 km/h EU |
| S700SE-D | AWD | 500 W F + 500 W R | Dual-drive | 35 km/h |
| S5 | Rear | 350 W | — | 25 km/h |
| S6 | Rear | 350 W | — | 25 km/h |
| S700SE | Rear | 1500 W | 52 V / 40 A | Confirm per market |
| S700PRO | Rear | 3000 W peak | 60 V / 60 A peak | Confirm per market |

> **On the two "confirm per market" entries.** S700SE and S700PRO are capable of
> more than the legal limit everywhere we sell. We publish the figures we can stand
> behind and leave these blank until the factory limit for each market is fixed.
> Ask us for the configured limit, or book a test ride.

---

## Battery and Charging

| Model | Battery | Voltage | Charger |
|-------|---------|---------|---------|
| S26-PRO-D | 18 Ah (14S4P) · ~936 Wh · UL 2271 | 52 V / 58.8 V | 58.8 V / 3 A |
| S8-D | 25 Ah (13S5P) · UL 2271 | 48 V / 54.6 V | 54.6 V / 2 A |
| S700SE-D | 25 Ah | 52 V / 58.8 V | 52 V / 3 A |
| S5 | 9 Ah (13S2P) | 48 V / 54.6 V | 54.6 V / 2 A |
| S6 | 9 Ah (13S2P) | 48 V / 54.6 V | 54.6 V / 2 A |
| S700SE | 25 Ah | 52 V / 58.8 V | 52 V / 3 A |
| S700PRO | 35 Ah (16S) | 60 V / 67.2 V | 60 V / 5 A |

---

## Chassis, Wheels and Brakes

| Model | Wheels | Brakes | Front suspension | Rear |
|-------|--------|--------|-------------------|------|
| S26-PRO-D | 26 x 4.0 | Hydraulic disc 180 mm, 2-piston | 290S, 80 mm, lockout | 150 mm coil, 450 lb |
| S8-D | 20 x 4.0 | Hydraulic disc 180 mm | Spring, 50 mm | — |
| S700SE-D | 20 x 4.0 | Hydraulic disc 160 mm | Spring | Dual air shock, 1500 lb |
| S5 | 26 x 2.125 | Mechanical disc 160 mm | 188S spring, lockout | — |
| S6 | 26 x 2.125 | Mechanical disc 160 / 180 mm | Spring, adjustable lockout | — |
| S700SE | 70/100-19 | Hydraulic disc 203 mm | Aluminium hydraulic, 110 mm | Dual, 220 mm, 1200 lb |
| S700PRO | 70/100-19 | Hydraulic disc 203 mm, **4-piston** | Aluminium hydraulic, thru-axle | Dual hydraulic, 220 mm, 1200 lb |

---

## Transmission and Display

All seven run a Shimano 7-speed drivetrain with a 14-28T freewheel, and all seven
carry a colour LCD display with a status readout that tells you what has faulted
rather than just going quiet.

Display protocol, fault codes and field definitions are published in
[`04_Technical/`](../04_Technical/).

---

## How We Publish Specs

- **Numbers only once verified against a built bike.** Anything still under test
  is left out, not estimated in public.
- **Speed is the configured legal limit**, not a datasheet maximum.
- **Certification is stated per component.** We do not call a whole bike
  certified on the strength of its battery alone.
- **Figures vary** with battery state, terrain, rider weight and assist level.
  Ask us for a configuration sheet for your market.

The reasoning behind these rules is in
[`PUBLISHING_RULES.md`](../PUBLISHING_RULES.md).

---

[Back to lineup](../README.md)