# Xiaomi Mi 9 Pro 5G (crux) — vendor tree

English | [简体中文](README_CN.md)

Proprietary vendor blobs for the Xiaomi Mi 9 Pro 5G (codename `crux`), used for Android 13-era ROM bring-up.

Pairs with:

- [device_xiaomi_crux](https://github.com/coachpo/device_xiaomi_crux) — device tree
- [kernel_xiaomi_crux](https://github.com/coachpo/kernel_xiaomi_crux) — kernel source

`thirteen-plus` is the only working branch. This source baseline combines the
PixelExperience Cepheus donor set with identified Crux sensor and SDX50 inputs;
it has not passed a complete ROM build or hardware validation.

## Inputs and regeneration

See [PROVENANCE.md](PROVENANCE.md) for the imported blob sources, pinned
configuration files, extraction tool revision, and validation boundary.

From the Android source root:

```sh
python3 device/xiaomi/crux/update-sha1sums.py --check
device/xiaomi/crux/setup-makefiles.sh
```

The first command checks the complete blob inventory and all pinned SHA-1 values.
The second regenerates `Android.bp`, `Android.mk`, `BoardConfigVendor.mk`, and
`crux-vendor.mk` using the PixelExperience `tools/extract-utils` checkout.
Keep packaging changes in the device extraction list or generator script.
