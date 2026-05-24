# Case Studies

This document presents representative vulnerabilities discovered by NASchecker, corresponding to **Section IV-D3** of the paper. These cases demonstrate the effectiveness of NASchecker across its three detection methods: protocol fuzzing, paired-operation fuzzing, and firmware reverse engineering.

We have been assigned **20 CVE identifiers** and **6 NVDB identifiers** in total. Among them, 16 are high-risk vulnerabilities. The manufacturers of D2 and D4 have acknowledged the vulnerabilities and will assign CVE identifiers after remediation.

---

## Case 1 — Command Injection (CVE-2025-14106)

| Field | Detail |
|-------|--------|
| **Device** | D3 (ZSP\*\*\*, Q2C-AGQP, firmware V1.1.02) |
| **Severity** | High |
| **Type** | Command Injection |
| **Discovery** | Protocol Fuzzing |

**Vulnerability Description**

Device D3 exposes an interface to disable a user "safe box". The interface accepts a field `safe_dir` in the request message that specifies the safe-box path. Although the implementation only checks that `safe_dir` starts with a required prefix, it does not validate or sanitize the remainder of the path string. The value of `safe_dir` is directly concatenated into a system command `umount` without any escaping.

**Exploit Steps**

1. NASchecker captures the safe-box disable request during traffic collection.
2. It appends a `reboot` command to the `safe_dir` field using shell injection syntax (e.g., `; reboot`).
3. The modified request is re-sent to the device.
4. The device reboots immediately, confirming that the injected command was executed.

**Root Cause**

Only a prefix check is performed on `safe_dir`. The rest of the string passes unsanitized into a `system()`-style call, allowing arbitrary OS command execution by a remote authenticated user.

---

## Case 2 — Memory Corruption / Heap Buffer Overflow (CVE-2025-14187)

| Field | Detail |
|-------|--------|
| **Device** | D5 (UGR\*\*\*, DH2100+, firmware V4.2.0.601) |
| **Severity** | High |
| **Type** | Memory Corruption (Heap Buffer Overflow) |
| **Discovery** | Protocol Fuzzing |

**Vulnerability Description**

When implementing the file backup creation interface of D5, the `path` field in the request message is optional. The backend handler allocates a heap buffer whose size is calculated based only on the lengths of the two mandatory fields `id` and `alias`. If `path` is present in the request, its content is appended into the same fixed-size buffer. When `path` is excessively long, this logic leads to a heap buffer overflow, corrupting adjacent heap memory.

**Exploit Steps**

1. NASchecker identifies the backup creation interface and its optional `path` field during protocol analysis.
2. It constructs a request where `path` contains an excessively long string (far exceeding the buffer allocated for `id` + `alias`).
3. The modified request is sent to the device.
4. The NAS crashes (HTTP 502/503 or no response), confirming the heap overflow.

**Root Cause**

The buffer allocation does not account for the optional `path` field. The developer assumed `path` would either be absent or short, creating a length mismatch between the allocated buffer and the actual data written into it.

---

## Case 3 — Improper Access Control / Directory Traversal (NVDB-CNVDB-2026573314)

| Field | Detail |
|-------|--------|
| **Device** | D8 (EZV\*\*\*, CS-R5C-V100-8F, firmware V5.5.0) |
| **Severity** | Medium |
| **Type** | Improper Access Control (Vertical Privilege Escalation via Directory Traversal) |
| **Discovery** | Protocol Fuzzing |

**Vulnerability Description**

When implementing the file rename interface of D8, the developers fail to sanitize the directory traversal sequence `../` in the `old_name` and `new_name` request fields. Because NAS device storage paths embed user identifiers (e.g., `guest0`, `admin`) as path components, an attacker can escape their own directory and operate on another user's — or the administrator's — private file space.

For example, a normal user path:
```
/xxx/guest0/file.txt
```
can be rewritten as:
```
/xxx/guest0/../admin/file.txt
```
to access or overwrite files in the administrator's directory.

**Exploit Steps**

