# Performance Analysis Results

This document presents detailed performance analysis results from NASchecker's evaluation of 12 commercial NAS devices.

---

## RQ2: Performance Optimization Methods Implemented in NAS Devices

**Answer:** None of the 12 NAS devices implement file compression or file deduplication. 7/12 implement file chunking; only 1/12 supports file bundling.

---

## Table 1: Summary of Optimization Methods per Device

| Device | File Compression | File Deduplication | File Chunking | File Bundling |
|--------|:---:|:---:|:---:|:---:|
| D1 | No | No | C \| 2 MB \| Single TCP | — |
| D2 | No | No | Whole file | Each file = 1 TCP |
| D3 | No | No | Whole file | Each file = 1 TCP |
| D4 | No | No | C \| 2 MB \| Single TCP | Each file = 1 TCP |
| D5 | No | No | C \| 2 MB \| Multi TCP | Each file = 1 TCP |
| D6 | No | No | — (HTTPS/pinning) | Each file = 1 TCP |
| D7 | No | No | C \| 10 MB \| Single TCP | **Multi-file bundled in 1 TCP** |
| D8 | No | No | Whole file | Each file = 1 TCP |
| D9 | No | No | C \| 2 MB \| Single TCP | — |
| D10 | No | No | C \| 2 MB \| Single TCP | — |
| D11 | No | No | C \| 2 MB \| Single TCP | — |
| D12 | No | No | Whole file | — |

**Legend:**
- `C` = file divided into chunks; `W` = whole file uploaded (no chunks)
- `Single TCP` = all chunks in one TCP connection; `Multi TCP` = multiple TCP connections
- `Each file = 1 TCP` = no bundling; `Multi-file bundled` = file bundling implemented
- `—` = undetermined (HTTPS/certificate pinning prevents analysis) or multi-file upload not supported

---

## Table 2: Traffic Size When Uploading Compressible Files (File Compression Test)

*All values show total packet size captured during upload of compressible (all-zero) files.*  
*None of the devices compress files (traffic size ≥ file size in all cases).*

| Device | 1 KB | 10 KB | 100 KB | 1 MB | 10 MB | 100 MB |
|--------|------|-------|--------|------|-------|--------|
| D1 | 2.7 KB | 12.1 KB | 106.2 KB | 1.0 MB | 10.7 MB | 106.0 MB |
| D2 | 1.9 KB | 11.1 KB | 105.6 KB | 1.0 MB | 10.4 MB | 105.1 MB |
| D3 | 2.5 KB | 12.0 KB | 106.4 KB | 1.0 MB | 10.4 MB | 105.3 MB |
| D4 | 3.3 KB | 12.8 KB | 107.3 KB | 1.0 MB | 10.5 MB | 105.0 MB |
| D5 | 3.1 KB | 12.6 KB | 107.0 KB | 1.0 MB | 10.5 MB | 106.1 MB |
| D6 | 1.5 KB | 11.0 KB | 108.6 KB | 1.0 MB | 10.5 MB | 104.9 MB |
| D7 | 3.6 KB | 11.9 KB | 106.4 KB | 1.0 MB | 10.4 MB | 105.3 MB |
| D8 | 1.9 KB | 11.2 KB | 111.5 KB | 1.0 MB | 10.5 MB | 105.0 MB |
| D9 | 2.7 KB | 12.1 KB | 106.2 KB | 1.0 MB | 10.6 MB | 107.9 MB |
| D10 | 2.9 KB | 12.1 KB | 106.3 KB | 1.0 MB | 10.6 MB | 104.8 MB |
| D11 | 2.7 KB | 12.1 KB | 106.3 KB | 1.0 MB | 10.6 MB | 105.1 MB |
| D12 | 4.4 KB | 13.7 KB | 108.2 KB | 1.0 MB | 10.5 MB | 105.4 MB |

> **Finding:** Traffic overhead exceeds file size for all devices and all sizes. No device compresses files before uploading.

---

## Table 3: Traffic Size — Original vs. Renamed File Upload (File Deduplication Test)

*`O` = original file upload; `R` = renamed file upload (same content). If deduplication were implemented, `R` traffic would be near-zero.*

