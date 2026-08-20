# DESIGN.md — 设计与维护规范

> 本文件是这个站点的单一设计事实源。任何改动（人或 AI 会话）动手前先读它，交付前过底部的质量闸。
> 已拍板的方向不因单次会话的偏好推翻；要改方向，先改本文件。

## 1. 设计方向（已拍板，2026-08）

**浅色工程风 + 暗色终端块**：页面浅色承载长文可读性，终端组件保持暗色承载技术身份。终端/OS 是全站唯一隐喻，从开机仪式贯穿到 `$ ./restart` 收尾。参考对象为 hiesther.me 的**信息架构与交互逻辑**（行为层面）；其代码仓无 LICENSE，一行代码不得复制。

## 2. Token

| Token | 值 | 用途 |
|---|---|---|
| `--bg` | `#fafaf9` | 页面底（暖石白，非纯白） |
| `--surface` | `#ffffff` | 卡片面 |
| `--border` | `#e7e5e4` | 描边 |
| `--text` | `#1c1917` | 主文字（非纯黑） |
| `--muted` | `#57534e` | 次要文字 |
| `--faint` | `#a8a29e` | 辅助文字 |
| `--accent` | `#0f766e` | 唯一强调色（深青绿），链接/悬停/标记 |
| `--term-bg / bar / border` | `#0d1117 / #161b22 / #30363d` | 终端暗面 |
| `--term-text / muted / green` | `#e6edf3 / #8b949e / #3fb950` | 终端文字/提示符 |
| `--radius` | `10px` | 全站唯一圆角 |

规则：页面层只有 `--accent` 一个强调色；`--term-green` 只在终端组件内出现，不外溢到页面层。不引入第二强调色。

## 3. 字体与排版

- 中文正文：系统栈（PingFang SC / Noto Sans SC），不加载中文 webfont（体积不可接受）。
- 等宽：`ui-monospace / SF Mono / JetBrains Mono` 栈，用于终端、日期、标签、水印。
- 尺寸全部 `clamp()` 流式：section 间距 `clamp(72px, 11vh, 128px)`，标题 `clamp(1.7rem, 3.4vw, 2.15rem)`。
- 字号对比拉开：大的够大（水印 `clamp(2.8rem, 8vw, 4.6rem)`），小的真的小（辅助 0.78rem）。
- 禁用 em-dash（—）与 en-dash（–）；日期区间用连字符。

## 4. 终端组件

结构：`term-bar`（红黄绿灯 + 标题 `leiyuxuan@web ~ zsh`）+ `term-body`（行）。行三型：`cmd`（`$` 提示符 + 命令）、`out`（输出）、光标行。输出**瞬时打印，永不淡入**（真实终端没有 fade）。

## 5. 开机仪式（首访交互）

状态机 `typing → ready → launching → done`：

1. **typing**：命令逐字符敲出（字符间 35-85ms 随机抖动），光标跟随；输出行瞬时出现，命令敲完停 160ms 模拟回车。
2. **ready**：出现「按 Enter 或点击终端启动」。
3. **launching**：提示符自动敲入 `open ~/portfolio` → ASCII 进度条 → shell 回到新提示符。
4. **done**：FLIP 让终端滑入常驻位，内容在下方展开。叙事必须闭合：命令打开的就是本页。

**skip 路径（不可删）**：回访（localStorage `lyx_site_visited`）、带 hash 深链、`prefers-reduced-motion` 三者任一 → 完全静态直出，不锁滚动；typing 期间任意键/点击 → 立即完成。`?boot` 查询参数强制重演仪式（调试用）。

## 6. 布局纪律

- 每个板块布局家族不同；平行内容（工作/研究）允许共用叙事块家族。
- 大号透明 mono 水印（WORK / RESEARCH / OPEN SOURCE）仅限三个主板块，opacity ≤ 0.05，不再加。
- 无滚动提示、无小节编号、无装饰性状态点；终端红黄绿灯是隐喻语义，不算。
- 图片一律 `loading="lazy"` + 显式宽高，装进 `.fig` 容器，caption 只写功能性一句话。
- 移动端是重排不是缩小；横向溢出零容忍（`overflow-x: clip` 兜底水印）。

## 7. 上站质量闸（每次改动交付前过）

1. **披露闸**：站点公开可索引。内部信息（公司内部统计与未经外部验证的数字、内部服务名、未发布产品名、同事真名）不上站。具体禁用清单维护在**私有台账**（不随本仓分发），改内容先对照它。
2. **证据闸**：每个上站数字必须可溯源到公开可验证的来源（GitHub、公开 README、已发布产物）。溯源对照表在私有台账。
3. **文字闸**：零 em-dash；逐条重读可见文案，绕嘴/解释腔/AI 腔改掉。
4. **无障碍闸**：`prefers-reduced-motion` 全覆盖；对比度 WCAG AA；仪式必须有 skip 路径；键盘可达（`./restart` 有 tabindex）。
5. **技术闸**：内联 JS 过 `node --check`；无外链依赖（字体、JS、CSS 全内联或同仓）；单页总重克制。

## 8. 发布

- 部署 = push 到 `PlutoLei/PlutoLei.github.io` main 分支，GitHub Pages 自动构建。
- 图片资产放 `assets/`，宽度 ≤ 1600px。
