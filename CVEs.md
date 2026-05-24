# CVE Details — NASchecker

This document lists all CVE vulnerabilities related to NASchecker research, organized into two sections:
1. **Pre-collected known CVEs** — used to guide NASchecker's fuzzing targets (Table I in paper)
2. **Discovered CVEs** — newly discovered and assigned through this research (Table X in paper)

---

## Part 1: Pre-collected Known CVEs in NAS Devices

These CVEs were collected from public databases to identify the most prevalent vulnerability types in commercial NAS devices, which guided the design of NASchecker's fuzzing strategies.

| Manufacturer | Vulnerability Type | CVE ID |
|-------------|-------------------|--------|
| Western Digital | Command Injection | [CVE-2025-30247](https://www.cve.org/CVERecord?id=CVE-2025-30247) |
| Synology | Command Injection | [CVE-2024-10443](https://www.cve.org/CVERecord?id=CVE-2024-10443) |
| D-Link | Command Injection | [CVE-2024-3273](https://www.cve.org/CVERecord?id=CVE-2024-3273) |
| QNAP | Command Injection | [CVE-2024-21900](https://www.cve.org/CVERecord?id=CVE-2024-21900) |
| Zyxel | Command Injection | [CVE-2023-27988](https://www.cve.org/CVERecord?id=CVE-2023-27988) |
| ASUS | Command Injection | [CVE-2022-4221](https://www.cve.org/CVERecord?id=CVE-2022-4221) |
| H3C | Improper Access Control | [CVE-2025-8907](https://www.cve.org/CVERecord?id=CVE-2025-8907) |
| Synology | Improper Access Control | [CVE-2024-29241](https://www.cve.org/CVERecord?id=CVE-2024-29241) |
| QNAP | Improper Access Control | [CVE-2024-21899](https://www.cve.org/CVERecord?id=CVE-2024-21899) |
| iptime | Memory Corruption | [CVE-2025-50464](https://www.cve.org/CVERecord?id=CVE-2025-50464) |
| Synology | Memory Corruption | [CVE-2024-5463](https://www.cve.org/CVERecord?id=CVE-2024-5463) |
| QNAP | Memory Corruption | [CVE-2021-38687](https://www.cve.org/CVERecord?id=CVE-2021-38687) |
| iptime | Cross-Site Request Forgery | [CVE-2022-23771](https://www.cve.org/CVERecord?id=CVE-2022-23771) |

> **Note:** CSRF (Cross-Site Request Forgery) was excluded from NASchecker's detection scope because most NAS devices only provide companion mobile/PC clients rather than web-based front-ends, making CSRF exploitation impractical.

---

## Part 2: Vulnerabilities Discovered by NASchecker

All vulnerabilities were discovered via protocol fuzzing or firmware reverse engineering (RE) on 12 commercial NAS devices. Device names are anonymized per responsible disclosure policy.

### Complete Vulnerability Table

| Device | Vuln Type | ID | Severity | Discovery Method | Firmware Lang |
|--------|----------|----|----------|-----------------|---------------|
| D1 | Improper Access Control | [CVE-2025-14220](https://www.cve.org/CVERecord?id=CVE-2025-14220) | Medium | Fuzzing | Go |
| D1 | Improper Access Control | [CVE-2025-69429](https://www.cve.org/CVERecord?id=CVE-2025-69429) | Medium | Fuzzing | Go |
| D2 | Command Injection | Vendor Confirmed | — | RE | C |
| D2 | Command Injection | Vendor Confirmed | — | Fuzzing | C |
| D2 | Command Injection | Vendor Confirmed | — | Fuzzing | C |
| D2 | Memory Corruption | Vendor Confirmed | — | Fuzzing | C |
| D2 | Improper Access Control | Vendor Confirmed | — | Fuzzing | C |
| D2 | Improper Access Control | Vendor Confirmed | — | Fuzzing | C |
| D3 | Command Injection | **[CVE-2025-14106](https://www.cve.org/CVERecord?id=CVE-2025-14106)** | **High** | Fuzzing | Go |
| D3 | Command Injection | **[CVE-2025-14107](https://www.cve.org/CVERecord?id=CVE-2025-14107)** | **High** | RE | Go |
| D3 | Command Injection | **[CVE-2025-14108](https://www.cve.org/CVERecord?id=CVE-2025-14108)** | **High** | RE | Go |
| D3 | Improper Access Control | [CVE-2025-69431](https://www.cve.org/CVERecord?id=CVE-2025-69431) | Medium | Fuzzing | Go |
| D4 | Command Injection | Vendor Confirmed | — | Fuzzing | Go |
| D4 | Command Injection | Vendor Confirmed | — | Fuzzing | Go |
| D4 | Command Injection | Vendor Confirmed | — | Fuzzing | Go |
| D4 | Improper Access Control | Vendor Confirmed | — | Fuzzing | Go |
| D5 | Command Injection | **[CVE-2025-14188](https://www.cve.org/CVERecord?id=CVE-2025-14188)** | **High** | RE | C++ |
| D5 | Memory Corruption | **[CVE-2025-14187](https://www.cve.org/CVERecord?id=CVE-2025-14187)** | **High** | Fuzzing | C++ |
| D5 | Improper Access Control | **[CVE-2025-14593](https://www.cve.org/CVERecord?id=CVE-2025-14593)** | **High** | Fuzzing | C++ |
| D6 | Command Injection | **[CVE-2025-14705](https://www.cve.org/CVERecord?id=CVE-2025-14705)** | **High** | Fuzzing | C |
| D6 | Command Injection | **[CVE-2025-14706](https://www.cve.org/CVERecord?id=CVE-2025-14706)** | **High** | Fuzzing | C |
| D6 | Command Injection | **[CVE-2025-14707](https://www.cve.org/CVERecord?id=CVE-2025-14707)** | **High** | Fuzzing | C |
| D6 | Memory Corruption | **[CVE-2025-14708](https://www.cve.org/CVERecord?id=CVE-2025-14708)** | **High** | Fuzzing | C |
| D6 | Memory Corruption | **[CVE-2025-14709](https://www.cve.org/CVERecord?id=CVE-2025-14709)** | **High** | Fuzzing | C |
| D6 | Improper Access Control | [CVE-2025-14703](https://www.cve.org/CVERecord?id=CVE-2025-14703) | Medium | Fuzzing | C |
| D6 | Improper Access Control | **[CVE-2025-14704](https://www.cve.org/CVERecord?id=CVE-2025-14704)** | **High** | Fuzzing | C |
| D7 | Command Injection | [CVE-2025-14184](https://www.cve.org/CVERecord?id=CVE-2025-14184) | Medium | Fuzzing | C |
| D7 | Improper Access Control | [CVE-2025-14183](https://www.cve.org/CVERecord?id=CVE-2025-14183) | Medium | Fuzzing | C |
| D8 | Command Injection | **[NVDB-CNVDB-2026287134](https://nvdb.org.cn/vuln/detail/CNVDB-2026287134)** | **High** | RE | C |
| D8 | Command Injection | **[NVDB-CNVDB-2026203824](https://nvdb.org.cn/vuln/detail/CNVDB-2026203824)** | **High** | Fuzzing | C |
| D8 | Command Injection | **[NVDB-CNVDB-2026117087](https://nvdb.org.cn/vuln/detail/CNVDB-2026117087)** | **High** | Fuzzing | C |
| D8 | Memory Corruption | [NVDB-CNVDB-2026982540](https://nvdb.org.cn/vuln/detail/CNVDB-2026982540) | Medium | Fuzzing | C |
| D8 | Memory Corruption | **[NVDB-CNVDB-2026621739](https://nvdb.org.cn/vuln/detail/CNVDB-2026621739)** | **High** | Fuzzing | C |
| D8 | Improper Access Control | [NVDB-CNVDB-2026573314](https://nvdb.org.cn/vuln/detail/CNVDB-2026573314) | Medium | Fuzzing | C |
| D9–D11 | Improper Access Control | [CVE-2025-14224](https://www.cve.org/CVERecord?id=CVE-2025-14224) | Medium | Fuzzing | Go |
| D9–D11 | Improper Access Control | [CVE-2025-69430](https://www.cve.org/CVERecord?id=CVE-2025-69430) | Medium | Fuzzing | Go |

**Totals:** 20 CVEs + 6 NVDB entries | 16 high-severity | 5 additional via firmware RE

### Statistics

| Vulnerability Type | Affected Devices | Total Entries |
|-------------------|-----------------|---------------|
| Command Injection | D2, D3, D4, D5, D6, D7, D8 (7 devices) | 14 |
| Memory Corruption | D2, D5, D6, D8 (4 devices) | 7 |
| Improper Access Control | D1–D11 (11 devices) | 15 |

| Firmware Language | Devices | Memory Corruption Issues |
|------------------|---------|--------------------------|
| Go | D1, D3, D4, D9, D10, D11 | **0** |
| C | D2, D6, D7, D8 | 4 |
| C++ | D5 | 1 |

> **Key Insight:** All 4 memory corruption findings occur exclusively in C/C++ firmware. The 6 devices written in Go exhibit zero memory corruption, corroborating Go's built-in memory safety guarantees.

---

## Part 3: Vulnerability Case Studies

### Case Study 1 — Command Injection (CVE-2025-14106)

**Device:** D3 (ZSP\*\*\*, model Q2C-AGQP)  
**Severity:** High  
**Discovery:** Protocol Fuzzing

**Description:**  
Device D3 exposes an interface to disable a user "safe box". The interface accepts a field `safe_dir` in the message that specifies the safe-box path. Although the implementation only checks that `safe_dir` has a required prefix, it does not validate or sanitize the remainder of the path. The value of `safe_dir` is directly concatenated into a system command (`umount`).

**Exploitation:**  
NASchecker appended a `reboot` command to the `safe_dir` field and re-sent the request message. The device rebooted immediately, confirming execution of the injected command.

**Root Cause:** Insufficient input sanitization — only prefix validation, no escaping of shell metacharacters.

---

### Case Study 2 — Memory Corruption / Heap Buffer Overflow (CVE-2025-14187)

**Device:** D5 (UGR\*\*\*, model DH2100+)  
**Severity:** High  
**Discovery:** Protocol Fuzzing

**Description:**  
When implementing the file backup creation interface of D5, the `path` field in the request message is optional. After allocating a heap buffer based on the sizes of mandatory fields (`id` and `alias`), the code appends `path` into the same buffer if it is present. This logic leads to a **heap buffer overflow** when `path` is excessively long.

**Exploitation:**  
NASchecker remotely triggered this overflow by sending an excessively long `path` value, crashing the NAS device.

**Root Cause:** The heap buffer was sized based only on mandatory fields, without accounting for the optional `path` field length.

---

### Case Study 3 — Improper Access Control / Directory Traversal (NVDB-CNVDB-2026573314)

**Device:** D8 (EZV\*\*\*, model CS-R5C-V100-8F)  
**Severity:** Medium  
**Discovery:** Protocol Fuzzing

**Description:**  
When implementing the file rename interface of D8, the developers fail to sanitize the directory traversal sequence `../` in the `old_name` and `new_name` fields. A normal user path (e.g., `/xxx/guest0/xxx`) can be rewritten (e.g., `/xxx/guest0/../admin/xxx`) to operate on an administrator's private file space.

**Exploitation:**  
After detecting the normal user identifier `guest0`, NASchecker constructs a directory traversal payload by combining it with the administrator identifier `admin`. After receiving a normal (200 OK) response, the existence of vertical privilege escalation is confirmed.

**Root Cause:** Missing input sanitization for path traversal sequences in file operation handlers.

---

### Case Study 4 — Command Injection via Paired Operations (CVE-2025-14184)

**Device:** D7 (SGA\*\*\*, model N1211DS)  
**Severity:** Medium  
**Discovery:** Protocol Fuzzing (Paired Operations)

**Description:**  
This vulnerability demonstrates **paired-operation command injection**. Creating a folder with a command injection payload does not execute the injected command. However, when *deleting* that folder, the command is executed immediately. The first operation (create) is a prerequisite for the second (delete), but only the second operation triggers the vulnerability.

**Exploitation:**  
NASchecker first injects a malicious command (e.g., `reboot`) into the folder name field during creation. Then it immediately sends the delete operation. If the device becomes unreachable, command injection is confirmed.

**Root Cause:** The delete handler constructs a system command incorporating the folder path without sanitization, while the create handler validates input independently.

---

## Part 4: Additional Vulnerability Context

### CVE-2025-14108 — Precondition-Dependent Command Injection

**Device:** D3 (ZSP\*\*\*, model Q2C-AGQP)  
**Severity:** High  
**Discovery:** Firmware Reverse Engineering

This vulnerability only triggers when a particular directory contains a `gocryptfs.conf` file. An attacker can create this file to exploit the flaw, but a standard protocol fuzzer lacks the context to predict or satisfy such preconditions — demonstrating why firmware reverse engineering is necessary to complement protocol fuzzing.

---

## Responsible Disclosure Timeline

| Device | Status | Notes |
|--------|--------|-------|
| D2 | Vendor Confirmed | Will assign CVEs after remediation |
| D3 | CVE Assigned | CVE-2025-14106, 14107, 14108, 69431 |
| D4 | Vendor Confirmed | Will assign CVEs after remediation |
| D5 | CVE Assigned | CVE-2025-14187, 14188, 14593 |
| D6 | CVE Assigned | CVE-2025-14703~14709 (7 CVEs) |
| D7 | CVE Assigned | CVE-2025-14183, 14184 |
| D8 | NVDB Assigned | 6 NVDB entries |
| D1, D9–D11 | CVE Assigned | Access control issues |
| Others | No response | CVE assignments obtained independently |
