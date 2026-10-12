# AleksControllerTester v1.0.3 — a real PlayStation layout, wider result tables

**A small update of 1.0.2: the PlayStation layout now draws a DualSense-style
pad, and the result tables on Advanced / Lab get more width. Measurements,
scores and file formats are unchanged.**

## What's new

- **The PlayStation layout draws a DualSense-style pad.** Picking
  **PlayStation** in the Controller page's Layout box used to change only the
  labels on the Xbox-style drawing. It now draws a pad shaped like a DualSense:
  the **d-pad top left**, **both sticks low and symmetric**, Triangle / Circle /
  Cross / Square top right, a large **touchpad** with **Create** and **Options**
  beside it, the **PS** button between the sticks with **Mute** below it, L1 / R1
  and L2 / R2 on the shoulders and the four back-paddle positions on the grips.
  No logo or lettering is drawn on the body. The badges and their lines follow
  the new positions, and no line crosses another.
- **Auto picks it for Sony controllers.** With the Layout box on **Auto**, a
  Sony controller gets the DualSense-style drawing; every other controller keeps
  the Xbox-style drawing. An explicit pick in the Layout box always wins.
- **Advanced / Lab: wider result tables.** The **Description** box beside the
  graph is half as wide, and the freed width goes to the right column, so the
  **Input quality / Latency / XInput cross-check** tables get wider columns. At
  2560 × 1440 the right column grows from 640 to 922 px and the **Note** column
  from 264 to 546 px; the Note column now shows from windows about 1410 px wide
  (was about 1590). The graph keeps its width and the **Score breakdown** keeps
  its bars.

## Everything from 1.0.0 – 1.0.2 still applies

The tested hardware (one controller so far: a GameSir G7 HE over a USB cable,
on the Xbox-compatible HID path with XInput, one Windows 11 PC), the known
limitations, the system requirements and the notes on the unsigned build are
unchanged — see the
[1.0.0 release notes](https://github.com/moneks1106-code/AleksControllerTester/releases/tag/v1.0.0)
and the README (**Tested hardware**, **Known limitations**, **Is it safe?**).
The PlayStation drawing is a display layout; the PlayStation, Nintendo and
generic D-input input profiles are still **not yet verified on hardware**.
Saved runs, settings and exported reports from earlier versions open as before.

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only.
  Self-contained: the .NET 10 runtime is included — unzip the whole folder and
  run `AleksControllerTester.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected
  your PC" — click **More info → Run anyway**. With Smart App Control switched
  on, Windows blocks the app (there is no "Run anyway").

## Verify the download

- The zip's **SHA-256** is on this release page (not inside the zip). Check it in
  PowerShell: `Get-FileHash .\AleksControllerTester-v1.0.3-win-x64.zip -Algorithm SHA256`
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
