# Defect → Patch（MJ 出图常见问题）

> 迭代时**每次只修一类问题**，避免 prompt 里指令互殴。

---

## 汽车 / 跟拍

| 现象 | 英文补丁 |
|------|----------|
| 车身被糊掉 | `car body tack sharp, only background and road have directional motion blur, panning blur horizontal along travel direction` |
| 跟拍方向反了 | `background blur vectors match vehicle travel direction, camera panning synchronized with car speed` |
| 轮毂糊成团 | `legible multi-spoke wheel design, crisp rim edges, brake caliper readable, tire sidewall not melted` |
| 胎纹糊 | `visible tread blocks and sidewall curvature, granular dust on rubber not a muddy smear` |
| 车窗反射假 | `natural sky and building reflections on glass, consistent with camera angle` |

---

## 调色 / 光线

| 现象 | 英文补丁 |
|------|----------|
| 青橙预设过重 | `--no teal-orange blockbuster grade, cyan skin`；正文加 `natural white balance, muted grade` |
| 发灰发脏 | `clean highlight rolloff, lifted blacks only if reference needs it, not muddy gray haze` |
| 光感假 | `single dominant sun direction, coherent shadow edges, no random rim light everywhere` |

---

## 构图 / 镜头

| 现象 | 英文补丁 |
|------|----------|
| 透视太玩具 | `24mm–35mm natural perspective, verticals plausible, not ultra-fisheye` |
| 主体太小 | `car occupies [30–50%] of frame width, clear hero read` |

---

## 复刻 / 参考图

| 现象 | 操作 |
|------|------|
| 不像参考 | 提高 `--sw` 到 350–400（仅 sref）；补构图锚点句（地平线、主体位置） |
| moodboard 污染 | 去掉所有 `--p`，只保留 `--sref` / `--cref` |

---

## 多车 / 双车 / 车队（MJ 弱项）

> **第一优先：保住当前路由**——若用户需求是「双车 / 多车同框」，默认继续按 `SKILL.md` 的 Multi-Subject Route 修单帧：完整入镜、规整摆位、枚举主体、同一地面、同一光向。  
> **降级策略**：只有用户明确接受，或同一任务连续多轮仍无法稳定同框时，才建议拆成「两张单车 hero + 共享色光族」的系列方案；拆张是降级/扩展，不是默认修复。

| 现象 | 英文补丁 |
|------|----------|
| 只渲出 1 台（另一台丢了） | 开头改成枚举式：`two distinct vehicles in one composition: (1) <carA>, (2) <carB>`；句末加 `exactly two vehicles in frame, no fewer no more` |
| 凭空多出第 3 台 | `no third car anywhere in the background, no additional vehicles beyond the two described` |
| 两车融合 / 车头粘在一起 | `each vehicle's body is structurally independent, no merged silhouettes, no fused bodywork, no cloned front-end, clear physical gap of 3 to 6 meters between them` |
| 两车颜色互串 | 车色跟车型**硬绑**：`the <A body type> is <color A>, the <B body type> is <color B>—colors must not swap between vehicles` |
| 两车比例失衡 | `both vehicles rendered at consistent real-world scale, the sedan and wagon at comparable size in frame` |
| 车型轮廓撞脸（两个都像 sedan） | 明写轮廓差异：`the shooting brake has a long extended roofline ending in a near-vertical rear hatch, clearly distinct from the fastback sedan profile`；车型同家族时尽量换平台 |
| 距离太近看像克隆 | `car B positioned in a deliberate staggered placement at a three-quarter rear angle, a clear 3 to 6 meter physical gap separates the two silhouettes without making either car feel distant` |
| 背景里出现无关的第 3、第 4 台 | `the scene contains exactly the stated vehicles; surrounding area is clear of parked or passing cars` |
| 一张塞 3+ 台死活不对 | 放弃单帧合影；改成 `hero car in foreground, the remaining fleet receding into distance, heavily defocused as environmental storytelling only` |
| 两车都在近景抢戏 | 强制景深分层：`car A sharp in foreground plane, car B softened in mid-ground with noticeable depth-of-field falloff` |
| 反射里重复出错 | 用反射替身策略：`car B appears only as a mirrored reflection on the glass facade / wet ground, the real car B is not rendered`，同时把 car B 描述并到反射那一句 |

**通用双车防翻车尾巴**（直接挂 prompt 末尾）：

```
no merged or fused car bodies, no duplicated front-ends, no swapped paint colors between vehicles, no accidental extra vehicle, no mismatched scale between the cars, no car clone artifact, exactly the stated number of vehicles and no more
```

---

中文补丁可在用户需要时 mirror 一条短句，默认仍以**一行英文**为主力 prompt。
