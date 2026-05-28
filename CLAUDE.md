# ORKA 绘听 1.0.0 — Claude 工作指南

## 项目概述

**Orka 助听器伴侣 App 的 iOS PWA 交互原型。**
- 单文件实现：所有 HTML、CSS、JS 都在 `index.html`（约 6500+ 行）
- 设计 token 集中在 `orka-tokens.css`
- 完整设计系统文档见 `design-system.md`
- 目标设备：iPhone（390×844），模拟手机外壳展示

---

## 文件结构

| 文件 | 作用 |
|------|------|
| `index.html` | 全部 HTML + CSS + JS，单一真相来源 |
| `orka-tokens.css` | CSS 自定义属性（颜色、间距、圆角、阴影、动画） |
| `design-system.md` | 组件清单、排版规范、动画参数（开发参考） |
| `tokens.json` | 设计 token 的 JSON 导出（备用） |

---

## 导航流程（CRITICAL — 改界面前必读）

### 层级结构

```
.phone（手机外壳容器）
├── .status（iOS 状态栏，固定顶部）
├── main.scroll × 4（主页面，同一时间只有一个 display:block）
│   ├── #volumePage     ← Tab 1：音量控制
│   ├── #noisePage      ← Tab 2：降噪模式
│   ├── #mediaPage      ← Tab 3：媒体（流媒体/通话）
│   └── #listeningPage  ← Tab 4：聆听方案
├── .tabbar（底部标签栏，始终可见）
├── .disconnect-overlay ← 蓝牙断连状态（z-index 叠加）
├── .bt-pair-overlay    ← 蓝牙配对弹窗
├── .splash-overlay     ← 启动页（最先显示）
├── .family-overlay#friendFlow  ← 亲友代调整体容器
├── .user-code-overlay  ← 用户端连接码视图
├── .user-perm-overlay  ← 用户授权弹窗
├── .user-remote-overlay← 远程调节中状态
└── .net-error-overlay  ← 网络错误弹窗
```

### Tab 导航

Tab 点击 → 隐藏所有 `main.scroll` → 显示对应 `data-page` 的页面。  
Tab class `.active` 控制高亮状态。

### Overlay 显示规则

所有 overlay 默认 `display:none`，加 `.active` class 后显示。  
`disconnectOverlay` 用 `data-state="disconnected|connected"` 控制内部状态。

---

## 亲友代调流程（最容易搞混的部分）

```
启动页 splashOverlay
  ├── "探索" → 关闭 splash，进入主 Tab 区
  └── "我是家属" → 打开 friendFlow (data-mode="friend")

friendFlow (data-mode="friend")  ← 家属视角
  fpWelcome → fpNickname → fpBind → fpConnecting → fpHome
  
  fpHome（好友端绑定后主页）包含：
  - 产品图 + 设备名 + 序列号 chip
  - "申请连接"按钮 (#fhConnectBtn)
  - "操作指引"卡片 (.fh-guide)
  - 危险区：解除家人绑定 (#fhUnbindBtn)

userCodeOverlay (data-mode="user")  ← 用户视角（被调节方）
  uc-code-card    → 显示 4 位连接码
  ucBoundView     → 已绑定好友后的状态视图（含解除绑定）
```

**关键：friend 视角 ≠ user 视角。**  
- `fh-*` class = friend home（家属操作界面）  
- `ucbv-*` class = user code bound view（用户端已绑定状态）  
- `uc-*` class = user code（用户连接码区域）

### 页面内子状态

| 页面 | 子状态 |
|------|--------|
| `listeningPage` | 点击方案卡片 → 打开 `listeningDetail`；`.detail-back` 关闭 |
| `noisePage` | 4 个 mode 按钮切换 `data-mode`（off/comfort/strong/ultimate） |
| `mediaPage` | stream/call 双 tab，控制对应 panel 显示 |

---

## CSS 约定

### Class 命名前缀

| 前缀 | 对应区域 |
|------|----------|
| `fh-` | Friend Home（好友端绑定后主页） |
| `fp-` | Friend flow Page（亲友流程步骤） |
| `uc-` | User Code（用户连接码区域） |
| `ucbv-` | User Code Bound View（用户已绑定状态） |
| `tut-` | Tutorial（引导步骤） |
| `bt-` | Bluetooth 配对相关 |
| `urb-` | User Remote Banner |

### Token 使用规则（来自 Figma 同步）

- 颜色必须引用 `orka-tokens.css` 里的 CSS 变量，不写裸 hex 值
- 若 Figma 颜色在 token 里无精确匹配，标出来让设计师决定
- 已知 token 速查：`--blue-main #234d77`、`--warm-main #c89e72`、`--txt-dark #18212d`、`--txt-soft #6d7682`、`--color-error #FF4B3A`

---

## Figma → HTML 同步工作流

1. 在 Figma 选中 Frame，复制链接
2. 发链接给 Claude，说明目标 + "颜色用 orka-tokens.css 变量"
3. Claude 通过 MCP 读取 Figma 数据，比对 token，更新 index.html

Figma MCP 已配置为项目级 SSE 服务器，重启 Claude Code 后自动可用。
