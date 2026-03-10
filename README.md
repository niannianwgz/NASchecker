# NASchecker

**NASchecker** is the first systematic black-box measurement framework for automatically identifying performance, security, and privacy issues in commercial NAS (Network-Attached Storage) devices.

> Associated with the paper: *"NASchecker: Automatically Identifying the Performance, Security, and Privacy Issues of NAS Devices"*, under review at IEEE Internet of Things Journal.

> **Note**: The full source code will be released upon paper acceptance.

---

## Overview

NASchecker analyzes NAS devices from three dimensions without requiring access to device internals or APIs:

- **Performance**: Infers whether the device implements file compression, deduplication, chunking, and bundling by comparing network traffic against crafted test files.
- **Security**: Detects passive sniffing vulnerabilities, replay attack susceptibility, and misconfiguration risks via traffic analysis; discovers command injection, memory corruption, and improper access control vulnerabilities via protocol fuzzing and firmware reverse engineering.
- **Privacy**: Identifies personally identifiable information (PII) leaked in network traffic and checks compliance against the device's privacy policy.

---

## Framework Architecture

NASchecker consists of three modules:

**1. Traffic Collection Module**
Performs automated file upload and download operations using UI Automator, captures plaintext traffic via tcpdump, and decrypts HTTPS traffic using a mitmproxy-based MITM setup. Heartbeat, background synchronization, and management traffic are also collected during idle periods.

**2. Performance Analysis Module**
Compares the size of captured traffic against crafted test files (varying in size from 1 KB to 100 MB and in content type: compressible vs. incompressible) to infer whether compression, deduplication, chunking, and bundling are implemented.

**3. Security & Privacy Analysis Module**
- *Security flaw detection*: Checks for unencrypted transmission (passive sniffing), lack of token expiry (replay attack), and risky default configurations (open SMB, ports 139/445).
- *Vulnerability detection*: Performs protocol fuzzing targeting command injection, memory corruption, and improper access control; supplements with firmware reverse engineering to uncover deep-seated vulnerabilities not reachable via traffic alone.
- *Privacy compliance*: Searches for PII (phone number, IMEI, NAS serial number, NAS MAC, ADID, geolocation) in traffic—both plaintext and encoded (e.g., Base64, SHA-256)—and cross-checks against the device's privacy policy using a customized PoliCheck.

---

## Toolchain

| Module                          | Tool                          |
|---------------------------------|-------------------------------|
| Traffic capture                 | tcpdump                       |
| HTTPS interception (MITM)       | mitmproxy                     |
| UI automation (upload/download) | UI Automator2                 |
| Offline traffic analysis        | Wireshark / Fiddler           |
| Replay attack verification      | Python requests               |
| Port & misconfiguration scan    | Nmap                          |
| Protocol fuzzing                | Custom Python script          |
| Firmware unpacking              | Binwalk                       |
| Firmware reverse engineering    | Ghidra / IDA Pro              |
| Privacy compliance analysis     | PoliCheck (customized)        |

---

## Evaluated Devices

We evaluated NASchecker on 12 commercial NAS devices purchased between June 2024 and November 2025. All devices were running the latest firmware available at the time of purchase. Device identities are anonymized; refer to Table IV of the paper for the full manufacturer-to-device mapping.

| Device ID | Manufacturer | Firmware Version  | Android Client |
|-----------|--------------|-------------------|----------------|
| D1        | ORI\*\*\*    | V1.9.12-1         | io.\*\*\*\*.mobile |
| D2        | HIK\*\*\*    | V4.4.3            | com.\*\*\*\*.histor |
| D3        | ZSP\*\*\*    | V1.1.02           | com.\*\*\*\*.zspace |
| D4        | LEN\*\*\*    | V5.5.6            | com.\*\*\*\*.smartpan |
| D5        | UGR\*\*\*    | V4.2.0.601        | com.\*\*\*\*.nas |
| D6        | SGW\*\*\*    | V2.0.11           | com.\*\*\*\*.box |
| D7        | SGA\*\*\*    | V1.0.915          | com.\*\*\*\*.sgai |
| D8        | EZV\*\*\*    | V5.5.0            | com.\*\*\*\*.histor |
| D9        | YOT\*\*\*    | V1.9.12-1         | io.\*\*\*\*.mobile |
| D10       | YOT\*\*\*    | V1.9.12-1         | io.\*\*\*\*.mobile |
| D11       | YOT\*\*\*    | V1.2.9-1          | io.\*\*\*\*.mobile |
| D12       | SYN\*\*\*    | DSM 7.2.2-72806   | com.\*\*\*\*.dsdrive |

---

## Key Findings

### Performance

| Optimization     | Implemented by (out of 12) |
|------------------|---------------------------|
| File Compression | 0                         |
| File Deduplication | 0                       |
| File Chunking    | 7 (chunk size: 2 MB or 10 MB) |
| File Bundling    | 1                         |

### Security

| Security Issue                        | Affected Devices |
|---------------------------------------|-----------------|
| Passive Sniffing (LAN)                | 10 / 12         |
| Passive Sniffing (WAN)                | 4 / 12          |
| Replay Attack                         | 7 / 12          |
| Improper Default Configuration        | 8 / 12          |
| Command Injection                     | 7 / 12          |
| Memory Corruption                     | 4 / 12          |
| Improper Access Control               | 11 / 12         |

**CVE / NVDB Assignments**: 20 CVEs and 6 NVDB entries in total, of which **15 are rated high severity**.

Selected CVEs:

| Vulnerability Type    | CVE ID              | Severity |
|-----------------------|---------------------|----------|
| Command Injection     | CVE-2025-30247      | High     |
| Command Injection     | CVE-2025-14106      | High     |
| Command Injection     | CVE-2025-14107      | High     |
| Command Injection     | CVE-2025-14108      | High     |
| Memory Corruption     | CVE-2025-14187      | High     |
| Memory Corruption     | CVE-2025-14188      | High     |
| Improper Access Control | CVE-2025-14593    | High     |

### Privacy

| Finding                                              | Count |
|------------------------------------------------------|-------|
| Total PII leakages detected                          | 13    |
| PII leakages not disclosed in privacy policies       | 5     |
| Devices with incomplete privacy policies             | 4     |
| PII types leaked (NAS SN, NAS MAC, PN, ADID, IMEI)  | 5     |

---

## Citation

If you use NASchecker in your research, please cite:

```bibtex
@article{ren2026naschecker,
  title     = {{NASchecker}: Automatically Identifying the Performance,
               Security, and Privacy Issues of {NAS} Devices},
  author    = {Ren, Guangyue and Wang, Jincheng and Yu, Le and Han, Liping
               and Hu, Mingzhe and Chen, Wei and Liu, Tingting and
               Luo, Xiapu and Sun, Guozi},
  journal   = {IEEE Internet of Things Journal},
  year      = {2026}
}
```

---

## License

This project is released under the [MIT License](LICENSE).
