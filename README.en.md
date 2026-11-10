# AstrBot Heart Monitor

**Language: [简体中文](README.md) | English**

> **Status: Planning — not installable yet.** This repository does not currently contain a working AstrBot plugin. Real-device BLE interoperability, AstrBot APIs, and WebUI extension compatibility have not been validated. Do not treat it as a released health monitoring product.

A planned native AstrBot plugin that receives heart-rate broadcasts over Windows Bluetooth Low Energy (BLE), enables authorized bot queries, and displays trends and history in the AstrBot WebUI.

## Planned features

- **Continuous collection:** Automatically connect to the paired device when AstrBot starts, independent of chat requests or an open browser.
- **Live dashboard:** Latest BPM, receive timestamp, connection status, and data freshness.
- **History:** SQLite raw samples retained for **90 days**, longer-lived minute/hour summaries, and CSV export.
- **WebUI:** Live dashboard, history and analysis, device management, and settings.
- **Bot integration:** Commands, authorized read-only AI tools, and chart responses.
- **Privacy by default:** Only the explicitly configured owner's QQ user ID can query data; deny access if no owner is configured; no automatic group sharing.
- **Reliability:** Reconnection, stale-data detection, lifecycle cleanup, and failure isolation from AstrBot and NapCat.

These are **requirements**, not implemented or verified features.

## Planned V1.0 support

| Item | Scope |
| --- | --- |
| Device | Xiaomi Smart Band 9 with heart-rate broadcast enabled |
| Host | AstrBot running natively on Windows |
| BLE | Standard Heart Rate Service (0x180D) and Measurement (0x2A37), subject to real-device verification |
| Storage | Local SQLite |
| Messaging | QQ/NapCat through AstrBot |
| Docker, Linux, macOS | Not supported or guaranteed in V1.0 |
| Other wearables | Untested; no compatibility claim |

**Note:** Finding the band in a BLE scan does not prove its GATT heart-rate notifications can be subscribed to. Actual firmware and hardware behavior must be tested.

## Privacy and data principles

1. Server-side access control based on a configured QQ user ID; prefer private chats and do not expose personal measurements in group conversations by default.
2. AI tools are strictly read-only and authorization-checked; no arbitrary database statements or device control.
3. Never commit personal measurements, device identifiers, accounts, tokens, or local databases.
4. Retain raw data for **90 days** by default. Generate aggregates before expiry. Missing data stays missing, not 0 BPM.
5. Clearly distinguish fresh measurements, stale readings, and disconnection.
6. For personal information only; not for diagnosis, emergency monitoring, or medical decisions.

## Roadmap

| Phase | Goal | Status |
| --- | --- | --- |
| 0 | Validate AstrBot plugin APIs, Windows BLE, and Smart Band 9 broadcasts | Pending |
| 1 | Plugin lifecycle and basic BLE collection | Not started |
| 2 | Continuous collection, reconnection, SQLite, 90-day retention | Not started |
| 3 | AstrBot WebUI dashboard and history | Not started |
| 4 | Owner-only QQ commands and read-only AI tools | Not started |
| 5 | Privacy, security, reload, and long-running validation | Not started |
| 6 | Prepare AstrBot marketplace submission after acceptance | Not started |

See the [English requirements](docs/REQUIREMENTS.en.md) for more detail.

## Installation

**There is no installable release and this plugin is not listed in the AstrBot marketplace.** Do not install this repository as a finished plugin. Verified installation instructions, AstrBot compatibility, firmware requirements, and screenshots will be added after testing.

## Feedback

Design proposals and compatibility reports are welcome via GitHub Issues. Redact QQ identifiers, tokens, BLE addresses, and personal heart-rate records from logs.

## License

[MIT License](LICENSE) © 2026 Polarzen
