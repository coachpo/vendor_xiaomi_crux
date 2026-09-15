# 小米 Mi 9 Pro 5G (crux) —— vendor 树

[English](README.md) | 简体中文

小米 Mi 9 Pro 5G(代号 `crux`)的专有 vendor blobs,用于 Android 13 时代的 ROM 移植。

配套仓库:

- [device_xiaomi_crux](https://github.com/coachpo/device_xiaomi_crux) —— 设备树
- [kernel_xiaomi_crux](https://github.com/coachpo/kernel_xiaomi_crux) —— 内核源码

`thirteen-plus` 分支跟踪 A13 移植状态。

## 来源和打包文件再生成

[PROVENANCE.md](PROVENANCE.md) 记录 blobs 的导入来源、固定配置文件、
提取工具版本及验证范围。

在 Android 源码根目录执行：

```sh
python3 device/xiaomi/crux/update-sha1sums.py --check
device/xiaomi/crux/setup-makefiles.sh
```

第一条命令核验完整文件清单和所有 SHA-1 固定值。第二条命令使用
PixelExperience 的 `tools/extract-utils` 生成 `Android.bp`、`Android.mk`、
`BoardConfigVendor.mk` 和 `crux-vendor.mk`。打包变更应修改设备树中的提取清单或生成脚本。
