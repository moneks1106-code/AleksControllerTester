## AleksControllerTester v1.0.0 — First release

**A free Windows tool for game controllers: how evenly your controller's reports
actually arrive in Windows, how clean its sticks, triggers and buttons are — and
honest about what those numbers mean. Tested on hardware with one controller so
far; results from other controllers are very welcome.**

Many testers show a polling-rate number. AleksControllerTester timestamps every
Raw Input HID report the moment Windows hands it over and shows how steady the
intervals are: gaps, stutters, and reports Windows delivered in batches instead
of one by one. Next to that it checks the inputs themselves — stick drift and
noise at rest, stick range and circularity, trigger range and resolution,
button chatter. It is built on the measurement core of AleksMouseTester and runs
side by side with it.

### Highlights

- **How evenly reports arrive, not just how many.** Every Raw Input HID report
  is timestamped on arrival, including reports that changed nothing. Reports
  that share a timestamp (batched or buffered delivery) are counted and shown,
  but never turned into fake 0 ms intervals.
- **A score that rewards stability.** The 1–10 delivery score rewards the
  highest *stable* rate, not the biggest nominal number: a modal "1000 Hz" with
  frequent gaps is not stable 1000 Hz delivery. It comes only from Stick Motion,
  while both sticks keep moving; an invalid run shows "—", never a number.
- **Change-driven or streaming?** The tool detects whether your pad sends
  reports constantly or only when something changes — an idle gap on a
  change-driven pad isn't a stutter.
- **Latency, honestly.** Host-side lower bounds from the measured intervals
  (average and 99th-percentile wait), and on Xbox-compatible pads which Windows
  input path (Raw Input HID or XInput) saw a button press first — a relative
  path difference, not input lag. Never an invented "input lag" number.
- **Sticks, triggers, buttons.** Centre offset, idle noise, drift, circularity,
  outer range, centre repeatability and resolution; trigger range, effective
  bits and start deadzone; press durations, mash rate and chatter.
- **Live Controller page** with every button, both sticks with trails, both
  triggers and the report rate per Windows input path; Scan picks the
  controller you touch, and with only one controller connected it is selected
  for you.
- **Fair comparisons.** Every run is saved locally; Compare ranks a run only
  against runs of the same controller, connection, settings, PC, load and test,
  and says why it left others out.
- **Listen-only and offline.** No drivers, no rumble, no firmware or settings
  changes, no network access, no telemetry.

### What it measures

| Quick Test | You do | You get |
|---|---|---|
| **Stick Motion** (10 / 20 / 30 s) | rotate both sticks in full circles | the delivery score, report rate, jitter, gaps, batching, latency lower bounds |
| **Idle Drift** (10 / 30 / 60 s) | put the controller down | stick noise, centre offset and drift, trigger rest noise, ghost presses |
| **Button Burst** (10 s) | tap A as fast as you can | press durations, mash rate, chatter; on Xbox-compatible pads the HID vs XInput first-detection delta |
| **Trigger Sweep** (15 s) | pull the triggers slowly, fully | range reached, resolution (effective bits), start deadzone |
| **Stick Range** (20 s) | circle each stick along its edge | circularity, outer range, centre repeatability, resolution |
| **Manual capture** | start / stop | delivery statistics without a score |

Also in 1.0.0: **Advanced / Lab** (every metric with explanations and graphs),
**Timeline** (sticks, triggers and buttons over the delivery intervals, with
**Show longest gap**), **measurement regimes** (Host Baseline / Desktop Load /
Gaming Load Stress, never mixed in Compare), **Export report** (Markdown), every
Quick Test auto-saved as session JSON plus `_metrics.csv` (see EXPORT_SCHEMA.md),
**Separate LT/RT** for Xbox-style pads, **Assign buttons…** for extra buttons of
generic pads, button labels (Xbox / PlayStation / Nintendo / Generic), and a
read-only **power-saving check**.

What it does **not** measure: the controller's internal scan or USB polling,
end-to-end button-to-screen latency, or a verdict on your controller. Quality
thresholds are provisional.

### Tested hardware

- **GameSir G7 HE**, USB cable, Xbox-compatible HID path (`IG_`) with XInput, on
  one Windows 11 PC. Stick Motion ran many times (1000 reports/s); Idle Drift,
  Button Burst, Trigger Sweep and Stick Range ran on earlier development builds.

That is the whole hardware verification so far.

### Known limitations

- **One controller tested.** The PlayStation (DualSense / DualShock 4),
  Nintendo (Switch Pro) and generic D-input profiles are implemented but not yet
  verified on hardware; neither is **Assign buttons…** with a real pad that has
  extra buttons, nor the one-click **Separate LT/RT** button.
- **Connections:** Bluetooth, 2.4 GHz receivers / dongles and USB hubs are not
  yet tested; the Xbox Wireless Adapter detection and its XInput opt-in are not
  yet verified on hardware.
- **Xbox Elite back paddles** (read through Windows.Gaming.Input, live view
  only) are not yet verified on a real Elite controller, and they show only
  while the tester's window is in front (a Windows rule). The Xbox Series
  **Share** button is not available yet.
- **Keep the tester in front while a test runs** — input while another window
  has focus is not yet verified.
- Two or more controllers at once, virtual controllers (Steam Input,
  DS4Windows / ViGEm), 8 kHz pads, Windows 10, Windows on Arm and other Windows
  display languages are untested.

### System requirements

- Windows 11 or Windows 10, 64-bit (x64). Tested on Windows 11 only; Microsoft
  supports .NET 10 on Windows 11 24H2 and newer, older versions are expected to
  work but are untested. Windows on Arm runs the x64 build under emulation (not
  validated).
- Self-contained: the .NET 10 runtime is included — nothing to install. Unzip
  the whole folder and run `AleksControllerTester.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected
  your PC" — click **More info → Run anyway**. With Smart App Control switched on,
  Windows blocks the app (there is no "Run anyway"); I don't recommend turning
  it off for this tool.

### Verify the download

- The zip's **SHA-256** is on this release page (not inside the zip). Check it in
  PowerShell: `Get-FileHash .\AleksControllerTester-v1.0.0-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or
  WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET
  runtime and the pinned obfuscation-tool version.
- The hash proves you got the exact, untampered file — not that the program is
  audited. You can also scan the zip on VirusTotal.

### Feedback welcome

This is a first release, and every quality threshold is provisional until
results from many controllers exist. If you measure a controller, please open
an issue with the controller model, the connection and mode, your Stick Motion
result and the exported Markdown report (**Export report**). Bugs and odd
results are just as welcome.
