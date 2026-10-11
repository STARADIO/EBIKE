# STARADIO EBIKE

**Public marketing & technical documentation repository for 7 STARADIO eBike models.**

This repo is the shop window: product photos, brand assets, publishable
specifications and shared technical protocols. Anything commercially sensitive
stays out. The rules below are not advisory — read them before adding anything.

---

## Publishing Rules

### ✅ MAY be published

| Content | Destination |
|---|---|
| Product photos (white-background, multi-angle) | `01_Models/<MODEL>/Photos/` |
| **Product specification / parameter sheets** | `02_Specs/` |
| Brand framework, visual identity, colour system | `03_Brand/` |
| Shared technical protocols (battery, display, motor/controller) | `04_Technical/` |
| Logo source files, brand templates | `05_Assets/` |
| Accessories catalogue | `06_Accessories/` |

### ⛔ MUST NOT be published — no exceptions

| Forbidden | Why |
|---|---|
| **Supplier quotations / factory price lists** | Reveals sourcing cost and margin structure |
| **BOM files** (bill of materials) | Lists suppliers and part numbers |
| **Supplier / factory contact details** — contact names, phone, email, WeChat, WhatsApp, address | Exposes the supply chain |
| **Internal cost, margin, tax, logistics figures** | Commercially sensitive |
| **Customer, order or sales data** | Privacy |
| **Unverified performance claims** | Legal exposure — see *Speed claims* below |

> ⚠️ `.gitignore` in this repo carries a deny-list as a safety net, but a filename
> filter is **not** a security control — anyone can rename a file. The actual rule is:
> **if in doubt, it does not go in.** When you are unsure whether a document is
> publishable, keep it out and ask.

### Speed claims

A published top-speed figure must reflect what the bike can **actually deliver and
legally comply with**, not what a controller or motor datasheet claims in passing.

- Factory speed limits are set **per destination market** — e.g. 45 km/h (28 mph)
  for U.S. Class 3, 25 km/h for the EU/UK.
- The display itself can only enforce limits up to **50 km/h** (50 = unlimited).
  A figure above that cannot be enforced and must not be published.
- Example — S26-PRO-D: the controller datasheet claimed 80 km/h, but the display
  cannot enforce it. Published figure is **45 km/h**.

---

## Structure

```
EBIKE/
├── README.md
├── .gitignore                          ⛔ deny-list for non-publishable content
│
├── 01_Models/                          ★ One folder per model (7 models)
│   ├── S5/                             · 26" Mountain 48V 350W
│   │   └── Photos/
│   ├── S6/                             · 26" City Step-Thru 48V 350W
│   │   └── Photos/
│   ├── S8-D/                           · 20" Snow Dual-Drive 48V 1000W
│   │   └── Photos/
│   ├── S26-PRO-D/                      · 26" Premium Snow Dual-Drive 52V 1000W
│   │   └── Photos/
│   ├── S700PRO/                        · 19" Moped Flagship 60V 3000W
│   │   └── Photos/
│   ├── S700SE/                         · 19" Moped Economy 52V 1500W
│   │   └── Photos/
│   └── S700SE-D/                       · 20" Snow Moped Dual-Drive 52V 1000W
│       └── Photos/
│
├── 02_Specs/                           ★ Publishable spec / parameter sheets ONLY
│
├── 03_Brand/                           ★ Brand framework, visual identity, colour system
│
├── 04_Technical/                       ★ Shared technical documents
│   ├── Battery_Protocol/               · Battery BMS communication protocols
│   ├── Certifications/                 · UL / CE / ISO certification references
│   ├── Display_Protocol/               · Display communication protocols
│   └── Motor_Controller/               · Motor / controller protocol documents
│
├── 05_Assets/                          ★ Shared assets
│   ├── Logos/                          · STARADIO logo source files
│   └── Templates/                      · Brand / GTM templates (NO quotation templates)
│
└── 06_Accessories/                     ★ Accessories
```

---

## 7 Models at a Glance

| Model | Category | Battery | Motor | Tires | Top Speed |
|-------|----------|---------|-------|-------|-----------|
| S5 | Entry MTB | 48V 9Ah | 48V 350W | 26×2.125 | 35 km/h |
| S6 | City Step-Thru | 48V 9Ah | 48V 350W | 26×2.125 | 35 km/h |
| S8-D | Snow Dual | 48V 25Ah | 48V 1000W AWD | 20×4.0 | 45 km/h |
| S26-PRO-D | Premium Snow Dual | 52V 18Ah | 52V 1000W AWD | 26×4.0 | 45 km/h |
| S700PRO | Moped Flagship | 60V 27.5Ah | 60V 3000W | 70/100-19 | ⚠️ pending verification |
| S700SE | Moped Economy | 52V 18Ah | 52V 1500W | 70/100-19 | ⚠️ pending verification |
| S700SE-D | Snow Moped Dual | 52V 18Ah | 52V 1000W AWD | 20×4.0 | 45 km/h |

> ⚠️ **Figures marked *pending verification* must not be published anywhere** — not
> in the spec sheet, not in a product page, not in video. Verify the deliverable
> speed and the legal limit per market first, then replace with the real number.

---

## File Naming

```
Model_Type_Version.ext

Examples:
  S5_Photo_LeftFront_White.jpg
  S8-D_Photo_RightFront_White.png
  S700PRO_Photo_LeftRear_White.jpg
  S26-PRO-D_Spec_V2.1.pdf
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
  git commit -m "[Specs] Add S26-PRO-D spec sheet V2.1"
  git commit -m "[Brand] Add STARADIO brand framework HTML"
  git commit -m "[Technical] Add battery BMS protocol #2 for 52V systems"
  git commit -m "[Assets] Add logo source files (AI/PNG/SVG)"
```

> Do not write a commit message that names a quotation, a factory price or a
> supplier contact — even in a private branch of this repo. Commit messages are
> part of the permanent public history.

---

## Note

- **Supplier quotations, BOM files and supplier contact details are NOT published
  in this repo.** They live in the internal workspace only.
- **Specification and parameter files MAY be published** (see `02_Specs/`), but only
  after the figures have been verified against the built bike.
- Photos should be named by angle: `_LeftFront_`, `_RightFront_`, `_LeftFrontSide_`,
  `_RightFrontSide_`, `_LeftRearSide_`, `_RightRearSide_`.