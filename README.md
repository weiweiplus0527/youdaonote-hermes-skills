# 有道云笔记 × Hermes Skills

把有道云笔记接入 Hermes Agent 的两个适配 Skill 文件。

## 文件说明

| 文件 | 用途 |
|------|------|
| `youdaonote-hermes.md` | 基础 Skill — 笔记 CRUD、待办、网页剪藏 |
| `youdaonote-llm-wiki-hermes.md` | Wiki Skill — 构建结构化知识库 |

## 安装

将文件放到 Hermes 的 skills 目录：

```bash
# 基础 Skill
cp youdaonote-hermes.md ~/.hermes/profiles/jizhe/skills/youdaonote/SKILL.md

# Wiki Skill
cp youdaonote-llm-wiki-hermes.md ~/.hermes/profiles/jizhe/skills/youdaonote-llm-wiki/SKILL.md
```

如果你用的是其他 profile，把 `jizhe` 换成你的 profile 名。

## 前置依赖

1. [youdaonote CLI](https://note.youdao.com/help-center/cli-install-guide.html) ≥ 1.3.3
2. [API Key](https://mopen.163.com)（手机号登录，需绑定有道云笔记账号）

## 完整教程

见有道云笔记帮助中心：
- [Skill 安装指南（编程 AI Agent）](https://note.youdao.com/help-center/skill-install-guide.html)
- [LLM Wiki 快速上手](https://note.youdao.com/help-center/skill-wiki-guide.html)
