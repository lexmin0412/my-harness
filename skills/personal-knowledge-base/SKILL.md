---
name: personal-knowledge-base
description: 基于 LLM Wiki 模式构建的个人知识库系统。用于知识沉淀、文章归档、概念整理、决策记录和知识库查询。当用户说"记下来"、"归档"、"沉淀一下"、"查知识库"、"wiki 里查一下"、"翻一下笔记"、"健康检查"、"lint 一下"，或者任何涉及笔记管理、文章归档、决策记录、知识查询的场景，即使没有明确提到"知识库"，也应使用此技能。
---

核心分工：**用户策展方向，我负责所有记账工作**。

## 知识库位置

```
~/knowledge/
```

**自动创建**：如果 `~/knowledge/` 目录不存在，我会自动创建完整的目录结构（包括 `raw/` 和 `wiki/` 下的所有子目录），无需用户手动创建。

## 目录结构

```
~/knowledge/
├── raw/{articles,notes,assets}/     ← 原始材料，我只读
└── wiki/{concepts,summaries,decisions,profiles,projects,collections,tasks}/
    ├── index.md                      ← 目录索引（每条更新）
    ├── log.md                        ← 操作日志（每次追加）
    ├── concepts/                     ← 知识概念、技术原理
    ├── summaries/                    ← 文章/笔记摘要
    ├── decisions/                    ← 架构决策记录 (ADR)
    ├── profiles/                     ← 个人资料（财务/简历/开源贡献）
    ├── collections/                  ← 收藏（GitHub 项目 + 站点 + 前端库）
    │   ├── github-repos.md           ← GitHub 项目收藏聚合索引（主表 + 简要记录表）
    │   ├── sites.md                  ← 站点收藏索引（工具/文档/博客）
    │   ├── mcp.md                    ← MCP Server 索引
    │   ├── skills.md                 ← Agent Skill 索引
    │   ├── frontend-libs.md          ← 前端常用 UI / 工具库索引（按子专题分区）
    │   ├── mirror.md                 ← 容器镜像加速 / 镜像代理索引
    │   ├── desktop-software.md       ← 桌面端应用软件索引（按专题分区）
    │   └── {name}.md                 ← 收藏项目的独立详情页
    ├── projects/                     ← 个人在做的项目（含追踪/看板子页面）
    └── tasks/
        ├── index.md                  ← 当期待办（按周分组，含源页面链接）
        └── done.md                   ← 历史完成记录（归档用）
```

## 触发规则

### 用户主动触发

| 触发词 | 流程 |
|---|---|
| "记下来" / "归档" / "沉淀一下" / "记录" / "保存" / "这个值得记" | Ingest — 见下方 |
| "收藏这个项目" / "收藏 xxx" / "收藏 GitHub 项目" / "这个项目不错" | GitHub 项目收藏 — 见下方 |
| "收藏这个站点" / "收藏这个网站" / "记一下这个站" / "这个网站有用" | 站点收藏 — 见下方 |
| "收藏这个 MCP" / "记一下这个 MCP" / "这个 MCP 不错" | MCP 收藏 — 见下方 |
| "收藏这个 Skill" / "记一下这个 Skill" / "这个 Skill 有用" | Skills 收藏 — 见下方 |
| 遍历 UI 库 / 前端库列表集 / "看看这些前端库" | 前端常用库收藏 — 见下方（经用户筛选后入 frontend-libs.md） |
| "查知识库" / "wiki 里查一下" / "翻一下笔记" / "之前记录过什么" | Query — 见下方 |
| "健康检查" / "lint 一下" / "检查知识库" / "整理一下知识库" | Lint — 见下方 |
| "这个讨论归档" / "把这段对话记下来" | 提取当前对话 → Ingest |
| "看下待办" / "我还有什么没做" / "最近要干什么" / "待办事项" | Show tasks — 读 `wiki/tasks/index.md` |
| "更新待办" / "勾掉 xxx" / "xxx 完成了" / "标记完成" | Toggle task — 勾选 tasks 条目并同步更新源页面 |
| "增加/创建一个 xxx 收藏专题" / "新建一个 xxx 专题页" / "创建新的分类" | 新增专题聚合页 — 见下方 |

### 我主动询问"这个要归档吗？"

- 架构决策确定后
- 非 trivial bug 修完并找到根因后
- 我帮你读完一篇长文并做了分析后
- 你详细解释了一个业务概念或技术原理后

### 不触发的场景

