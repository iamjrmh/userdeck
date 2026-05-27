<p align="center">
  <a href="https://github.com/iamjrmh/userdeck">
    <img src="assets/userdeck.svg" alt="userdeck" width="180" />
  </a>
</p>

<h1 align="center">userdeck</h1>

<p align="center">
  A DIY programmable streamdeck - 3D-printed, mechanical-switch, Raspberry Pi Pico inside.
  <br />
  This repo ships the companion Configurator app and the firmware that runs on the deck.
</p>

<p align="center">
  <a href="https://github.com/iamjrmh/userdeck">
    <img src="assets/logo.svg" alt="userdeck logo" width="48" />
  </a>
</p>

---

## What it is

userdeck is a small 3×6 grid of physical buttons backed by a Raspberry Pi Pico. Each cell on each of 5 pages can be bound to:

- **Keyboard shortcuts** - any chord (Ctrl/Shift/Alt/Win + key)
- **Typed text** - sends a literal string of keystrokes
- **Media keys** - Play/Pause, Mute, Volume, Mic mute, Brightness, Screenshot window…
- **System actions** - Sleep / Wake (toggle) and Power On / Off
- **Launch a program** - anything from your Start menu, with auto-detected working directory
- **Sequences (macros)** - chain up to 8 actions in any order, 1 ms between steps

Two physical buttons are reserved as **prev page / next page**. The two bottom corners become **cancel / confirm** when a binding asks for confirmation.

---

## Installing

1. Run the **userdeck Configurator** installer.
2. On first launch the app drops a shortcut into your Startup folder so it stays running in the background. Close the window any time - it lives in the system tray.
3. Plug in your userdeck. If it's the very first time, the **setup wizard** walks you through binding each of the 18 physical buttons to its on-screen cell.

The app needs a one-time UAC prompt the first time it launches a program that requires admin rights (some games, some installers). Standard launches don't prompt.

---

## The window at a glance

| Section | What it's for |
|---|---|
| **Top-right pill** | Live connection status. Shows `searching…`, a COM port, or `N devices`. |
| **Device tab strip** *(only when 2+ decks are plugged in)* | Switch between userdecks - each remembers its own keymap, launchers, and bindings. |
| **Launchers** | Map a name (e.g. `obs`) to a program on your machine. The Keymap tab's *Launch program* action points at these names. |
| **Keymap** | Edit what each cell does on each of the 5 pages. Click a cell to bind it. |
| **Activity** | Live JSON stream from the device - useful for diagnosing weird button behaviour. |
| **Settings** | USB IDs, idle timeout, press-label duration, rename/forget device, firmware flashing. |

---

## Day-to-day usage

### Adding a launcher

1. Open the **Launchers** tab → **+ Add**.
2. Start typing the program's name. The dropdown lists everything in your Start menu - pick from there and the app fills in the path, args, and working directory automatically.
3. The launcher's *name* (left column) is what you reference from the Keymap. Keep it short - `obs`, `vscode`, `discord`.
4. The **Test** button next to each row fires the launcher right then, so you can confirm it works.

> **Right-click a launcher row** for quick **Test · Duplicate · Delete**.

### Binding a key

1. Open **Keymap**, click any non-reserved cell.
2. Choose a type from the **Type** dropdown:
   - **Key combo or text** - click *record*, then either press a chord (with at least one of Ctrl/Alt/Win - Shift alone counts as text) or type a sequence of plain characters. Press **Enter** to save, **Esc** to cancel.
   - **Media key** - pick from the list.
   - **System** - Sleep / Wake (toggle) or Power On / Off.
   - **Launch program** - pick a launcher name from the dropdown.
   - **Sequence (multi-step)** - see below.
3. **Label on screen** is the text shown on the device's circular display when you press that button (max 12 characters).
4. **Apply** saves and pushes the binding to the device immediately.

> **Right-click a cell** for **Edit · Copy · Cut · Paste · Clear**. The clipboard works across pages and across devices, so you can duplicate a binding without retyping it.
>
> **Right-click a page dot** (1/2/3/4/5) for **Switch · Rename page · Clear page**. Page names show up on the device's circular display.

