# Local Manifests for Lenovo Tab (`TB311FU` / `poplar`)

This repository provides the Android `repo` local manifest (`poplar.xml`) required to sync the device, vendor, kernel, and MediaTek platform dependency trees for building **LineageOS** (`lineage-21.0` / `lineage-22.1`) for the **Lenovo Tab** (`TB311FU` / `poplar`).

---

## Manifest Contents (`poplar.xml`)

| Path | Repository | Remote | Branch | Purpose |
|---|---|---|---|---|
| `device/lenovo/poplar` | `BangerSoul/android_device_lenovo_poplar` | `github` | `lineage-21.0` | LineageOS device tree, BoardConfig, fstab, init, overlays, embedded Boot v4 recovery |
| `vendor/lenovo/poplar` | `BangerSoul/proprietary_vendor_lenovo_poplar` | `github` | `lineage-21.0` | 473 proprietary blobs & DLKMs extracted from stock ZUI 17.5.36.508 |
| `kernel/lenovo/poplar` | `BangerSoul/android_kernel_lenovo_poplar` | `github` | `lineage-21.0` | Linux 6.6.82 GKI 2.0 `tb311fu_defconfig`, hardware patches, LK boot vector tool |
| `hardware/mediatek` | `LineageOS/android_hardware_mediatek` | `lineage` | `lineage-21.0` | LineageOS MediaTek common HAL interfaces |
| `device/mediatek/sepolicy_vndr` | `LineageOS/android_device_mediatek_sepolicy_vndr` | `lineage` | `lineage-21.0` | LineageOS MediaTek vendor SELinux policies |

---

## Repo Initialization & Sync Instructions

### 1. Initialize LineageOS Source Tree

Create a working directory for LineageOS and initialize the upstream manifest (use `-b lineage-21.0` for Android 14 baseline or `-b lineage-22.1` for Android 15):

```bash
mkdir -p ~/android/lineage
cd ~/android/lineage

repo init -u https://github.com/LineageOS/android.git -b lineage-21.0 --git-lfs
```

### 2. Install Local Manifest

Clone this repository directly into `.repo/local_manifests` (or copy `poplar.xml` into `.repo/local_manifests/`):

```bash
git clone https://github.com/BangerSoul/local_manifests_poplar.git -b lineage-21.0 .repo/local_manifests
```

*(Note: For a `lineage-22.1` build, update the `revision` attributes in `.repo/local_manifests/poplar.xml` to `lineage-22.1` after confirming upstream branch availability.)*

### 3. Sync Sources

```bash
repo sync -c -j$(nproc) --force-sync --no-clone-bundle --no-tags
```

### 4. Start Build

> **Kernel Source vs. Prebuilt GKI Fallback**: `device/lenovo/poplar/BoardConfig.mk` checks `$(wildcard kernel/lenovo/poplar/Makefile)`. Because `kernel/lenovo/poplar` tracks `tb311fu_defconfig`, hardware patches, and `assemble_boot_vector.py` without full C sources, either extract the Lenovo GPL `kernel-6.6` archive / clone `kernel_lenovo_TB311FU` into `kernel/lenovo/poplar/` for inline compilation, or stage a prebuilt `Image.gz` at `device/lenovo/poplar/prebuilt/Image.gz` (compiled standalone or unpacked from stock `boot.img` via `assemble_boot_vector.py --unpack`). Optionally stage `device/lenovo/poplar/prebuilt/dtbo.img` from stock `dtbo.img` to set `BOARD_PREBUILT_DTBOIMAGE` and include `dtbo` in `AB_OTA_PARTITIONS` (when omitted, `BoardConfig.mk` excludes `dtbo` from `AB_OTA_PARTITIONS` so `m bacon` builds without error).

```bash
source build/envsetup.sh
lunch lineage_poplar-userdebug
m bacon
```

---

## Repository & GitHub Publishing

- **Remote (`origin`)**: `https://github.com/BangerSoul/local_manifests_poplar.git`
- **Target Branch**: `lineage-21.0`

```bash
git remote -v
git push -u origin lineage-21.0
```
