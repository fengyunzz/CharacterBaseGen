# 表情 tag 到中文提示词参考

从这里挑选表情，再转写成自然中文。表情变化要有强度差异，不要只用同义词凑数。训练底图里表情应服务于角色身份稳定，避免夸张表情占比过高。

## 基础情绪

| tag | 中文转写 |
|---|---|
| neutral | 平静表情 |
| calm | 冷静 |
| gentle | 温和 |
| smile | 微笑 |
| soft smile | 轻柔微笑 |
| happy | 开心 |
| cheerful | 开朗愉快 |
| excited | 兴奋 |
| serious | 认真 |
| focused | 专注 |
| determined | 坚定 |
| confident | 自信 |
| curious | 好奇 |
| surprised | 惊讶 |
| shy | 害羞 |
| embarrassed | 尴尬害羞 |
| worried | 担心 |
| sad | 难过 |
| angry | 生气 |
| sleepy | 困倦 |
| playful | 调皮 |

## 情绪强度变化

| tag | 中文转写 |
|---|---|
| faint smile | 淡淡微笑 |
| big smile | 灿烂笑容 |
| laughing | 开怀笑着 |
| grin | 露齿笑 |
| smug | 得意 |
| proud | 骄傲 |
| relieved | 松了一口气 |
| moved | 感动 |
| touched | 被打动 |
| surprised slight | 微微惊讶 |
| shocked | 震惊 |
| nervous | 紧张 |
| anxious | 焦虑 |
| frustrated | 沮丧 |
| annoyed | 不耐烦 |
| irritated | 恼火 |
| furious | 愤怒 |
| tearful | 眼眶含泪 |
| crying | 哭泣 |
| exhausted | 疲惫 |

## 视线与眼神

| tag | 中文转写 |
|---|---|
| looking at viewer | 直视镜头 |
| looking away | 看向别处 |
| looking down | 垂眼看向下方 |
| looking up | 抬眼看向上方 |
| side glance | 侧眼看去 |
| glance back | 回眸看向镜头 |
| half-lidded eyes | 半眯眼神 |
| wide eyes | 睁大眼睛 |
| closed eyes | 闭眼 |
| one eye closed | 单眼闭起 |
| sparkling eyes | 眼神明亮 |
| sharp gaze | 眼神锐利 |
| distant gaze | 目光放空 |
| gentle gaze | 温柔目光 |
| suspicious gaze | 怀疑的眼神 |
| blank stare | 茫然注视 |

## 性格气质

| tag | 中文转写 |
|---|---|
| innocent | 天真 |
| mature | 成熟稳重 |
| elegant | 优雅 |
| cool | 冷淡帅气 |
| aloof | 疏离 |
| mischievous | 狡黠调皮 |
| teasing | 带着逗弄意味 |
| bashful | 腼腆 |
| brave | 勇敢 |
| heroic | 英气 |
| mysterious | 神秘 |
| quiet | 安静 |
| lively | 活泼 |
| obedient | 乖巧 |
| rebellious | 叛逆 |
| proud smile | 带着骄傲的微笑 |
| cautious | 谨慎 |
| confused | 困惑 |
| thoughtful | 若有所思 |
| lonely | 孤独 |

## 口型与面部状态

| tag | 中文转写 |
|---|---|
| mouth closed | 闭口表情 |
| mouth slightly open | 嘴唇微张 |
| open mouth | 张口表情 |
| speaking | 像是在说话 |
| sighing | 轻轻叹气 |
| pouting | 鼓起脸颊 |
| biting lip | 轻咬嘴唇 |
| tongue out | 轻轻吐舌 |
| blushing | 脸颊泛红 |
| sweatdrop | 带着尴尬汗滴 |
| frown | 皱眉 |
| raised eyebrow | 挑眉 |
| furrowed brow | 眉头紧锁 |
| relaxed face | 面部放松 |
| stern face | 严肃板起脸 |

## 选择规则

- 默认以 `neutral`、`soft smile`、`calm`、`focused` 这类稳定表情作为身份锚定。
- 每批提示词可以少量加入强表情，但不要让夸张表情超过整体的三分之一。
- 表情变化必须和动作、视角、景别至少有一项形成有效差异。
- 训练底图不要过度依赖闭眼、遮脸、张口大笑等会削弱五官稳定性的表情。
- 同一含义的表情不要反复使用，例如 `happy`、`cheerful`、`big smile` 可以分别用于不同强度，但不要在同一批里密集堆叠。
- 如果角色设定偏冷静、严肃或神秘，优先使用低强度表情，少量加入反差表情即可。
