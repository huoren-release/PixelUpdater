# Pixel Updater Fork

本仓库 fork 自 [PixelUpdater/PixelUpdater](https://github.com/PixelUpdater/PixelUpdater)（其本身 fork 自 [Custota](https://github.com/chenxiaolong/Custota)），沿用原项目 **GPL-3.0** 协议，原 [LICENSE](LICENSE) 完整保留，各源文件版权头未改动。

以下为相对上游 PixelUpdater/PixelUpdater（fork 基点 `9304cb8`）的全部修改：

## 新增：从本地 zip 安装 OTA

- 支持通过系统文件选择器选择本地 OTA zip 直接安装
- 优先零复制：直接使用 content provider 返回的 fd 传给 `applyPayloadFd`，不再整包复制
- 不支持零复制时降级为整包复制，复制为原子操作，中断不会留下损坏的包
- 修复 DirectFdSource 中 FileInputStream 引用被提前回收导致 fd 失效的问题
- 设置中增加管理入口：可清除已选本地文件、恢复网络更新
- app 重启后清除本地文件选择，回落到配置的 OTA URL

## 修复：从 URL 安装

- 切换 OTA 来源（URL / 本地文件）时立即作废已缓存的检查结果
- 从 OTA metadata 的 postcondition 解析友好版本号用于显示
- 修复版本号格式化字符串在 root shell 中变量被吞的问题

## 修复：兼容性与构建

- 跳过缺失的 dm-verity care map：Android 17 Q3+ 的 OTA 包不再包含 care_map.pb
- 发布构建使用本地 keystore 自动签名

## 试图修复：OTA 后自动修补 Magisk（尚未在真机完整验证）

原实现在系统更新安装后修补对侧槽位 boot 镜像时，存在"报告成功但重启后无 root"的静默失败：

- 补齐官方 Magisk app 的 root shell 环境（`PATH`、`MAGISKTMP`），消除 `install_magisk` 末尾 `copy_preinit_files` 步骤的假失败
- `install_magisk` 改在隔离子 shell 中运行，其 `exit 1` 不再杀死 app 的常驻 root shell；目标槽位缺失时从 `getprop ro.boot.slot_suffix` 重新推导
- 不再信任脚本退出码或输出字符串：刷写后用 magiskboot 实际检查对侧槽位 boot 镜像是否包含 Magisk 补丁

## 安装

在 Magisk app 中刷入 [最新 Release](https://github.com/huoren-release/PixelUpdater/releases/latest) 的 Module zip。
