# Export formats

*AleksControllerTester 1.0.0. Every completed Quick Test is saved automatically in
`Documents\AleksControllerTester\Sessions`: a session file `<id>.json`, a `<id>_metrics.csv` next to it, and the
run's raw report lines in `raw\<id>_<capture>.jsonl`. **Export report** writes a Markdown report (default folder
`Documents\AleksControllerTester\Reports`). All of it stays on your PC.*

## `_metrics.csv` — schema `ct-2`

One header line, one data row per file. Numbers use the invariant culture (`.` decimal separator) regardless of the
Windows display language. The schema is a string (`ct-2`); the `product` column names the writer. Each change after
this first release is a new `ct-N`; columns are only ever appended.

| # | column | notes |
|---:|---|---|
| 1 | `schema_version` | `ct-2` |
| 2 | `product` | always `AleksControllerTester` |
| 3 | `timing_claim` | always `host_delivery_not_device_polling` |
| 4 | `timestamp_basis` | `Stopwatch host timestamp` |
| 5 | `valid_dt_policy` | `exclude intra-batch and buffered intervals` |
| 6–8 | `protocol,duration_s,target_hz` | test identity; `target_hz` is the report rate the controller is **set** to (firmware / companion app), `0` = not set |
| 9 | `connection` | `UsbCable` / `UsbReceiver` / `Bluetooth` / `Virtual`; **empty = not known** (never `Unknown` as data) |
| 10 | `connection_provenance` | `auto` (classified from the device tree) / `manual` (picked by the user); empty when `connection` is empty |
| 11–12 | `controller_model,controller_mode` | free text from the setup (quoted when needed, line breaks folded to spaces) |
| 13 | `input_source` | backend of the headline timing metrics (`RawInputHid`; the delivery score only ever comes from it); empty = not recorded |
| 14–19 | `T_nom_ms,f_nom_Hz,median_ms,IQR_ms,p01_ms,p99_ms` | core timing |
| 20–27 | `double_pct,outlier_pct,overall_ev_s,N,valid_dt,buffered_pct,max_batch,timing_valid` | rates / delivery integrity |
| 28–29 | `drain_errors,corrupt_headers` | capture integrity |
| 30–35 | `changed_reports,identical_reports,channel_changes,stick_active_reports,changes_s,changed_pct` | activity: reports with a channel change, reports that changed nothing at all (not even the report bytes), the sum of individual channel changes, reports with either stick beyond 12 % of its range, changed reports per second, changed reports as % of `N` |
| 36–45 | `testEnvironment.profile,gameRunning,gameName,fpsCap,overlaysEnabled,powerProfile,usbPortNotes,pollingRateHz,backgroundLoadNotes,claimScope` | measurement regime and environment (`testEnvironment.profile`: `Unknown` / `HostBaseline` / `DesktopLoad` / `GamingLoadStress`) |
| 46 | `environment_interpretation` | how to read a run of that regime |
| 47–51 | `p95_ms,p99_9_ms,max_gap_ms,gap_count,burst_count` | tail and gap metrics |
| 52 | `delivery_score` | the 1–10 headline, **empty unless the run has a valid delivery score** — only a Stick Motion run has one (never a sentinel like `1.0`) |
| 53 | `score_status` | `Valid` / `InvalidLowSamples` / `InvalidCaptureIntegrity` / `InvalidMultiDevice` / `NotScored` (a valid capture of a test without a delivery headline: Idle Drift, Button Burst, Trigger Sweep, Stick Range, Manual capture); empty when no score report exists |
| 54 | `score_algorithm_version` | the score formula's version (`controller-delivery-score-v1`); scores are comparable only between rows with the same value here |
| 55 | `invalid_reasons` | pipe-joined (`CaptureIntegrity|LowSamples`), empty when valid or not scored |
| 56 | `device_event_counts` | event counts per Raw Input HID stream (input source + device), semicolon-joined, descending (`79000;1026`). **Empty = unverified** — never to be read as "1 device". XInput events are never counted here |
| 57 | `report_mode` | `Streaming` / `ChangeDriven` / `Mixed` / `Unknown` (a `ChangeDriven` verdict from a moving-stick test is hedged); empty = not measured |
| 58–59 | `quality_protocol,quality_algorithm_version` | the input-quality report's test (`StickMotion`, `IdleDrift`, …) and version (`controller-quality-v1-provisional`) |
| 60–62 | `quality_sticks_avg,quality_triggers_avg,quality_buttons_avg` | plain average of each group's rated rows (1–10, **provisional** thresholds); empty when the group has no rated row |
| 63–64 | `quality_rated_rows,quality_not_rated_rows` | how many rows were rated, and how many failed their gate ("not rated") |
| 65 | `stick_coverage_passed` | Stick Motion coverage gate (`true` / `false`); empty for the other tests |
| 66 | `latency_method` | the method of every `latency_*` value in this row: `polling_estimate` (host-side, lower bound); empty when the row carries no latency value — **a latency value is never exported without its method** |
| 67–69 | `latency_report_wait_mean_ms,latency_report_wait_p99_ms,latency_worst_case_ms` | lower bounds from the valid HID intervals (Stick Motion): mean added wait `Σdt²/(2Σdt)`, its 99th percentile, the largest valid interval |
| 70–71 | `latency_button_polling_avg_ms,latency_button_polling_worst_ms` | the polling component of button results (+T_nom/2, +T_nom), with T_nom from this run (streaming pads) or the latest Stick Motion run of the same controller |
| 72 | `xinput_sampling_mode` | the XInput mode the run polled with (`Off` / `Efficient` / `Precise`); empty = not recorded |
| 73–75 | `xinput_events,xinput_f_nom_Hz,xinput_missed_packets` | secondary XInput statistics of the linked slot (change events, modal packet rate, packets the poller missed); display only, never scored |
| 76 | `cross_backend_method` | `cross_backend_relative` whenever `cross_backend_delta_median_ms` has a value |
| 77–79 | `cross_backend_delta_median_ms,cross_backend_matched_edges,cross_backend_leader` | Button Burst on an Xbox-compatible pad with XInput Precise: median first-detection delta (XInput − HID; a relative delivery-path difference, **not input latency**), matched button edges, which path saw them first (`raw_input_hid` / `xinput` / `neither_within_quantization`) |
| 80 | `path_agreement_ratio` | HID changed reports ÷ XInput packet advances (Stick Motion with the XInput cross-check); outside 0.90–1.10 the HID compatibility collection misses states XInput sees |