日常闲聊、简单命令（查日志/git/改配置）、纯问答不涉及新知识、你没有明确表示要记录。

---

## Ingest 流程

> 一次 Ingest 可能涉及 **10-15 个 wiki 页面**——不只是新建一个摘要页，还包含更新所有关联的概念页、决策页。这不是过度设计，这是核心价值：新知识和旧知识在 wiki 中汇合。

### 模式选择

- **单篇模式（默认）**：一次处理一个来源，每步与你确认。精度高。
- **批量模式（可选）**：一次处理多个来源（"把这周读的 5 篇文章一起归档"），跳过逐篇讨论直接写页面。速度快。

询问用户偏好，如未指定用单篇模式。

### 执行步骤

写页面时，先读 `references/templates.md` 选对应模板。

- [ ] **步骤 0 — 检查知识库目录**：如果 `~/knowledge/` 目录不存在，自动创建完整的目录结构（包括 `raw/articles/`、`raw/notes/`、`raw/assets/`、`wiki/concepts/`、`wiki/summaries/`、`wiki/decisions/`、`wiki/profiles/`、`wiki/projects/`、`wiki/collections/`、`wiki/tasks/`），并初始化 `index.md` 和 `log.md` 文件。
- [ ] **步骤 1 — 确认来源**：问用户要记什么（对话内容 / URL / 本地文件 / GitHub 项目链接 / Web 站点）。
  - **特殊分支：GitHub 项目收藏** — 如果来源是 GitHub 项目链接，走独立流程（见下方「GitHub 项目收藏流程」），不走通用 Ingest 的 3-8 步。
  - **特殊分支：站点收藏** — 如果来源是实用工具/文档/博客站点，走独立流程（见下方「站点收藏流程」），不走通用 Ingest 的 3-8 步。
- [ ] **步骤 2 — 读取**：获取原始材料内容
- [ ] **步骤 3 — 讨论**：与用户确认核心 takeaways，问需要强调什么、忽略什么。**单篇模式必做，批量模式跳过。**
- [ ] **步骤 4 — 写页面**：读 `references/templates.md`，选对应类型模板写文件
- [ ] **步骤 5 — 横扫关联页**：搜索 wiki 中所有受此新信息影响的已有页面（概念、决策、项目、收藏），逐一更新、补充交叉引用、标注矛盾。目标是让新旧知识汇合。
- [ ] **步骤 6 — 更新 index.md**：在对应分类下追加一条
- [ ] **步骤 7 — 更新 log.md**：追加操作日志（格式见下方）
- [ ] **步骤 8 — 追加待办**：如果本次操作产生了后续行动（如"需要推进项目实现"、"需要补充某个关联页面"），在 `wiki/tasks/index.md` 对应周下添加条目，标注 `→ [path]`

---

## GitHub 项目收藏流程

收藏 GitHub 开源项目时的独立流程。本质是 Ingest 的一个特化分支。

### 三级决策

收藏决策分三级，用户逐项判断：

| 级别 | 动作 | 产出 |
|------|------|------|
| **收藏** (collection) | 完整流程 | `collections/{name}.md` 详情页 + 聚合索引 + 横扫关联页 |
| **简要记录** (brief) | 只记录一行 | `github-repos.md`「简要记录」表新增一行，无需详情页 |
| **跳过** (skip) | 不动 | 无产出，可能取消 Star 或保留 Star |

### 触发词

"收藏这个项目" / "收藏 xxx（GitHub 链接）" — 触发收藏流程
"简要记录" / "记一下吧" — 触发简要记录
"取消 Star" / "跳过" — 跳过

### 执行步骤

#### 收藏（完整流程）

- [ ] **步骤 1 — 获取项目信息**：调用 GitHub API 获取仓库元数据（名称、描述、Star 数、语言、Topics、许可协议）
- [ ] **步骤 2 — 创建详情页**：按项目模板（`references/templates.md` 的 collection 类）写 `collections/{name}.md`
  - 标题用项目名
  - 描述用 GitHub description
  - source 填 GitHub URL
  - frontmatter tags 用 `[collection]`（区别于 personal project 的 `[project]`）
  - related 标注与本 wiki 已有内容的关联（如 llm-wiki-pattern、agent-architecture 等）
