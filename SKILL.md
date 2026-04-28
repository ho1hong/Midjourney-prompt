---
name: midjourney-prompt
description: Builds Midjourney prompts and iterates them using learned cross-image patterns from the user’s reference set (not a single fixed “house style”). Use when the user asks for MJ prompts, mentions Midjourney / --sref / --cref / --p / moodboard, uploads satisfied outputs for learning, or wants prompt iteration aligned with evolving taste.
---

# Midjourney Prompt Formatter

## Purpose

将用户的文字意图或参考图，转换为可直接粘贴到 Midjourney 的 prompt。

**核心原则（与用户约定一致）**  
- **审美母语置顶**：任何 prompt 生成前，先在 `aesthetic.md` 顶部「审美母语（Top Principle）」对齐——用户是 **brand-asset imagery**（汽车品牌资产化影像），不是摄影作品集审美。中心句：**「不是在拍车，是在为车建立世界观」**。8 词母语（真实/克制/秩序/人格/空间/余韵/系统/品牌化）是所有判断的根。  
- **不**把某一类题材（汽车大片）或某位参考摄影师（如 Schlosser）当成全局默认画风；数据集 A–D 与「摄影师参照」**只是风格词典**，**按当条需求取用**。  
- **不设默认车型**：单条任务里的车型只在该条生效（如揽胜、Taycan、G 级）；**不沉淀为全局默认**。用户未指定时用泛化描述（`modern premium full-size SUV` / `premium EV sport sedan` 等）或直接问一句，**不**自作主张填品牌。车型专有语义（例如揽胜的 `floating roof / clamshell bonnet / flush glazing`）只在当条明确点名该车型时写入。  
- **由 Agent 自主判断风格**：根据当条主体、地点、情绪、画幅暗示、用户是否点名特定语言（如「广告感」「纪实」「电影感」「奇幻」），在多条注册/数据集里**挑选最贴合的一条或混合两条**；若用户只给了题材与场景（如「林芝原始森林里的车」），就优先服务**主体与场景的真实逻辑**，审美共性作底层保障、不喧宾夺主。  
- **组图 / 系列任务**：默认按 `hero / tracking / detail / POV / KV` 做**镜头分工**，跨图对齐**色温弧 + 色彩呼应 + 镜头族**，不要同职位重复。单张任务也要留「可被扩成一组」的基因（调色族、光族、机位族可复现）。  
- **要**从用户持续提供的「满意图」里抽象 **可迁移共性**（光线逻辑、对比与调色习惯、镜头与动态、质感底线、排斥项），写入并维护 `aesthetic.md`，在**迭代与变体**里**默认融入 1–3 条**，可跨题材（同样偏好「硬光切割、低饱和、主体锐利」亦可迁移到人像/静物），但**不**硬套题材专有词。  
- **反审美清单是硬约束**：一旦检出 `fake CG / game render / cheap cyberpunk / unjustified ultra-wide / over-dramatic worm's-eye / shock-only composition / landscape dwarfing car / prop overload / no lens division across a set / style killing model credibility / generic cool shot without brand character`，重写，不心疼。  
- `--p` / moodboard 是**个人审美锚点**，补充而非替代上述「从图里学来的结构」。

**四条取舍底线**（遇到冲突时按序决定）  
1. 宁可克制，也不要失控  2. 宁可真实，也不要假炫  3. 宁可系列统一，也不要单张过猛  4. 宁可设计过的安静，也不要廉价的热闹

本技能优先级：**用户明确硬指令 > 当条任务目标 > 审美母语（默认审美与防跑偏 guardrail） > 审美底层共性自动融入 > 参数压榨 > 冗余解释**。  
说明：审美母语是默认判断系统，不是用来压过用户当条明确要求的硬规则；当用户临时要求「更夸张 / 更商业 / 更赛博 / 更艺术」时，先满足任务目标，再用审美母语控制真实感、秩序和品牌可信度。

三层解耦（抗 MJ 版本迭代）：
1. **意图层**（本文件）：视觉语法与模板路由  
2. **参数层**（`params.md`）：版本号、`--p` 策略、默认值  
3. **学习层**（`aesthetic.md`）：从满意图归纳的**可迁移共性** + 分题材的**观察笔记** + **更新日志**

## Input Types

1. 纯文字意图（人物/场景/产品/情绪）
2. 文字 + 参考图（复刻 / 风格迁移 / 身份锁定）
3. 已有 prompt 要求优化迭代

