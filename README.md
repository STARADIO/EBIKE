# STARADIO EBIKE

Product & technical documentation repository for 7 STARADIO eBike models.

---

## Structure

```
EBIKE/
├── README.md
├── Motor_Controller/              ← existing: Lithium battery protocol #2
│
├── 01_Models/                     ★ One folder per model (7 models)
│   ├── S5/                        · 26" Mountain 48V 350W
│   │   └── Photos/
│   ├── S6/                        · 26" City Step-Thru 48V 350W
│   │   └── Photos/
│   ├── S8-D/                      · 20" Snow Dual-Drive 48V 1000W
│   │   └── Photos/
│   ├── S26-PRO-D/                 · 26" Premium Snow Dual-Drive 52V 1000W
│   │   └── Photos/
│   ├── S700PRO/                   · 19" Moped Flagship 60V 3000W
│   │   └── Photos/
│   ├── S700SE/                    · 19" Moped Economy 52V 1500W
│   │   └── Photos/
│   └── S700SE-D/                  · 20" Snow Moped Dual-Drive 52V 1000W
│       └── Photos/
│
├── 02_Quotations/                 ★ Quotation sheets (OEM/ODM pricing)
│
├── 03_Brand/                      ★ Brand framework, visual identity, color system
│
├── 04_Technical/                  ★ Shared technical documents
│   ├── Battery_Protocol/          · Battery BMS communication protocols
│   ├── Certifications/            · UL / CE / ISO certification references
│   └── Display_Protocol/          · Display communication protocols
│
└── 05_Assets/                     ★ Shared assets
    ├── Logos/                     · STARADIO logo source files
    └── Templates/                 · BOM / GTM / quotation templates
```

---

## 7 Models at a Glance

| Model | Category | Battery | Motor | Tires | Top Speed |
|-------|----------|---------|-------|-------|-----------|
| S5 | Entry MTB | 48V 9Ah | 48V 350W | 26×2.125 | 35 km/h |
| S6 | City Step-Thru | 48V 9Ah | 48V 350W | 26×2.125 | 35 km/h |
| S8-D | Snow Dual | 48V 25Ah | 48V 1000W AWD | 20×4.0 | 45 km/h |
| S26-PRO-D | Premium Snow Dual | 52V 18Ah | 52V 1000W AWD | 26×4.0 | 60 km/h |
| S700PRO | Moped Flagship | 60V 27.5Ah | 60V 3000W | 70/100-19 | 80 km/h |
| S700SE | Moped Economy | 52V 18Ah | 52V 1500W | 70/100-19 | 60 km/h |
| S700SE-D | Snow Moped Dual | 52V 18Ah | 52V 1000W AWD | 20×4.0 | 45 km/h |

---

## File Naming

```
Model_Type_Version.ext

Examples:
  S5_Photo_LeftFront_White.jpg
  S8-D_Photo_RightFront_White.png
  S700PRO_Photo_LeftRear_White.jpg
  STARADIO_7Models_Quotation_V0.1.xlsx
  STARADIO_Brand_Framework_V2.0.html

Versions:
  V0.x = Draft / internal
  V1.x = Released / client-facing
  V2.x = Major revision (spec change)
```

---

## Commit Convention

```bash
git commit -m "[Model/Area] Description"

Examples:
  git commit -m "[S5] Add 6-angle white-background product photos"
  git commit -m "[S26-PRO-D] Add 6-angle white-background product photos"
  git commit -m "[Quotations] Update 7-model price list with factory quotes"
  git commit -m "[Brand] Add STARADIO brand framework HTML"
  git commit -m "[Technical] Add battery BMS protocol #2 for 52V systems"
  git commit -m "[Assets] Add logo source files (AI/PNG/SVG)"
```

---

## Note

- **BOM files are NOT published in this repo.** Pricing and supplier data remain internal.
- **Model spec sheets / parameter files are NOT published yet.** Pending final verification.
- Photos should be named by angle: `_LeftFront_`, `_RightFront_`, `_LeftFrontSide_`, `_RightFrontSide_`, `_LeftRearSide_`, `_RightRearSide_`.
