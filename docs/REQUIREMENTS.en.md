# AstrBot Heart Monitor V1.0 Requirements and Acceptance Specification

English | [简体中文](REQUIREMENTS.zh-CN.md)

**Status: approved planning baseline; no implementation or real-device acceptance has been completed. This document specifies targets, not verified capabilities.**

## 1. Scope and language policy

- Target: Xiaomi Smart Band 9. The user has confirmed a heart-rate broadcast option exists.
- **Fixed A+B architecture:** Channel A collects live heart rate through BLE inside AstrBot running natively on Windows; channel B receives historical data from a separate, non-root Android Companion.
- Retain the official Mi Fitness app without APK modifications or Android rooting.
- The server is a native AstrBot plugin. No separate Windows desktop client or second resident web server is required. The Companion is a separately distributed Android APK.
- **AstrBot WebUI is Simplified Chinese only.** No English WebUI and no language switch. Bot replies and configuration labels default to Chinese; bilingual Bot/configuration UI is not a V1.0 requirement.
- **GitHub-facing documentation is bilingual Chinese and English**: paired READMEs, requirements, and future installation/release/contributor documents. An English Android Companion UI is optional, not mandatory.
- Raw heart-rate samples are retained for **90 days**. Only the explicitly bound owner may query health data through private Bot chats; deny by default.
- AstrBot patch-level updates in the same major version should be checked by API capabilities rather than pinned to one exact patch number; major upgrades warrant stricter review.

## 2. Architecture and failure isolation

Smart Band 9 → (A) Windows BLE GATT → AstrBot Core; Smart Band 9 → Mi Fitness → Health Connect → Android Companion → protected upload API → AstrBot Core. Both share SQLite, authorization, analytics, WebUI and Bot integration.

- A and B have independent lifecycle and health states; failure of either must not block the other, AstrBot, or NapCat.
- Exactly one BLE connection manager. WebUI, Bot and AI tools only read cache/database, not open competing BLE connections.
- Live display prioritizes A; delayed historical readings from B never masquerade as current readings.
- Missing Windows BLE disables A only; B ingestion remains available. Missing Companion does not block A.
- Store normalized UTC timestamps with provenance and validity information.

## 3. Channel A: Windows BLE live heart rate

- Prefer Bleak or a reviewed compatible library. Validate Heart Rate Service `0x180D`, Measurement `0x2A37`, and actual GATT notifications before claiming compatibility. A broadcast menu alone is not a successful test.
- Implement device scanning and explicit binding; connect/subscribe; parse standard BPM; continuously collect; queue and commit samples; detect notification stalls; retry with backoff; recover from sleep; shut down cleanly.
- States: Disabled, Scanning, Connecting, Subscribing, Streaming, Stale, Reconnecting, Unavailable, Error.
- Persist only genuine valid device notifications. Never invent samples, write missing data as 0 BPM, or claim a host receive timestamp equals exact sensor measurement time.
- Proposed defaults: stale after 15 seconds; reconnect delay starts at 2 seconds and backs off to 60 seconds with jitter. Tune by device tests.
- Post-notification software performance targets: P95 ≤1 second to durable storage, P95 ≤2 seconds to WebUI; neither implies a 1 Hz device sampling guarantee.
- Test Mi Fitness coexistence, broadcast persistence, out-of-range recovery, Windows sleep, and hot-reload cleanup with no duplicate collectors.
- Reuse upstream code only after reviewing its license and maintenance; avoid an external Windows HR application.

## 4. Channel B: Android historical sync

- Ship a separate Android Companion APK using authorized Health Connect APIs; no root, Mi Fitness private-data access, or injected hooks.
- In-scope where actually shared by Mi Fitness: steps, sleep intervals/stages, exercise, distance, energy expenditure and workout heart rate. SpO2, stress and all-day HR without verified accessible sources remain extension points, not required V1.0 outputs.
- Components: permission management, source adapter, per-data-type incremental cursors, scheduler, persistent offline queue, pairing credential manager, transport client, status/errors.
- Proposed defaults: periodic sync requested every 30 minutes (OS may delay); launch/manual sync; network recovery; recheck recent 48 hours.
- Configurable offline queue target of 7 days; never silently drop unacknowledged data. Handle queue saturation, permission revocation and token invalidation visibly.
- Background/historical Health Connect access must be guarded by device capability checks and explicit user permission.
- Upload in authenticated HTTPS batches to AstrBot and process durable acknowledgments. Do not expose an unprotected public HTTP endpoint.