信息不足时，按 `params.md` 的默认值保守生成，不反复追问。

## Output Rule (Strict)

当用户要求「出 MJ prompt / Midjourney prompt / 给我一版 mj」时，**默认只输出自然语言 prompt**（不含 Midjourney `--` 参数）。英文与中文 prompt **分开两个独立代码块**，方便用户各自复制；注释独立成段，不混进代码块。

### 交付模式（持续迭代用）

根据用户语气自动选择交付重量；用户未指定时用 **standard**。

| 模式 | 触发 | 输出 |
|------|------|------|
| **quick** | 「快给一条 / 只要英文 / 先试一版 / quick」 | 只给 **1 个英文 prompt 代码块**，无中文、无注释、无参数 |
| **standard** | 默认；「给我一版 mj / 出 prompt」 | 英文 + 中文双代码块 + 注释 + 恰好 3 条发散方向 |
| **campaign** | 「一组 / 系列 / campaign / KV / 镜头矩阵 / 多张」 | 先给系列策略（镜头职位、色温弧、色彩呼应），再按张输出 prompt；每张一职，避免重复 |

规则：quick 模式也必须先过审美母语和反审美清单，只是**少说**；campaign 模式优先建立系统，而不是堆单张帅图。

### 默认交付结构（严格遵守）

**① 英文 Prompt（可直接粘贴到 Midjourney）**

```text
<english_prompt_one_line>
```

**② 中文 Prompt（同语义，供用户本地备忘或中文 MJ 使用）**

```text
<中文一行 prompt，结构与英文对齐，逗号链>
```

**③ 注释（单独一段，不进代码块，≤6 行）**  
开头用简洁一行说明本条的风格选配（对齐审美母语哪几条、走哪个数据集/注册）与关键构图/光取舍；随后列出**恰好 3 条发散方向**，用 `v1 / v2 / v3` 或 `方向 A / B / C` 标注差异维度，每条一行。发散要真的「拉开差异」（题材细节 / 光 / 机位 / 时间 / 车色/ 叙事焦点 挑不同维度扩），不要只微调形容词。

示例（仅示意格式，不是内容）：  
> 风格选配：对齐「真实 / 克制 / 场景共生」，走注册 B 金时刻 + 场景共生色彩呼应。  
> A. 换蓝调薄暮，车色改冷银灰，同一机位 → 更电影感  
> B. 前景加当地人推自行车剪影，眼平 35mm → 更故事性  
> C. 换高机位俯视窄巷，车对角线引导 → 更 KV / 品牌海报感

### 规则细节
- 英文 / 中文两个代码块**内容必须一致**（一句对一句，不要中文少翻或多翻）。中文保持逗号链、英文术语（lens / grade / POV 等）可保留原词以便 MJ 中文端解析。
- **默认两个代码块都不含** `--` 参数。
- **例外**：仅当用户**明确索要**「带参数 / 完整命令 / 一行复制含 --」时，另起第三个代码块给完整命令（参数顺序见 `params.md`）；此时中文块仍保持无参数。
- **不要**输出长分析、教学、对比；注释就是注释，3 条发散即止。
- 若用户要求「多版本 / 给几个变体 / give me variations」，把 3 条发散升级成 3 组完整产出（每组各配英文+中文双代码块），注释段合并在最末给出差异维度总览。

## Global Hard Rules

- 所有 prompt 写成**一行英文**，逗号分隔子句，MJ 对结构化逗号链解析最好
- **默认不输出 `--` 参数**；需要否定与约束时，优先写进**自然英语**（如 `no watermark, no extra logos, tack-sharp vehicle body`），少用或不用 `--no` 除非是用户要的「完整命令」模式
- 若输出完整命令（用户明确要求时），参数顺序见 `params.md`：`--p（可多枚）→ --sref → --cref → --ar → --style → --s → --c → --w → --no → --v`
- 语义上绝不混用冲突指令（例如写实与重度风格化在同一句里互殴）
- 复刻/身份锁定场景：不在 prompt 里强塞 moodboard 描述；**参考图 workflow** 仍用 `--sref`/`--cref`——仅当用户要**附参数**时写出（见 `params.md` 注入策略）
- 需要可读文字时，文字用**英文双引号**包裹（V8+ 对引号内文字渲染最佳）
- 写实人像必须显式声明光线方向与质感（`natural skin texture, no plastic retouch`）
- 下文 **Template 示例**中带 `--` 仅供内部结构与查阅；**交付用户时改为纯描述句**，剥掉 `--` 段
- **多主体路由硬规则**：用户提到「双车 / 两台 / 三台 / 车队 / fleet / 多车」→ 默认走 **Multi-Subject Route**（见下文）。**默认一张图完整展示所有车**，按 Dual-Hero Composition Grammar 的规整构图写；拆张只在用户明确要系列套图时才启用。

