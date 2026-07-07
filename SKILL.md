---
name: lora-base-image-prompts
description: Use when working from character three-view references to initialize one-folder LoRA base-image projects, plan Chinese natural-language prompt lists, review and run image_gen with explicit approval, guide post-generation Danbooru-style label correction, or archive completed character dataset projects.
---

# LoRA 三视图底图提示词

## 核心原则

先把三视图理解成角色身份参考，再产出可审查的提示词清单。不要直接出图。

- 使用中文自然语言写提示词，像在对中文图像编辑模型说明画面。
- 每张图只允许出现一个角色，必须明确写入提示词。
- 不写僵硬 tag 堆，不写复杂负面约束，不追求长提示词。
- 角色核心特征来自三视图固定参考，不要在每条提示词里反复描述细节；统一写“保持角色画风与特征一致”。
- 三视图只作为角色身份和外观参考；每条提示词必须明确说明不使用参考图原有姿势、动作和表情。
- `/lora-prompts` 必须先列出投喂给 `image_gen` 的原始参考图相对路径，且只能来自 `projects/<项目名>/refs/`。
- 初始化时三视图必须复制到该角色自己的 `projects/<项目名>/refs/`；`brief.md` 和 `prompts.md` 都必须记录这些项目内实际路径，不强制重命名原图。
- 不要把 `projects/<项目名>/images/` 里的生成图当作参考图继续生图。
- 默认画幅比例为 `1:1`，除非用户明确提出其他比例。
- 生成清单时必须同时读取 `references/proportion.md`、`references/action-tag-llm.md`、`references/emotion-tag-llm.md`。
- 提示词清单不能靠相近小变化凑数量，必须让视角、景别、姿态、动作、表情或背景有真实差异。
- 动作和表情必须从对应 tag 参考中适量选取，再转写成中文自然语言。
- 每条清单都要有内部查重键：`view + shot + action_tag + expression_tag + background_type`。
- 调用 `image_gen` 前，必须展示清单并获得用户明确同意。
- `/lora-label` 是洗标签流程，必须读取 `projects/<项目名>/images/` 下所有 `.txt` 标签文件；不调用视觉工具，不读取 `prompts.md` 推断图片内容。
- 需要打标修正方法论时，读取 `references/core-methodology.md`。

## 固定目录

新项目只使用一个工作区和一个归档区：

```text
projects/<项目名>/
  brief.md
  refs/
  prompts.md
  images/
  captions.jsonl
  tags.csv
  quality-check.csv
  revisions.md
archive/<项目名>/
```

- `projects/<项目名>/`：进行中的角色项目。
- `refs/`：三视图、正面、侧面、背面和其他参考图；这是当前项目包内部目录，即 `projects/<项目名>/refs/`。
- `prompts.md`：出图提示词清单、数量确认、批次计划和确认记录。
- `images/`：生成图、重生成图、接受图和废弃图记录。
- `captions.jsonl`、`tags.csv`、`quality-check.csv`：全部图片满意后才生成或修正。
- `revisions.md`：不满意图片的修改、重生成、废弃记录。
- `archive/<项目名>/`：归档后的完整项目包。归档是迁移整个项目文件夹，不是复制零散文件。

不要为新项目继续使用顶层 `input/`、`lists/`、`output/` 分散结构。

## 调用入口

项目内 `commands/commands.json` 是命令说明索引，不是公开仓库必须依赖的注册机制。公开使用时优先通过 `@lora-base-image-prompts` 或自然语言意图触发；某些环境中的 `/prompts:lora-*` 只是一层本地 wrapper，不应视为 Skill 的唯一入口。

