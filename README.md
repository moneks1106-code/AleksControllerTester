# AleksControllerTester

**A free Windows tool that measures how _evenly_ your game controller's reports actually arrive in Windows, checks its sticks, triggers and buttons — and is honest about what that means.**

Many controller testers show a polling-rate number. AleksControllerTester timestamps every Raw Input HID report the moment Windows hands it over and shows **how steady the intervals are** — gaps, stutters, and reports Windows delivered in batches instead of one by one. It also checks the **quality of the inputs themselves**: stick drift and noise at rest, stick range and circularity, trigger range and resolution, button chatter.

> **Honest by design.** AleksControllerTester reports **Windows host input-delivery timing — not** the controller's internal scan rate or its USB polling, **not** button-to-screen latency, **not** a verdict on your hardware. Latency numbers are clearly labelled lower bounds or relative differences between Windows input paths. Results describe one controller, on one PC, under one load. When a measurement isn't trustworthy, the tool says so ("—", "Not enough data") instead of glowing green.

**Version 1.0.1 — tested on hardware with one controller so far.** Please read [Tested hardware](#tested-hardware) and [Known limitations](#known-limitations) before you trust a result from another controller or connection. Results from other controllers are very welcome.

**Free, and it stays free.** If it helps you, you can [support it](#support) with a voluntary donation — nothing is unlocked by it.

## What it does

- **Controller** — the page the app opens on: a live view of your controller with every button, both sticks with trails and a live circularity outline, both triggers, the four back-paddle positions, and how many reports per second Windows delivers right now. **Scan (press a button)** picks the controller you touch; with only one controller connected, it is selected for you. Button labels: Auto / Xbox / PlayStation / Nintendo / Generic. The live view pauses while a test runs, so it never disturbs a measurement.
  - **Separate LT/RT** — Xbox-style pads report both triggers on one shared axis in Windows' generic input path; one click turns on XInput so both triggers show separately.
  - **Assign buttons…** — a generic / D-input pad with extra buttons ("Extra 1" … "Extra 8") can map them to L4, R4, L5, R5, Share, Guide, Touchpad or Mute. A live dot shows which physical button is which; the mapping is saved per controller model and used in every capture and analysis.
- **Quick Tests** — guided tests of 10 to 60 seconds, each with its own countdown and instructions:
  - **Stick Motion** (10 / 20 / 30 s) — rotate both sticks; gives the plain-language **delivery score** (report rate, jitter, gaps, batching) and host-side latency lower bounds.
  - **Idle Drift** (10 / 30 / 60 s, default 30) — put the controller down; stick noise, centre offset and drift, trigger rest noise, ghost presses.
  - **Button Burst** (10 s) — tap A as fast as you can; press durations, mash rate and chatter — and, on Xbox-compatible pads, which Windows input path saw each press first (a relative difference, not input lag).
  - **Trigger Sweep** (15 s) — pull the triggers slowly; range reached, resolution, start deadzone.
  - **Stick Range** (20 s) — circle each stick along its edge; circularity, outer range, centre repeatability, resolution.
  - **Manual capture** — start / stop; delivery statistics without a score.
- **Advanced / Lab** — the full metric breakdown with explanations, graphs of the delivery timing and of your stick, trigger and button inputs (double-click for full screen), host-side latency lower bounds and, for Xbox-style pads, a cross-check against XInput.
- **Timeline** — the finished run's sticks, triggers and buttons as the raw reported values (no smoothing) over its delivery intervals, so a gap can be read against what you were doing at that moment; **Show longest gap** jumps to it.
- **Compare** — every run is saved locally and ranked only against runs of the **same controller, connection, settings, PC and test**, so you can find which setup delivers most cleanly on *your* PC. Compare says how many runs it used and why others were left out.
- **Measurement regimes** — mark a run as Host Baseline, Desktop Load or Gaming Load Stress; runs under different loads are never ranked against each other.
- **Export report** — a Markdown report of a run, ready to share. Every Quick Test is also saved automatically as a session JSON with a `_metrics.csv` next to it, in `Documents\AleksControllerTester` (format: `EXPORT_SCHEMA.md` in the GitHub repository).
- **Power-saving check** — a read-only look at the Windows power settings and the controller's USB power saving that can interrupt input delivery. The app never changes them.

## The core idea

> It doesn't reward the highest nominal report rate. It rewards the highest rate that stays **stable** under real host delivery.

A "1000 Hz" controller that delivers raggedly can score *below* a steady lower-rate one — and the tool shows you why, with the numbers. The delivery score comes only from Stick Motion, while both sticks keep moving. Input-quality results (sticks, triggers, buttons) are reported separately and never move the delivery score; their thresholds are provisional.

## Tested hardware

| Controller | Connection | Windows input path | Tested |
|---|---|---|---|
| GameSir G7 HE | USB cable | Xbox-compatible HID (`IG_`) + XInput | Stick Motion many times (1000 reports/s); Idle Drift, Button Burst, Trigger Sweep and Stick Range on earlier development builds |

All of it on one Windows 11 PC. That is the whole hardware verification so far.

## Known limitations

- **One controller tested.** The PlayStation (DualSense / DualShock 4), Nintendo (Switch Pro) and generic D-input profiles are implemented but **not yet verified on hardware** — neither is **Assign buttons…** with a real pad that has extra buttons, nor the one-click **Separate LT/RT** button (switching XInput on by hand was checked).
- **Connections:** Bluetooth, 2.4 GHz wireless receivers / dongles and USB hubs are **not yet tested**. The **Xbox Wireless Adapter** detection, and the XInput opt-in that comes with it, are **not yet verified on hardware**.
- **Xbox Elite back paddles** are read through Windows.Gaming.Input for the live view only and drawn by position (P1 upper right, P2 lower right, P3 upper left, P4 lower left) — **not yet verified on a real Elite controller**. Windows.Gaming.Input delivers only while the tester's window is in front (a Windows rule) and is never part of a measurement. The **Share button of Xbox Series controllers is not available** yet.
- **Keep the tester in front while a test runs.** Whether input keeps arriving while another window has focus is not yet verified.
- **Latency is host-side only:** lower bounds from the measured report intervals, and relative differences between two Windows input paths. Absolute button-to-PC latency needs an external hardware rig, which is not part of this release.
- **Report rate means Windows host delivery** — not the controller's internal or USB polling.
- **Thresholds are provisional:** the quality ratings will be tuned once results from many controllers exist.
- **Untested so far:** two or more controllers at once, virtual controllers (Steam Input, DS4Windows / ViGEm, HidHide), 8 kHz controllers, Windows 10, Windows on Arm, and Windows display languages other than the developer's.
- **Unsigned:** Windows SmartScreen warns on the first run, and Smart App Control blocks the app (see [Is it safe?](#is-it-safe)). Some overlays or security tools that inject code into other programs can make the app close right after starting (Windows' hardware stack protection, CET) — please open an issue and name the software if that happens.

## Good to know

- **Xbox controllers** (and many Xbox-style pads) reach Windows' generic input path only in a compatibility form: both triggers share one axis there, and the Guide button is missing. The tool reads the separate triggers and Guide through XInput where it can, labels results from the compatibility path, and asks for one trigger at a time when it cannot.
- **Change-driven pads** send nothing while nobody touches them — an idle gap on such a pad is not a stutter. The Controller page shows a live guess (streaming / change-driven), and the delivery score is computed only while both sticks keep moving.
- **Bluetooth** delivery follows the Bluetooth link, not a USB polling rate — compare Bluetooth runs with Bluetooth runs.
- **Steam Input, DS4Windows and similar tools** create virtual controllers, marked "not measurable". Measure your real controller, not "All controllers".

## Download & run

1. Grab the latest `AleksControllerTester-vX.Y.Z-win-x64.zip` from [**Releases**](../../releases).
2. Unzip (keep the whole folder) and run **`AleksControllerTester.exe`**.
3. **Windows 11 or Windows 10, 64-bit (x64).** Self-contained — no .NET install needed (it brings its own .NET 10). Microsoft supports .NET 10 on Windows 11 24H2 and newer; older Windows 11 versions and Windows 10 22H2 are expected to work but are untested. On Windows 11 on Arm the x64 build runs under Windows' x64 emulation — it has **not** been validated there.

Verify your download against the `SHA256` value listed on the release.

> On the first run of each release, Windows SmartScreen may say "Windows protected your PC" — because this is a small, free, **unsigned** tool, not because it's malware. Click **More info → Run anyway**. On a PC where **Smart App Control** is switched on there is no "Run anyway" — see [Is it safe?](#is-it-safe) below.

## Is it safe?

Short version: it's a small, offline, listen-only tool — and you don't have to take my word for it.

- **Why the SmartScreen warning?** AleksControllerTester is a free tool I don't earn anything from, and it is **not code-signed — by decision, for now**. Unsigned doesn't mean unsafe — it only means Windows has no verified publisher to attach a reputation to. SmartScreen tracks reputation per file for unsigned programs, so **every new release starts again at zero reputation**.
- **Smart App Control (Windows 11).** In its *On* (enforcing) mode, Windows blocks an unsigned program without established reputation outright — there is no "Run anyway". I do **not** recommend turning Smart App Control off for this tool.
- **No network, no telemetry.** It reads your controller's input **locally** — **nothing leaves your PC**, no internet calls, no tracking. Test history is stored only in `Documents\AleksControllerTester`, plus a small **local** error log (`%LocalAppData%\AleksControllerTester\logs`) so crashes can be diagnosed. The **♥ Support** button changes nothing about that: only your web browser opens its PayPal link, and only when you click **Open PayPal** (see [Support](#support)).
- **Listen-only.** It never sends anything to your controller (no rumble, no settings, no firmware), installs no drivers, and never changes Windows settings. Games and other apps keep receiving your input as usual.
- **Runs next to AleksMouseTester.** The two tools keep completely separate settings, history and logs and can run at the same time — for a measurement, leave the other one idle.

**Don't trust me — verify:**
- Check the download's **SHA256** matches the release — it confirms you got my exact, untampered file (for what the hash *can't* prove, see [How this was built](#how-this-was-built-transparency)).
- After unzipping, verify **every individual file**: `sha256sum -c SHA256SUMS.txt` (standard format — works in git-bash/WSL out of the box).
- `BUILDINFO.txt` in the folder records the exact source commit, the .NET SDK, the bundled .NET runtime and the pinned obfuscation-tool version the build used.
- Drag the `.zip` onto [**VirusTotal**](https://www.virustotal.com) to scan it with 70+ antivirus engines yourself.
- Block it in a firewall if you like — it never needs the internet.

## How this was built (transparency)

- **AI-assisted.** I designed and directed AleksControllerTester — the measurement methodology, the "honest instrument" rules, what to measure and how to score it — and wrote the code with heavy help from an AI coding assistant. The design and testing decisions are mine; much of the code is AI-written. Saying so plainly because you deserve to know.
- **Built on AleksMouseTester.** Its host-delivery measurement core (capture, quiet measurement window, timing statistics, delivery score) was carried over and extended for controllers.
- **Closed-source, by choice.** This repo is binary-only (README, license, notes, download). The source stays private for now — a deliberate choice, but honestly **it limits independent verification**: you can't read or rebuild the code.
- **What the SHA-256 does and doesn't prove.** It only confirms your download matches the file I uploaded — it does **not** prove the program is safe. Treat it as "you got the right, untampered bytes," not "it's been audited."

## Status

1.0.1 is a small update of 1.0.0, the first public release — it adds the **♥ Support** button. The measurement pipeline and every feature above are complete and covered by automated tests, but hardware verification is **one controller** so far (see [Known limitations](#known-limitations)), and every input-quality threshold is provisional until results from many controllers exist.

## Feedback

Found a bug, or measured a controller that isn't listed above? Please open an issue with:

- the controller model, the connection (USB cable / receiver / Bluetooth) and its mode (XInput / D-input / Switch / PlayStation),
- your **Stick Motion** result (score and report rate),
- the exported Markdown report (**Export report**) of that run — it lists the controller, the results and some PC context (no user or computer name, an anonymized device id); have a quick look before you post it.

## Support

AleksControllerTester is free, and it stays free. I build it alone, in my spare time. If it helped you, a **voluntary** donation helps keep it going and pays for testing with more controllers:

**[paypal.me/AKrstic43](https://paypal.me/AKrstic43)**

- **Nothing is unlocked by donating** — every feature stays free for everyone.
- In the app, the **♥ Support** button at the right end of the toolbar opens a small window with the same link: **Open PayPal** opens it in your web browser (the tester itself sends nothing), **Copy link** copies it. It never pops up by itself.
- On GitHub, the **Sponsor** button at the top of this repository leads to the same link.

Thank you either way — a bug report or a result from a controller that isn't listed above helps just as much.

## License

Free to use. Binary-only release — see the license terms (`LICENSE.txt` in the download). Not open source. Third-party components bundled with the app (the .NET runtime) keep their own licenses — see `THIRD-PARTY-NOTICES.txt` and `DOTNET-RUNTIME-THIRD-PARTY-NOTICES.TXT` in the download.

Xbox, DualSense, DualShock, Nintendo Switch, Steam and other product names are trademarks of their respective owners. AleksControllerTester is an independent tool, not affiliated with or endorsed by any of them.

---

*AleksControllerTester is a diagnostic instrument for controller input delivery and input quality — not a marketing-number generator.*
