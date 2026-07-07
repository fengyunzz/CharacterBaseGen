# /lora-prompts

生成中文自然语言出图提示词清单。

## 步骤

1. 先确认训练集最终数量。用户未指定时，读取 `references/proportion.md` 后提出 50 张默认计划，但仍要等待确认。
2. 列出将投喂给 `image_gen` 的原始参考图相对路径。路径只能来自 `projects/<项目名>/refs/`；如果该目录没有三视图，先回到初始化补齐。
3. 读取：
   - `references/proportion.md`
   - `references/action-tag-llm.md`
   - `references/emotion-tag-llm.md`
4. 建立动作池、表情池、视角池、景别池和背景池。
5. 按确认数量生成清单，确保每条有真实差异。
6. 为每条建立查重键：`view + shot + action_tag + expression_tag + background_type`。
7. 自检近重复，列出风险并改写。
8. 写入 `projects/<项目名>/prompts.md`，并在“参考原图”中逐条记录 `projects/<项目名>/refs/` 下的三视图实际路径。

## 提示词结构

```text
以三视图角色为画面唯一角色，三视图只用于角色画风&特征一致性参考，不使用参考图原有姿势、动作和表情，以 1:1 画幅绘制<视角>、<景别>的角色图；角色正在<动作>，表情是<表情>。背景保持<背景>，不要让背景抢走主体，适合作为角色 LoRA 训练底图
```

## 禁止

- 不要调用 `image_gen`。
- 不要把 `projects/<项目名>/images/` 里的生成图当参考图。
- 不要用同义句、尾巴位置、饰品小变化凑数量。
- 不要在每条 prompt 里重复铺陈全部角色细节。
