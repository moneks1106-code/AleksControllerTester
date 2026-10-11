# AleksControllerTester v1.0.1 — Support button

**A small update of 1.0.0: a Support button, and a log that names the build
properly. Measurements, scores and file formats are unchanged.**

## What's new

- **♥ Support** — a button at the right end of the toolbar, on every page. It
  opens a small window that says it plainly: the tester is free and built by
  one person in their spare time; if it helped you, a voluntary donation via
  PayPal (paypal.me/AKrstic43) helps keep it going and pays for testing with
  more controllers. **Nothing is unlocked by donating** — every feature stays
  free. **Open PayPal** opens the link in your web browser (the tester itself
  still sends nothing and connects to nothing), **Copy link** copies it. The
  window never pops up by itself — not at start, not after a test — and the
  button is locked while a test runs.
- **The log names the build.** Each line of the local error log now shows,
  after its timestamp and level, the version and the short source commit, `1.0.1 (<short commit>)` —
  the commit `BUILDINFO.txt` names — instead of an unreadable piece of the
  commit hash. The exported report's "App version" row uses the same form.

## Everything from 1.0.0 still applies

The tested hardware (one controller so far: a GameSir G7 HE over a USB cable,
on the Xbox-compatible HID path with XInput, one Windows 11 PC), the known
limitations, the system requirements and the notes on the unsigned build are
unchanged — see the
[1.0.0 release notes](https://github.com/moneks1106-code/AleksControllerTester/releases/tag/v1.0.0)
and the README (**Tested hardware**, **Known limitations**, **Is it safe?**).
Saved runs, settings and exported reports from 1.0.0 open as before.

## System requirements

- Windows 11 or Windows 10, 64-bit (x64); tested on Windows 11 only.
  Self-contained: the .NET 10 runtime is included — unzip the whole folder and
  run `AleksControllerTester.exe`.
- **Unsigned.** On the first run Windows SmartScreen may say "Windows protected
  your PC" — click **More info → Run anyway**. With Smart App Control switched
  on, Windows blocks the app (there is no "Run anyway").

## Verify the download

- The zip's **SHA-256** is on this release page (not inside the zip). Check it in
  PowerShell: `Get-FileHash .\AleksControllerTester-v1.0.1-win-x64.zip -Algorithm SHA256`
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