### Semantics guarantees

- `delivery_score`, `score_status` and `invalid_reasons` come from the **same** validity rule the saved session uses —
  the CSV and the session can never disagree on validity.
- A score value is never written for an invalid run: treat an empty `delivery_score` with a non-`Valid`
  `score_status` as "not evaluable", not as zero.
- `drain_errors` counts native read failures of both the per-message read (`GetRawInputData`) and buffered drains
  (`GetRawInputBuffer`); `corrupt_headers` counts malformed records. Reports of device types the capture does not
  measure are skipped and count in neither. Either counter above zero makes the run invalid (capture integrity).

## Session JSON (`<id>.json`)

- `"schema_version": 100` and `"product": "AleksControllerTester"`. A file of any other product is refused on load
  and never pruned or deleted.
- Members are absent when not measured or not recorded — never a guessed default.

### Setup

| member | notes |
|---|---|
| `PollingTarget` | the report rate the controller is set to, `0` = not set |
| `ConnectionType` | `Unknown` / `UsbCable` / `UsbReceiver` / `Bluetooth` / `Virtual` |
| `ConnectionProvenance` | `ReadOk` = detected from the device tree at run start, `Manual` = picked by the user, `Unknown` = neither |
| `ControllerModel`, `ControllerMode`, `Notes` | free text |

### `device` — the measured controller

Absent when no single controller was selected ("All controllers"). Members: `vendorId`, `productId`, `versionNumber`
(the HID release number, not necessarily the firmware), `usagePage`, `usage`, `isXInputCompat` (the Xbox-compatible
`IG_` HID path), `linkedXInputSlot` (0..3, absent when not linked), `xinputVendorId` / `xinputProductId` (what
`XInputGetCapabilitiesEx` reported for the linked slot — often a generic 045E:02FF), `usbVendorId` / `usbProductId`
(the physical USB device behind a compatibility collection, e.g. the real pad behind 045E:02FF; absent for Bluetooth,
behind a wireless receiver, or when unknown), `mappingProfile` (`Generic` / `XInputCompat` / `Sony` / `Nintendo`),
`connectionType`, `deviceId` (an anonymized 16-hex id — never the device path) and `axes`: one entry per HID value
with `usagePage`, `usage`, `logicalMin`, `logicalMax`, `bitSize` and the `channel` it feeds (`LX`, `LY`, `RX`, `RY`,
`LT`, `RT`, `LT+RT` for the combined trigger axis of an `IG_` collection, `Hat`; empty = unmapped).

- `buttonAliases` (absent = none): the **Assign buttons…** mapping the run was measured with, one
  `{"button": "Extra2", "target": "L5"}` per assigned extra button (`Extra0`..`Extra7` → `L4`, `R4`, `L5`, `R5`,
  `Share`, `Guide`, `Touchpad` or `Mute`). The run's events carry the target button.
