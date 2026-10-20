# Changelog

Release files for both drivers are published in [Releases](https://github.com/rkamiya/a1644-windows-driver/releases).

## v0.4.0 — 2026-10-20

Applies to both `a1644-lightning-umdf` and `a1644-bluetooth-umdf`. No changes to key mapping or driver behavior.

- Corrected the copyright holder in the bundled LICENSE (still MIT)
- Driver version (DriverVer) updated to 0.4.0.0
- First release published in this repository

## v0.3.0 — 2026-10-18

Applies to both `a1644-lightning-umdf` and `a1644-bluetooth-umdf`.

- Eject → Delete
- Caps Lock and left Control swapped
- Option → Windows, Command → Alt
- IME off/on with the 英数 / かな keys
- Fn+F1–F12 for brightness, Task View, Quick Settings, media, and volume
- Remapping runs inside UMDF; no helper app needs to stay running
- SHA-256 checksum file attached to each ZIP

Known limitation: running install/uninstall on real hardware and the new features have not been fully verified yet.
