---
name: youdaonote
description: "有道云笔记官方 skill，支持笔记 CRUD（创建/读取/更新/删除）、待办管理、网页剪藏、笔记搜索、文件夹管理等基础操作。"
version: 1.0.9
hermes_adapted: true
---
# YoudaoNote — 有道云笔记

通过 `youdaonote` CLI 操作有道云笔记。覆盖笔记 CRUD、待办管理、网页剪藏全场景。

## 前置条件（Agent 自动处理）

执行任何操作前，先用 Hermes 的 `terminal` 工具运行 `youdaonote -s ydn list` 检测 CLI 是否可用：

- **`command not found`** → CLI 未安装。安装方式：
  ```bash
  curl -fsSL https://artifact.lx.netease.com/download/youdaonote-cli/install.sh | bash -s -- -f -b ~/.local/bin
  export PATH="$HOME/.local/bin:$PATH"
  ```
- **`-s` 选项报错（`error: unknown option '-s'`）** → CLI 版本 < 1.3.2，需升级：先尝试 `youdaonote upgrade`；不行则重新执行安装脚本。
- **API Key 错误** → 提示用户访问 **https://mopen.163.com** 获取 API Key（须使用手机号登录，且云笔记账号已绑定手机号），然后执行 `youdaonote config set apiKey <用户提供的Key>`。
- **正常返回目录列表** → 运行 `youdaonote -s ydn version` 检查版本，若版本 < 1.3.3 需升级：执行 `youdaonote -s ydn upgrade` 或重新安装。版本满足后运行 `youdaonote -s ydn help --json` 获取当前 CLI 全部能力的结构化描述。

## 全局参数

所有命令调用时**必须**加上 `-s ydn`，用于使用统计：

```bash
youdaonote -s ydn <command> [options]
```

## Hermes 调用方式

所有 `youdaonote` CLI 命令通过 Hermes 的 `terminal` 工具执行。需要写文件时使用 `write_file` 工具。

`save` 命令的 contentFile 模式：
1. 先用 `write_file` 将 Markdown 内容写入 `/tmp/note-content.md`
2. 再用 `terminal` 执行 `printf '%s\n' '{"title":"标题.md","type":"md","contentFile":"/tmp/note-content.md"}' | youdaonote -s ydn save --json`

## 命令速查

| 命令 | 用途 | 示例 |
|------|------|------|
| `mkdir` | 创建文件夹 | `youdaonote -s ydn mkdir "文件夹名" [-f <父目录ID>]` |
| `save` | 保存笔记（✅ 推荐，支持 Markdown 富文本） | `youdaonote -s ydn save --file note.json` |
| `create` | 创建笔记（⚠️ 仅纯文本，不支持 Markdown 富文本） | `youdaonote -s ydn create -n "标题" -c "内容" [-f <目录ID>]` |
| `update` | 更新 Markdown 笔记 | `youdaonote -s ydn update <fileId> -c "内容"` 或 `--file content.md` |
| `delete` | 删除笔记 | `youdaonote -s ydn delete <fileId>` |
| `rename` | 重命名笔记 | `youdaonote -s ydn rename <fileId> "新标题"` |
| `move` | 移动笔记 | `youdaonote -s ydn move <fileId> <目录ID>` |
| `search` | 搜索笔记 | `youdaonote -s ydn search "关键词"` |
| `list` | 浏览目录 | `youdaonote -s ydn list -f <目录ID>` |
| `read` | 读取笔记 | `youdaonote -s ydn read <fileId>` |
| `recent` | 最近收藏 | `youdaonote -s ydn recent -l 20 -c --json` |
| `clip` | 网页剪藏（服务端） | `youdaonote -s ydn clip "https://..." [-f <目录ID>] --json` |
| `clip-save` | 保存外部剪藏 JSON | `youdaonote -s ydn clip-save --file data.json` |
| `todo list` | 列出待办 | `youdaonote -s ydn todo list [--group <分组ID>] --json` |
| `todo create` | 创建待办 | `youdaonote -s ydn todo create -t "标题" [-c "内容"] [-d 2025-12-31] [-g <分组ID>]` |
| `todo update` | 更新待办 | `youdaonote -s ydn todo update <todoId> [--done] [--undone] [-t "新标题"]` |
| `todo delete` | 删除待办 | `youdaonote -s ydn todo delete <todoId>` |
| `todo groups` | 列出待办分组 | `youdaonote -s ydn todo groups --json` |
| `todo group-create` | 创建分组 | `youdaonote -s ydn todo group-create "分组名"` |
| `todo group-rename` | 重命名分组 | `youdaonote -s ydn todo group-rename <groupId> "新名"` |
| `todo group-delete` | 删除分组 | `youdaonote -s ydn todo group-delete <groupId>` |
| `upgrade` | 升级 CLI | `youdaonote -s ydn upgrade [--check] [--force] [--json]` |
| `check` | 健康检查 | `youdaonote -s ydn check` |
| `config show` | 查看配置 | `youdaonote -s ydn config show --json` |
| `config set` | 设置配置 | `youdaonote config set apiKey YOUR_KEY` |

