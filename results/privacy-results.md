# Privacy Analysis Results

This document presents detailed privacy leakage analysis results from NASchecker's evaluation of 12 commercial NAS devices.

---

## Background: PII Categories

NASchecker pre-collects PII values from two sources before analysis:

| Source | PII Types |
|--------|-----------|
| **Smartphone** | Phone Number (PN), MAC Address, IMEI, Serial Number (SN), Advertising ID (ADID), Location (LOC) |
| **NAS Device** | Serial Number (NAS SN), MAC Address (NAS MAC) |

All PII values are searched in both plain text and encoded forms (Base64, SHA256) within captured network traffic.

---

## RQ5: Privacy Leakage and Privacy Policy Compliance

**Answer:** 13 PII leakages detected across 6 devices. 5 leakages not disclosed in privacy policies → 4 devices with incomplete privacy policies.

---

## Table 1: PIIs in Request Messages Sent to Remote Servers

*These are PIIs transmitted FROM mobile clients/NAS devices TO remote servers.*

| Device | PII Type | Protocol | Destination Domain | Disclosed in Privacy Policy? |
|--------|---------|---------|-------------------|:---:|
| D2 | NAS SN | HTTPS | https://api.\*\*\*\*\*\*\*.cn | ✓ |
| D2 | NAS SN | HTTPS | https://toolkit.\*\*\*\*\*\*\*.cn | ✓ |
| D2 | NAS SN | HTTPS | https://api.\*\*\*\*\*\*\*.cn | ✓ |
| D3 | NAS SN | **HTTP** | http://forum.\*\*\*\*\*\*.cn | **✗** |
| D3 | NAS SN | **HTTP** | http://api.\*\*\*\*\*\*.cn | **✗** |
| D4 | NAS SN | HTTP | http://stat.\*\*\*\*\*\*.com | **✗** |
| D5 | NAS SN | HTTPS | https://cloud.\*\*\*\*\*\*\*.com | ✓ |
| D5 | ADID | HTTPS | https://cloud.\*\*\*\*\*\*\*.com | **✗** |
| D7 | NAS MAC | HTTPS | https://services.\*\*\*.\*\*\*.com | ✓ |
| D7 | NAS MAC | HTTPS | https://support.\*\*.com | ✓ |
| D7 | PN | HTTPS | https://support.\*\*.com | **✗** |
| D8 | NAS SN | HTTPS | https://api.\*\*\*\*\*\*\*.cn | ✓ |
| D8 | NAS SN | HTTPS | https://toolkit.\*\*\*\*\*\*\*.cn | ✓ |

**Summary:**
- Total leakages: **13**
- Undisclosed in privacy policy: **5** (D3×2, D4×1, D5 ADID×1, D7 PN×1)
- Devices with incomplete privacy policies: **4** (D3, D4, D5, D7)
- All leakages transmitted in plain text (no encoding)
- D3 is the only device transmitting NAS SN over **unencrypted HTTP** — a critical risk

**Notable cases:**
- **D5 (ADID):** Mobile client sends Android Advertising ID to `https://cloud.*******.com` for targeted advertisement delivery — not disclosed in privacy policy
- **D7 (Phone Number):** Mobile client sends user's phone number to `https://support.**.com` for user identity association in third-party service support — not disclosed in privacy policy

---

## Table 2: PIIs in Response Messages Sent by Remote Servers

*These are PIIs transmitted FROM remote servers BACK TO mobile clients/NAS devices (obtained during account registration or device pairing).*

| Device | PII Type | Protocol | Source Domain |
|--------|---------|---------|--------------|
| D2 | NAS SN | HTTPS | https://api.\*\*\*\*\*\*\*.cn |
| D2 | PN (Phone Number) | HTTPS | https://api.\*\*\*\*\*\*\*.cn |
| D2 | **IMEI** (Base64-encoded) | HTTPS | https://ulogs.\*\*\*\*\*.com |
| D3 | NAS SN | HTTPS | https://api.\*\*\*\*\*\*\*\*\*\*\*.net |
| D4 | NAS SN | HTTPS | https://siot-nas.\*\*\*\*\*\*.com.cn |
| D5 | NAS MAC | HTTPS | https://cloud.\*\*\*\*\*\*\*\*\*\*\*.com |
| D5 | PN (Phone Number) | HTTPS | https://cloud.\*\*\*\*\*\*\*\*\*\*\*.com |
| D7 | NAS SN | HTTPS | https://services.space.\*\*\*\*\*\*.com |
| D7 | **NAS MAC** (Base64-encoded) | HTTPS | https://services.space.\*\*\*\*\*\*.com |
| D7 | PN (Phone Number) | HTTPS | https://services.space.\*\*\*\*\*\*.com |

**Summary:**
- Total server-to-client PII transmissions: **10** across 5 devices
- All transmitted over HTTPS
- **2 PIIs encoded** with Base64: D2's IMEI and D7's NAS MAC
- **1 undisclosed in privacy policy:** D3's NAS SN (received from `https://api.***********.net`)

---

## Privacy Compliance Analysis

| Device | Privacy Policy Status |
|--------|----------------------|
| D1 | ✓ Compliant (no PII leakage detected) |
| D2 | ✓ Compliant (all leakages disclosed) |
| D3 | **✗ Non-compliant** — NAS SN leaked via HTTP, not in privacy policy |
| D4 | **✗ Non-compliant** — NAS SN leaked to analytics server, not in privacy policy |
| D5 | **✗ Non-compliant** — ADID sent for advertisement targeting, not in privacy policy |
| D6 | ✓ Compliant (no PII leakage detected) |
| D7 | **✗ Non-compliant** — Phone number sent to third-party support service, not in privacy policy |
| D8 | ✓ Compliant (all leakages disclosed) |
| D9–D12 | ✓ Compliant (no PII leakage detected) |

**4 devices with incomplete privacy policies: D3, D4, D5, D7**

Such omissions constitute direct violations of privacy regulations including:
- **GDPR** (General Data Protection Regulation)
- **PIPL** (China's Personal Information Protection Law)

---

## Most Frequently Leaked PII Types

| PII Type | # Devices Leaking (Request) | # Devices Leaking (Response) |
|----------|:---:|:---:|
| NAS Serial Number (NAS SN) | 4 | 4 |
| Phone Number (PN) | 1 | 3 |
| NAS MAC Address | 1 | 2 |
| IMEI | 0 | 1 |
| Advertising ID (ADID) | 1 | 0 |

---

## Privacy Recommendations for Manufacturers

1. **Minimize PII collection:** Mobile clients and NAS devices must not transmit non-anonymized PII to remote servers unless strictly necessary for declared service functionality.

2. **Disclose all transmissions:** When PII transmission is unavoidable, explicitly disclose in the privacy policy: data types involved, recipient entities, and intended processing purposes.

3. **Encrypt all PII transmissions:** D3 transmits NAS SN via unencrypted HTTP — all PII must be transmitted over HTTPS.

4. **Integrate automated compliance tools:** Integrate tools like PoliCheck into release pipelines to detect undisclosed data flows before firmware reaches end users.

5. **Third-party service transparency:** Clearly disclose when PII is shared with third-party analytics, advertising, or support services (as in D5 and D7 cases).
