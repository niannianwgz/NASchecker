# Security Analysis Results

This document presents detailed security analysis results from NASchecker's evaluation of 12 commercial NAS devices.

---

## RQ3: Security Flaws (Passive Sniffing, Replay Attack, Improper Configurations)

**Answer:** 10/12 devices vulnerable to passive sniffing; 7/12 vulnerable to replay attacks; 8/12 have risky default configurations.

---

## Table 1: Passive Sniffing and Replay Attack Results (LAN)

| Device | Vulnerable to Passive Sniffing | Vulnerable to Replay Attack |
|--------|:---:|:---:|
| D1 | ✓ | ✓ |
| D2 | ✓ | ✓ |
| D3 | ✓ | ✗ |
| D4 | ✓ | ✓ |
| D5 | ✓ | ✓ |
| D6 | ✗ | ✗ |
| D7 | ✓ | ✗ |
| D8 | ✓ | ✗ |
| D9 | ✓ | ✓ |
| D10 | ✓ | ✓ |
| D11 | ✓ | ✓ |
| D12 | ✗ | ✗ |
| **Total** | **10/12** | **7/12** |

**Replay-attack-immune devices (explanations):**
- **D3:** Token-based mechanism prevents replaying download URLs.
- **D6:** Uses HTTPS with certificate pinning — MITM attack failed, URL extraction impossible.
- **D7:** Device doubles as a Wi-Fi router; attacker cannot obtain the Wi-Fi password, so replay attack failed.
- **D8:** Short-lived tokens make URL replay ineffective.
- **D12:** Uses HTTPS throughout — all traffic encrypted.

---

## Table 2: Improper Default Configurations

| Device | SMB Enabled by Default | Port 139 Open by Default | Port 445 Open by Default | Risky? |
|--------|:---:|:---:|:---:|:---:|
| D1 | ✓ | ✓ | ✗ | **✓** |
| D2 | ✗ | ✗ | ✗ | ✗ |
| D3 | ✗ | ✗ | ✗ | ✗ |
| D4 | ✓ | ✓ | ✗ | **✓** |
| D5 | ✓ | ✓ | ✓ | **✓** |
| D6 | ✗ | ✓ | ✓ | **✓** |
| D7 | ✗ | ✗ | ✗ | ✗ |
| D8 | ✗ | ✗ | ✗ | ✗ |
| D9 | ✓ | ✓ | ✗ | **✓** |
| D10 | ✓ | ✓ | ✗ | **✓** |
| D11 | ✓ | ✓ | ✗ | **✓** |
| D12 | ✓ | ✓ | ✓ | **✓** |
| **Total** | 7/12 | 8/12 | 3/12 | **8/12** |

**Risky devices:** D1, D4, D5, D6, D9, D10, D11, D12

> Enabling SMB or opening ports 139/445 by default exposes devices to SambaCry-style attacks (CVE-2017-7494) and StorageCrypt ransomware before users apply any security hardening.

---

## RQ4: Security Vulnerabilities (Command Injection, Memory Corruption, Improper Access Control)

**Answer:** 7/12 devices have command injection; 4/12 have memory corruption; 11/12 have improper access control.

---

## Table 3: Vulnerability Detection Results

| Device | Cmd Injection | Mem Corruption | Improper Access Ctrl | Firmware Lang |
|--------|:---:|:---:|:---:|:---:|
| D1 | ✗ | ✗ | ✓ | Go |
| D2 | ✓ | ✓ | ✓ | C |
| D3 | ✓ | ✗ | ✓ | Go |
| D4 | ✓ | ✗ | ✓ | Go |
| D5 | ✓ | ✓ | ✓ | C++ |
| D6 | ✓ | ✓ | ✓ | C |
| D7 | ✓ | ✗ | ✓ | C |
| D8 | ✓ | ✓ | ✓ | C |
| D9 | ✗ | ✗ | ✓ | Go |
| D10 | ✗ | ✗ | ✓ | Go |
| D11 | ✗ | ✗ | ✓ | Go |
| D12 | ✗ | ✗ | ✗ | — |
| **Total** | **7/12** | **4/12** | **11/12** | |

