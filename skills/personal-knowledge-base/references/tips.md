# 工具与扩展技巧

以下内容对 Wiki 用户有价值，Agent 不需要操作它们，但可以在帮助搭建环境时引用。

---

## Obsidian 配置建议

### Web Clipper

浏览器扩展，一键将网页文章转为 Markdown 存入 `raw/articles/`。

- 安装 [Obsidian Web Clipper](https://obsidian.md/clipper)
- 设置保存路径为 `~/knowledge/raw/articles/`

### 图片自动下载

避免文章中的图片因外链失效而丢失。

1. Obsidian 设置 → 文件与链接 → Attachment folder path 设为 `raw/assets/`
2. 设置 → 快捷键 → 搜索"Download attachments for current file" → 绑定快捷键（如 Ctrl+Shift+D）
3. 剪藏文章后按快捷键，所有图片自动下载到 `raw/assets/`

### AI 读取图片的能力

当前多模态 AI 模型（如 Minimax-m3、Mimo-v2.5 等）已具备图片理解能力。不过，不同环境和模型的支持程度存在差异：

- 部分模型可能不支持图片输入
- 即使支持，也可能有图片数量、分辨率或格式限制
- 在 VS Code 等集成环境中，图片可能需要通过特定工具（如 view_image）提供

如果希望 AI 理解 Markdown 中的图片内容，可参考以下流程：

1. AI 先读取文章的文字部分
2. 用户告知需要分析的图片（可发截图或说明图片文件名）
3. AI 使用视觉工具（如 view_image）读取图片

这样能确保在各种环境下都能有效处理图片内容。

### Graph View

Obsidian 的图谱视图是查看 Wiki 结构最好的方式——哪些页面是枢纽、哪些是孤岛，一目了然。

### Dataview 插件

如果 wiki 页面的 frontmatter 规范（tags、dates、source counts），Dataview 可以自动生成动态列表和统计。例如：

```dataview
TABLE created, status
FROM "wiki/decisions"
SORT created DESC
```

列出所有决策记录。

### Marp 插件

Marp 是基于 Markdown 的幻灯片格式。Obsidian 有 Marp 插件。当用户需要输出演示文稿时，AI 会直接生成 Marp 格式的 Markdown。

---

## 搜索工具（未来扩展）

当 wiki 增长到数百个页面时，`index.md` 的线性浏览可能不够用。届时可以引入 [qmd](https://github.com/tobi/qmd)：

- 本地 Markdown 文件搜索引擎
- 混合 BM25/向量搜索 + LLM 重排序
- 有 CLI（AI 可以通过 shell 调用）和 MCP Server（AI 可以作为原生工具使用）

或者也可以让 AI 临时写一个简单的搜索脚本。什么时候觉得 index 不够用了，可以告知。

---

## 版本管理

整个 `~/knowledge/` 就是一个 Git 仓库。这带来：

- 每次变更都有历史记录，可以回退
- 可以开分支实验不同的组织结构
- 可以多设备同步（配合 GitHub / 私有仓库）