- [ ] **步骤 3 — 注册到聚合索引**：在 `collections/github-repos.md` 的主表最上方插入一行，字段：项目名（链接到详情页）、Star、语言、领域、收藏日期、状态；**注意每次只匹配表头行插入，不替换已有数据行，避免覆盖相邻条目**
- [ ] **步骤 4 — 横扫关联页**：搜索 wiki 中所有可能受此项目影响的页面，补充交叉引用
- [ ] **步骤 5 — 更新 index.md**：**Collections 区只保留 github-repos.md 聚合索引入口，不追加子项目链接**
- [ ] **步骤 6 — 更新 log.md**：追加操作记录，格式 `collection | 项目名`（见下方 log.md 格式）

#### 简要记录（轻量流程）

- [ ] **步骤 1 — 获取项目信息**：同上
- [ ] **步骤 2 — 写入简要记录表**：在 `collections/github-repos.md` 的「简要记录」分区最上方插入一行，字段：项目名（链接到 GitHub URL）、Star、一句话备注、记录日期
- [ ] **步骤 3 — 更新 log.md**：追加操作记录，格式 `brief | 项目名`

> **注意**：用户可能的补充要求："和 xxx 对比"（如已收藏同类项目，需做对比分析写入详情页），"深度分析实现原理"（走通用 Ingest 补充分析到详情页），"给 GitHub 地址"（每个展示都要附链接）。

---

## 站点收藏流程

收藏实用 Web 站点时的独立流程。无须详情页，仅一行记录。

### 类型限定

限三种：**工具** / **文档** / **博客**。不增设新类型。

### 触发词

"收藏这个站点" / "收藏这个网站" / "记一下这个站"

### 执行步骤

- [ ] **步骤 1 — 确认站点信息**：名称、URL、类型、一句话备注
- [ ] **步骤 2 — 写入聚合表**：在 `collections/sites.md` 最上方插入一行，字段：站点名（链接到URL）、类型、备注、收藏日期
- [ ] **步骤 3 — 更新 log.md**：追加操作记录，格式 `site | 站点名`

---

## MCP 收藏流程

收藏日常使用的 MCP Server。

### 执行步骤

- [ ] **步骤 1 — 确认信息**：名称、来源链接、一句话备注
- [ ] **步骤 2 — 写入聚合表**：在 `collections/mcp.md` 最上方插入一行，字段：名称、来源、备注、收藏日期
- [ ] **步骤 3 — 更新 log.md**：追加操作记录，格式 `mcp | 名称`

---

## Skills 收藏流程

收藏日常使用的 Agent Skill。包含单个 Skill 及聚合仓库。

### 执行步骤

- [ ] **步骤 1 — 确认信息**：名称、来源链接、一句话备注
- [ ] **步骤 2 — 写入聚合表**：在 `collections/skills.md` 最上方插入一行，字段：名称、来源、备注、收藏日期
- [ ] **步骤 3 — 更新 log.md**：追加操作记录，格式 `skill | 名称`

---

## 前端常用库收藏流程

收藏前端生态的 UI 库、工具库，按子专题分类整理。

### 触发场景

遍历 Star 列表或前端生态项目时，逐项请用户决策（收藏/跳过），经筛选后写入专题页。

### 执行步骤

- [ ] **步骤 1 — 确认信息**：名称、Stars、一句话备注、适合哪个子专题
- [ ] **步骤 2 — 写入专题页**：在 `collections/frontend-libs.md` 对应子专题分区最上方插入一行，字段：名称（链接到 GitHub）、Star、备注。**如果是该领域事实标准（如 Chart.js、xyflow、TailwindCSS），在 Star 数后加 🏆 标识**
- [ ] **步骤 3 — 更新 log.md**：追加操作记录，格式 `frontend | 名称`

---

## 新增专题聚合页流程

创建 collections/ 下新的专题聚合页（如 desktop-software.md、mirror.md）。

### 触发词

"增加一个 xxx 收藏专题页面" / "新建一个 xxx 专题页" / "创建 xxx 收藏分类"

### 执行步骤

- [ ] **步骤 1 — 确认专题信息**：专题名称、描述、首条条目
- [ ] **步骤 2 — 创建聚合页**：参考 `frontend-libs.md` 的格式，按专题分区设计表格结构（名称 + 核心指标 + 备注）
- [ ] **步骤 3 — 注册首条条目**：从已有收藏中将匹配该专题的项目迁移/引用到新聚合页，更新该项目的 `related` 指向新聚合页
- [ ] **步骤 4 — 更新 index.md**：在 Collections 分类下新增一条指向该聚合页
- [ ] **步骤 5 — 更新 log.md**：追加操作记录，格式 `topic | 专题名`

