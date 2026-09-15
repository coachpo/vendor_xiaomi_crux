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
while applying either fixup stops the script.

`proprietary-files.txt` is the packaging source of truth. Regeneration with the
helper above produces the checked-in build files. All 913 unique file paths are
present and packaged, and all 29 generated Soong modules are selected by the
vendor product makefile.

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
