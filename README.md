# CharacterBaseGen

CharacterBaseGen 是一个面向 Codex 的 LoRA 角色底图生成 Skill。它把角色三视图整理成一个可控工作流：初始化项目、生成中文自然语言出图清单、人工确认后调用 `image_gen`、清洗训练标签，并在完成后归档项目。


## 适用场景

- 你有角色三视图、正面图、侧面图或背面图。
- 你想批量规划 LoRA 训练底图，而不是直接随手出图。
- 你希望每张图只有一个角色，并且动作、表情、视角、背景有真实差异。
- 你希望在出图前强制人工复核，避免误调用生成工具。
- 你希望最终清洗 Danbooru、WD14 Tagger 或类似工具生成的 `.txt` 标签。

## 安装

把整个仓库目录复制或克隆到 Codex 的技能目录，例如：

```powershell
git clone https://github.com/fengyunzz/CharacterBaseGen.git "$env:USERPROFILE\.codex\skills\character-base-gen"
```

重启或刷新 Codex 后，Skill 会通过 `SKILL.md` 的 frontmatter 被发现。

## 目录结构

```text
CharacterBaseGen
├─ SKILL.md
├─ README.md
├─ .gitignore
├─ agents/
│  └─ openai.yaml
├─ commands/
│  ├─ commands.json
│  ├─ lora-init.md
│  ├─ lora-prompts.md
│  ├─ lora-generate.md
│  ├─ lora-label.md
│  └─ lora-archive.md
├─ references/
│  ├─ proportion.md
│  ├─ action-tag-llm.md
│  ├─ emotion-tag-llm.md
│  └─ core-methodology.md
├─ projects/
│  └─ project-template.md
└─ archive/
   └─ .gitkeep
```

真实项目应由使用者在本地创建到 `projects/<项目名>/`。请不要把真实项目素材提交到 GitHub。

## 调用方式

这个仓库发布的是 Codex Skill，不依赖你本地的 `/prompts:*` wrapper。

- 推荐调用：`@lora-base-image-prompts`
- 也可以直接用自然语言描述意图，让 Codex 按 Skill 的 frontmatter 自动命中
- `/prompts:lora-*` 这类 slash wrapper 如果存在，通常只是某个用户本地额外注册的 prompt，不应作为公开文档的主入口

## 5 个调用意图

| 调用意图 | 用途 |
|---|---|
| `@lora-base-image-prompts 初始化项目 <项目名>` | 初始化项目包，接收三视图并自动分析角色信息 |
| `@lora-base-image-prompts 生成提示词清单` | 先询问训练集数量，再生成中文自然语言出图提示词清单 |
| `@lora-base-image-prompts 开始出图` | 先询问单次或批量与 `n`，复核后调用 `image_gen` |
| `@lora-base-image-prompts 清洗标签` | 全部图片满意后，读取 `images/*.txt` 并清洗标签 |
| `@lora-base-image-prompts 归档项目 <项目名>` | 确认后把完整项目包迁到 `archive/` |

## 推荐工作流

1. 初始化项目

   把三视图发给 Codex，并说：

   ```text
   @lora-base-image-prompts 初始化项目 demo-character，接收我的角色三视图并创建项目包
   ```

   Codex 建立 `projects/demo-character/`，记录参考图位置，并从三视图、文件名和上下文推断角色名、触发词、画风、必须保留和禁忌元素。

2. 生成提示词清单

   ```text
   @lora-base-image-prompts 生成提示词清单
   ```

   Codex 必须先询问训练集数量。若用户未指定数量，可依据 `references/proportion.md` 提出 50 张默认计划，但仍要等待用户确认。

3. 复核后出图

   ```text
   @lora-base-image-prompts 开始出图
   ```

   Codex 必须展示本次模式、张数、prompt 编号、完整提示词、变量覆盖和输出目录。只有用户明确说“同意出图”“开始生成”或“按这个清单生成”，才可调用 `image_gen`。

4. 清洗标签

   所有图都满意后，先用外部打标器在 `projects/<项目名>/images/` 生成同名 `.txt` 文件，再说：

   ```text
   @lora-base-image-prompts 清洗标签
   ```

   Codex 只读取并清洗标签文本，不通过图片或 `prompts.md` 猜测画面。

5. 归档

   ```text
   @lora-base-image-prompts 归档项目 demo-character
   ```

   Codex 确认后把整个项目文件夹移动到 `archive/demo-character/`。

## 兼容性

- 公开仓库的核心依赖只有 `SKILL.md`、frontmatter 和配套参考文档。
- 只要对方把仓库放进自己的 Codex skills 目录，这个 Skill 就应能被发现。
- 如果某人机器上没有你本地的 `/prompts:*` 注册项，也不影响 Skill 使用，因为公开版不依赖那套 wrapper。


