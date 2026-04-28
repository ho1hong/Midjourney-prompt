# Midjourney Parameters Config

> **参数层**：MJ 升级时优先只改本文件。`SKILL.md` 中的 `{{VARIABLE}}` 由此处替换。  
> **默认不向用户粘贴 `--` 参数**（用户在客户端自选）；仅当用户**明确要完整命令**时，由 Agent 按本文件拼接。

**Last updated: 2026-04-28**

---

## Current Version Policy

| 模型 | 状态 | 何时使用 |
|------|------|----------|
| `--v 7` | **默认** | 写实 / 汽车 / 人像 / 产品 / 复刻 |
| `--v 8` / `--v 8.1` | 可选 | 用户明确要 v8、更强文字、或原生 2K / `--hd` |
| `--niji 6` | 场景专用 | 二次元 / 动漫 / 插画 |

### `{{VERSION}}`

```text
{{VERSION}} = --v 7
```

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

按模板覆盖见 SKILL；汽车商业大片默认 **`--style raw --s 80–120`**（写实商业感）。

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
{{QUALITY_TAIL}} = --style raw --s {{DEFAULT_STYLIZE}}
```

---

## Parameter Order（严格）

```text
--p ... --p ... --sref ... --sw ...
--cref ... --cw ...
--ar ... --style raw --s ...
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

启用时：改 `{{VERSION}}`，阅读 SKILL 中 V8 小节；`--p` / moodboard 一般继续有效。

参数状态不是长期真理。用户要求「完整命令 / 带参数」时，若 MJ 客户端或官方参数近期有变化，先以当前客户端可用参数为准，再更新本文件；自然语言 prompt 不受版本切换影响。

---

## Update Log

| 日期 | 改动 |
|------|------|
| 2026-04-20 | 初版 V7、三 moodboard、双叠 `STACKED_MOODBOARDS` |
| 2026-04-20 | 根据用户满意汽车图对齐语义与 `--sw` 默认 |
| 2026-04-28 | 补充参数时效提醒：完整命令前需按当前 MJ 客户端确认 |
