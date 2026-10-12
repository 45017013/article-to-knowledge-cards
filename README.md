# make-knowledge-cards

把一篇文章转换成 **5～8 张知识卡片**：每张卡片只讲一个知识点，包含四个字段——**标题、核心知识、简明解释、例子或自测**。

## 1. 项目解决什么问题

读完一篇长文，真正值得记住的往往只有几个知识点，但它们散落在铺垫、故事和营销话术之间：直接做笔记容易变成"抄全文"，心里想复习又无从下手；而把文章丢进 Anki 之类的工具，又需要先自己完成"什么是重点"这一步。

这个技能把"从一篇文章里挑出值得记住的知识点"固定成一套可复用的流程：通读全文并列出候选知识点 → 用重要性标准筛选 → 合并重复内容 → 按原文信息量决定数量 → 逐张成卡 → 自检。它做的是**提取**而不是压缩：宁可输出 4 张卡片并说明原因，也不为了凑数把两个知识点塞进一张卡。

## 2. 主要功能

| 功能 | 说明 |
| --- | --- |
| 两种输入 | 直接粘贴的文章正文；本地 `.md` / `.markdown` / `.txt` 文件路径 |
| 输出 5～8 张卡片 | Markdown 格式，每张卡片含标题、核心知识、简明解释、例子或自测四个字段 |
| 一张卡片只讲一个知识点 | 相互独立的主张会拆成两张卡；原文自身的一个编号步骤作为一个整体只占一张卡 |
| 只提取重要知识 | 保留定义与命名概念、机制与因果、可执行的规则、颠覆预期的非显然事实；剔除场景铺垫、没有结论的轶事、未经论证的观点、营销与行动号召、更新日志、导航类文字，以及已保留知识点的重复表述 |
| 删除重复内容 | 同一主张的不同表述合并到最具体的那张卡片 |
| 绝不编造 | 不引入原文没有的事实、定义、数字、日期、例子与专有名词；保留原文术语与"通常""建议"等限定；不把原文的示例数值推广成公式；原文没说的关键信息写"原文未说明：…"，而不是自行补充 |
| 数量随原文决定 | 目标 5～8 张；原文支持不足时如实输出更少并说明原因；候选超过 8 个时先合并重复项、把支撑性细节并入相应卡片，再舍弃最次要项，并用一行说明略去了几个知识点 |
| 语言跟随原文 | 表头、卡片标题与字段标签全部使用原文语言；简体中文与英文的模板已在 `references/label-sets.md` 中固定，其他语言按英文模板逐项翻译 |
| 边界明确 | 网页抓取、PDF、Anki 导出、图形界面均不支持，遇到时会说明原因并给出替代做法 |

## 3. 安装方法

技能以文件夹为单位分发。把 `make-knowledge-cards` 整个目录放进任意一个技能搜索目录即可：

| 作用范围 | 放置位置 |
| --- | --- |
| 当前项目 | `.github/skills/make-knowledge-cards/`（同时也支持 `.agents/skills/`、`.claude/skills/`） |
| 个人（所有项目） | `~/.copilot/skills/make-knowledge-cards/`（同时也支持 `~/.agents/skills/`、`~/.claude/skills/`） |

在本仓库中使用（Windows PowerShell）：

```powershell
New-Item -ItemType Directory -Force .github\skills | Out-Null
Copy-Item -Recurse skill\make-knowledge-cards .github\skills\
```

放好后重新加载窗口（或新开一个会话），技能就会被自动识别。注意目录名必须与 `SKILL.md` 里的 `name` 字段一致（这里都是 `make-knowledge-cards`），否则不会加载。

## 4. 使用方法

**斜杠命令**：输入 `/make-knowledge-cards`，然后粘贴正文或给出文件路径。

**自然语言**：直接描述需求也能触发，例如"把下面这篇文章做成知识卡片""帮我把 `docs/http-cache.md` 整理成几张知识卡片"。

**常见用法**：

