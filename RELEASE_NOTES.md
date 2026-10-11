# AleksControllerTester v1.0.2 — fits every screen, a taller graph

**A small update of 1.0.1: the toolbar and the Advanced / Lab page fit small
screens and high display scaling, and the Advanced / Lab graph gets the room
the result bands left. Measurements, scores and file formats are unchanged.**

## What's new

- **The toolbar never overlaps.** On a narrow window, a 1024 px screen or at
  high display scaling, "Check power saving" could slide under the ♥ Support
  button. Now the toolbar gives way step by step instead: the device picker
  narrows, then **♥ Support** shrinks to the heart **♥** alone (same tooltip),
  then the "Power:" and "Device:" labels and the app name hide. Each step comes
  back as the window widens.
- **Advanced / Lab: the result bands moved to the right.** "Input quality",
  "Latency (host-side lower bounds)" and "XInput cross-check" now fill the
  empty space under the **QUICK TEST / TEST SETUP** block. On a tall window
  (for example 2560 × 1440) all three are stacked there; on a shorter one (for
  example 1920 × 1080) they share that place behind three small tabs —
  **Input quality / Latency / XInput**; on a small window the right column
  scrolls, and scrolled down the bands fill it. Values are never cut to "…";
  a long note ends with "…" and shows in full when you hover over it.
- **A taller graph.** The graph and its description take the room the bands
  left. On a large window (for example 2560 × 1440) the graph sits under the
  top panels without scrolling, about twice as tall as in 1.0.1. On a smaller
  one (for example 1920 × 1080 or 1280 × 800) the page scrolls as before, to a
  graph as tall as the page — 884 px at 1920 × 1080 instead of 320 px — so
  scrolled down, the graph fills the page. It is never squeezed until its
  labels overlap.
- **Nothing is cut off any more.** The "Compare" tab, the "Stop Quick Test",
  "Export Report" and "Open Folder" buttons and the "Cross-check with XInput"
  text no longer run off a narrow window: the tabs narrow, the buttons wrap
  into a second row, the check box uses a shorter text (the full one is its
  tooltip), and on a narrow page the panels rearrange so no value is clipped.
  The toolbar rows and the new layout follow the display scaling (checked at
  100–200 %).

## Everything from 1.0.0 and 1.0.1 still applies

The tested hardware (one controller so far: a GameSir G7 HE over a USB cable,
on the Xbox-compatible HID path with XInput, one Windows 11 PC), the known
limitations, the system requirements and the notes on the unsigned build are
unchanged — see the
[1.0.0 release notes](https://github.com/moneks1106-code/AleksControllerTester/releases/tag/v1.0.0)
and the README (**Tested hardware**, **Known limitations**, **Is it safe?**).
The **♥ Support** button from
[1.0.1](https://github.com/moneks1106-code/AleksControllerTester/releases/tag/v1.0.1)
works as before. Saved runs, settings and exported reports from 1.0.0 and
1.0.1 open as before.

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only.
  Self-contained: the .NET 10 runtime is included — unzip the whole folder and
  run `AleksControllerTester.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected
  your PC" — click **More info → Run anyway**. With Smart App Control switched
  on, Windows blocks the app (there is no "Run anyway").

## Verify the download

- The zip's **SHA-256** is on this release page (not inside the zip). Check it in
  PowerShell: `Get-FileHash .\AleksControllerTester-v1.0.2-win-x64.zip -Algorithm SHA256`
- Inside the zip, `SHA256SUMS.txt` lets you check every file — in git-bash or
  WSL: `sha256sum -c SHA256SUMS.txt`
- `BUILDINFO.txt` names the source commit, the .NET SDK, the bundled .NET
  runtime and the pinned obfuscation-tool version.
- The hash proves you got the exact, untampered file — not that the program is
  audited. You can also scan the zip on VirusTotal.

## Feedback welcome

If you measure a controller, please open an issue with the controller model,
the connection and mode, your Stick Motion result and the exported Markdown
report (**Export report**). Bugs and odd results are just as welcome.
