# 有道云笔记接入 Hermes 桌面客户端：纯小白教程

这篇教程写给正在用 Hermes 桌面客户端、不想碰命令行的用户。

整个过程只需要开一次终端（一次性配置，5 分钟），之后所有操作都在 Hermes 客户端聊天窗口里用中文对话完成，再也不需要碰终端。

---

## 在开始之前

确认两件事：

1. 你的电脑上装了 Hermes 桌面客户端，并且能正常聊天
2. 你知道怎么打开「终端」（按 `Command + 空格`，输入「终端」回车）

---

## 第一步：安装 youdaonote CLI（一次性的）

CLI 是让 Hermes 能读写有道云笔记的桥梁工具。虽然你用的是桌面客户端，但这个桥需要在终端里安装——**只需要这一次**，装完就再也不用开终端了。

打开终端，复制下面这行命令，按回车：

```bash
curl -fsSL https://artifact.lx.netease.com/download/youdaonote-cli/install.sh | bash -s -- -f -b ~/.local/bin
```

等几秒钟，看到 `✓ 安装完成` 就行。

接着执行这行（让系统能找到刚装的工具）：

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

验证一下。在终端输入：

```bash
youdaonote --help
```

如果屏幕上弹出一堆命令说明——list、read、create 之类的——说明装好了。

> 如果提示 `command not found`：关掉终端，重新打开，再输入 `youdaonote --help` 试一次。

---

## 第二步：获取 API Key（一次性的）

API Key 是让 CLI 有权限访问你有道云笔记的密码。

用浏览器打开：**https://mopen.163.com**

用手机号登录。注意：**这个手机号必须是你有道云笔记 App 绑定的手机号**。如果没绑过，先去有道云笔记 App 的「设置」里绑定。

登录后找到「API Key」相关的页面（通常在左侧菜单栏），创建或复制你的 Key。它看起来像一长串乱码，`iv1gFXkvKdzb...` 这样。复制下来。

回到终端，输入：

```bash
youdaonote config set apiKey 粘贴你的Key
```

比如：

```bash
youdaonote config set apiKey iv1gFXkvKdzbKOjUdsTPP0mBmEx_7lEb
```

验证是否通。在终端输入：

```bash
youdaonote list
```

看到 `📁 我的资源` 就对了。

---

## 第三步：安装 Skill 文件（一次性的）

Skill 是给 Hermes 看的操作手册。装完这两个文件，Hermes 就知道怎么操作你有道云笔记了。

在终端里复制执行：

```bash
# 基础 Skill（存笔记、搜笔记、待办管理）
mkdir -p ~/.hermes/skills/youdaonote/
curl -o ~/.hermes/skills/youdaonote/SKILL.md \
  https://raw.githubusercontent.com/weiweiplus0527/youdaonote-hermes-skills/main/youdaonote-hermes.md

# Wiki Skill（建知识库）
mkdir -p ~/.hermes/skills/youdaonote-llm-wiki/
curl -o ~/.hermes/skills/youdaonote-llm-wiki/SKILL.md \
  https://raw.githubusercontent.com/weiweiplus0527/youdaonote-hermes-skills/main/youdaonote-llm-wiki-hermes.md
```

没有报错就是装好了。

---

## 完成！关掉终端

从这一刻起，你不需要再打开终端。关掉它。

重启 Hermes 桌面客户端（完全退出再打开），然后直接在聊天窗口里说话就行。

---

## 在 Hermes 里你能做什么

以下所有操作，直接在 Hermes 聊天框里说中文就行。不需要任何命令、不需要参数、不需要知道 CLI 是什么。

**日常笔记操作：**

| 你可以说 | Hermes 做什么 |
|---------|-------------|
| 「列出我的笔记」 | 展示你有道云笔记里的全部内容 |
| 「创建一个笔记，标题是本周周报」 | 新建一篇笔记 |
| 「搜一下笔记里关于年终总结的内容」 | 按关键字搜索 |
| 「打开我上周写的会议纪要」 | 读取指定笔记 |
| 「把这篇笔记删掉」 | 删除笔记 |
| 「剪藏这个网页到我的笔记：https://xxx」 | 把网页内容保存到有道云笔记 |
| 「创建一个待办：周五前交季度报告」 | 添加待办事项 |
| 「我的待办有哪些」 | 列出所有待办 |

**Wiki 知识库：**

| 你可以说 | Hermes 做什么 |
|---------|-------------|
| 「帮我建一个 AI 研究知识库」 | 自动创建文件夹结构和系统笔记 |
| 「把这篇文章加进 AI 知识库：https://xxx」 | 抓取网页、拆实体、建概念页、加交叉引用 |
| 「在 AI 知识库里查一下 Transformer 的注意力机制」 | 跨多页面综合分析，标注来源 |
| 「记下来」 | 把当前聊天的结论归档到知识库 |
| 「帮我检查一下知识库有没有矛盾」 | 审计孤立页面和矛盾声明 |

---

## 如果遇到问题

**Hermes 说「找不到 youdaonote 命令」**

关掉 Hermes 桌面客户端，重新打开。如果还不行，打开终端输入 `which youdaonote` 看看有没有结果。没有的话重做第一步。

**「API Key 无效」**

去 https://mopen.163.com 重新登录，确认用的是绑定有道云笔记的手机号。拿到新 Key 后在终端里重新执行 `youdaonote config set apiKey 新Key`。

**「没有检测到 Skill」**

确认第三步的命令执行了没有报错。在终端输入 `hermes skills list | grep youdaonote` 看看有没有两行 enabled。

---

## 这背后发生了什么？（可选阅读）

你可能会好奇：为什么装完这些东西之后，在 Hermes 里聊天就能自动操作有道云笔记了？

简单来说：

- **youdaonote CLI** 是一个翻译官，把 Hermes 的指令翻译成有道云笔记能懂的操作
- **API Key** 是身份证明，让翻译官有权访问你的笔记
- **Skill 文件** 是操作手册，告诉 Hermes「如果要存笔记，应该用 save 命令」「如果要搜笔记，应该用 search 命令」之类

三者配合的流程：

```
你在 Hermes 说「帮我存一篇笔记」
      ↓
Hermes 读取 Skill 手册 → 知道用 youdaonote save 命令
      ↓
Hermes 通过终端执行命令
      ↓
CLI 翻译官用 API Key 敲门 → 有道云笔记云端执行
      ↓
结果返回给你
```

你只需要开口说中文，中间这些步骤 Hermes 全自动完成。
