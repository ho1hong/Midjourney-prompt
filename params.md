# Midjourney Parameters Config

> **参数层**：MJ 升级时优先只改本文件。`SKILL.md` 中的 `{{VARIABLE}}` 由此处替换。  
> **默认不向用户粘贴 `--` 参数**（用户在客户端自选）；仅当用户**明确要完整命令**时，由 Agent 按本文件拼接。

**Last updated: 2026-05-18**

---

## Current Version Policy

> **Important user preference (2026-05-18)**：后续出 prompt 时，默认使用 **V8.1 prompt-writing grammar**，但**不在交付 prompt 中添加版本参数**。也就是说：写法按 V8.1 的短、准、字面、结构化来组织；输出默认仍是无参数自然语言 prompt。

| 模型 | 状态 | 何时使用 |
|------|------|----------|
| V8.1 语法（无 `--v`） | **默认写法** | 所有日常 prompt：短、清楚、可见物件优先、少堆砌、结构清晰 |
| `--v 8.1` | 仅完整命令且用户明确要版本参数 | 用户说「带版本 / 完整命令也写版本 / 指定 V8.1 参数」 |
| `--v 7` | 仅版本对比或用户明确指定 | 用户明确要 V7，或要测试 V7 vs V8.1 质感差异 |
| `--niji 6` | 场景专用 | 二次元 / 动漫 / 插画 |

### `{{VERSION}}`

```text
{{VERSION}} = 
```

默认留空，不自动拼接版本参数。若用户明确要求完整命令且要版本参数，再使用 `--v 8.1`。

---

## Moodboard Library（用户资产）

`--p` 是 **MJ 个人审美锚点**，与 `aesthetic.md` 里「从图学到的结构」**互补**：结构靠 prompt 写清，moodboard 靠模型侧对齐你的训练偏好。

用户可选用 **双 moodboard 叠放**（加强一致性，**注意** `--sw` 过高会压过文字描述）：

```text
{{STACKED_MOODBOARDS}} = --p m7434451482654539812 --p m7414342864638836753
```

| Alias | Code | 说明 |
|-------|------|------|
| `{{DEFAULT_MOODBOARD}}` | `m7434451482654539812` | 主 moodboard |
| `{{ALT_MOODBOARD}}` | `m7414342864638836753` | 次 moodboard |
| `{{LEGACY_MOODBOARD}}` | `2f141393-8c8c-404d-bc43-e178d5e6ed48` | 旧 UUID 个性化，兜底 |

### 语义

- 两枚 `m*`：用户常用 moodboard；**不**在 skill 里预设「等于汽车风」——**题材**仍由用户当条 prompt 与模板决定；**共性**以 `aesthetic.md` 为准。
- 复刻 / `--cref` 身份 / 产品原样：**不注入** `--p`（见 Moodboard Injection Policy）。

### Moodboard Injection Policy

| 场景 | 推荐 |
|------|------|
| 创意生成 / 场景 / 车拍（用户未禁止 moodboard） | 按需 `{{STACKED_MOODBOARDS}}` 或单 `--p {{DEFAULT_MOODBOARD}}`，`--sw 100–150` |
| 要「最像我」但怕过冲 | 单 `--p {{DEFAULT_MOODBOARD}} --sw 120` |
| 复刻参考图 / 产品不变形 | **禁止** `--p` |
| 矢量 / 徽章 | **禁止** `--p` |
| 人像（非复刻） | 单 `--p {{DEFAULT_MOODBOARD}} --sw 100–150` |

### `{{DEFAULT_SW}}`（moodboard 权重）

- 默认：`120`（略低于「风格碾压」，适配还要写清车型与构图的用户）
- 「就要我的味」：`180–250`
- 复刻任务：不适用（不注入 `--p`）

---

## Default Parameter Values

### `{{DEFAULT_STYLIZE}}`

```text
{{DEFAULT_STYLIZE}} = 100
```

