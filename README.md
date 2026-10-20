# A1644 Windows Driver

English | [日本語](README.ja.md)

UMDF drivers that make the Apple Magic Keyboard A1644 easier to use on Windows 11. This repository distributes prebuilt drivers only. The source code is not public.

There is one driver for each connection type:

| Driver | Connection | Release file |
|---|---|---|
| **a1644-lightning-umdf** | USB through a Lightning cable | `a1644-lightning-umdf-v<version>-win-x64.zip` |
| **a1644-bluetooth-umdf** | Bluetooth | `a1644-bluetooth-umdf-v<version>-win-x64.zip` |

Both drivers do the key remapping inside a Windows user-mode driver (UMDF), so no helper app has to keep running in the background.

## Features

### Key mapping

| Key | Action |
|---|---|
| Eject | Delete (forward delete) |
| Caps Lock | Left Ctrl |
| Left Control | Caps Lock |
| Left / Right Option | Left / Right Windows |
| Left / Right Command | Left / Right Alt |
| 英数 (Eisu) | IME off |
| かな (Kana) | IME on |
| Backslash / Yen key | Types the character from the Japanese layout |
| F1–F12 | Standard function keys |

### Fn combinations

| Keys | Action |
|---|---|
| Fn+F1 | Decrease display brightness |
| Fn+F2 | Increase display brightness |
| Fn+F3 | Task View |
| Fn+F4 | Quick Settings (Win+A) |
| Fn+F5 / F6 | No action |
| Fn+F7 | Previous track |
| Fn+F8 | Play / Pause |
| Fn+F9 | Next track |
| Fn+F10 | Mute |
| Fn+F11 | Volume down |
| Fn+F12 | Volume up |

The Fn state is fixed when the F key is pressed. For example, if you hold Fn+F11 and then release only Fn, the key does not switch to F11.

To adjust brightness, the display must support the standard Windows brightness control. Depending on the app and font, the backslash may look like a yen sign.

## Requirements

- Windows 11 (x64). ARM64 and 32-bit Windows are not supported.
- Apple Magic Keyboard A1644
  - Lightning driver: connected over USB with a Lightning cable that supports data transfer
  - Bluetooth driver: connected over Bluetooth
- Administrator rights to install and uninstall
- For Japanese input: a Japanese keyboard layout and a Japanese IME
- Tools needed at install time (not included in the release files):
  - SignTool from the [Windows SDK](https://developer.microsoft.com/windows/downloads/windows-sdk/)
  - Inf2Cat from the [WDK](https://learn.microsoft.com/windows-hardware/drivers/download-the-wdk)

## Signing

The drivers are not signed by Microsoft (they have no WHQL or attestation signature).

The installer (`install.bat`) creates a self-signed certificate on the target PC, signs the driver with it, and then installs the driver. That is why SignTool and Inf2Cat are needed at install time. You do not need them for normal use after installation.

- You do not need to turn off Memory Integrity, Secure Boot, or driver signature enforcement.
- The installer does not change those settings, the system-wide key mapping, or the persistent PowerShell execution policy.
- The certificate is created for the Windows account and PC you install on. Uninstalling removes it.

| Driver | Certificate subject / issuer |
|---|---|
| Lightning | `A1644 Lightning UMDF Local` |
| Bluetooth | `A1644 Bluetooth UMDF Local` |

| Console | Store | Purpose |
|---|---|---|
| certlm.msc | Trusted Root Certification Authorities → Certificates | Trust for the local signature |
| certlm.msc | Trusted Publishers → Certificates | Trust for the driver publisher |
| certmgr.msc | Personal → Certificates | Signing certificate and private key |

## Installation

### Before you start

1. Sign in with a Windows account that can approve administrator rights.
2. Install SignTool from the Windows SDK and Inf2Cat from the WDK.
3. Connect the A1644 to the PC and check that you can type normally.
   - Lightning: turn the A1644 on and connect it with a Lightning cable that supports data transfer.
   - Bluetooth: unplug the Lightning cable and connect over Bluetooth.
4. Connect a second keyboard so you can still type if something goes wrong.

### Steps

1. From [Releases](https://github.com/rkamiya/a1644-windows-driver/releases), download the `…-win-x64.zip` for your connection type. Choose the ZIP attached to the release, not "Source code".
2. (Optional) Check the download against the `.zip.sha256` file attached to the same release:
   ```powershell
   (Get-FileHash .\a1644-lightning-umdf-v0.4.0-win-x64.zip -Algorithm SHA256).Hash.ToLower()
   ```
3. Right-click the ZIP, choose "Extract All", and extract it to a local folder you can keep until you uninstall. Do not run anything directly from inside the ZIP.
4. Double-click **install.bat** in the extracted folder.
5. Choose "Yes" in User Account Control (UAC) and follow the prompts. Stay on your current Windows account; do not elevate with another account's credentials.
6. When the window shows `Installed.`, check typing and the key mapping on the A1644.

The installer finds SignTool and Inf2Cat in their standard install locations. If you installed them somewhere else, set the `A1644_SIGNTOOL` and `A1644_INF2CAT` environment variables to the full path of each EXE.

Only the A1644's input interface is changed: the Lightning driver changes only USB interface MI_01, and the Bluetooth driver changes only the Bluetooth HID keyboard input. Other physical keyboards and the driver for the other connection type are not affected.

> [!IMPORTANT]
> **Keep the extracted folder until uninstallation is complete.** Its `runtime` folder holds the records used to restore the original driver and remove the certificate. These records are specific to the PC and Windows account. Do not copy them to another PC.

## Uninstallation

1. Connect a second keyboard. Keep the A1644 connected the same way as when you installed.
2. Using the same Windows account as for installation, double-click **uninstall.bat** in the folder you kept.
3. Choose "Yes" in UAC and wait for it to finish.
4. Check that the window shows `Restored:`.
5. If needed, reconnect the A1644 (Lightning: unplug and replug the cable; Bluetooth: turn the keyboard off and on) and check that typing works with the default key mapping.

The uninstaller switches the A1644 input back to the standard Windows driver (`HidUsb / input.inf` for Lightning, `kbdhid / keyboard.inf` for Bluetooth) and removes the driver package. After confirming the restore, it also removes the certificate and private key. SignTool and Inf2Cat are not needed for uninstallation.

If a restart is required or removal fails, keep the extracted folder and `runtime`, restart or reconnect as instructed, and **run uninstall.bat again**. The certificate is not removed until the restore to the original driver is confirmed.

The logs and restore records stay in the folder after uninstallation. Once you have confirmed it finished, you can delete the folder.

## Checking the status

Run **inspect.bat** to see whether the driver and certificate are registered. Please attach its output when you report a problem.

## Known limitations

- As of v0.4.0, running install.bat and uninstall.bat on real hardware, the new features on real hardware, Japanese input, reconnection, and long-term use have not been fully verified yet.
- Because the drivers are not signed by Microsoft, installation needs tools from the SDK and WDK.

## Reporting problems

Please use [Issues](https://github.com/rkamiya/a1644-windows-driver/issues). The source code is not public, so pull requests are not accepted.

## License

See [LICENSE](LICENSE).

This software is not affiliated with Apple Inc. Apple, Magic Keyboard, and Lightning are trademarks of Apple Inc.