```
/make-knowledge-cards
（随后粘贴正文）

把 article.md 做成知识卡片

用 make-knowledge-cards 处理一下这段，我只想记住里面的要点：
（粘贴正文）
```

**输出格式**（中文原文使用简体中文标签集，英文原文使用英文标签集，见 `references/label-sets.md`）：

```markdown
# 知识卡片：<原文标题>

来源：<粘贴的文章 | 文件路径> ｜ 卡片数：<N>

---

## 卡片 1：<标题>
- **核心知识**：<一句话>
- **简明解释**：<2～5 句>
- **例子或自测**：<原文给出的例子，或一道能在原文中找到答案的问题>

---

## 卡片 2：<标题>
...
```

卡片之间用 `---` 分隔，字段顺序与标签在整组卡片中保持一致。标题优先沿用原文标题；原文标题里自带冒号等标点时原样保留。

除卡片本身外，输出中可能多出一行说明：

- **原文信息量不足 5 个知识点时**：一行说明输出的张数和原因，例如"本文只包含 2 个独立知识点，因此输出 2 张卡片。"（这句在 `references/label-sets.md` 中有固定模板）
- **确有知识点被舍弃时**：一行报告略去的数量，不列出具体内容（措辞按原文语言组织，没有固定模板）

## 5. 输入输出示例

### 示例一：粘贴短文（原文只支持 2 个知识点）

**输入**

```
把下面这段做成知识卡片：

响应缓存中的条目默认 60 秒后过期。写入时传入 ttl，可以覆盖该条目的默认时长。
缓存键区分大小写，因此 /User 和 /user 是两个不同的条目。
```

**输出**

```markdown
# 知识卡片：响应缓存的过期与键匹配

来源：粘贴的文章 ｜ 卡片数：2

本文只包含 2 个独立知识点，因此输出 2 张卡片。

---

## 卡片 1：条目默认 60 秒过期，写入时可传 ttl 覆盖
- **核心知识**：响应缓存中的条目默认存活 60 秒，除非写入时用 ttl 覆盖这一条目的默认时长。
- **简明解释**：60 秒是缓存条目的默认过期时间。写入某个条目时传入 ttl，只改变这一个条目的时长，而不是全局默认值。
- **例子或自测**：自测：不传 ttl 写入的条目，默认存活多久？答案：60 秒。

---

## 卡片 2：缓存键区分大小写
- **核心知识**：缓存键区分大小写，所以 /User 和 /user 是两个不同的条目。
- **简明解释**：因为比较缓存键时区分大小写，这两个只有大小写不同的路径不会命中同一个条目，而是各自对应一个条目。
- **例子或自测**：自测：先缓存 /User 的响应，再请求 /user，会命中同一个条目吗？答案：不会——缓存键区分大小写，两者是两个不同的条目。
```

### 示例二：本地文件（候选知识点超过 8 个）

**输入**

```
把 docs/git-rebase.md 做成知识卡片
```

**输出（节选）**

```markdown
# 知识卡片：用交互式 rebase 把凌乱的提交整理成一条干净的历史

来源：docs/git-rebase.md ｜ 卡片数：8

另有 2 个知识点在按信息量筛选时被略去。

---

## 卡片 1：交互式 rebase 的用途
...
```

此处 `卡片数：8` 落在目标区间内，因此不需要数量说明；被舍弃的知识点只报数量，具体内容不列出。

### 完整样例（`examples/` 目录）

仓库附带 5 组完整样例，均取自技能开发过程中对最终版规则的实测运行，未经人工美化。`*.input.md` 是原文，`*.cards.md` 是对应输出：