## Multi-Subject Route（双车 / 多车 / 车队）

### 默认策略：单帧完整展示 + 摆放规整（用户 2026-04-21 确认）

用户需求明确：**「双车的展示都需要默认以双车的完整展示，并且摆放规整」**。这是所有汽车品牌级 campaign 的正典（见数据集 F 的 12 张行业双车参考）。所以：

- **默认路线**：双车 = **一张图 + 两台车完整入镜 + 规整摆位**，按下文 **Dual-Hero Composition Grammar** 的 5 种经典构图之一来写  
- **次选路线**：拆两张单车 hero 做系列套图——仅当用户**明确说**「给我一组 / 系列 / day-to-night / 姊妹张」才启用，不做默认  
- MJ 对单帧双车并不完美，我们**靠规整构图 + 显式约束**把命中率压到可用：枚举式主体、车型 × 车色硬绑、规整间距、宽画幅、防融合/防数量尾巴

### Dual-Hero Composition Grammar（5 种规整双车摆位，任选一种）

从用户 2026-04-21 提供的 12 张行业双车参考归纳，按场景挑一种：

| 代号 | 名称 | 摆位描述 | 适用 |
|------|------|----------|------|
| **DC-1** | Mirrored 3/4 Pair | A **前三四分** + B **后三四分**，朝向微微相背，间距 3–6m，创造微妙 yin-yang / V 形 | **最常用最稳**；双车气质并列（轿 vs 猎装 / coupe vs cabrio） |
| **DC-2** | Parallel Stagger | 两车同朝向，一台略前一台略后错开 10–30°，做纵深 | 纵深叙事 / 公路 / 平台型场地 |
| **DC-3** | Opposing Bookends | 严格镜像对称，共享中央轴，朝内或朝外 | KV 海报 / 高仪式感产品发布 |
| **DC-4** | Symmetric Line-up | 并排同向一排，3/4 机位看过去，2–4 台通用 | 车系横向展示 / 年代传承片 |
| **DC-5** | Close-Stage Pair | 两车紧凑 + 强环境框景（工作室 / 建筑结构） | 舞台化 / 发布会 backdrop |

### Dual-Hero 硬指标（全部满足）

1. **两车完整入镜**：`both vehicles fully visible in frame, no cropping of any vehicle, no occlusion by foreground people or props`
2. **同一地面层**：`both cars standing on the same unified ground plane (tarmac / polished concrete plaza / salt flat / limestone pavement)`——地面是双车的视觉锚
3. **同一光向**：`single dominant light source from <方向>, coherent shadow direction across both cars, both paintworks receive the same light angle`
4. **同尺度**：`both vehicles rendered at consistent real-world scale, within similar distance to camera`——不允许近大远小失衡
5. **规整摆位**：`deliberate formal placement, clear physical gap of ~<N> meters between them, not touching, not overlapping silhouettes`
6. **水平 / 机位克制**：`eye-level or slightly low 35mm perspective, horizon in lower third, plumb verticals, no dutch tilt, no worm's-eye, no bird's-eye (unless DC-5)`
7. **宽画幅暗示**：`widescreen cinematic framing` / `broad horizontal composition`（用户在 MJ 客户端选 16:9 / 2:1 / 21:9；竖海报除外）
8. **色彩对比**：车色**不同家族** —— 一冷一暖 / 一明一暗 / 互补色（银↔红、黑↔香槟、蓝↔金、香槟↔深绿），**杜绝双胞胎银**

### Dual-Hero 写法模板（英文 prompt 骨架）

