# 工具与扩展技巧

以下内容对 Wiki 用户（你）有价值，我（Agent）不需要操作它们，但可以在我帮你搭建环境时引用。

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

### 我读图片的局限

我不能一次性地通过内联图片阅读 Markdown 中的图片。如果你希望我理解图片内容：

1. 我先读取文章的文字部分
2. 你再告诉我哪张图片需要分析（可以发截图或告诉我图片文件名）
3. 我用视觉工具单独读取

这有点笨重但够用。

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

Marp 是基于 Markdown 的幻灯片格式。Obsidian 有 Marp 插件。当 Query 需要输出演示文稿时，我会直接生成 Marp 格式的 Markdown。

---

## 搜索工具（未来扩展）

当 wiki 增长到数百个页面时，`index.md` 的线性浏览可能不够用。届时可以引入 [qmd](https://github.com/tobi/qmd)：

- 本地 Markdown 文件搜索引擎
- 混合 BM25/向量搜索 + LLM 重排序
- 有 CLI（我可以 shell 调用）和 MCP Server（我可以当原生工具使用）

或者你也可以让我临时写一个简单的搜索脚本。什么时候觉得 index 不够用了，告诉我一声。

---

## 版本管理

整个 `~/knowledge/` 就是一个 Git 仓库。这带来：

- 每次变更都有历史记录，可以回退
- 可以开分支实验不同的组织结构
- 可以多设备同步（配合 GitHub / 私有仓库）
