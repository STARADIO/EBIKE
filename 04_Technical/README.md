# Technical Documentation

We publish the interface protocols our own assembly and service teams work from.
These are the documents that explain how the battery, controller and instrument
talk to each other — useful to anyone integrating, diagnosing or reselling these
systems.

---

## Published Protocols

### Lithium Battery Protocol No.2

[`Lithium-Battery-Protocol-No2.md`](Motor_Controller/Lithium-Battery-Protocol-No2.md)

Battery management system to controller signalling. Covers:

- Status bit definitions across the controller status fields
- Fault code enumeration and interpretation
- Field-level parameter layout
- Power and ground reference conventions

This is the protocol behind the decimal status codes (0–13) shown on our colour
LCD displays.

### KM5S Protocol V1.0

[`KM5S-Protocol-V1.0.md`](Motor_Controller/KM5S-Protocol-V1.0.md)

Controller to instrument signalling.

---

## Why we publish these

A protocol document that only the factory has seen is a support ticket waiting to
happen. Publishing them means integrators and repair shops can work from the same
reference we do, and it means the protocol is versioned in the open.

---

## What is not here

Supplier specification sheets, bills of materials and factory test procedures are
commercial documents and are not published — see
[`PUBLISHING_RULES.md`](../PUBLISHING_RULES.md).

---

[Back to the main README](../README.md)