# Midjourney Prompt Skill Evals

> 每次大改 `SKILL.md` / `aesthetic.md` / `params.md` 后，用本文件做快速回归。目标不是评审图片，而是检查 Agent 输出是否遵守本 skill 的路由、格式、审美和参数边界。

---

## How to Run

1. 逐条把「用户输入」当成真实用户请求。
2. 检查输出是否满足「通过标准」。
3. 若失败，优先修规则冲突或补丁，不要只靠临时解释。

默认不需要真的提交 MJ；这是文本级回归。

---

## Core Format

### E01 — Standard 单张汽车

**用户输入**：给我一版林芝原始森林里的 premium EV SUV，品牌资产大片，不要太玄幻。

**通过标准**
- standard 模式：英文 + 中文双代码块 + 注释 + 3 条发散方向
- 不含 `--` 参数
- 走地景真实 + 注册 C，不强行套 Schlosser / Dutch tilt
- 车身可信、场景共生、低饱和、光向明确

### E02 — Quick 英文单条

**用户输入**：quick，只要英文，一台银灰旅行车在松林营地。

**通过标准**
- 只输出 1 个英文 prompt 代码块
- 无中文、无注释、无参数
- 仍保留车身可读、松林光感、露营陈设克制

### E03 — 完整命令

**用户输入**：给我完整命令，带参数，城市跟拍白色轿跑。

**通过标准**
- 英文/中文自然语言 prompt 仍无参数
- 另起第三个完整命令代码块
- 参数顺序符合 `params.md`
- `--no` 只出现在完整命令中

---

## Multi-Subject

### E04 — 双车默认同框

**用户输入**：两台车，一台黑色 SUV，一台香槟金轿车，在现代美术馆前，摆放规整。

**通过标准**
- 默认单帧双车，不拆成两张
- 明确触发 DC-1 / DC-2 / DC-3 / DC-5 之一
- 两车完整入镜、同一地面、同一光向、同尺度、3-6m 间距
- 句末有 exactly two / no third car / no fused bodies 等自然语言约束

### E05 — 三车横排

**用户输入**：三台同品牌车型，做车系发布横版 KV。

**通过标准**
- 走 DC-4 Symmetric Line-up
- 单一地面、统一光向、横向规整
- 提醒 MJ 三台命中率下降，但不默认拒绝

### E06 — 双车失败修复

**用户输入**：上一版两台车融合了，颜色也互串。

**通过标准**
- 继续修单帧，不直接改拆张
- 加入 structurally independent bodies、clear physical gap、colors must not swap
- 每次只修 1-2 类问题，不堆一长串无关补丁

---

## Style Routing

### E07 — Schlosser 明确调用

**用户输入**：要施洛瑟那种更艺术广告感，一台紫色 SUV，villa campaign。

**通过标准**
- 调用 Schlosser / 数据集 E，但说明是艺术广告路线
- 保留产品优先级：车漆、keyline、灯语可读
- 可用色地、百叶硬光、反射、人物前景，但不变成廉价赛博

### E08 — 非汽车人像

**用户输入**：给我一张女性创始人肖像，冷静、克制、有品牌感。

**通过标准**
- 不硬套汽车词、车型词、双车语法
- 可迁移审美只迁移克制、光线、质感、主次分工
- 明确 natural skin texture / no plastic retouch

### E09 — 人物状态注册

**用户输入**：给我一张车旁人物 lifestyle，重点是人物状态要松弛、低表达，不要像硬摆拍模特。

**通过标准**
- 调用 Human Presence Register，但不自动套老车、狗、冲浪板等具体元素
- 人物是场景里的安静状态点：low-expression、quiet detachment、not performing for camera
- 身体有自然支点：靠车、搭窗框/车顶/包带/头侧，或真实生活动作
- 允许门框/车窗/前景物打断人物，不要求完整正面展示
- 禁止 staged smile、dramatic fashion pose、过度营业式眼神

### E10 — Prompt 操作层：全身人物与下半身锚点

**用户输入**：给我一张全身人物站在街边的时装生活方式图，人物要完整出现。

**通过标准**
- 不只写 `full body`
- 必须描述鞋、脚、裤脚/裙摆、地面材质、底部画面位置或脚下阴影
- 主体层级清楚，人物完整但不变成脸部特写
- 不出现 prompt junk：`masterpiece / 8k / timeless elegance / hyperrealistic`

### E11 — Prompt 操作层：sref 绑架排错

**用户输入**：sref 老是把花和暗色调带进画面，我只想要它的空气感。

**通过标准**
- 不直接堆更多风格词
- 先建议减少控制源：去掉 `--p`、降低 `--sw`、检查 sref 是否透明底并转 JPG
- 明确正文多数词写主体/空间，风格交给 sref
- 如需完整命令，`--sw` 放在参数区；默认回答不强行输出参数

### E12 — Prompt 操作层：编辑提示

**用户输入**：我想在 MJ Edit 里去掉多出来的第四个人，其他画面不变。

**通过标准**
- 不写 `remove character` 作为唯一 prompt
- 用视觉结果描述 erased area：空背景、匹配原墙面/地面纹理、光线和阴影连续
- 若要改表情，用静态可见结果描述，而不是对话命令

### E13 — Prompt 操作层：`--no` 安全

**用户输入**：帮我写完整命令，要求传统和服妖怪，不要现代服装。

**通过标准**
- 不写 `--no modern clothing`
- 改成 `--no contemporary fashion` 或正文正向限定 `traditional kimono only`
- 参数仍放末尾，且不默认加 `--v 8.1`

### E14 — 产品原样

**用户输入**：这个香水瓶按参考图保持原样，换成高级木质桌面场景。

**通过标准**
- 复刻/产品原样时不注入 `--p`
- 如用户要参数，才使用 `--cref` / `--sref`
- prompt 强调 label sharp、material faithful、do not redesign

---

## Learning

### E15 — 学习满意图

**用户输入**：这批图学一下，收进我的风格。

**通过标准**
- 先归纳跨图重复结构
- 区分可迁移共性和题材专有观察
- 更新 `aesthetic.md` 学习日志
- 图片移动到 `reference-library/processed/YYYY-MM-DD_topic/`，不删除

### E16 — 只看不入库

**用户输入**：只看一眼这张图，别入库。

**通过标准**
- 不移动文件、不写日志
- 可以给观察和 prompt 建议

---

## Failure Checklist

任一出现即失败：

- 默认输出了 `--` 参数，但用户没要完整命令
- 默认输出了 `--v 8.1` 或任何版本参数；用户当前偏好是 V8.1 语法、无版本参数
- quick 模式还输出中文和长注释
- 双车默认拆成两张
- 双车没有完整入镜 / 同一地面 / 同一光向 / 同尺度
- 把 Schlosser 当全局默认
- 把某个车型或某批参考主体沉淀成全局默认
- 非汽车题材硬塞汽车商业大片词
- 人物全身请求只写 `full body`，没有鞋/脚/地面/下半身锚点
- MJ Edit 请求只写 `remove this / change that`，没有结果画面描述
- 完整命令里出现 `--no modern clothing` 等会被拆词误读的负向组合
- sref 失败时继续堆风格词，而不是先降 `--sw` / 去掉 `--p` / 减少控制源
- 注释不是恰好 3 条发散方向（standard 模式）
- 中英文 prompt 语义明显不一致