1. NASchecker detects the `guest0` identifier in the path field of a rename request.
2. It constructs a directory traversal payload: replaces `/guest0/` with `/guest0/../admin/`.
3. The modified request is sent with the original user's credentials.
4. The server returns a normal `200 OK` response, confirming that the operation succeeded on the administrator's files — vertical privilege escalation achieved.

**Root Cause**

The rename handler passes path fields directly to file system APIs (e.g., `rename`, `stat`) without stripping or rejecting `../` sequences, allowing path escape from the authenticated user's directory.

---

## Case 4 — Command Injection via Paired Operations (CVE-2025-14184)

| Field | Detail |
|-------|--------|
| **Device** | D7 (SGA\*\*\*, N1211DS, firmware V1.0.915) |
| **Severity** | Medium |
| **Type** | Command Injection (Paired Operations) |
| **Discovery** | Protocol Fuzzing |

**Vulnerability Description**

This case demonstrates a **paired-operation command injection** pattern. NAS devices support operations where one action is a prerequisite for another (e.g., create folder → delete folder). The first operation (create) validates input and does not trigger injection. The second operation (delete) constructs a system command from the stored folder name without re-validating it, triggering the injection.

Injecting a payload at the create stage stores a malicious folder name on the device. When the delete operation is subsequently performed, the stored payload is incorporated into a shell command and executed.

**Exploit Steps**

1. NASchecker labels message pairs (create/delete) during traffic collection.
2. It injects a `reboot` payload into the folder name field of the **create** request and sends it.
3. It immediately sends the **delete** request with the corresponding folder path.
4. If the device becomes unreachable (ping fails), command injection is confirmed.

**Root Cause**

The delete handler reconstructs a shell command using the folder name retrieved from storage, without sanitizing the stored value. Input validation at create time does not prevent the payload from being stored; validation at delete time is absent entirely.

> This pattern was inspired by SRFuzzer [ACSAC'19] and highlights a class of vulnerabilities that single-request fuzzers miss entirely.

---

## Case 5 — Precondition-Dependent Command Injection (CVE-2025-14108)

| Field | Detail |
|-------|--------|
| **Device** | D3 (ZSP\*\*\*, Q2C-AGQP, firmware V1.1.02) |
| **Severity** | High |
| **Type** | Command Injection (Precondition-Dependent) |
| **Discovery** | Firmware Reverse Engineering |

**Vulnerability Description**

This vulnerability only triggers when a specific directory on the NAS contains a `gocryptfs.conf` file. The command injection payload is activated by sending a request that references that directory; if the configuration file is absent, the vulnerable code path is never reached.

An attacker who knows the trigger condition can create the `gocryptfs.conf` file first (e.g., by uploading it), then send the crafted request to execute arbitrary commands.

**Why Protocol Fuzzing Alone Cannot Find This**

A standard protocol fuzzer mutates request fields but has no knowledge of the device's internal file system state. It cannot predict that:
1. A specific file must exist in a specific location, and
2. That file must be created before sending the exploit request.

Firmware reverse engineering was required to identify the vulnerable code path, understand the precondition, and construct the working exploit.

**Root Cause**

The handler reads `gocryptfs.conf` to determine a path, then incorporates that path into a system command without sanitization. The precondition gates access to the vulnerable code, making the bug invisible to black-box fuzzers without prior knowledge of the internal state.

---

## Summary

| Case | Device | CVE / ID | Type | Severity | Method |
|------|--------|---------|------|----------|--------|
| 1 | D3 | CVE-2025-14106 | Command Injection | High | Fuzzing |
| 2 | D5 | CVE-2025-14187 | Memory Corruption (Heap Overflow) | High | Fuzzing |
| 3 | D8 | NVDB-CNVDB-2026573314 | Improper Access Control (Dir. Traversal) | Medium | Fuzzing |
| 4 | D7 | CVE-2025-14184 | Command Injection (Paired Ops) | Medium | Fuzzing |
| 5 | D3 | CVE-2025-14108 | Command Injection (Precondition) | High | Firmware RE |

These five cases together illustrate why NASchecker combines **protocol fuzzing**, **paired-operation fuzzing**, and **firmware reverse engineering** — each method uncovers a distinct class of vulnerability that the others cannot.