---

## Query 流程

- [ ] **步骤 1 — 定位**：读 `wiki/index.md`，找相关页面
- [ ] **步骤 2 — 读取**：读具体页面内容
- [ ] **步骤 3 — 选择输出格式**：根据问题性质选最合适的格式：
  - **Markdown 页面** — 标准回答
  - **比较表** — 方案对比、概念辨析（Markdown table）
  - **Marp 幻灯片** — 适合分享/演示（Obsidian Marp 插件）
  - **图表** — 数据趋势、统计（matplotlib，存为图片并引用）
  - **Canvas / 思维导图** — 关系梳理
- [ ] **步骤 4 — 归档判断**：如果回答本身有存档价值 → 问用户"这个要归档吗？"（一个好的回答本身就是新的 wiki 页面素材）

---

## Tasks 流程

> 待办系统是「所有 wiki 页面 checklists 的聚合视图」。tasks/index.md 是索引，源页面是数据本体。

### 触发方式

| 触发词 | 动作 |
|--------|------|
| "看下待办" / "我还有什么没做" | 读 `wiki/tasks/index.md` 展示当周待办 |
| "更新待办" / "勾掉 xxx" | 在 tasks/index.md 中勾选 → 我同步更新源页面 |
| "存档待办" | 将过期条目归档到 `tasks/done.md` |

### 同步机制

```
用户在 tasks/index.md 勾选 [x]
    ↓
我读取条目中的 → [path] 定位源页面
    ↓
在源页面中找到匹配的 checkbox（按关键词模糊匹配）
    ↓
同步改为 [x]，更新源页面 frontmatter updated 字段
    ↓
（可选）记录到 tasks/done.md
```

匹配规则：取 tasks 条目标题中的关键词（如 `S2：React 性能`），在源页面的 checklists 中查找包含该关键词的 `- [ ]` 行。

### 维护节奏

- **我主动**：每次 Ingest/Update 后，如果有后续行动，同步追加到 `tasks/index.md`
- **你主动**：去 `tasks/index.md` 勾选，我下次看到就同步源页面
- **周切**：每周一归档上周条目到 `tasks/done.md`，刷新当周视图

---

## Lint 流程

- [ ] **步骤 1 — 孤儿页扫描**：找出 wiki 中没有入链的页面
- [ ] **步骤 2 — 矛盾检查**：比对相关页面，标记冲突声明
- [ ] **步骤 3 — 过时检查**：根据 frontmatter 中 updated 日期，标记超过 90 天未更新的页面
- [ ] **步骤 4 — 完整性检查**：发现被引用但缺失的页面，建议补充
- [ ] **步骤 5 — 建议新方向**：基于 wiki 当前的内容和缺口，建议：
  - 值得深入的新问题
  - 值得寻找的新来源
  - 可以补充的数据缺口
- [ ] **步骤 6 — 输出报告**：逐项问用户是否要处理

---

## index.md 与 log.md 格式

### index.md

```
# Wiki Index

## Concepts
- [概念名称](concepts/page.md) — 一句话描述

## Summaries
- [文章标题](summaries/page.md) — 2026-05-19

## Decisions
- [决策标题](decisions/page.md) — 2026-05-19 [accepted]

## Projects
- [项目名](projects/page.md) — active

## Collections
- [GitHub 项目收藏索引](collections/github-repos.md) — active（收藏的 GitHub 项目聚合列表，含全部详情页及简要记录）
- [站点收藏索引](collections/sites.md) — active（收藏的实用工具/文档/博客站点）
- [MCP / Skills 收藏索引](collections/mcp-skills.md) — active（收藏的 MCP Server / Agent Skill / 工具集成）
- [前端常用 UI / 工具库收藏索引](collections/frontend-libs.md) — active（前端生态常用库，按子专题分区）
- [桌面软件收藏索引](collections/desktop-software.md) — active（录屏/演示/AI 工具等桌面应用）
- [容器镜像加速 / 镜像代理索引](collections/mirror.md) — active（镜像加速方案）
- 注意：**Collections 区只保留聚合索引入口**，子项目全部通过对应聚合索引访问

### log.md

```
## [YYYY-MM-DD] site | 站点名
- 收藏: [站点名](URL) — 类型 | 备注
- 更新: collections/sites.md — 新增一行

## [YYYY-MM-DD] mcp | 名称
- 收藏: 名称 — MCP | 备注
- 更新: collections/mcp.md — 新增一行