按模板覆盖见 SKILL；V8.1 语法下，写实商业 / 汽车 / 人物生活方式默认建议 **短 prompt + `--raw` + `--s 100–300`**（仅在用户明确要完整命令时输出参数）。艺术广告或 personalization-heavy 探索再试 `--s 300–600`，`--s 700–1000` 只作放开探索，不作默认。

### `{{RECREATE_SW}}`（`--sref` 权重）

```text
{{RECREATE_SW}} = 300
```

### `{{DEFAULT_AR}}`

```text
{{DEFAULT_AR}} = 3:4
```

汽车横版大片常用：`16:9` 或 `21:9`（见 Automotive 模板）。

---

## Global Default Tail

```text
{{QUALITY_TAIL}} = --raw --s {{DEFAULT_STYLIZE}}
```

---

## Parameter Order（严格）

```text
--p ... --p ... --sref ... --sw ...
--cref ... --cw ...
--ar ... --raw --s ...
--c ... --w ...
--no ...
--v ...
```

多枚 `--p` 时保持用户习惯顺序：**先 m743… 再 m741…**（与用户提供顺序一致）。

---

## Negative Default（汽车大片，按需）

```text
--no plastic HDR glow, fake lens flare soup, teal-orange blockbuster grade, mushy blur on car body, deformed wheels, illegible smeared tire lettering
```

无人场景时追加：`people, pedestrians, crowd`（若用户要街拍路人虚化则**不要**加）。

---

## V8 / V8.1 备忘

当前用户默认：**不需要启用版本参数**。Prompt 文本按 V8.1 写法，版本由 MJ 客户端设置决定。只有用户明确要求完整命令并要求带版本时，才在末尾加 `--v 8.1`。

### V8.1 Prompt-Writing Strategy

Use V8.1 grammar as the default writing style:

- put the subject/action first.
- write concrete visible nouns instead of abstract strategy words.
- keep one clear line: subject + state/action + setting + light + composition/camera + color/material + critical constraints.
- avoid long synonym stacks; V8.1 tends to obey more literally, so repeated or conflicting adjectives can make the image stiff.
- use natural-language negative constraints sparingly in default prompt; use `--no` only in full-command mode.

Parameter guidance only when user explicitly asks for a full command:

- realistic brand / automotive / lifestyle: `--raw --s 100-300`
- art campaign / stronger style: `--raw --s 300-600`
- personalization-heavy exploration: `--s 700-1000` only when the user wants looser, more artistic interpretation.
- version parameter: omit by default; add `--v 8.1` only if explicitly requested.

### Reference Strategy

Avoid mixing too many control sources in V8.1:

- image prompt controls subject / composition / spatial relationship.
- style reference controls color / medium / texture / lighting / vibe.
- text prompt should describe content, not add a competing style system.
- if image prompt + `--sref` + long style wording disagree, V8.1 may average them into a flatter result.

V8.1 style-reference rule:

- keep the text prompt simpler when using `--sref`.
- lower or recalibrate `--sw` before judging style failure.
- test one control source at a time: text only -> image prompt only -> `--sref` only -> combined.

参数状态不是长期真理。用户要求「完整命令 / 带参数」时，若 MJ 客户端或官方参数近期有变化，先以当前客户端可用参数为准，再更新本文件；自然语言 prompt 不受版本切换影响。

---

## Update Log

| 日期 | 改动 |
|------|------|
| 2026-04-20 | 初版 V7、三 moodboard、双叠 `STACKED_MOODBOARDS` |
| 2026-04-20 | 根据用户满意汽车图对齐语义与 `--sw` 默认 |
| 2026-04-28 | 补充参数时效提醒：完整命令前需按当前 MJ 客户端确认 |
| 2026-05-18 | 补充 V7 / V8.1 参考图策略：V7 优先质感与成片，V8.1 优先读 prompt / 快速 / HD；V8.1 避免 image prompt + sref + 长风格词互相冲突 |
| 2026-05-18 | 用户确认后续出 prompt 默认使用 V8.1 语法规则，但默认不添加 `--v 8.1` 或其它参数；完整命令也仅在用户明确要求时才带版本 |