**Memory corruption by firmware language:**

| Language | Devices | Memory Corruption |
|----------|---------|:-----------------:|
| Go | D1, D3, D4, D9, D10, D11 (6 devices) | **0** |
| C | D2, D6, D7, D8 (4 devices) | **4** |
| C++ | D5 (1 device) | **1** |

> Go's built-in memory safety eliminates entire classes of buffer overflow, type confusion, and null pointer dereference vulnerabilities.

---

## Table 4: Passive Sniffing in WAN (Remote Upload/Download)

When mobile client and NAS device are on different LANs (WAN scenario):

| Device | Internet Layer | Transport Layer | Vulnerable to Passive Sniffing? |
|--------|---------------|----------------|:---:|
| D1 | IPv4 | UDP (encrypted) | ✗ |
| D2 | IPv6 | UDP (unencrypted) | **✓** |
| D3 | IPv4 | TCP (encrypted) | ✗ |
| D4 | IPv6 | UDP (encrypted) | ✗ |
| D5 | IPv4 | TCP (unencrypted) | **✓** |
| D6 | IPv4 | TCP (unencrypted) | **✓** |
| D7 | IPv4 | UDP (encrypted) | ✗ |
| D8 | IPv6 | UDP (unencrypted) | **✓** |
| D9 | IPv4 | UDP (encrypted) | ✗ |
| D10 | IPv4 | UDP (encrypted) | ✗ |
| D11 | IPv4 | UDP (encrypted) | ✗ |
| D12 | IPv4 | TCP (encrypted) | ✗ |

**Still vulnerable over WAN:** D2, D5, D6, D8 (4 devices)

> 7 devices (D1, D3, D4, D7, D9, D10, D11) encrypt WAN traffic. D1, D7, D9, D10, D11 use IPv4+UDP with encrypted payload; D3 uses IPv4+TCP encrypted; D4 uses IPv6+UDP encrypted.

---

## Firmware Reverse Engineering Findings

5 additional vulnerabilities were discovered exclusively through firmware reverse engineering (marked `RE` in [CVEs.md](../CVEs.md)):

| CVE | Device | Type | How RE Enabled Discovery |
|-----|--------|------|--------------------------|
| CVE-2025-14107 | D3 | Command Injection | Hidden backend interface not exposed to frontend traffic |
| CVE-2025-14108 | D3 | Command Injection | Precondition-dependent: only triggers when `gocryptfs.conf` exists in a specific directory |
| CVE-2025-14188 | D5 | Command Injection | Interface reachable only via firmware analysis, not captured traffic |
| NVDB-CNVDB-2026287134 | D8 | Command Injection | Interface not exposed to frontend; found via URI mapping in firmware |
| (D2) | D2 | Command Injection | Deep backend handler not reachable from captured traffic |

---

## Security Recommendations for Manufacturers

Based on the experimental findings, we provide the following actionable recommendations:

### 1. Encryption
- All communication between mobile clients and NAS devices must use **HTTPS or equivalent TLS** regardless of network topology (LAN or WAN)
- D2, D5, D6, D8 remain unencrypted even in WAN scenarios — immediate fix required

### 2. Authentication Token Security
- Tokens must incorporate **short-lived timestamps or cryptographic nonces** (expire within minutes)
- Long-validity tokens (as seen in D2) enable successful replay attacks even with token authentication

### 3. Default Configuration Hardening
- Adopt **"deny-all, enable-on-demand"** posture
- SMB must be **disabled by default**; ports 139 and 445 must be closed by default
- Only 4 devices (D2, D3, D7, D8) have safe default configurations

### 4. Input Validation
- Enforce robust sanitization for all user-controllable inputs
- Implement fine-grained access control to prevent horizontal and vertical privilege escalation
- Prohibit unsafe symlink handling for externally connected storage (USB drives)

### 5. Memory-Safe Firmware
- Migrate firmware to memory-safe languages (**Go** or **Rust**) to eliminate buffer overflow, type confusion, and null pointer dereference vulnerability classes
- All 6 Go-based firmware devices show zero memory corruption issues