```
[brand] brand-asset dual-hero automotive photograph, two [brand] vehicles fully visible in one composition:
(1) a [model A, body type A] in [paint color A], [position] facing camera at a [front three-quarter / rear three-quarter / profile] angle, [wheel detail], [light signature detail], [distinguishing brand cue];
(2) a [model B, body type B] in [paint color B], [position] facing camera at a [opposite-angle from A] angle, [wheel detail], [light signature detail], [clearly distinct silhouette cue from car A],
the two cars placed roughly [N] meters apart on [unified ground plane] at [location], [environment / architecture / horizon / botanical context],
[single dominant light: time of day + direction], coherent shadow direction across both cars, [grade / mood keywords],
[camera: eye-level 35mm, formal framing, horizon position],
both vehicles rendered at consistent real-world scale, structurally independent bodies with a clear physical gap between them, each vehicle's paint color locked to its body type and must not swap,
[quiet luxury / editorial / KV / 其它情绪], photorealistic industrial finish with readable keyline on both cars, natural color balance,
exactly two vehicles in frame, no third car anywhere in the background, no additional vehicles, no merged or fused car bodies, no cloned front-end, no swapped paint colors, no mismatched scale, no teal-orange blockbuster grade, no CG plastic look
```

### 交付格式（默认单帧双车）

- **1 份英文 prompt + 1 份中文 prompt**（双代码块，和单车格式一致）  
- 注释说明**触发了哪种 DC 构图 + 色彩对比选择 + 光感注册**  
- **3 条发散方向**真正拉开差异（一条换 DC 代号 / 一条换光时段 / 一条换车色或地点）

若用户**明确要求**拆两张系列套图，或用户接受「MJ 连续失败后的降级方案」，才改走 2 张单车 hero 的旧路线（共享色光族 + 系列统一注释）。默认不主动把双车需求拆掉。

### 3 辆以上 / 车队

- **单帧 3 台**：按 **DC-4 Symmetric Line-up** 写（数据集 F / 01 Alfa Romeo trio 为范本），间距均匀，全部前 3/4 朝向，同一光同一地面。MJ 对 3 台仍可操作但命中率下降，建议多跑  
- **4 台以上**：用 `hero car（或 hero 对）in foreground + remaining fleet receding, defocused as environmental storytelling`；**全员清晰合影**建议 PS 合成

### 反审美复核（双车必查）

- ❌ 两车融合 / 车头粘连 / 颜色互串 / 尺度失衡 / 凭空冒出第 3 台  
- ❌ 有人物或道具**遮挡**车身（双车图里不放前景人物，除非极远的尺度剪影）  
- ❌ 地平线歪 / Dutch tilt / 贴地仰视（**规整构图**是双车底线，违反即不是「完整展示」）  
- ❌ 两车离得过远看成「远 + 近各一台」失去对话感

### 负向约束词库（防翻车尾巴，直接挂句末可用）

```
exactly two vehicles in frame, no third car anywhere in the background, no additional vehicles, no merged or fused car bodies, no duplicated front-ends, no swapped paint colors between vehicles, no mismatched scale between the two cars, no car clone artifact, no cropping of any vehicle, no occlusion of either car by foreground people or props
```

## Base Prompt Syntax

常规场景按以下顺序写进一行（从高优先级到低优先级）：

```text
[Subject with specific attributes], [Action or State], [Setting: place/time/weather/mood],
[Style or Medium reference], [Composition & Camera: focal length, angle, distance],
[Lighting: direction, quality, color temperature], [Color palette & Grading],
[Material & Texture details], [Negative constraints if critical]
--parameters
```

核心原则：
- **具体名词 > 模糊形容词**（用 `Kodak Portra 400 grain` 不是 `film look`）
- **光线单独成句**，不要和环境混在一起
- **颜色用具名词汇**（`muted oxblood, dusty sage, warm ivory`）不要只说 `vintage colors`
- **镜头参数具体化**（`shot on 85mm f/1.4, shallow depth of field`）

## Routing Rules

根据用户意图自动选模板；`--` 参数默认不输出（见 Output Rule），「注入」列仅作 Agent 推理 moodboard 逻辑用，不落到 prompt 正文：

| 意图关键词 | 模板 | Moodboard 默认 | 常见画幅暗示 |
|---|---|---|---|
| 肖像 / 人脸 / 特写 / 情绪 | Template 1 Portrait | 注入 | 3:4 |
| 产品 / 电商 / 持握 / 品牌 | Template 2 Product | 弱注入 | 1:1 |
| 复刻 / match reference / 同款 | Template 3 Recreate | 禁止 | 参考图比例 |
| 九宫格 / storyboard / 多动作 | Template 4 Storyboard | 注入 | 1:1 或 3:4 |
| logo / 徽章 / Y2K / 矢量品牌 | Template 5 Graphic | 禁止 | 1:1 |
| 场景 / 城市 / 车辆 / 叙事 / 自然地景 | Template 6 Scene | 注入 | 16:9 或 21:9 |
| 汽车 / 跟拍 / 汽车广告 | Template 9 Automotive | 注入（可双 moodboard） | 16:9 / 21:9 |
| split / 一半 / 对比风格 | Template 7 Stylized | 弱注入 | 3:4 |
| 胶片质感 / film / 35mm / Portra | Template 8 Film | 注入 | 3:2 |
| 无法判断 | Base Syntax 通用版 | 注入 | 3:4 |