- `wgi` (absent = no Windows.Gaming.Input pad linked): the gamepad linked for the live view when the run started, e.g.
  `{"vendorId": 1118, "productId": 2816, "paddles": ["L4", "R4", "L5", "R5"], "share": false, "rawButtonCount": 15}`.
  Reference only: Windows.Gaming.Input never feeds a run's events, metrics or scores.

### `systemContext` — the PC, read before the run (never during it)

Windows build and display version, CPU, power plan, the controller's connection classified from the device tree,
the USB port (USB connections only), the Bluetooth radio's IDs (Bluetooth only), controller software present
(`SteamRunning`, `ViGEmBusPresent`, `HidHidePresent`, `GameInputSvcPresent`), and inventory values
(`VbsConfigured`, `HvciConfigured` — configured, not proven running; `EnergySaverActive`, `OnBattery`,
`ProcessArchitecture`, `OsArchitecture`, `IsEmulated`). `Provenance` records how each value was read; absent = not
captured, never "off". None of these enter a score.

### Runs

| member | notes |
|---|---|
| `ProtocolId` | `StickMotion` / `IdleDrift` / `ButtonBurst` / `TriggerSweep` / `StickRange` / `ManualCapture` |
| `ScoreStatus` | as `score_status` above; a `NotScored` run stores no delivery score |
| `source` | backend of the headline timing metrics (`RawInputHid`) |
| `Metrics` | the delivery statistics of the CSV, plus the activity counts |
| `reportMode` | `Streaming` / `ChangeDriven` / `Mixed` / `Unknown` |
| `quality` | the input-quality report: `AlgorithmVersion`, `Protocol`, `Source`, `Rows` (`Group`, `Metric`, `Channel`, `Name`, `ValueText`, `ValueTextInvariant`, `Value`, `Unit`, `Status` = `scored` / `informational` / `not_rated`, `Score`, `Provisional`, `Note`, `Method`, `Claim`), `GroupAverages`, `Notes` and the detail records (`ReportMode`, `Latency`, `ButtonPolling`, `CrossBackend`, `PathAgreement`, `StickMotion`, `StickIdle`, `StickRange`, `TriggerRest`, `TriggerSweep`, `ButtonBurst`, `ButtonIdle`). The stored `Value`s are the observation; the scores are re-derived under the current provisional thresholds whenever a run is shown or exported. On an Xbox-compatible (`IG_`) pad linked to its XInput slot, Trigger Sweep and Idle Drift read the triggers from XInput (8-bit): the row `trigger_source` says `via XInput (8-bit)`, `Raw Input HID (combined LT/RT axis)` or `Raw Input HID` |
| `xinputMetrics` | the XInput stream's delivery statistics; display only |
| `crossBackend` | the first-detection delta (`Method` = `cross_backend_relative`) |
| `latency` | the polling-latency lower bounds (`Method` = `polling_estimate`, `Claim` = `host-side, lower bound`) |
| `xinputSamplingMode`, `xinputStatistics` | the XInput mode during the run and the linked slot's poll statistics (`pollsPerSecond`, `quantizationUs`, `missedPackets`, …) |
| `preRollMs` | the discarded warm-up right before the measured window (500 ms) |
| `gcInWindow` | garbage collections of the tester's own process during the measured window (expected `0`) |
| `wgiFootprint` | `"WGI not loaded"` or `"WGI loaded (idle)"`: whether Windows.Gaming.Input was in the tester's process while it measured (its thread is stopped for every measurement) |

The session's `ScoreAlgorithmVersion` is `controller-delivery-score-v1`. `graphTrace` holds up to 8000 points with
`t, dt, v, b`, the sticks `lx, ly, rx, ry` (−32767..32767, +Y = up), the triggers `lt, rt` (0..65535) and `btn`
(button bits).

## Raw report lines (`raw\<id>_<capture>.jsonl`)

One JSON object per captured report (schema 2): `buttons, lx, ly, rx, ry, lt, rt, hat, present, changed, reportId,
packetNumber, packetDelta, prevObservationTicks, deviceClock`, the `inputSource` (`RawInputHid` / `XInput`) and the
`hostTimestampKind` (one report per message, several reports in one message, a buffered drain, an XInput poll).
`deviceHandleHash` is anonymized like `deviceId`.

## Markdown report (**Export report**)

The setup and device rows, the PC context, and per run the delivery metrics, the input-quality table
(`Group | Metric | Value | Status | Score | Method | Note`) and the controller details. Its header states the timing
claim and that latency values are host-side lower bounds or relative differences, each with its method. An empty
cell means "not recorded" — never a guessed value.
