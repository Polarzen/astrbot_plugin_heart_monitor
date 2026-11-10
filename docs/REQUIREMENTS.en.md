# Heart Monitor V1.0 Requirements

English | [简体中文](REQUIREMENTS.zh-CN.md)

**Status: design baseline, not implemented or hardware-validated.**

## Architecture
A single native AstrBot plugin owns exactly one BLE connection manager. The WebUI, commands, and AI tools read from cache or SQLite and never initiate independent BLE connections. Acquisition failures must not block AstrBot startup or NapCat.

## Device and collection
- Initial device: Xiaomi Smart Band 9; initial host: native Windows.
- Validate Heart Rate Service 0x180D and Measurement 0x2A37 through actual GATT notifications before relying on them.
- Auto-connect at startup, continuously collect, retry with backoff after disconnect, and support manual pause.
- Handle connection failures, notification stalls, hot reload, and clean shutdown separately.
- A host receive timestamp is not necessarily the wearable's measurement timestamp.
- Preserve gaps: never invent samples, interpolate as real readings, or turn missing heart rate into 0 BPM.

## Storage and analysis
- SQLite stores raw readings, sessions, connection events, and minute/hour aggregates.
- Retain raw readings for 90 days, minute summaries for a proposed 365 days, and hourly summaries long-term.
- Ensure aggregates are committed before deleting expired raw samples.
- Use time indexes and bounded batch writes and queries.
- Report sample counts and coverage, and label sample-mean calculations accurately.
- Enable date-filtered historical queries and CSV export.
- Store databases in AstrBot's persistent plugin data directory, never in source control.

## WebUI
- Four sections: Live, History, Devices, Settings.
- Display BPM, last notification timestamp, connection status, 5/30/60-minute windows, and missing-data gaps.
- Provide date filtering, aggregation selection, CSV export, device diagnostics, binding, and controlled connect/disconnect.
- Authorize each management API server-side; collection continues when the WebUI is closed.
- Validate supported plugin view APIs against the actual AstrBot installation; do not patch the Dashboard core.

## QQ and AI## Internationalization (i18n)
- V1.0 must support Simplified Chinese (zh-CN) and English (en) for the WebUI, configuration labels, status messages, and bot responses.
- WebUI defaults to the browser/AstrBot language and supports a persistent manual override, with a deterministic fallback for untranslated strings.
- Bot responses use the configured language by default, with a preference for the authorized user.
- Keep translations separate from data logic; localize date/number display without altering collection or storage.


- Only a configured owner's QQ user ID can query. Deny by default when unconfigured.
- Return private measurements in direct messages; do not post them to group chats by default.
- Planned commands: current BPM, connection status, interval statistics, history, trend charts.
- AI tools must be read-only and perform server-side authorization, never arbitrary SQL or BLE control.
- Never present stale readings as live or give medical diagnoses.

## Acceptance criteria
1. Receive real, valid heart-rate notifications from the Smart Band 9.
2. Pass a 24-hour stability test without crashing AstrBot.
3. No duplicate BLE collectors after plugin reload.
4. Reconnection works and BLE failure does not break other plugins.
5. The 90-day expiry process is reproducible and preserves aggregates.
6. Unauthorized QQ, AI, and WebUI users cannot access private data.
7. No measurements, device identifiers, or credentials are committed to Git or routine logs.
8. Validate actual AstrBot APIs, dependencies, and marketplace submission requirements before release.

**Release gate:** Publish an installable version only after these checks pass; do not claim untested wearables are compatible.
