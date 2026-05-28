# Orka 绘听 1.0.0

Orka 助听器伴侣 App 的 **iOS PWA 交互原型**——演示音量控制、降噪模式、媒体流转、亲友远程代调、家属端使用数据看板等核心场景。

🌐 **在线演示**：https://orka-app.julie-gao369.workers.dev

> 推荐用 iPhone Safari 打开（"分享 → 添加到主屏幕"后可作为 PWA 独立运行）；桌面浏览器中会以 iPhone 390×844 外壳的形式展示。

---

## 项目结构

```
绘听1.0.0/
├── index.html               # 主原型（中文，HTML + CSS + JS 全在这一个文件）
├── index-en.html            # 英文版（备份）
├── index-zh.html            # 中文备份
├── orka-tokens.css          # 设计 token 源（颜色 / 间距 / 圆角 / 阴影 / 动画）
├── design-system.md         # 组件清单 + 排版规范
├── orka-components.html     # 组件库展示页
├── tokens.json              # 设计 token 的 JSON 导出
├── CLAUDE.md                # 给 AI 协作者的项目工作指南（导航流程、CSS 约定、Figma 工作流）
├── fonts/                   # Inter / Playfair
├── *.png / *.webp / *.svg   # 产品图、icon、背景图
├── manifest.json            # PWA 元数据
├── wrangler.toml            # Cloudflare Workers 部署配置
├── .assetsignore            # 部署时排除的文件清单
└── .github/workflows/       # CI 自动部署
```

## 已实现的主要界面

**用户端（底部 4 个 tab）**
- 音量控制：左右耳音量 + 通用调节
- 降噪模式：off / 舒适 / 强劲 / 极致 + 自定义场景
- 媒体：流媒体 / 通话双面板
- 聆听方案：进入方案详情 + 远程验配入口

**亲友代调流程**（splash → 我是家属）
- 教程 → 昵称 → 蓝牙绑定 → 连接中 → 家属主页
- 家属端两个 tab：代调 / 使用数据
- **使用数据看板**：关怀 banner（达标/未达标双态、声波弧纹）+ 总时长/自上次记录下拉切换 + 推荐12h 阈值对比条 + 聆听环境/程序偏好/音量档位

**用户端连接码视图**（被代调方）
- 4 位连接码 + 已绑定亲属管理 + 解除绑定

详细的层级关系、CSS 命名前缀（`fh-`/`fp-`/`uc-`/`ucbv-`/`tut-`/`bt-`/`urb-`）和 Figma 同步工作流见 [CLAUDE.md](./CLAUDE.md)。

---

## 本地开发

零构建、零依赖。最简单：

```bash
# 在浏览器直接打开
open index.html
```

如果需要 PWA / service worker / 跨域请求行为，起一个本地 server：

```bash
python3 -m http.server 8000
# 访问 http://localhost:8000
```

或：

```bash
npx serve .
```

---

## 部署

### 自动（推荐）

push 到 `main` 分支 → GitHub Actions 自动跑 `wrangler deploy` → 约 30 秒后线上更新。

```bash
git add .
git commit -m "feat: 加了某某交互"
git push
```

查看部署状态：

```bash
gh run watch                # 看最新一次 workflow
gh run list --limit 5       # 看最近 5 次
```

### 手动（调试或紧急时）

```bash
npx wrangler deploy         # 需要 CLOUDFLARE_API_TOKEN 在 ~/.zshrc
```

`.assetsignore` 控制哪些文件**不**部署到 Cloudflare（如 `.md`、`orka-tokens.css`、`index-en/zh.html`、`.claude/`、`.wrangler/` 等）；只有 `index.html` 和它引用的资源上线。

---

## 设计同步（Figma → HTML）

设计 token 集中在 `orka-tokens.css`。从 Figma 同步样式时：

- **颜色必须引用** `orka-tokens.css` 里的 CSS 变量（如 `var(--blue-main)`），不写裸 hex 值。
- Figma 颜色在 token 里无精确匹配时，标出来让设计师决定。
- 已知 token 速查：`--blue-main #234d77`、`--blue-deep #102b46`、`--warm-main #c89e72`、`--txt-dark #18212d`、`--txt-soft #6d7682`、`--color-error #FF4B3A`。
- 家属端使用数据看板另用一组 token（`--fpd-ink/-muted/-track/-fill/-met/-accent`），同步自 Figma 数据看板 DS。

相关 Figma 文件：
- 设计系统：`WjU4vBX87UWVEMeqNPt871`
- 数据看板：`dBl3ZoaVm5WU1DZ6nkFfsw`

---

## 技术备注

- **单文件原型**：所有 HTML / CSS / JS 都在 `index.html`（约 7500+ 行）。这样部署/分享简单，代价是大模块编辑时上下文密集——参考 `CLAUDE.md` 的导航说明。
- **不可变性优先**：JS 改 DOM 时避免就地变更全局状态；用 class toggle + 数据驱动 render 函数（如 `fpdRender(scope)`）。
- **目标设备**：iPhone（390×844）。所有截图/调试都以这个尺寸为准。
- **总仓库 ≈ 1.4 MB**，`index.html` 单文件 ≈ 316 KB。
