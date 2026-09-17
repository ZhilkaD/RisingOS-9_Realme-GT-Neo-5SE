# RisingOS 9 for Realme GT Neo 5 SE (senna_jr / RMX3700)

![Android](https://img.shields.io/badge/Android-17-blue?style=flat-square)
![Status](https://img.shields.io/badge/Build-COMMUNITY-orange?style=flat-square)
![SELinux](https://img.shields.io/badge/SELinux-Enforcing-success?style=flat-square)

Unofficial personal port of **RisingOS 9 (Android 17)** for the **Realme GT Neo 5 SE** (`senna_jr` / `RMX3700`).
Base: RisingOS Revived branch `seventeen` (LineageOS 24.0), GAPPS included.
*Personal port, not affiliated with RisingOS or LineageOS.*

* **Build:** `RisingOS-9-260917-0943-GAPPS-COMMUNITY-senna_jr`
* **Type:** `userdebug`

> **Important Notices:**
> * **Hobby Build:** Signed with the public AOSP testkey. Any APK signed with that key gets platform permissions.
> * **Integrity:** AVB is off (`vbmeta` and `vbmeta_system` flashed with verity/verification disabled). Play Integrity fails, Google Pay and some banking apps will not work.
> * **Security:** adb is secure (`ro.adb.secure=1`, `ro.debuggable=0`) and SELinux is enforcing. No adb root.
> * **Updates:** No OTA or recovery sideload. All updates are manual fastboot flashes or via Fastboot Firmware Flasher.
> * **Single-slot:** Slot B is not a backup. Rollback = reflash images.
> * **ColorOS:** Stock `my_*` logical partitions were removed from super metadata to fit the new system. Returning to ColorOS requires a full stock firmware package.
> * **Kernel:** Stock prebuilt `Image` and vendor modules are used verbatim. Sources: [realme-kernel-opensource](https://github.com/realme-kernel-opensource).

---

## Changelog — 2026-09-17

* First Android 17 build for this device (RisingOS 9, LineageOS 24.0 base, GAPPS in-box).
* All functionality of the Android 16 port carried over and re-verified: hotspot, USB tethering, in-display fingerprint at any brightness, 144 Hz, cameras, voice calls.
* Hotspot fix re-applied on the new tree: `POSIX_SPAWN_CLOEXEC_DEFAULT` removed in netd (`dnsmasq` died with status 127 because kernel 5.10 lacks `CLOSE_RANGE_CLOEXEC`).

---

## Tested Base (Read Before Flashing)

Exactly one firmware base was tested: **Global RMX3701**:
```text
ro.build.display.id      = RMX3701_16.0.5.1010(EX01)
ro.build.version.ota     = RMX3701_11.H.43_3430_202607021803
ro.boot.prjname          = 22623
ro.product.vendor.device = RE58D1L1
```

Check your parameters before flashing:
```bash
adb shell getprop ro.boot.prjname
adb shell getprop ro.product.vendor.device
```
> If `prjname` is not `22623` or vendor device is not `RE58D1L1`, do not flash. Chinese ColorOS bases are untested.

---

## Hardware Status

### Working
* Boot and system stability (daily driver ready)
* Display at 144 Hz (real, measured)
* Touch response
* Speaker and earpiece audio, voice calls (VoLTE)
* Mobile data & Wi-Fi
* Wi-Fi hotspot and USB tethering
* In-display fingerprint (at all brightness levels)
* All 4 cameras, video recording
* NFC (payments work)
* Bluetooth audio and headsets
* GPS / satellite navigation
* DRM video (Netflix etc.)
* Hardware sensors, vibration
* SELinux Enforcing

### Known Issues
* **GPU Composition:** All UI layers are composed by the GPU (Oplus hardware composer incompatible with our display stack).
  * Minor input delay, completely smooth with 0 dropped frames.
  * Higher power draw.
* **Single-slot:** No A/B fallback; the second copy of the system does not fit into super.

---

## Installation Guide

### Prerequisites
* Unlocked bootloader.
* Latest Android [platform-tools](https://developer.android.com/studio/releases/platform-tools) extracted to `C:\platform-tools`.
* Firmware archive unpacked to `C:\senna` (contains `BOOTLOADER\` and `SYSTEM\` folders).
* **Full backup:** All data will be wiped.

### Option 1: Fastboot Firmware Flasher (recommended)

1. Download Fastboot Firmware Flasher
2. Copy `BOOTLOADER`, `SYSTEM` and `firmware_info.json` from the archive into FFF's `FIRMWARE\RisingOS_9_20260917_senna_jr` folder
3. Open FFF → Flasher → select `RisingOS_9_20260917_senna_jr`
4. Enable "Disable VBMeta verification"
5. Start tab: Android **16** (there is no 17 in the list — normal, does not affect the result), ADB mode
6. Click "To FastbootD" → "Start"

### Option 2: flash.bat

1. Extract archive to `C:\senna`
2. Power off → Volume Down + Power (bootloader)
3. Run `flash.bat` (the script adds `C:\platform-tools` to PATH itself if needed)

### Option 3: Manual commands

**Bootloader stage:**
```cmd
set PATH=C:\platform-tools;%PATH%
cd /d C:\senna
fastboot --disable-verity --disable-verification flash vbmeta BOOTLOADER\vbmeta.img
fastboot --disable-verity --disable-verification flash vbmeta_system BOOTLOADER\vbmeta_system.img
fastboot flash boot BOOTLOADER\boot.img
fastboot flash dtbo BOOTLOADER\dtbo.img
fastboot flash vendor_boot BOOTLOADER\vendor_boot.img
fastboot flash recovery BOOTLOADER\recovery.img
fastboot reboot fastboot
```

**fastbootd stage:**
```cmd
fastboot flash system SYSTEM\system.img
fastboot flash system_ext SYSTEM\system_ext.img
fastboot flash product SYSTEM\product.img
fastboot flash vendor SYSTEM\vendor.img
fastboot flash vendor_dlkm SYSTEM\vendor_dlkm.img
fastboot flash odm SYSTEM\odm.img
fastboot flash odm_dlkm SYSTEM\odm_dlkm.img
fastboot erase metadata
fastboot erase userdata
fastboot reboot
```

*First boot takes 5–10 minutes (GAPPS optimization).*

---

## How to Revert

* **To Android 16 (build 20260913):** same manual commands with images from that release, plus `erase metadata` and `erase userdata`.
* **To ColorOS:** full stock firmware package only — the `my_*` partitions no longer exist in super metadata and must be recreated by the stock tooling.

---
Google Drive (Fastboot Archive): [Download Folder](https://drive.google.com/drive/folders/1BV4O97a6VqyDi00YzqZvb4TBMjPuV2Qc)

## Credits & Sources

* **[LolD:O](https://4pda.to/forum/index.php?showuser=9090479)** — original device tree (`senna_jr`) and OnePlus `sm8450-common` base.
* **The RisingOS Revived team** — platform base (branch `seventeen`).
* **The LineageOS Team** — upstream platform sources.
* **Realme / Oplus** — official [kernel releases](https://github.com/realme-kernel-opensource).
