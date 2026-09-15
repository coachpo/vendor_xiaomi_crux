# 小米 Mi 9 Pro 5G (crux) —— vendor 树

[English](README.md) | 简体中文

小米 Mi 9 Pro 5G(代号 `crux`)的专有 vendor blobs,用于 Android 13 时代的 ROM 移植。

配套仓库:

- [device_xiaomi_crux](https://github.com/coachpo/device_xiaomi_crux) —— 设备树
- [kernel_xiaomi_crux](https://github.com/coachpo/kernel_xiaomi_crux) —— 内核源码

`thirteen-plus` 是唯一工作分支。本源码基线沿用 PixelExperience 的 Cepheus
参考 blobs，并引入已定位来源的 Crux 传感器配置和 SDX50 加载组件；尚未完成
完整 ROM 编译和真机验证。

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
