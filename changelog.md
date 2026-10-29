### 2026-10-29 v0.210.r20.g2242f91-release

修正发布产物：此前的 zip 里 `module.prop` 的 `updateJson` 指向上游
`PixelUpdater/PixelUpdater`，导致 Magisk 内检查更新会认为上游 v0.210 更新，
实际是降级。现已改为指向本 fork 的 `update-links` 分支。

- 修复从 URL 安装
- 新增从本地 zip 安装
- OTA 后自动修补 Magisk（已用 magiskboot 验证对侧槽位）
