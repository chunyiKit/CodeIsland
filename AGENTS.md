# AGENTS.md

## 项目概览
- 这是一个基于 Swift Package Manager 的 macOS 应用，目标平台为 `macOS 14+`，Swift 版本为 `5.9+`。
- 产品目标：在 MacBook 刘海/灵动岛区域实时展示 AI coding agent 的会话状态、工具调用、权限请求与问答。
- 核心链路是：各类 CLI/IDE hook → `codeisland-bridge` / OpenCode 插件 → Unix socket → 主应用更新 UI。

## 目录说明
- `Sources/CodeIsland/`：主应用代码，包含 SwiftUI / AppKit UI、`AppState`、窗口控制、设置页、hook 服务端等。
- `Sources/CodeIslandCore/`：共享模型与纯逻辑，包含事件归一化、session 聚合、socket 路径等；优先把可测试逻辑放这里。
- `Sources/CodeIslandBridge/`：桥接可执行文件入口。
- `Tests/CodeIslandCoreTests/`：核心逻辑测试。
- `Tests/CodeIslandTests/`：应用层测试。
- `docs/island-state-machine.zh-CN.md`：Island 的 Surface/AgentStatus 状态机、尺寸规则、显隐条件与交互转移总览。
- `README.md` / `README.zh-CN.md`：英文/中文说明文档，产品行为有变化时尽量同步。

## 开发约定
- 保持改动小而集中，遵循现有 Swift 风格，不随意重命名或搬动文件。
- UI 状态与窗口交互优先沿用现有 `@MainActor` / `@Observable` 模式，避免把 UI 状态逻辑塞进非主线程对象。
- 共享数据结构、事件解析、归一化、session 推导优先放在 `CodeIslandCore`，并补对应测试。
- 所有用户可见文案统一走 `Sources/CodeIsland/L10n.swift`，新增文案时同时补全 `en` 和 `zh`。
- 若新增或调整 hook / 事件支持，至少同步检查：
  - `Sources/CodeIsland/ConfigInstaller.swift`
  - `Sources/CodeIslandCore/EventNormalizer.swift`
  - `Sources/CodeIslandCore/SessionSnapshot.swift`
  - 相关测试与 README
- 不要改 `.build/` 产物；不要引入新依赖，除非确有必要。

## 常用命令
- 构建：`swift build`
- 测试：`swift test`
- 定向测试：`swift test --filter CodeIslandCoreTests`
- 本地启动（按 README 约定）：`swift build && open .build/debug/CodeIsland.app`
- 发布构建：`./build.sh`

## 修改前优先了解
- 先看 `README.zh-CN.md` 了解产品目标与用户视角。
- 再看 `Package.swift` 确认 target 边界。
- 涉及状态流转时，优先读 `Sources/CodeIsland/AppState.swift`、`Sources/CodeIsland/HookServer.swift`、`Sources/CodeIslandCore/SessionSnapshot.swift`。
- 涉及设置或文案时，优先读 `Sources/CodeIsland/Settings.swift`、`Sources/CodeIsland/SettingsView.swift`、`Sources/CodeIsland/L10n.swift`。