| 调用方式 | 用途 | 默认动作 |
|---|---|---|
| `@lora-base-image-prompts 初始化项目 <项目名>` | 初始化项目 | 创建或说明项目包，接收三视图，自动分析角色信息 |
| `@lora-base-image-prompts 生成提示词清单` | 出提示词清单 | 先询问训练集数量，再写 `projects/<项目名>/prompts.md` |
| `@lora-base-image-prompts 开始出图` | 调用 `image_gen` | 先询问单次或批量，批量必须问 `n`，复核同意后出图并保存到 `images/` |
| `@lora-base-image-prompts 清洗标签` | 最终训练集洗标签 | 读取 `images/*.txt`，按最小变量原则清洗标签 |
| `@lora-base-image-prompts 归档项目 <项目名>` | 归档项目 | 确认后把 `projects/<项目名>/` 迁到 `archive/<项目名>/` |

## 标准流程

1. **初始化项目**
   - 用户说 `@lora-base-image-prompts 初始化项目 <项目名>`，或发送三视图并表达创建 LoRA 底图项目时，使用本 Skill。
   - 建立或说明 `projects/<项目名>/` 项目包。
   - 复制三视图到 `projects/<项目名>/refs/`，记录这些项目内实际路径到 `brief.md`；默认保留原始文件名，重名时只追加最小后缀。
   - 不要要求用户填表。先从用户消息、文件名、三视图内容和上下文自动推断角色名、触发词、画风、必须保留、禁忌元素。
   - 只有关键信息无法判断且会影响后续出图时，才问最多一个简短问题。

2. **自动分析角色**
   - 项目名：优先用用户给出的名字；没有就用文件名；仍没有就用 `character-YYYYMMDD`。
   - 角色名：优先用用户给出的名字；没有就用项目名。
   - 触发词：优先用用户给出的 trigger；没有就用项目名的安全英文或拼音形式。
   - 画风：从图像风格自然描述，例如二次元、Q版、kemono、厚涂、3D、写实。
   - 必须保留：从三视图提取稳定身份特征、配色、服装、耳尾、发型、装饰。
   - 禁忌元素：默认写“不要出现第二角色、文字水印、复杂遮挡；不要改变已识别的核心配色和结构”。
   - 不要问训练超参数，除非用户主动提到底模或训练器。

3. **生成提示词清单**
   - 用户说 `@lora-base-image-prompts 生成提示词清单`，或要求“出提示词清单”时，先确认训练集最终数量。
   - 写清单前，先列出将投喂给 `image_gen` 的原始参考图相对路径，路径必须位于 `projects/<项目名>/refs/`。
   - 如果 `projects/<项目名>/refs/` 没有三视图，先回到初始化补齐；`prompts.md` 的“参考原图”必须逐条写入这些项目内路径。
   - 严禁使用 `projects/<项目名>/images/` 内的生成图作为参考图。
   - 如果用户没给数量，读取 `references/proportion.md` 后提出 50 张默认计划，但仍必须询问用户是否按 50 张，还是指定其他数量。
   - 用户指定其他数量时，按 `references/proportion.md` 的比例重新调整视角、景别、动作、表情、背景覆盖。
   - 同时读取 `references/action-tag-llm.md` 和 `references/emotion-tag-llm.md`，建立动作池、表情池、视角池、景别池、背景池。
   - 在 `prompts.md` 写入“适配索引”小节，记录选用的动作 tag、表情 tag、视角、景别、背景类型，以及中文自然语言转写。
   - 每条提示词必须写明三视图只用于角色身份参考，不使用参考图原有姿势、动作和表情。
   - 默认每条提示词使用 `1:1` 比例；只有用户提出横图、竖图或其他比例时才改。
   - `出图清单` 的 `用途` 栏使用简短规范标签，例如 `正面`、`背面`、`左侧面`、`右侧面`、`3/4左侧面`、`3/4右侧面`、`半身近景`、`表情变化`、`动作变化`、`轻场景`、`结构强化`。
   - 每条清单都要建立内部查重键：`view + shot + action_tag + expression_tag + background_type`。
   - 草稿完成后必须自检近重复：列出重复风险编号并改写，直到没有明显近重复。
   - 清单写入 `projects/<项目名>/prompts.md`，但不调用 `image_gen`。

4. **提示词写法**
   - 每条提示词按这个顺序组织：角色身份锚点；画面只有一个角色；本张变量；简洁背景或轻场景；LoRA 训练底图用途说明。
   - 示例结构：

