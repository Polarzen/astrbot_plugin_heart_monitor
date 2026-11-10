# AstrBot Heart Monitor

**Language: [简体中文](README.md) | English**

> **Status: Planning; not installable yet.** This repository does not yet contain a fully validated plugin or Android APK. The following are design targets, not claims of completed features.

An AstrBot health-monitoring project for the **Xiaomi Smart Band 9**, with a confirmed **A+B dual-channel architecture**:

- **A — Live heart rate via Windows BLE:** The native Windows AstrBot plugin subscribes to heart-rate notifications while the band's heart-rate broadcast mode is enabled. The standard Heart Rate Service `0x180D` and Measurement characteristic `0x2A37` are the initial validation targets. Continuous collection, stale-data handling, and reconnection are planned.
- **B — Historical health data via Android:** Keep the official Mi Fitness app unchanged. A separate **Android Companion**, without root access, reads authorized Health Connect records where actually shared and securely uploads them to AstrBot.
- **Unified core:** SQLite, provenance-aware deduplication, historical statistics, a Chinese-only WebUI, and owner-only QQ/Bot queries. Either channel should fail independently.

## Language policy (finalized for V1.0)

- **AstrBot WebUI: Simplified Chinese only**. No UI language switch or English WebUI is required.
- **GitHub-facing documentation: bilingual Chinese and English**, including paired READMEs and requirement specifications; installation, release, and contributor-facing documentation should have both languages when introduced.
- Bot replies and plugin configuration labels default to Simplified Chinese; bilingual Bot/settings output is **not a V1.0 acceptance requirement**.
- The Android Companion UI defaults to Chinese; an English Companion UI is optional for a later release and is not part of the GitHub documentation policy.

## Planned V1.0 capabilities

| Area | Target |
| --- | --- |
| Live heart rate | Windows BLE GATT notifications, continuous monitoring, staleness, reconnection |
| Historical health | Android Companion, Health Connect, incremental sync, offline queue and retry |
| Historical metrics | Steps, sleep, exercise, distance, energy, and exercise heart rate where Mi Fitness actually shares them |
| Not guaranteed | Daily non-exercise heart rate, SpO2, stress, or any inaccessible Mi Fitness data |
| Storage | SQLite; raw heart-rate samples retained for **90 days** |
| WebUI | **Simplified Chinese only**: Overview, Live, History, Sync, Devices, Settings |
| QQ / AI | Read-only queries by the explicitly bound owner in private chat, with server-side authorization |
| Reliability | Channel isolation, lifecycle cleanup, retries, safe duplicate handling |

**Validation boundary:** A heart-rate broadcast option on the band does not by itself prove successful continuous Windows GATT notifications, simultaneous Mi Fitness connectivity, or long-term stability. Health Connect availability likewise does not establish that Mi Fitness shares every metric.

## Deliverables

1. **Native AstrBot plugin:** One Windows BLE manager, an Android ingestion endpoint, SQLite, Chinese-only WebUI, Bot integration and access control.
2. **Separate Android Companion APK:** Authorized reads, offline storage, active upload over protected connections; no root and no modification of Mi Fitness.
3. **Reuse-first implementation:** Prefer Bleak and standard BLE APIs. Inspect upstream code and licenses before reusing; do not require an external Windows heart-rate desktop application or copy code with unclear permission.

## Compatibility, privacy and safety

- Initial device: Xiaomi Smart Band 9; channel A targets native Windows; channel B ingestion should not depend on Windows BLE.
- No untested compatibility claims for other bands, firmware, or BLE acquisition on Linux/Docker.
- Keep the database in AstrBot persistent storage, not in the repository.
- Only the explicitly bound owner can retrieve health data via private Bot chat; deny by default until configured. Enforce WebUI/API permissions and separate Companion authentication with protected transport.
- Missing or stale data must never be presented as live measurements.
- Personal reference only; not for diagnosis or emergency medical monitoring.

## Roadmap

| Phase | Goal | Status |
| --- | --- | --- |
| 0 | Repository/licensing review, AstrBot API and device validation plan | Not accepted |
| 1 | Windows BLE live collection and reconnection | Not started |
| 2 | SQLite, lifecycle and retention | Not started |
| 3 | Chinese-only WebUI and owner-only Bot | Not started |
| 4 | Android Companion and Health Connect sync | Not started |
| 5 | A+B integration, provenance and fault isolation | Not started |
| 6 | 72-hour stability, security and release review | Not started |

[中文需求规格](docs/REQUIREMENTS.zh-CN.md) · [Full English requirements](docs/REQUIREMENTS.en.md)

## Installation and release

**No verified installable release or marketplace listing is available yet.** Installation instructions and an Android APK will follow device, permission, reliability and compatibility validation.

Issues are welcome. Redact health records, QQ identifiers, BLE addresses and credentials from diagnostic material.

[MIT License](LICENSE) © 2026 Polarzen