### Sequences (macros)

A *Sequence* binding chains up to 8 sub-actions and runs them in order with a 1 ms gap between each step. Useful for:

- A single button that sends **Win+Shift+S** then types a filename.
- "Stream start" - launch OBS, then send Mute, then send a hotkey to a launched window.
- Anything you'd normally need to mash three buttons for.

Each step picks its own kind (key / text / media / system / launch). Reorder with **▲ ▼**, delete with **✕**, max 8 steps.

### Resetting a keymap

Keymap tab → **Reset keymap** clears every page's bindings + page names on the active device. Launchers and physical button bindings (from the wizard) are kept.

---

## Building one (hardware)

### Bill of materials

| Part | What I use | Notes |
|---|---|---|
| **Microcontroller** | [Raspberry Pi Pico](https://www.raspberrypi.com/products/raspberry-pi-pico/) | The original RP2040 board. Pico 2 also works. |
| **Key switches (x18)** | [Kailh Low Profile Cherry MX-style switches](https://www.amazon.com/dp/B0B4626RK3) | Any Cherry MX-compatible switch fits. Low-profile keeps the deck thin. |
| **Wire (signal)** | [TUOFENG 30AWG colored solid](https://www.amazon.com/dp/B0C1J5M3P7) | Thin enough to snake between switches. |
| **Wire (heavier runs)** | [TUOFENG 28AWG colored solid](https://www.amazon.com/dp/B093GZZZ6Y) | Optional, for power / longer pin runs. |
| **Filament** | [SUNLU PLA+ 1.75mm](https://www.amazon.com/dp/B0DHCT8YDN) | Any decent PLA prints the enclosure fine. |
| **Cable** | **micro USB** | The Pico's native port. See USB-C section below if you'd rather swap it. |
| **Display** *(optional, recommended)* | GC9A01 1.28" circular SPI display | Shows the current page, button labels, and confirmation prompts. The deck works fine without it; you just lose the visual feedback layer. |

3D-printable enclosure files are in [`hardware/`](https://github.com/iamjrmh/userdeck/tree/main/hardware).

### Want USB-C instead?

The Pico ships with a micro USB port. If you'd rather have USB-C (and you're comfortable with a bit of extra soldering), wire in an **[Adafruit USB Type C Breakout Board - Downstream Connection (Product ID 4090)](https://www.adafruit.com/product/4090)**. It exposes D+, D-, VBUS, and GND pads that drop straight onto the Pico's USB pins. The enclosure has space for it if you mount it at the back. Nothing in the firmware or configurator changes - the deck still enumerates as the same userdeck device.

### My workshop setup

You don't need any of this to build a userdeck, but if you're starting from zero, this is what I use:

- **3D printer** - [Elegoo Neptune 3 Pro](https://us.elegoo.com/products/elegoo-neptune-3-pro-fdm-3d-printer-225x225x280mm)
- **Soldering / hot-air reflow station** - [combo unit on Amazon](https://www.amazon.com/dp/B0DGD8813V)
- **Microscope** - [Andonstar AD240](https://www.amazon.com/dp/B0C5Q4YKV9) (helpful for inspecting GC9A01 solder joints)

---

## Multi-device support

If you've built more than one userdeck:

- Each board has a unique factory ID baked into the chip. Plug a second deck in and the strip appears with both.
- The **active tab** drives everything - Launchers, Keymap, and Settings all scope to whichever device pill is selected.
- Plug in a brand-new userdeck and the configurator detects it, runs the setup wizard for *that* device, and remembers its profile for next time.

### Right-click on a device pill

- **Switch to this device**
- **Rename…** - pick a friendly name (the pill shows it instead of "userdeck").
- **Copy keymap from…** - clones launchers + keymap + page names from another device. Physical button bindings are **not** copied - those are unique to each board.
- **Forget this device** - deletes the saved profile. The deck shows up as new next time it's plugged in.

The Settings tab has the same actions for the active device if you'd rather click than right-click.

---

## Firmware

Firmware lives inside the Configurator - there's nothing to download separately. The bundled UF2 is the latest version every time you install.

### Flashing the active device

**Settings → Firmware section → Flash**. The configurator asks the deck to reboot into BOOTSEL mode, then drops the UF2 onto the drive that appears. The deck re-enumerates and the binding wizard runs automatically (since the keymap is reset on every flash).

### Flashing a fresh Pico

Plug in a Raspberry Pi Pico **while holding the white BOOTSEL button**. The `RPI-RP2` drive shows up and the configurator pops a styled **New device detected** modal asking if you want to install userdeck onto it. Click **Install userdeck** - same as above from there.

### Manual / custom UF2

If something goes weird, **Settings → Firmware → Choose .uf2…** lets you point at any UF2 file. The "Send to BOOTSEL" button reboots the active device into BOOTSEL without flashing, so you can drop a file there manually.

---

## Power management (Sleep / Wake / Power)

userdeck can put your PC to sleep, shut it down, or wake it back up - all from a single bound button.

- **Sleep / Wake (toggle)** - sends sleep when the host is awake, wake when it's asleep. Same binding does both.
- **Power On / Off** - shuts down when awake, attempts wake when off (BIOS support permitting).

For wake-from-sleep to work, two things must be set up once:

1. **Device Manager** → userdeck → Properties → Power Management → **Allow this device to wake the computer** ✓
2. **BIOS** → enable **Resume by USB** / **ErP Disabled** / **USB Power in S4/S5** (exact name varies)

Wake-from-full-shutdown (S5) depends on your motherboard. Some chipsets only support wake from sleep (S3). The button still works to shut down - it just won't bring the PC back without supported BIOS settings.

---

## Settings reference

| Setting | What it does |
|---|---|
| **USB VID / PID** | Hex values used to match userdeck against connected USB ports. Don't change unless you've built custom firmware. |
| **Idle timeout** | Seconds of inactivity before the device's screen switches to a screensaver animation. |
| **Press label duration** | How long (ms) the label of a pressed button stays on screen before the page indicator returns. |
| **Reconnect device** | Tears down the serial listener and rediscovers. Use if a deck got stuck after a USB hub swap. |
| **Rebind buttons…** | Re-runs the first-time wizard for the active device (lets you re-do the physical button → on-screen cell mapping). |
| **Reset to default…** | Wipes ALL config - every device, every keymap, every launcher. Re-opens the wizard. There is no undo. |

---

## Troubleshooting

**Pill says `searching…` and nothing happens.**
Make sure the deck is plugged in directly (not behind a USB hub that's powered-off in sleep). **Settings → Reconnect device** retries discovery. If the deck has older firmware, plug it in, then **Settings → Firmware → Flash** to update.

**Pressing a button does nothing.**
Open the **Activity** tab and press the button. If you see a `press` event there, the device works - check the Keymap tab to confirm the cell is bound. If you don't see a press event, run **Settings → Rebind buttons** to re-record the physical wiring.

**A program launch fails with `requires elevation`.**
The configurator transparently re-prompts UAC for that one launch. If you keep declining, the program won't run - accept the prompt once and Windows remembers.

**OBS / a portable game says it can't find its files.**
The launcher needs a working directory. Pick the program from the Start-menu dropdown rather than typing a path manually - that auto-fills `cwd` from the Start menu shortcut.

**Two decks plugged in, only one shows up.**
Older firmware reports a hardcoded USB serial. Flash the deck (any deck) once with the bundled firmware to give it a unique ID baked in. Until then it appears as `legacy-COMx` in the strip - clicking into it lets you flash from Settings.

**The window closes but the app is still in the tray.**
That's intentional - userdeck keeps relaying device events to your OS in the background so press events still fire. Right-click the tray icon → Quit to exit fully.

---

## Support

Found a bug, want a feature, or have a hardware question?

- **Issues**: open one on this repo
- **Email**: `2iamjrmh@gmail.com`

userdeck's hardware (3D files, BOM, wiring) is open source. The configurator and firmware that ship in the installer are closed source, but everything you need to *use* the deck is documented here.
