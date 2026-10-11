# Publishing Rules — STARADIO EBIKE (public repo)

This repository is **public**. It is the shop window, not the filing cabinet.

These rules are binding on every commit. They exist because a public repo is
indexed, scraped and archived — anything pushed here should be assumed to be
public and permanent.

---

## ✅ MAY be published

| Content | Destination |
|---|---|
| Product photos (white-background, multi-angle) | `01_Models/<MODEL>/Photos/` |
| Product specification / parameter sheets | `01_Models/<MODEL>/README.md`, `02_Specs/` |
| Brand story, positioning, visual identity | `03_Brand/` |
| Technical interface protocols (battery, display, motor/controller) | `04_Technical/` |
| Marketing landing page | `README.md` |

## ⛔ MUST NOT be published — no exceptions

| Forbidden | Why |
|---|---|
| **Supplier quotations / factory price lists** | Reveals sourcing cost and margin structure |
| **BOM files** (bill of materials) | Lists suppliers and part numbers |
| **Supplier / factory contact details** — contact names, phone, email, WeChat, WhatsApp, address | Exposes the supply chain |
| **Internal cost, margin, tax, logistics figures** | Commercially sensitive |
| **Customer, order or sales data** | Privacy |
| **Unverified performance claims** | Legal exposure — see *Speed claims* |

> ⚠️ `.gitignore` carries a deny-list as a safety net, but a filename filter is
> **not** a security control — anyone can rename a file to get past it. The actual
> rule is: **if in doubt, it does not go in.** When unsure whether a document is
> publishable, keep it out and ask.

> ⚠️ **Do not recycle internal source files.** Internal working files carry supplier
> columns and supplier contact details as a matter of course, and their figures are
> often still being verified. Marketing content here is **written fresh**, not copied
> across. Treat "it exists in our internal docs" as irrelevant to whether it may be
> published — check it against this page first.

---

## Speed claims

A published top-speed figure must reflect what the bike can **actually deliver and
legally comply with**, not what a controller or motor datasheet claims in passing.

- Factory speed limits are set **per destination market** — e.g. 45 km/h (28 mph)
  for U.S. Class 3, 25 km/h for the EU/UK.
- The display itself can only enforce limits up to **50 km/h** (50 = unlimited).
  A figure above that cannot be enforced and must not be published.
- Where a model's compliant limit is not yet verified, publish **no number** at all.
  Invite the reader to test-ride. Do not publish a datasheet figure "for reference"
  and do not carry a number across from a previous revision.

## Certification claims

- State what a **component** is certified to. Do not say the **whole bike** is
  certified unless a whole-vehicle standard is actually held (e.g. UL 2849 for the U.S.).
- "Meets the Class 3 speed definition" ✅ · "Class 3 compliant" ❌ (unless UL 2849 held)
- A pending certification is never announced as a fait accompli.

## Contact details

The repo points to the website. Personal addresses and personal phone numbers are
not published here — they live on the site, where they can be changed without a
history rewrite.

---

## File Naming

```
Model_Type_Version.ext

Examples:
  S5_Photo_LeftFront.png
  S8-D_Photo_RightFrontSide.png
  S26-PRO-D_Spec_V2.1.pdf
  STARADIO_Brand_Framework_V2.0.html

Photo angles:
  LeftFront · RightFront · LeftFrontSide · RightFrontSide · LeftRearSide · RightRearSide

Versions:
  V0.x = Draft / internal
  V1.x = Released / client-facing
  V2.x = Major revision (spec change)
```

## Commit Convention

```bash
git commit -m "[Model/Area] Description"

Examples:
  git commit -m "[S5] Add 5-angle white-background product photos"
  git commit -m "[Specs] Publish S26-PRO-D spec sheet"
  git commit -m "[Brand] Add STARADIO brand framework"
  git commit -m "[Technical] Add battery BMS protocol #2 for 52V systems"
  git commit -m "[Assets] Add logo source files"
```

> Do not write a commit message that names a quotation, a factory price or a
> supplier contact — even in a private branch of this repo. Commit messages are
> part of the permanent public history.