### 风格选配（由 Agent 自主判断，不设默认唯一风格）

给定当条需求，从 `aesthetic.md` 的**数据集 A–D** 与**摄影师参照**里**挑选**最贴合的一条（或混合两条）。常用映射作参考：

| 当条线索 | 可选对齐 |
|----------|----------|
| 城市街拍 / 跟拍 / 速度 | 数据集 A + 注册 A 光 |
| 庄园 / 度假 / 家庭 / 旅行感 | 数据集 B + 注册 B 光 |
| 森林 / 雾 / 体积光 / 静谧 | 数据集 C + 注册 C 光 |
| 极简几何 / 抽象布景 / 广告艺术化 / 时装感 | 数据集 D + 注册 D |
| 荒野 / 地貌叙事（雪山、沙漠、峡谷、原始森林） | **优先服务地景真实感**，再按光感选 A/B/C，不强行套 D |
| 奇幻 / 故事 / 装置 / 概念 | 可借 D 的大胆机位，但**不强**；以用户具体故事为主 |

**判断原则**  
- **地点与情绪 > 摄影师风格**：除非用户点名某位参考，不要把布景几何化或动作时装化。  
- **允许混合**：例如「林间 + 荷兰角低机位」= 注册 C × 数据集 D 的机位，但保持 C 的真实自然。  
- **保留底层共性**：不管选哪条，都默认附带 **aesthetic 的可迁移共性**（主次清晰区、低饱和克制调色、前景层做纵深、质感底线反 plastic）。

## Template Library

### Template 1 — Cinematic Portrait（电影感人像）

适用：人像特写 / 情绪肖像 / 环境人像。

```text
[age + gender + ethnicity if known] [specific outfit with texture], [specific pose and gaze], [specific setting with 1-2 atmospheric details], shot on [85mm / 50mm / 35mm] lens, [shallow / medium] depth of field, [rim light from behind / chiaroscuro slit light / soft window light] as the primary light source, [cool blue / warm amber / neutral daylight] color temperature, [specific mood adjective: intimate, moody, resolute, contemplative], natural skin texture with visible pores, no plastic retouch, [film stock reference if desired] --style raw --ar 3:4 --s {{DEFAULT_STYLIZE}} --p {{DEFAULT_MOODBOARD}}
```

硬规则：
- 光线必须单独成句且明确方向
- 必须包含 `natural skin texture` 类防塑料感约束
- 身份锁定任务用 `--cref <url> --cw 100`（不改脸）或 `--cw 50`（保留氛围）

### Template 2 — Premium Product（高级商业产品）

适用：电商主图 / 品牌产品 / 场景植入。

```text
close-up commercial product photograph of [product with specific material and label], [brand name in quotes if text needed], [hand interaction or environment placement], [specific surface: brushed concrete, raw linen, warm oak], soft diffused studio lighting with gentle highlight rolloff, [color palette: muted warm neutrals / clean minimalist whites / editorial dark mood], sharp focus on product label, shallow depth of field background, premium minimal branding aesthetic, photorealistic --style raw --ar 1:1 --s 100 --p {{DEFAULT_MOODBOARD}} --sw 50
```

硬规则：
- 产品保留原样场景必须加 `--cref <product_url> --cw 100`
- moodboard 用**弱权重 `--sw 50`**，避免污染产品本身的品牌色
- 有文字一律写成 `labeled "Brand Name"` 格式

### Template 3 — Reference Recreation（参考图复刻）

适用：`match reference / 复刻这张 / same composition`。

```text
[one-line subject description matching reference], same composition and framing as reference, [explicit camera anchor: eye-level / low angle / bird view], [horizon position], [dominant palette from reference], [lighting direction and time of day from reference], preserve subject identity and proportions, do not redesign or restyle --sref <REF_URL or code> --sw {{RECREATE_SW}} --cref <REF_URL if person> --cw 100 --ar {{REFERENCE_AR}} --style raw --s 50
```