## [YYYY-MM-DD] skill | 名称
- 收藏: 名称 — Skill | 备注
- 更新: collections/skills.md — 新增一行

## [YYYY-MM-DD] frontend | 名称
- 收藏: 名称（Nk Stars）— 子专题 | 备注
- 更新: collections/frontend-libs.md — 对应子分区新增一行

## [YYYY-MM-DD] collection | 项目名
- 新增: collections/page.md — 一句话简介（Nk Stars, Language, License）
- 备注xxx
- 更新: collections/github-repos.md — 插入新行（最上方）
- 更新: index.md — Collections（仅聚合索引入口）
- 横扫: concepts/page.md — 补充交叉引用

## [YYYY-MM-DD] brief | 项目名
- 简要记录: owner/repo — 一句话备注（Nk Stars, Language, License）
- 更新: collections/github-repos.md — 简要记录表新增一行

## [YYYY-MM-DD] ingest | 源名称
- 新增: categories/page.md
- 更新: categories/page.md

## [YYYY-MM-DD] query | 问题摘要
- 生成: decisions/page.md

## [YYYY-MM-DD] project | 页面名
- 新增: projects/page.md — 一句话说明
- 更新: projects/career-transition.md — related 补充引用
- 更新: index.md — Projects 新增条目

## [YYYY-MM-DD] topic | 专题名
- 新增: collections/page.md — 专题聚合页，首条 xxx（Nk Stars, 领域）
- 更新: collections/xxx.md — related 补充 page.md
- 更新: index.md — Collections 新增专题入口

## [YYYY-MM-DD] lint
- 孤儿页: page.md
- 矛盾: page-a.md vs page-b.md
```

---

## 命名规则

```
文件名：    小写英文，连字符分隔（micro-frontend-architecture.md）
frontmatter tags：小写英文
日期格式：  YYYY-MM-DD
```

---

## Gotchas

- **目录自动创建**：如果 `~/knowledge/` 目录不存在，我会在首次操作时自动创建完整的目录结构，无需用户手动创建。
- index.md 必须在每次 Ingest/归档后更新，不要依赖我"之后统一处理"。每新增/修改一个页面，index.md 立刻同步。
- 页面路径在 index.md 和 log.md 中必须使用**相对路径**（相对于 wiki/ 目录），如 `concepts/xxx.md`，不要用绝对路径。
- summary 类页面的 `source` 字段可能是一个 URL 或本地文件路径，不要省略。
- decision 类页面的 `status` 必须是 `proposed` / `accepted` / `deprecated` 三者之一，不能自己发明值。
- 如果用户说"记下来"但没有指定具体内容，优先问清楚，不要自己猜。
- log.md 是 append-only，永远不要修改或删除历史记录。如果写错了，追加一条新的 corrigendum 条目。
- 更新已有页面时，必须更新 frontmatter 中的 `updated` 字段。
- **不要跳过"横扫关联页"步骤**。只写新页不更新旧页会导致知识孤岛，这是本体系失败的主要原因。
- **tasks/index.md 是源页面 checklists 的聚合索引，不是孤立的待办列表。** 每项必须包含 `→ [path]` 指向源页面。勾选时我负责双向同步。
- **不要直接在 tasks/index.md 中新增源页面不存在的待办。** 如果确实需要，先在源页面添加对应 checklists，再聚合到 tasks/index.md。
- **GitHub 项目收藏必须双向注册**：创建详情页的同时，必须在 `collections/github-repos.md` 聚合表中插入行，保持两个页面互相引用（详情页 `related` 指向 `github-repos`，github-repos 表链接指向详情页）。
- **`github-repos.md` 表格保持倒序**（最新的在最上方），方便快速浏览最近收藏。**主表无序号列**，插入新项目时直接在第一行后（表头下一行）插入新行，无需重编号。
- **GitHub API Token 安全**：调用 GitHub API 获取项目信息时，从环境变量 `GITHUB_TOKEN` 读取认证信息，不要在代码、日志或对话中暴露 token 明文。
- **敏感内容保护**：`profiles/` 目录可能包含个人敏感信息（财务、简历等）。如果知识库通过 Git 同步，务必使用私有仓库，或将敏感文件加入 `.gitignore`。

## Schema 迭代说明

这个 SKILL.md 不是一成不变的。随着使用你会发现问题：某个流程太繁琐、某种页面类型用得少、触发词不自然。指出问题，我来改。**Schema 是我们共同演化的活文档。**