| Device | | 1 KB | 10 KB | 100 KB | 1 MB | 10 MB | 100 MB |
|--------|--|------|-------|--------|------|-------|--------|
| D1 | O | 2.7 KB | 12.1 KB | 106.2 KB | 1.0 MB | 10.7 MB | 106.0 MB |
|    | R | 2.7 KB | 12.1 KB | 106.2 KB | 1.0 MB | 10.9 MB | 104.8 MB |
| D2 | O | 1.9 KB | 11.1 KB | 105.6 KB | 1.0 MB | 10.4 MB | 105.1 MB |
|    | R | 1.7 KB | 11.1 KB | 105.6 KB | 1.0 MB | 10.7 MB | 106.2 MB |
| D3 | O | 2.5 KB | 12.0 KB | 106.4 KB | 1.0 MB | 10.4 MB | 105.3 MB |
|    | R | 2.5 KB | 12.0 KB | 106.4 KB | 1.0 MB | 10.6 MB | 105.4 MB |
| D4 | O | 3.3 KB | 12.8 KB | 107.3 KB | 1.0 MB | 10.5 MB | 105.0 MB |
|    | R | 3.3 KB | 12.8 KB | 107.3 KB | 1.0 MB | 10.6 MB | 105.2 MB |
| D5 | O | 3.1 KB | 12.6 KB | 107.0 KB | 1.0 MB | 10.5 MB | 106.1 MB |
|    | R | 3.2 KB | 12.6 KB | 107.0 KB | 1.0 MB | 10.5 MB | 105.7 MB |
| D6 | O | 1.5 KB | 11.0 KB | 108.6 KB | 1.0 MB | 10.5 MB | 104.9 MB |
|    | R | 1.5 KB | 11.0 KB | 106.2 KB | 1.0 MB | 10.5 MB | 105.1 MB |
| D7 | O | 3.6 KB | 11.9 KB | 106.4 KB | 1.0 MB | 10.4 MB | 105.3 MB |
|    | R | 2.4 KB | 11.9 KB | 106.3 KB | 1.0 MB | 10.4 MB | 104.9 MB |
| D8 | O | 1.9 KB | 11.2 KB | 111.5 KB | 1.0 MB | 10.5 MB | 105.0 MB |
|    | R | 1.7 KB | 11.2 KB | 113.2 KB | 1.0 MB | 10.6 MB | 105.4 MB |
| D9 | O | 2.7 KB | 12.1 KB | 106.2 KB | 1.0 MB | 10.6 MB | 107.9 MB |
|    | R | 2.7 KB | 12.1 KB | 106.2 KB | 1.0 MB | 11.0 MB | 105.2 MB |
| D10 | O | 2.9 KB | 12.1 KB | 106.3 KB | 1.0 MB | 10.6 MB | 104.8 MB |
|     | R | 2.7 KB | 12.1 KB | 106.2 KB | 1.0 MB | 10.8 MB | 105.9 MB |
| D11 | O | 2.7 KB | 12.1 KB | 106.3 KB | 1.0 MB | 10.6 MB | 105.1 MB |
|     | R | 2.7 KB | 12.1 KB | 106.3 KB | 1.0 MB | 10.7 MB | 105.0 MB |
| D12 | O | 4.4 KB | 13.7 KB | 108.2 KB | 1.0 MB | 10.5 MB | 105.4 MB |
|     | R | 4.5 KB | 13.7 KB | 108.9 KB | 1.0 MB | 10.5 MB | 105.2 MB |

> **Finding:** Traffic size for renamed file upload (R) is nearly identical to original file upload (O) for all devices. No network-level deduplication is implemented. All devices except D3 support **local** (on-device) deduplication via GUI, but not network-level deduplication.

---

## Table 4: Heartbeat Traffic Between NAS Devices and Mobile Clients

| Device | Interval | Method | Request Size | Request Content | Response Size | Response Content |
|--------|----------|--------|-------------|-----------------|--------------|-----------------|
| D2 | 20 s | GET | 346 B | token/path/action | 651 B | path/time/status bit |
| D3 | 5 s | POST | 1,010 B | Cookie | 77 B | device_id/version/time |
| D4 | 2 s | POST | 439 B | method | 268 B | session/result/data |
| D5 | 10 s | PUT | 336 B | token/requestId | 466 B | data/msg |
| D6 | 10 s | PING | 74 B | — | 74 B | — |
| D7 | 10 s | GET | 210 B | — | 512 B | status/bit |
| D8 | 60 s | POST | 376 B | token/action/time | 526 B | time/code |
| D10 | 30 s | UDP Broadcast | 245 B | — | — | — |
| D11 | 30 s | UDP Broadcast | 245 B | — | — | — |

> **Notes:**
> - D1, D9, D12: No periodic heartbeat from mobile client to NAS device.
> - D6: Uses MQTT protocol (Ping Request/Response).
> - D10, D11: NAS device **actively broadcasts** UDP messages to mobile clients (client sends no response) — saves smartphone battery.
> - D4 has the shortest interval (2 s), generating ~0.52 J/min of radio-induced energy consumption.

---

## File Upload Procedures

### Single-Request Upload (3 devices)
D2, D3, D8 upload one file per HTTP POST request.

### Multi-Request Upload (8 devices)

| Device | Method | Details |
|--------|--------|---------|
| D1, D4, D9, D10, D11, D12 | Multiple POST | MIME form with content offset; single TCP connection |
| D4 | Multiple POST | Uses "First boundary"/"Last boundary" in MIME form for chunks |
| D5 | GET + POST | GET creates multipart session; POST sends chunks; GET ends session; **multiple TCP connections** |
| D7 | POST + PATCH | POST sends Base64-encoded metadata; PATCH sends content with `Upload-Offset`; `100 Continue` / `204 No Content` |

---

## File Download Procedure

All 12 devices use a similar download procedure: mobile client sends HTTP GET → NAS responds with file content.

| Status Code | Devices |
|-------------|---------|
| 206 Partial Content | D1, D7, D9, D10, D11 |
| 200 OK | D2, D3, D4, D5, D6, D8, D12 |

---

## File Status Synchronization Traffic

| Device | Sync Mechanism | Method | Request Size | Sync Interval |
|--------|---------------|--------|-------------|--------------|
| D2 | Polling | GET | 359 B | 20 s |
| D3 | Manual Refresh | POST | 1,251 B | — |
| D4 | Manual Refresh | POST | 460 B | — |
| D5 | Manual Refresh | POST | 407 B | — |
| D7 | Manual Refresh | POST | 479 B | — |
| D8 | Polling | GET | 352 B | 20 s |
| D9 | Manual Refresh | POST | 328 B | — |
| D10 | Manual Refresh | POST | 308 B | — |
| D11 | Manual Refresh | POST | 308 B | — |

> D6 and D12 use encrypted HTTPS/UDP (cannot analyze). D1 uses encrypted UDP.  
> **Recommendation:** Polling generates redundant traffic periodically — manual refresh is preferred.