硬规则：
- **不注入 `--p`**，避免 moodboard 覆盖参考信号
- `--sref` 权重 `--sw` 范围 200–400 最稳（低了不像，高了丢描述控制）
- 有人物身份时必带 `--cref + --cw 100`
- 构图描述必须包含：主体位置锚点 + 地平线位置 + 主色调

### Template 4 — Storyboard Grid（九宫格 / 多动作一致性）

适用：同一主体 × 多动作 / 分镜 / 社媒网格。

```text
9-panel 3x3 grid storyboard, same subject in identical outfit across all panels, consistent [background color] studio background, soft diffused lighting, editorial lifestyle photography, clean commercial aesthetic. Subject: [description]. Panels left-to-right top-to-bottom: 1 [action + shot size], 2 [action + shot size], ... 9 [action + shot size]. Mix of wide, medium, and close shots, consistent framing logic, balanced grid layout --ar 1:1 --style raw --s 100 --p {{DEFAULT_MOODBOARD}} --sw 150
```

硬规则：
- 必须给面板编号
- 必须声明 wide / medium / close 混合
- 必须声明背景与服装一致性
- 人物一致性强制加 `--cref <url> --cw 100`

### Template 5 — Graphic / Badge Design（徽章 / 矢量品牌）

适用：Y2K 徽章 / 街头品牌图形 / 矢量标贴。

```text
flat vector badge design, [brand name in quotes], [iconic shape: circular badge / shield / hexagon], [primary color] as dominant with [accent color] pops and [dark outline color], limited to 4 colors maximum, [key symbols: star, katakana, flag, speed lines], [brand DNA: Y2K chrome / 90s skate / retro racing], clean geometric structure, no gradients, no 3D rendering, no photographic elements, off-white flat background --ar 1:1 --s 400
```

硬规则：
- **禁止注入 `--p`**（moodboard 会污染矢量平面感）
- 必须声明 `no gradients, no 3D`
- 颜色硬上限 4 色
- 文字一律引号包裹

### Template 6 — Scene / Narrative（场景 / 叙事）

适用：城市街拍 / 车辆环境 / 电影级景观。

```text
[specific scene: overgrown highway / neon-lit alley / misty pine forest clearing], [subject or vehicle in specific position], [time of day with specific quality: golden hour raking light / overcast flat diffusion / blue hour with sodium streetlamps], shot from [camera position] with [focal length], [weather and atmospheric detail: light drizzle, volumetric haze, drifting dust], [color grade: desaturated teal shadows with warm highlights / natural cinema palette], cinematic composition, atmospheric depth, photorealistic --ar 16:9 --style raw --s 100 --p {{DEFAULT_MOODBOARD}} --sw 150
```

硬规则：
- 时间与光线必须具体（不接受 `beautiful lighting`）
- 大气细节必须有（雾 / 尘 / 光束 / 雨 三选一）
- 纯无人场景必加 `--no people, pedestrians, crowd`

### Template 9 — Automotive Commercial（高端汽车商业大片）

适用：**仅当**用户本条需求为汽车/车拍/汽车广告时启用。  
从满意图学到的**共性**（硬光、对比、跟拍清晰区）已收入 `aesthetic.md`，迭代非汽车题材时**引用共性条目**，**不要**套用本模板全文。

```text
premium automotive commercial photograph, [color + model class: white Porsche Taycan / silver EV sports sedan], [dynamic pose: cornering / accelerating straight], [camera: low-angle 24mm wide OR high-angle tracking three-quarter], strong sense of speed: horizontal panning motion blur on asphalt and background buildings, car body tack sharp, readable body lines and glass reflections, [lighting: golden hour hard sunlight OR chiaroscuro with deep building shadows OR sun shaft under brutalist overpass], [environment: luxury urban street, glass storefronts, stone facades, European classical architecture OR modern financial district], desaturated cinematic color grade, cool metallic paint vs warm sunlit architecture, [accent: neon lime inner rim / gold bronze wheels / yellow calipers - pick one], photorealistic 8k advertising finish --ar 16:9 --style raw --s 100 {{STACKED_MOODBOARDS}} --sw 150 --no plastic HDR glow, teal-orange blockbuster grade, mushy blur on car body, deformed wheels --v 7
```

