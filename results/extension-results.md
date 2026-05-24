# Extension: Applying NASchecker to Other IoT Devices

This document presents results from applying NASchecker's security and privacy detection modules to Vehicle-to-Everything (V2X) and Small Office/Home Office (SOHO) IoT devices, demonstrating the framework's extensibility beyond NAS devices.

---

## Overview

To evaluate the extensibility of NASchecker, we applied the **security and privacy detection modules** to analyze:
- **5 V2X devices** (On-Board Diagnostics / OBD devices)
- **8 SOHO devices** (router, camera, gateway, mesh box, smart hub, smart plug, doorbell)

> **Note:** Protocol fuzzing is not applicable to these devices since it mainly targets NAS file operations, which are not involved in other IoT devices. Only traffic analysis and privacy detection were applied.

**Traffic categories analyzed per device:**
1. Traffic between devices and their companion apps
2. Traffic between devices and their remote servers
3. Traffic between apps and remote servers

---

## RQ6: Can NASchecker Analyze Other IoT Devices?

**Answer:** Yes. NASchecker successfully identified security and privacy issues in all tested V2X and SOHO devices by analyzing network traffic and privacy policies.

---

## Table 1: Security and Privacy Results for V2X and SOHO Devices

### V2X Devices (On-Board Diagnostics)

| ID | Vendor | Type | Model | Passive Sniffing | Replay Attack | Leaked PII | In Privacy Policy? |
|----|--------|------|-------|:---:|:---:|---|:---:|
| V1 | HVD\*\*\* | OBD | Ultra | ✓ | ✗ | PN (Phone Number) | **✗** |
| V2 | MIN\*\*\* | OBD | D9 | ✗ | ✗ | LOC (Location) | ✓ |
| V3 | NEW\*\*\* | OBD | N50-M60 | ✓ | ✓ | LOC (Location) | ✓ |
| V4 | AUT\*\*\* | OBD | Base | ✗ | ✗ | — | — |
| V5 | WAN\*\*\* | OBD | — | ✓ | ✗ | LOC (Gaode) | **✗** |
|    |          |     |   |   |   | LOC (Tencent) | ✓ |

### SOHO Devices

| ID | Vendor | Type | Model | Passive Sniffing | Replay Attack | Leaked PII | In Privacy Policy? |
|----|--------|------|-------|:---:|:---:|---|:---:|
| S1 | Z\*E | Router | E2631 | ✓ | ✓ | SN | ✓ |
| S2 | TEN\*\*\* | Camera | CP3 | ✓ | ✗ | UUID | ✓ |
|    |          |        |     |   |   | Product_Key | **✗** |
| S3 | TPL\*\*\* | Doorbell | TL-DB54H | ✗ | ✗ | — | — |
| S4 | XIA\*\*\* | Smart Plug | ZNCZ301KK | ✗ | ✓ | DeviceID | ✓ |
|    |           |            |           |   |   | UserID | **✗** |
| S5 | ORA\*\*\* | Mesh Box | X1 | ✓ | ✓ | SN | ✓ |
|    |           |          |    |   |   | NetworkID | ✓ |
| S6 | XIA\*\*\* | Smart Hub | BE6500 Pro | ✗ | ✓ | DeviceID | ✓ |
|    |           |           |            |   |   | UserID | **✗** |
| S7 | H\*C | Gateway | UR7103 | ✓ | ✓ | PN (Phone Number) | ✓ |
|    |      |         |        |   |   | SN | ✓ |
|    |      |         |        |   |   | ProjectID | **✗** |
|    |      |         |        |   |   | RegionID | **✗** |
| S8 | MER\*\*\* | Camera | MIPC252W | ✗ | ✗ | — | — |

---

## Summary Statistics

### Security (V2X + SOHO Combined)

| Threat | V2X Devices Affected | SOHO Devices Affected |
|--------|:---:|:---:|
| Passive Sniffing | 3/5 (V1, V3, V5) | 4/8 (S1, S2, S5, S7) |
| Replay Attack | 1/5 (V3) | 5/8 (S1, S4, S5, S6, S7) |

### Privacy

| Category | V2X | SOHO |
|----------|:---:|:---:|
| Devices leaking PII | 4/5 | 6/8 |
| Devices with undisclosed PII leakage | 2/5 (V1, V5) | 4/8 (S2, S4, S6, S7) |

---

## Notable Findings

### V2X Privacy Issues
- **V1 (HVD\*\*\*):** App transmits user **phone number** to a third-party service without disclosure in privacy policy.
- **V5 (WAN\*\*\*):** App sends geolocation to Gaode Maps (undisclosed) but also to Tencent (disclosed). Dual third-party sharing with inconsistent disclosure.
- **V3:** Vulnerable to replay attack — replayed URL response contained navigation destination consistent with original traffic.

### SOHO Privacy Issues
- **S4 (XIA\*\*\* Smart Plug):** Privacy policy claims UserID is only used for device sharing, but the app sends UserID to the server during other operations — a policy **misrepresentation**.
- **S7 (H\*C Gateway):** Leaks 4 PII types; 2 of them (ProjectID, RegionID) are undisclosed.
- **S2 (TEN\*\*\* Camera):** Product_Key sent to remote server — not mentioned in privacy policy.

### SOHO Replay Attack Findings
Successful replay attacks allow attackers to perform multiple unauthorized actions:
- Turn off smart plug (S4)
- Turn on indicator lights (S4)
- Control gateway settings (S7)
- Access router configuration (S1)

---

## Limitations of Extension

- **Protocol fuzzing not applicable:** Fuzzing module targets NAS-specific file operation protocols and cannot be applied to general IoT devices.
- **Performance analysis not applicable:** Performance optimization detection (chunking, deduplication, etc.) is NAS-specific.
- The extension validates that NASchecker's **traffic analysis** and **privacy compliance** modules are broadly applicable to IoT devices beyond NAS.