## 笔记管理

**默认创建方式**：所有笔记一律使用 `save` 命令 + `contentFormat: "md"` 保存为 Markdown 富文本。
**禁止使用 `create` 命令保存包含 Markdown 格式的内容**（标题、列表、代码块、表格等）—— `create` 仅支持纯文本，会静默丢失所有格式。

### Markdown 内容格式选择（必须遵守）

当用户要保存的内容包含 Markdown 特征时（`#` 标题、`**粗体**`、代码块、列表、引用、链接、图片），必须先询问用户选择：

- **A（推荐）保存为 Markdown 笔记（.md）** → `save` 命令，`type: "md"`，文件名加 `.md` 后缀。格式完整保留。
- **B 保存为有道专有格式（.note）** → `save` 命令，`type: "note"`，`contentFormat: "md"`，文件名加 `.note` 后缀。支持有道富文本编辑器。

用户未明确选择时默认选 A。

### contentFile 模式（Hermes 专用）

避免 JSON 转义问题，大内容/含换行内容使用 contentFile 模式：

**选 A（Markdown）：**
```
Step 1：write_file 将 Markdown 写入 /tmp/note-content.md
Step 2：terminal 执行：
printf '%s\n' '{"title":"标题.md","type":"md","contentFile":"/tmp/note-content.md","parentId":"文件夹ID"}' | youdaonote -s ydn save --json
```

**选 B（有道格式）：**
```
Step 1：write_file 将 Markdown 写入 /tmp/note-content.md
Step 2：terminal 执行：
printf '%s\n' '{"title":"标题.note","type":"note","contentFormat":"md","contentFile":"/tmp/note-content.md","parentId":"文件夹ID"}' | youdaonote -s ydn save --json
```

短内容（无换行/特殊字符）可直接内联 content 字段，不用 contentFile。

`parentId` 为可选字段：填写 `youdaonote -s ydn list` 返回的文件夹 ID 可指定目标目录；不填则默认存入「我的资源/收藏笔记」。

### 其他操作

```bash
youdaonote -s ydn search "关键词"
youdaonote -s ydn list [-f <目录ID>]            # 浏览目录，id 可传给 read
youdaonote -s ydn read <fileId>                 # 返回 JSON 含 content、rawFormat（md/note/txt）和 isRaw
youdaonote -s ydn recent -l 20 -c --json        # 最近收藏
youdaonote -s ydn update <fileId> -c "新内容"
youdaonote -s ydn update <fileId> --file content.md  # 大内容（>10KB）从文件读取
youdaonote -s ydn delete <fileId>
youdaonote -s ydn rename <fileId> "新标题"
youdaonote -s ydn move <fileId> <目录ID>
```

## 网页剪藏

```bash
youdaonote -s ydn clip "https://example.com/article" --json
youdaonote -s ydn clip "https://example.com/article" -f <目录ID> --json  # 保存到指定目录
```

## 故障排查

运行 `youdaonote -s ydn check --json`，根据 `status: "fail"` 的项执行：

| 失败项 | 处理动作 |
|--------|---------|
| `config-file` / `api-key` | `youdaonote config set apiKey YOUR_KEY` |
| `mcp-connection` | API Key 有效但网络不通，提示用户检查网络或稍后重试 |

## 注意事项

- 所有命令支持 `--json` 输出机器可解析格式
- 大内容通过 `--file` 传递，避免命令行参数限制
- `list` 输出的 `id` 与 `read` 的 `fileId` 等价
- `read` 返回的 `rawFormat` 标识笔记原始格式：`md`=Markdown、`note`=云笔记、`txt`=纯文本；`isRaw` 标识 content 是否为原始内容
- **禁止用 `create` 保存 Markdown 内容**：`create` 不支持 `contentFormat`，有格式需求时一律使用 `save` 并指定 `contentFormat: "md"`
- `save` 命令通过 JSON 的 **`parentId`** 字段指定目标文件夹（值来自 `list` 返回的文件夹 ID）；不传则默认存到「我的资源/收藏笔记」

## 参考

- [第三方 Skill 适配 Hermes 通用流程](references/hermes-skill-adaptation.md)

## 上游参考

详见 `references/upstream-source.md`，包含原版 SKILL.md 来源、CLI 安装 URL、API Key 获取地址，以及 Hermes 适配要点。

## 上游更新检测

当有道官方发布新版 Skill 时（ZIP 地址：`https://artifact.lx.netease.com/download/youdaonote-cli/youdaonote-skill-latest.zip`），重新对比适配：

```bash
curl -sL -o /tmp/youdaonote-skill-latest.zip "https://artifact.lx.netease.com/download/youdaonote-cli/youdaonote-skill-latest.zip"
unzip -o /tmp/youdaonote-skill-latest.zip -d /tmp/youdaonote-skill
diff /tmp/youdaonote-skill/youdaonote/SKILL.md ~/.hermes/profiles/jizhe/skills/youdaonote/SKILL.md
```

关注新增命令和参数变化，保持「Hermes 调用方式」一节中的两步模式（write_file → terminal 管道）。