硬规则：
- **车身必须 sharp**，模糊只给路面与背景；写明 `panning` / `directional motion blur`
- 调色：**克制冷暖对撞**，用 `--no` 挡廉价青橙预设（除非用户要）
- 「最像我」：在**已学共性**基础上再考虑双 moodboard（`params.md` 的 `{{STACKED_MOODBOARDS}}`），`--sw 120–200`
- 复刻某张参考：**禁止** `--p`，改 `--sref` + 构图锚点
- 需要无人街景时再加 `--no people, pedestrians`；要街拍感则**不要**清场

### Template 7 — Split / Stylized（一图两风格对比）

适用：写实转插画 / split 视觉钩子。

```text
split-style portrait vertically divided into two halves, same subject identical pose and expression across halves. Left half: photorealistic photograph, natural lighting, authentic skin texture, subtle neon doodle outlines. Right half: clean flat digital illustration [style: soft pastel / bold vector / ghibli-adjacent], same pose, smooth lineart, [pastel / muted] tones. Background 100% identical across both halves. Divider: [torn paper / glitch line / clean vertical] --ar 3:4 --s 250 --p {{ALT_MOODBOARD}} --sw 100
```

### Template 8 — Film Emulation（胶片质感）

适用：明确要求胶片感 / 具体胶片型号。

```text
[subject and setting description], shot on [Kodak Portra 400 / Fuji 400H / CineStill 800T / Ektachrome E100] 35mm film, [grain character: fine natural grain / visible halation on highlights], [color science: Portra warm skin tones and muted greens / CineStill magenta halation in tungsten], natural light, [specific time of day], no digital sharpening, authentic analog color rendering, slight vignette --ar 3:2 --style raw --s 50 --p {{DEFAULT_MOODBOARD}} --sw 100
```

硬规则：
- 必须点名具体胶片型号（通用 `film look` 在 V7/V8 效果差）
- 不要和 `8K / ultra HD` 类现代词混用
- `--style raw` + 低 `--s` 才能拿到真实颗粒

## Learning Mode（用户上传满意图时）

当用户发来**新的满意出图**（可带或不带原 prompt）：

1. **归纳共性**：跨这批图写「重复出现的结构」——例如对比强弱、动态与清晰区的分工、调色是冷还是暖、构图偏好（留白 / 前景框 / 机位高低），**区分**「可跨题材迁移」与「仅适用于当前题材」。
2. **更新 `aesthetic.md`**：  
   - 在 **「可迁移共性」** 中合并或修正条目（去重，避免越写越碎）。  
   - 在 **「分题材观察」** 里为当前批次加一小节或更新「数据集记录」。  
   - 在 **「学习日志」** 里记一行日期 + 批次简述（不必长文）。
3. **不**自动把新图里的具体主体（如某车型）写成全局默认；**除非**用户明确说「以后默认都要这种主体」。
4. 用户点名**参考摄影师/导演**：在 `aesthetic.md` 增加「摄影师参照」小节，把风格 **翻译成可执行的构图/光/色/布景词**；MJ 对摄影师名字响应不稳，**优先写视觉语义**，人名仅作用户备忘。

用户若仅说「学一下这张图」：只更新观察笔记，不强行改路由默认值。

### Reference Library（待学习队列 + 已学归档）

为了让用户**异步投喂素材**更顺手，本 skill 维护一个本地队列：

```
midjourney-prompt/
├── reference-library/
│   ├── inbox/        ← 用户新扔进来的、**未学习**的素材
│   └── processed/    ← 已完成学习、按批次/日期归档的素材
```

**用户侧用法**（三选一）：
1. 直接把图片（或图片 URL，我用 `curl` 下载）放进 `reference-library/inbox/`  
2. 按主题建子文件夹：`inbox/2026-04-21_milan-night/` 之类，便于我按批次读  
3. 聊天里贴链接或图，并附一句**「学一下 / 收进去 / 入库」**——我会自动落到 `inbox/` 再走下面的流程

**Agent 侧处理流程**（触发词：`学一下`、`吸收一下`、`入库`、`处理 inbox`、`学习 reference-library`）：
1. `ls reference-library/inbox/`，按子文件夹或批次分组  
2. `Read` 每张图，跨图抽共性 → 合并进 `aesthetic.md`「可迁移共性」/「分题材观察」  
3. 在 `aesthetic.md` 的**学习日志**记一行：日期 + 批次名 + 图片数 + 主要收获（1–2 句）  
4. 处理完把整个批次**移动**到 `processed/YYYY-MM-DD_<topic>/`；**不删图**，便于后续回看  
5. 若用户说「只看一眼别入库」→ 只读不动文件不写日志

