# wx-md

把 Markdown 渲染成**可以直接粘贴进微信公众号编辑器**的 HTML —— 样式全部内联，粘过去不掉版。

> npm：`npm install wx-md` · 当前版本 v0.1.1

## 解决什么问题

微信公众号后台自带的编辑器不加载外部 CSS/JS，只保留标签的内联 `style`。所以从别处复制 Markdown 渲染结果粘进去，标题、列表、代码块的排版全丢。

wx-md 的做法是：**渲染时就为每个标签生成内联样式**，输出的 HTML 不依赖任何 `class`、外部样式表或定位属性，粘到公众号后台后排版原样保留。

## 设计要点

| 约束 | 应对 |
|---|---|
| 公众号不加载外部 CSS/JS | 自定义 `markdown-it` 的 renderer rules，给每个标签写内联 `style` |
| 编辑器会过滤部分标签和属性 | 输出保守的 HTML 结构，避免复杂嵌套与 `position` 定位 |
| 正文图片有大小限制 | 本地图片转 base64 内嵌；超限时提示并支持压缩 |
| 代码块粘过去掉色 | 高亮配色直接写进内联样式，不用外部主题文件 |

## 规划中的 API

纯 JS（ESM + UMD），无框架依赖：

```js
import { render, copyToClipboard, THEMES } from "wx-md";

// Markdown 字符串 -> 带内联样式的 HTML 字符串
const html = render(markdownSource, {
  theme: "wechat-green",   // 主题名，见 THEMES
  fontSize: 16,            // 正文字号（px）
  lineHeight: 1.75,        // 行高
  imageBase64: true,       // 本地图片是否转 base64
});

// 作为富文本复制到剪贴板
await copyToClipboard(html);
```

## 文档

| 文件 | 内容 |
|---|---|
| [`PRD.md`](./PRD.md) | 产品需求文档：功能需求（FR-1~FR-7）、平台限制与适配、API 设计、验收标准（AC-1~AC-8） |
| [`docs/design.md`](./docs/design.md) | 技术设计 |
| [`docs/design_guide.md`](./docs/design_guide.md) | 设计规范 |
| [`docs/proposal.md`](./docs/proposal.md) | 方案提案 |
| [`docs/tasks.md`](./docs/tasks.md) | 任务拆分 |
| [`docs/ui-design.html`](./docs/ui-design.html) | 可交互的 UI 原型 |

## 开发流程说明

这个项目同时是一次 **AI 辅助开发流程（SDD）** 的实践：先产出 PRD 明确边界 → 技术设计 → UI 原型 → 拆成细粒度任务再交给 AI 实现。

核心判断是**不能把大项目整个丢给 AI**：一是上下文有限，二是一次性处理的内容越多，AI 对细节的关注度越低。所以流程上强制做两件事——**精细的任务拆分**，以及**明确的项目规则约束**（任务执行边界、代码风格、技术栈、沟通方式）。

## 支持的 Markdown 语法（MVP 范围）

标题 H1~H6、段落与强调、有序/无序/嵌套/任务列表、引用、分割线、脚注、行内代码与代码块（带语言标识）、表格（含对齐）、链接、图片。

数学公式（KaTeX）和流程图（mermaid）在 MVP 中降级为纯文本，属于后续版本范围。

## 后续规划

- 图床上传（腾讯云 / 阿里云 OSS）
- 在线编辑工具（打开即用的 Web 页面）
- CLI 命令行工具（`.md` → HTML）
- 数学公式、mermaid 流程图
- 主题商店

## License

MIT
