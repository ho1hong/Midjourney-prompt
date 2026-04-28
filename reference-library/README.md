# Reference Library Workflow

本目录用于长期投喂和归档用户满意图。目标是让 skill 持续进化，而不是把单批图误写成永久默认风格。

## Directory

```text
reference-library/
├── inbox/        # 新素材，未学习
└── processed/    # 已学习，按日期和主题归档
```

## Batch Manifest

每个 processed 批次建议带一个 `manifest.yaml`，方便以后快速调用，不必重新读完整图片组。

```yaml
date: "2026-04-28"
topic: "short-topic-name"
source: "user upload / url / local folder"
image_count: 12
status: "processed"

learned_patterns:
  transferable:
    - "跨题材可迁移的结构，例如克制低饱和、单一光向、主体锐利"
  topic_specific:
    - "仅适用于当前题材的主体、车型、道具或场景"

usable_keywords:
  - "photorealistic industrial finish"
  - "single dominant light source"

avoid_keywords:
  - "teal-orange blockbuster grade"
  - "plastic CG finish"

best_routes:
  - "Template 9 Automotive"
  - "Multi-Subject Route / DC-1"

notes: "1-2 句说明这批图为什么对味，以及何时不该调用。"
```

## Learning Rule

- 先写共性，再写题材观察。
- 不把单批主体变成默认主体。
- 不把某位摄影师变成全局默认画风。
- 每次学习后在 `aesthetic.md` 的学习日志补一行。
