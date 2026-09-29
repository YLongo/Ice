<div align="center">
    <img src="Ice/Assets.xcassets/AppIcon.appiconset/icon_256x256.png" width=200 height=200>
    <h1>Ice（个人维护分支）</h1>
    <p>基于 <a href="https://github.com/jordanbaird/Ice">jordanbaird/Ice</a> 的菜单栏管理工具 fork</p>
</div>

![Platform](https://img.shields.io/badge/platform-macOS-blue?style=flat-square)
![Requirements](https://img.shields.io/badge/requirements-macOS%2014%2B-fa4e49?style=flat-square)
[![License](https://img.shields.io/github/license/jordanbaird/Ice?style=flat-square)](LICENSE)

Ice 是一款强大的 macOS 菜单栏管理工具：把菜单栏图标分为三段管理——**始终显示**、**点击展开的隐藏段**、**普通操作永远不显示的 Always-Hidden 段**，同时支持外观自定义、快捷键、图标搜索等。

## 本 fork 的改动

相对上游的三处修改（详见提交历史）：

1. **系统级自动隐藏下继续工作**：上游检测到 macOS「菜单栏自动隐藏 = 始终」时会主动弃权；本 fork 强制 `isMenuBarHiddenBySystemUserDefaults = false`，让 Ice 在系统隐藏菜单栏的同时继续管理三段图标——菜单栏平时完全不占屏幕，需要时唤出，图标照样折叠管理。
2. **修复上游 bug**：切换前台应用时，focusedApp 观察者无视「AutoRehide」开关强制收起隐藏段；本 fork 补上 `autoRehide` 检查，关闭自动收起后**展开状态绝不自动收起**。
3. **entitlements 关闭 library validation**：便于用开发者证书对主程序重签后与 Sparkle 框架共存。

## 使用

| 操作 | 效果 |
|---|---|
| 点击 Ice 控制图标（或菜单栏空白处） | 展开 / 收起隐藏段（不自动收起） |
| ⌥ + 点击 Ice 控制图标 | 展开 / 收起 Always-Hidden 段（唯一入口） |
| ⌘ + 拖动菜单栏图标 | 在三段之间归类：拖过 Ice 图标进隐藏段，继续拖过第二个分界进 Always-Hidden 段 |
| 右键 / ⌃ + 点击 Ice 控制图标 | 打开 Ice 菜单（设置等） |

推荐搭配系统设置「菜单栏自动隐藏 = 始终」，这是本 fork 的设计场景。

## 构建

```sh
# 无签名构建
xcodebuild -scheme Ice -configuration Release -derivedDataPath build \
  CODE_SIGN_IDENTITY=- CODE_SIGNING_REQUIRED=NO ENABLE_HARDENED_RUNTIME=NO build

# 用开发者证书深签（主程序 + Sparkle 框架需同一签名身份）
codesign --force --deep --sign "Apple Development: <你的身份>" \
  --entitlements Ice/Ice.entitlements build/Build/Products/Release/Ice.app
```

注意事项：

- 本地构建未经公证，首次运行需在「系统设置 → 隐私与安全性」放行；若被 AMFI 执行策略拦截（启动即无报错退出），需为 app 注册执行策略例外。
- 需要授权**辅助功能**与**屏幕录制**权限，图标管理才能工作。
- 授权后若仍无法读取图标，尝试重启 app。
- 已知限制：系统级自动隐藏开启时，设置里的 Menu Bar Layout 排列面板无法渲染图标预览，请用 ⌘ + 拖动排列。

## 功能一览

### 菜单栏图标管理

- [x] 隐藏菜单栏图标（三段模型）
- [x] Always-Hidden 段
- [x] 悬停 / 点击空白 / 滚动唤出隐藏图标（可配置）
- [x] 自动收起（本 fork 默认关闭，手动收起）
- [x] 应用菜单与图标重叠时自动隐藏应用菜单
- [x] 拖拽排列图标
- [x] Ice Bar 独立展示栏（适合刘海屏）
- [x] 图标搜索
- [x] 图标间距（BETA）
- [ ] 布局配置文件
- [ ] 独立分隔图标
- [ ] 图标分组
- [ ] 条件触发显示

### 菜单栏外观

- [x] 着色（纯色 / 渐变）、阴影、边框
- [x] 自定义形状（圆角 / 分离）
- [ ] 移除菜单栏背景
- [ ] 屏幕圆角
- [ ] 深浅色模式独立配置

### 快捷键与其他

- [x] 各分段独立 toggle 快捷键
- [x] 登录启动、自动更新
- [ ] 菜单栏小组件

> [!IMPORTANT]
> 使用本 fork 时**请关闭内置自动更新**，否则 Sparkle 会用官方版覆盖本 fork 的修改。

## 为什么只支持 macOS 14+？

Ice 使用了大量 macOS 14 起才提供的系统 API，没有支持更低版本的计划。

## 许可证

[GPL-3.0](LICENSE)（继承自上游）。感谢 [jordanbaird](https://github.com/jordanbaird) 的开源工作。
