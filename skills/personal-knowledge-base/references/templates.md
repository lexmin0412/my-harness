# 页面模板

Ingest 流程中写页面时，根据内容类型选择对应的模板。

---

## 通用 frontmatter

所有页面统一使用 YAML frontmatter，按需选用字段：

```yaml
---
title: "页面标题"
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: []
related: []
---
```

**tags 可选值**：`concept` `summary` `decision` `project`

---

## 概念页 → wiki/concepts/

知识概念、技术原理、业务术语等。

```markdown
---
title: "概念名称"
tags: [concept]
created: YYYY-MM-DD
sources: []
related: []
---

## 定义

## 要点

## 与其他概念的关系

## 来源
```

---

## 摘要页 → wiki/summaries/

文章、书籍、播客、视频的阅读笔记。

```markdown
---
title: "原标题"
tags: [summary]
created: YYYY-MM-DD
source: "链接或文件路径"
---

## 核心观点

## 关键论据

## 我的思考

## 相关页面
```

---

## 决策记录 → wiki/decisions/

架构选型、方案评审等有明确结论的决策。格式参考 ADR（Architecture Decision Record）。

```markdown
---
title: "决策标题"
tags: [decision]
created: YYYY-MM-DD
status: proposed | accepted | deprecated
---

## 背景

## 方案对比

## 决策

## 理由

## 后果
```

---

## 项目页 → wiki/projects/

长期维护的项目级知识汇总。

```markdown
---
title: "项目名称"
tags: [project]
created: YYYY-MM-DD
status: active | archived
---

## 目标

## 技术栈

## 关键决策

## 踩坑记录

## 相关页面
```
