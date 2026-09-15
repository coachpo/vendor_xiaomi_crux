# Proprietary input provenance

## Imported baseline

The binary baseline is vendor commit
[`07e0972f5b669a445f336264ac9c74f1f86244ff`](https://github.com/coachpo/vendor_xiaomi_crux/commit/07e0972f5b669a445f336264ac9c74f1f86244ff).
Its commit message records an import of
`PixelExperience-Blobs/vendor_xiaomi_cepheus`, branch `thirteen`, with generated
device references renamed to `crux`. This is an inherited cross-device blob set;
the import does not establish that every blob came from a stock Crux image.
The original import did not record the complete upstream commit or a stock-image
digest, so those details must not be inferred from the branch name.

The matching device extraction list records donor groups including nabu graphics
and Keymaster, vayu Listen and HotwordEnrollment, coral aptX, and QSSI 13 DPM,
RIL, and IMS. Those source comments describe the inherited selections. Their
144 SHA-1 pins identify the selected bytes; unpinned entries are tracked by the
vendor Git revision. SHA-1 here is the extract-utils pin format, not an image
signature or a security verification scheme.

## Crux hardware inputs

The Crux additions come from the public, stock-derived
[`AndroidBlobs/vendor_xiaomi_crux`, `92cfc58837275219946d74430f8f23c95de3c5b1`](https://github.com/AndroidBlobs/vendor_xiaomi_crux/tree/92cfc58837275219946d74430f8f23c95de3c5b1),
labelled `crux-user-9-PKQ1.190714.001-V10.4.6.0.PFXCNXM-release-keys`.
Files were checked against the Git blob IDs at that commit and recorded with
individual SHA-1 pins in the device extraction list. This establishes which
public dump supplied the bytes; the original Xiaomi package signature and digest
have not been verified.

### Sensor registry

All 41 JSON files present in that dump's `vendor/etc/sensors/config` directory
are imported unchanged. The old Cepheus platform filters omitted `CRUX`, although
the kernel's `socinfo` exposes that platform name. The Crux configuration also
sets the TCS3701 interrupt number to `119`, with its own light coefficients and
AOD thresholds. Sensor registry interrupt identifiers are retained as supplied;
they are not inferred from similarly numbered AP GPIOs.

The two inherited SHTW2 files have no Crux dump counterpart and remain excluded
by their platform filters. `sns_reg_config` retains the existing version `5` and
paths; the older dump differs only by version `2`, which is not needed to select
the Crux JSON files. Sensor HAL/DSP interoperability still needs hardware testing.

### SDX50 loader

The following missing components are imported unchanged from the same commit:

| Path below `proprietary/` | SHA-1 |
| --- | --- |
| `vendor/bin/mdm_helper` | `67179e70b4749bfe37576e13d2a081c4d1fb30aa` |
| `vendor/bin/ks` | `2198e9127990350f4a9205deceb2754b35c6b392` |
| `vendor/lib64/libmdmimgload.so` | `560cfe3afb59ecf693e48c242efd031f5b291c13` |

The Crux kernel's external SDX50 device waits for a userspace ESOC request engine.
`mdm_helper` implements that engine and uses `libmdmimgload.so`, which launches
`/vendor/bin/ks` for image transfer. Their ELF headers are AArch64. The existing
`libmdmdetect.so` exports the required `esoc_framework_supported`, `get_soc_link`,
`get_soc_name`, and `get_system_info` symbols. The remaining shared-library
dependencies are the standard vendor libc, libm, libdl, libc++, libcutils,
libutils, and liblog. This symbol/dependency check does not establish runtime
ABI or modem firmware compatibility.

The loader reads the existing `/vendor/firmware_mnt/image/sdx50m` images and modem
EFS partitions. It does not add a firmware payload to the OTA or update ABL/XBL.
The device tree supplies the service declaration; matching Qualcomm SELinux
policy provides its executable, ESOC, MHI, firmware, and EFS labels. Boot and modem
registration must be tested with the retained Crux stock firmware.

## Widevine init fixup

The inherited Widevine 1.3 RC starts an unprovided
`/system/bin/move_widevine_data.sh`. Google's
[original integration change](https://android.googlesource.com/device/google/wahoo/+/807b727fd90d43caccf03c7f9476a506acb19768%5E%21/)
defines this helper for moving pre-Pie DRM data during an Oreo-to-Pie upgrade.
Crux launched with Pie, and this baseline supports a clean Crux installation,
not migration of pre-Pie DRM storage. The extraction fixup removes only the
legacy service and its start command, retaining the DRM directory creation and
the complete Widevine 1.3 HAL service. Original and post-fixup RC hashes are pinned
together. This does not establish Widevine L1 availability or license validity.

## Remaining hardware provenance

| Group | Current baseline and validation still needed |
| --- | --- |
| Camera | Inherited Cepheus camera HAL, sensor modules, tuning and calibration tables; retain the two Android compatibility shims. Match the complete group to a current Crux dump before claiming camera support. |
| Radio / IMS | Inherited radio stack and QSSI 13 compatibility group, plus the Crux SDX50 loader above. Data XML includes `fusion_sm8150_sdx50`; its presence alone does not prove 5G registration or data. |
| NFC | Inherited SN100 firmware and the device tree's source-built NXP HAL/configuration; firmware/controller compatibility and secure-element routing need hardware validation. |
| Sensors | Crux registry JSON with inherited HAL and DSP-facing libraries. Verify sensor enumeration, light/proximity readings, wake gestures and suspend. |
| Audio / thermal | Inherited ACDB, mixer configuration, thermal binaries and encrypted policy profiles remain donor inputs. Validate them against the matching Crux firmware and physical hardware. |

## XML source bytes

The following upstream files reproduce the device list's original pins exactly:

| File below `proprietary/` | SHA-1 | Upstream source |
| --- | --- | --- |
| `vendor/etc/listen_platform_info.xml` | `02c1eab57442e34135d542d2205c3e77033c7d54` | [Listen import from vayu, `baa1042`](https://github.com/PixelExperience-Blobs/vendor_xiaomi_cepheus/blob/baa104283de889d52914233a39c23501d30cf98e/proprietary/vendor/etc/listen_platform_info.xml) |
| `system_ext/etc/permissions/com.android.hotwordenrollment.common.util.xml` | `563a58554d9c0dd0608552b644fbf74519a447bb` | [Hotword update, `0a9af03`](https://github.com/PixelExperience-Blobs/vendor_xiaomi_cepheus/blob/0a9af03581ac993a3515a1b0fb57bc2bf1ded737/proprietary/system_ext/etc/permissions/com.android.hotwordenrollment.common.util.xml) |

The local baseline lost one final newline in each XML. Restoring it reproduces
the upstream files byte for byte without changing their configuration or pins.
The Hotword permission XML predates the vayu group import; its upstream commit
is titled `cepheus: Update Hotword blobs from op8t`. The group label alone is
therefore not an exact source attribution for this individual file.

## Extraction and generated build files

The supported helper is PixelExperience
[`tools_extract-utils`, `36b53ca61b9b111ac5829e5e7cac516364637962`](https://github.com/PixelExperience/tools_extract-utils/tree/36b53ca61b9b111ac5829e5e7cac516364637962),
branch `thirteen`. Put it at `tools/extract-utils` in the Android source tree,
together with the extraction prebuilts provided by the matching PE manifest.
`extract-files.sh` uses the helper's configured `PATCHELF`, including an explicit
caller override, for the camera fixups:

- `camera.qcom.so`: remove `libMegviiFacepp-0.5.2.so` and `libmegface.so`; add
  `libshim_megvii.so` once.
- `com.qti.node.watermark.so`: add `libpiex_shim.so` once.

The checked-in binaries already have these dependencies. An extraction error
while applying either fixup stops the script. After extraction, the complete
retained inventory and pins are checked before generating build files, because
extract-utils can report an unavailable input without failing its shell command.

`proprietary-files.txt` is the packaging source of truth. Regeneration with the
helper above produces the checked-in build files. All 916 unique file paths are
present and packaged, and all 29 generated Soong modules are selected by the
vendor product makefile. There are 189 pinned entries: the 144 inherited pins,
41 Crux sensor files, three SDX50 loader files, and the Widevine RC fixup pair.

Run `python3 device/xiaomi/crux/update-sha1sums.py --check` from any working
directory using the appropriate path to the script. It checks missing or
unlisted blobs, duplicate destinations, and every source/fixup hash pin without
modifying files. Without `--check`, the script adds missing donor pins while
preserving existing source hashes and optional post-fixup hashes. A mismatch
fails instead of silently replacing the recorded source. For an intentional
donor update, use `extract-files.sh --kang <source>` to obtain source and
post-fixup hashes, verify the source, and update the relevant entries together.

These checks establish input and packaging consistency. ELF symbol resolution,
VINTF compatibility, and camera, radio, audio, and firmware behavior still need
the complete Android build and device testing.