```text
以三视图中的《角色名》为唯一角色，画面中只出现这一位角色。三视图只用于角色身份参考，不使用参考图原有姿势、动作和表情。保持角色画风与特征一致，以 1:1 画幅绘制<视角>、<景别>的训练底图；角色正在<动作>，表情是<表情>。背景保持<背景>，不要让背景抢走主体，适合作为角色 LoRA 训练底图。
```

5. **调用 image_gen**
   - 用户说 `@lora-base-image-prompts 开始出图`，或要求开始出图时，先询问本次用单次还是批量。
   - 单次：本次只出 1 张。
   - 批量：必须询问本批出 `n` 张，不设置默认张数。
   - 调用 `image_gen` 前必须展示本次模式与张数、prompt 编号和完整提示词、变量覆盖摘要、输出目录。
   - 调用 `image_gen` 时必须附带 `projects/<项目名>/refs/` 中记录的三视图作为图片输入参考，不能只在文字提示词里提到三视图。
   - 必须等待用户明确同意，例如“同意出图”“开始生成”“按这个清单生成”。
   - 用户只说“继续优化”“看看”“可以吗”时，不视为同意出图。
   - `image_gen` 返回图片后，必须立刻保存或复制到 `projects/<项目名>/images/`。
   - 文件按 prompt 编号和重生成版本命名：首次生成用 `001.png`、`002.png`；重生成用 `001-r02.png`、`001-r03.png`。
   - 保存后更新 `projects/<项目名>/prompts.md`，记录图片文件路径和状态。

6. **处理不满意图**
   - 用户可以要求修改提示词后重出、按原提示词重生成，或废弃并记录原因。
   - 每次修改或重生成都必须再次展示目标图片编号、提示词、输出位置，并等待明确同意。
   - 处理记录写入 `projects/<项目名>/revisions.md`。

7. **最终打标修正**
   - 只有当本项目计划图片全部出完，且用户确认全部满意后，才开启 `@lora-base-image-prompts 清洗标签`。
   - 开启时必须先确认外部打标器已经在 `projects/<项目名>/images/` 内为每张训练图生成同名 `.txt` 标签文件。
   - 一旦进入清洗标签流程，必须读取 `projects/<项目名>/images/*.txt` 全部标签文件后再判断是否要改。
   - 读取 `references/core-methodology.md` 做二次修正；动作或表情用词需要统一时，再读取动作和表情参考。
   - 清洗标签流程不调用视觉工具，不读取训练图片，不读取 `projects/<项目名>/prompts.md` 来推断画面。
   - 使用最小变量原则：标签非常干净时可以完全不改；需要修改时，只改具体 `.txt` 文件里最小必要片段，并说明改哪个文件、为什么改。
   - 如果用户提前明确 trigger，直接把 trigger 加到每个 `.txt` 最前面；如果没有明确 trigger，先完成其他安全清洗，最后再问是否添加 trigger。

## prompts.md 模板

```markdown
# <项目名> LoRA 底图清单

## 需求确认
- 角色名：
- 触发词：
- 类名：
- 画风：
- 必须保留：
- 禁忌元素：
- 推断说明：

## 参考原图
- image_gen 参考路径：
- 路径要求：逐条写入 `projects/<项目名>/refs/` 下的三视图实际路径
- 禁止参考来源：`projects/<项目名>/images/`

## 计划数量
- 用户确认数量：
- 比例依据：`references/proportion.md`

## 适配索引
- 动作参考：`references/action-tag-llm.md`
- 表情参考：`references/emotion-tag-llm.md`
- 动作池：
- 表情池：
- 视角池：
- 景别池：
- 背景池：

## 近重复自检
- 查重键：`view + shot + action_tag + expression_tag + background_type`
- 重复风险：
- 处理结果：

## 出图清单
| 编号 | 用途 | 提示词 | 输出文件 | 状态 |
|---|---|---|---|---|
| 001 | 正面身份锚定 | ... | images/001.png | 待生成 |
```
