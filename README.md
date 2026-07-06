# CharacterBaseGen

CharacterBaseGen 是一个面向 Codex 的 LoRA 角色底图生成 Skill。它把角色三视图整理成一个可控工作流：初始化项目、生成中文自然语言出图清单、人工确认后调用 `image_gen`、清洗训练标签，并在完成后归档项目。

这个仓库只包含可复用的 Skill、命令说明和公开参考文档；不包含任何真实角色项目、生成图片、训练标签或私人素材。

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
CharacterBaseGen/
  SKILL.md
  README.md
  agents/openai.yaml
  commands/
    commands.json
    lora-init.md
    lora-prompts.md
    lora-generate.md
    lora-label.md
    lora-archive.md
  references/
    proportion.md
    action-tag-llm.md
    emotion-tag-llm.md
    core-methodology.md
  projects/
    project-template.md
```

真实项目应由使用者在本地创建到 `projects/<项目名>/`。请不要把真实项目素材提交到 GitHub。

## 5 个入口

| 命令 | 用途 |
|---|---|
| `/prompts:lora-init <项目名>` | 初始化项目包，接收三视图并自动分析角色信息 |
| `/prompts:lora-prompts` | 先询问训练集数量，再生成中文自然语言出图提示词清单 |
| `/prompts:lora-generate` | 先询问单次或批量与 `n`，复核后调用 `image_gen` |
| `/prompts:lora-label` | 全部图片满意后，读取 `images/*.txt` 并清洗标签 |
| `/prompts:lora-archive <项目名>` | 确认后把完整项目包迁到 `archive/` |

如果用户直接说 `/lora-init`、`/lora-prompts` 这类短命令，Codex 也应按相同意图处理。

## 推荐工作流

1. 初始化项目

   把三视图发给 Codex，并说：

   ```text
   /prompts:lora-init demo-character
   ```

   Codex 建立 `projects/demo-character/`，记录参考图位置，并从三视图、文件名和上下文推断角色名、触发词、画风、必须保留和禁忌元素。

2. 生成提示词清单

   ```text
   /prompts:lora-prompts
   ```

   Codex 必须先询问训练集数量。若用户未指定数量，可依据 `references/proportion.md` 提出 50 张默认计划，但仍要等待用户确认。

3. 复核后出图

   ```text
   /prompts:lora-generate
   ```

   Codex 必须展示本次模式、张数、prompt 编号、完整提示词、变量覆盖和输出目录。只有用户明确说“同意出图”“开始生成”或“按这个清单生成”，才可调用 `image_gen`。

4. 清洗标签

   所有图都满意后，先用外部打标器在 `projects/<项目名>/images/` 生成同名 `.txt` 文件，再说：

   ```text
   /prompts:lora-label
   ```

   Codex 只读取并清洗标签文本，不通过图片或 `prompts.md` 猜测画面。

5. 归档

   ```text
   /prompts:lora-archive demo-character
   ```

   Codex 确认后把整个项目文件夹移动到 `archive/demo-character/`。

## 隐私与上传边界

仓库的 `.gitignore` 默认排除：

- `projects/*` 里的真实项目包。
- `archive/*` 里的归档项目包。
- 常见生成图、参考图、标签、训练产物和模型文件。

只保留 `projects/project-template.md` 作为公开模板。上传前建议运行：

```powershell
git status -sb
rg --files -uu | rg "projects/|archive/|\.png$|\.jpg$|\.jpeg$|\.webp$|\.safetensors$"
```

如果输出出现真实项目素材，先移出仓库或确认 `.gitignore` 是否生效。
