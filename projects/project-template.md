# 项目包模板

新角色项目使用一个文件夹打包：

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
```

## brief.md 建议内容

- 项目名：
- 角色名：
- 触发词：
- 类名：
- 画风：
- 必须保留：
- 禁忌元素：
- 推断说明：

## prompts.md 建议内容

- 用户确认数量：必须先询问用户
- 默认参考：用户未指定时，可按 `references/proportion.md` 提出 50 张计划，但仍需用户确认
- 每批提示词：
- 出图确认记录：
- 输出位置：`projects/<项目名>/images/`

## 打标文件

只有所有计划图片都出完且用户确认满意后，才生成或修正：

- `captions.jsonl`
- `tags.csv`
- `quality-check.csv`