## 5. Protocol and canonical records

- Data transport protocol versioned independently, initially `1.0`, regardless of plugin or APK version.
- Functions: initial pairing, device authentication, batch upload, durable acknowledgment, sync status, credential revocation and protocol compatibility negotiation.
- Common fields: record_id, device_id, source, metric_type, value/unit or structured interval, measured_at, optional measured_until, received_at, quality, schema_version, optional source_updated_at.
- Within one source use stable identifiers for idempotency; across sources do not delete different real measurements solely because values or timestamps match.
- Quality: Valid, Suspect, Invalid; live read state additionally Stale, Missing. Historical B heart-rate data must not replace a live A reading.
- Preserve sleep/exercise intervals and stages. Normalize to UTC, localize display time only.

## 6. Storage and retention

- SQLite in AstrBot's persistent plugin data directory, never source control; indexes, bounded transactions, appropriate WAL, migrations and recoverable backups.
- Entities: devices, raw heart rate, sleep, exercise, steps, extensible metrics, daily summaries, sync jobs/cursors, device events, migration versions.
- Raw heart rate **90 days**; proposed sleep/steps/exercise 365 days, other raw health metrics 90 days, connection logs 30 days; long-lived summaries remain deletable. Proposed durations can be frozen before acceptance.
- Daily bounded expiry work should not stall acquisition; commit necessary aggregates before expiry. Reports expose sample counts and coverage gaps.

## 7. WebUI: Chinese only

- Six sections: **Overview, Live Monitoring, History, Sync Center, Device Management, System Settings** (these names describe the Chinese UI sections; on-screen labels are Chinese).
- All user-facing WebUI headings, menus, buttons, errors, chart annotations and help text must be in Simplified Chinese. Universal technical terms, units and protocol identifiers may remain in their standard form.
- **No language switcher, auto locale detection, or English WebUI translation resources** in V1.0.
- Show live BPM/freshness, independent BLE and Companion status, last success timestamps, gaps, ranges, source filters and CSV/JSON export.
- Closing the browser cannot stop sampling. Reconnecting the UI event stream must not spawn another BLE collector.
- Verify actual supported AstrBot Views/API rather than modifying the Dashboard core. Authorize every backend API.

## 8. Bot authorization and security

- Only the explicitly configured owner's QQ identity may query health records in private chat; unbound/other/group requests are denied by default.
- Commands cover current heart rate, HR history, daily health, sleep, steps, exercise, weekly reports and device status. AI tools are read-only and recheck identity at the data access boundary.
- No arbitrary SQL, AI-triggered BLE controls or public group health results. Stale measurements must carry their timestamps.
- One-time Companion pairing, independent revocable credentials, protected transport, payload limits; authorize WebUI administration and data export.
- Logs/repository must not contain personal measurements, QQ identities, BLE addresses or secrets. This project is for personal reference, not diagnosis or emergency monitoring.

## 9. Phases and gates

| Phase | Deliverable | Acceptance gate |
| --- | --- | --- |
| 0 | Repository/upstream license review, AstrBot API/device test plan | No unnecessary rewrite or unlicensed copying |
| 1 | Windows BLE adapter | Valid real Smart Band 9 notifications, reconnection and verified Mi Fitness coexistence |
| 2 | SQLite core and lifecycle | Reproducible 90-day expiry, reboot/reload without lost committed data or duplicate collectors |
| 3 | **Chinese-only** WebUI and owner-only Bot | Real live/history display, authorization, full Chinese UI coverage |
| 4 | Android Companion APK | Real authorized Health Connect data, offline queue and recovery |
| 5 | A+B integration | Isolated channels, traceable provenance, idempotency and non-duplicated statistics |
| 6 | Release preparation | 72-hour real-device test, privacy/security, bilingual GitHub docs, compatibility/license review |

Record environment, procedure, expected/actual outcome, and evidence for every test. Distinguish simulations, automated tests, and physical-device validation.

**Release gate:** Do not claim unsupported metrics or platforms, or mark planning work as implemented. Keep the repository in planning status until verified evidence supports an actual release.