**边界与安全**：
- 移动/下载需要 `required_permissions: ["all"]` 执行 Shell；第一次跑前向用户确认一次即可  
- 批量建议 ≤ 50 张/次；更多分批处理，避免 context 爆炸  
- PDF / 视频不直接吃；提示用户先截图或截帧再放 inbox  
- 社交平台抓不下来（Pinterest/小红书/IG）不在自动范围；提示用户客户端另存为本地文件

## Iteration Mode

用户给出已有 prompt 要优化时：

1. 保留主体与核心风格词
2. **每次只改 1–2 个变量**（只改光线、只改镜头、只改调色）
3. 继承既有有效约束
4. **查阅 `aesthetic.md` 的「可迁移共性」**：若与当条任务不冲突，选 **1–3 条**融入（例如更克制的调色、更明确的主次清晰区），**不要**堆砌汽车专有词到非汽车任务
5. 输出完整新 prompt（**默认仅一行英文**，无 `--`），不要 diff

常见迭代映射：

| 用户说 | 调整方向 |
|---|---|
| 更电影感 | 加 `anamorphic lens flare, ultra-wide cinematic composition`；用户自建宽幅时客户端选 21:9 |
| 更写实 | 加 `raw photorealistic texture, natural materials`、具体胶片名；少说「插画感」形容词 |
| 更高级 | 减饱和词 / 加 `editorial minimal, restrained palette` |
| 更像我的风格 | 先对齐 `aesthetic.md` 共性；用户可在客户端加强 moodboard / stylize，交付中不单写 `--` |
| 不像参考图 | 用户上传参考 + 客户端调高 style ref 权重；正文里补构图锚点句 |
| 背景太乱 | 加 `clean minimal background, shallow depth of field, isolated subject` |
| 脸变了 | 用户侧用角色参考；正文强调 `preserve identity, same facial structure` |
| 颜色太青橙 | 加 `natural color balance, no teal-orange blockbuster grading` |

## Quality Checklist (self-check before output)

**审美母语（必查，先于一切）**
- [ ] 是否对齐「真实 / 克制 / 秩序 / 人格 / 空间 / 余韵 / 系统 / 品牌化」至少 3–5 条
- [ ] **车型可信度**是否保住（车身锐、比例对、材质可读，不被风格吞掉）
- [ ] **场景与车**是否共生（同一调色/光逻辑），而不是车站在「好看的背景」前
- [ ] **色彩呼应**是否成立（车色 ↔ 环境色同家族）
- [ ] 组图任务：是否有**镜头分工**（hero / tracking / detail / POV / KV 各司其职）；单张是否留下「可被扩成一组」的基因
- [ ] 是否触发**反审美**（假 CG / 游戏感 / 廉价赛博 / 无理由广角 / 过度仰视 / 冲击力导向 / 风景压车 / 元素堆砌 / 无秩序叙事 / 牺牲车型可信度 / 无品牌气质的「帅图」）——触发即重写

**结构与交付**
- [ ] 是否匹配了正确模板，注入策略是否符合 Routing Rules
- [ ] 默认交付是否**未**含 `--`（除非用户要完整命令）
- [ ] 是否输出了**英文 + 中文两个独立代码块**（内容对齐，各自可复制）
- [ ] 注释是否**独立成段**（不在代码块里），且包含**恰好 3 条**真正拉开差异的发散方向
- [ ] 若用户要完整命令，参数顺序是否见 `params.md`
- [ ] 光线是否单独成句（人像/场景必须）
- [ ] 是否有冲突指令（写实 vs 过度风格化 / minimal vs busy）
- [ ] 复刻任务：正文是否未依赖 moodboard 语义顶替参考图
- [ ] 文字是否用了英文双引号
- [ ] 是否为一行可直接复制
- [ ] 是否**误把**某批参考图的题材默认套到无关任务（应只迁移共性）

## Additional Resources

- 参数默认值 / 版本切换 / moodboard 分工：见 [params.md](params.md)
- **从满意图学习的共性 + 分题材笔记 + 日志**：见 [aesthetic.md](aesthetic.md)
- 失效类型 → 补丁：见 [defect-patches.md](defect-patches.md)
- 回归校验任务集：见 [evals.md](evals.md)