| 样例 | 输入 | 输出 | 卡片数 | 文章类型与考察点 |
| --- | --- | --- | --- | --- |
| 01 | `examples/01-tls-mechanism.input.md` | [01-tls-mechanism.cards.md](examples/01-tls-mechanism.cards.md) | 8 | 中文技术机制科普，末尾含产品推广段（考察剔除推广、机制类知识点） |
| 02 | `examples/02-git-interactive-rebase.input.md` | [02-git-interactive-rebase.cards.md](examples/02-git-interactive-rebase.cards.md) | 8 | 中文操作指南，候选知识点多于 8 个（考察取舍与提示行） |
| 03 | `examples/03-the-pump-on-broad-street.input.md` | [03-the-pump-on-broad-street.cards.md](examples/03-the-pump-on-broad-street.cards.md) | 7 | 英文历史叙事，原文没有现成例子（考察英文标签集与自测题兜底） |
| 04 | `examples/04-seat-based-pricing.input.md` | [04-seat-based-pricing.cards.md](examples/04-seat-based-pricing.cards.md) | 8 | 中文商业观点，含作者个人判断与推广段（考察立场类内容与噪音剔除） |
| 05 | `examples/05-rate-limit-notice.input.md` | [05-rate-limit-notice.cards.md](examples/05-rate-limit-notice.cards.md) | 6 | 中文极短通知，信息量偏少（考察不凑数、标签一致性） |

几点说明：

- 为便于公开发布，样例输出头部的来源行已改写为相对路径（`examples/….input.md`）。实际使用时这里会显示你粘贴的文本或你给出的文件路径。
- 样例 01 与 02 因候选知识点超过 8 个，各多出一行"略去"说明；两份样例的措辞不同，因为这句话没有固定模板（`references/label-sets.md` 只为"信息量不足 5 张"和"原文未说明"提供固定措辞）。样例 05 的卡片数落在区间内，因此没有这类附加行。
- 样例 04 的推广段落已被识别为噪音并整体剔除，卡片集中不含任何营销内容。

## 支持范围

| 输入 / 产出 | 是否支持 |
| --- | --- |
| 粘贴的文章正文 | ✅ |
| 本地 `.md` / `.markdown` / `.txt` | ✅ |
| 网页抓取 | ❌ |
| PDF | ❌ |
| 导出 Anki 牌组 | ❌ |
| 图形界面 | ❌ |

不支持的四项会明确说明，并提示替代做法（网页先另存为文本、PDF 先转成文本或 Markdown、卡片可粘贴到任何支持 Markdown 的地方）。

## 文件结构

```
skill/
├── README.md                 # 本文件
├── LICENSE                   # MIT License
├── examples/                 # 5 组完整样例：*.input.md 为原文，*.cards.md 为对应卡片输出
└── make-knowledge-cards/
    ├── SKILL.md              # 技能主体：输入处理、工作流、卡片规则、忠实性规则、计数策略、输出格式、自检清单
    ├── agents/
    │   └── openai.yaml       # 界面元数据：display_name / short_description / default_prompt
    └── references/
        └── label-sets.md     # 中英文的表头、卡片标题与字段标签模板
```

技能采用通用的 `SKILL.md` 目录格式；`agents/openai.yaml` 额外提供面向 OpenAI / Codex 类环境的界面元数据（显示名、简介、默认提示词）。

## 说明与校验

- **校验技能**：使用官方 `skill-creator` 的校验脚本（来自 [openai/skills](https://github.com/openai/skills) 的 `skills/.system/skill-creator/scripts/`）：

```bash
curl -sSL -o quick_validate.py \
  https://raw.githubusercontent.com/openai/skills/main/skills/.system/skill-creator/scripts/quick_validate.py

python quick_validate.py skill/make-knowledge-cards
# Skill is valid!
```

- **`SKILL.md` 为什么保持纯 ASCII**：该脚本按系统默认编码读取 `SKILL.md`。在中文 Windows（GBK）等非 UTF-8 区域设置下，只要 `SKILL.md` 含非 ASCII 字符就会抛 `UnicodeDecodeError`。因此中文标签放在 `references/label-sets.md`，`SKILL.md` 保持纯 ASCII，官方脚本开箱即用。
- **许可**：[MIT License](./LICENSE)，Copyright (c) 2026 Evan。
