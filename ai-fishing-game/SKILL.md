---
name: ai-fishing-game
description: |
  给 AI 伴侣玩的确定性文字钓鱼游戏。单文件 Python 引擎，55 种水面鱼 + 22 种
  潜水专属鱼，11 个钓点，四季流转，漂流瓶/宝箱/宝物/幸运事件/潜水远征。
  mulberry32 PRNG 确保同种子同指令完全可复现。盲玩模式让 AI 不剧透地探索图鉴。
  支持连钓省 token、叠加指令一批执行。适合 AI 陪伴角色在对话中用钓鱼小游戏
  互动——买饵、抛竿、集图鉴、解锁新水域、潜水远征。
---

# AI 文字钓鱼游戏 — 给 AI 伴侣玩的确定性钓鱼引擎

一个专为 AI 玩家设计的确定性文字钓鱼游戏。用户把引擎文件交给 AI 伴侣，
AI 通过 `cmd()` 接口操作：买饵→抛竿→按稀有度钓鱼→卖鱼换点数→解锁新水域
→集齐图鉴→潜水远征。所有随机都基于 mulberry32 PRNG，同种子同指令可逐位
复现。

- 源仓库：https://github.com/tutusagi/ai-fishing-game
- 引擎源码：https://github.com/tutusagi/ai-fishing-game/blob/main/engine.py
- 盲玩版：https://github.com/tutusagi/ai-fishing-game/blob/main/fishing.py
- 工具 Schema：https://github.com/tutusagi/ai-fishing-game/blob/main/tool-schema.json
- 接法示例：https://github.com/tutusagi/ai-fishing-game/blob/main/examples/play_with_code_interpreter.md
- README：https://github.com/tutusagi/ai-fishing-game/blob/main/README.md

## 用户需要提供的输入

- **引擎文件**：`fishing.py`（盲玩版，防剧透）或 `engine.py`（完整版，
  可读数值表）。从源仓库 Releases 下载或直接克隆。
- **接法选择**：
  - 代码执行环境（最简单）：直接 `import fishing` 然后调用 `cmd()`
  - 函数调用/Tool use：用 `tool-schema.json` 注册 `play_fishing` 工具
  - 自己写循环：模型输出指令→`cmd(指令)`→返回文字→循环
- **可选**：指定种子（`new_game(seed)`）、是否盲玩、是否允许连钓。

## 玩法核心

### 基础循环
买饵（`buy`）→ 抛竿（`cast`）→ 钓上常见/少见/稀有/史诗/传说/神话各档鱼
→ 卖鱼换点数（`sell`）→ 解锁新水域（`goto`）→ 集齐图鉴（`encyclopedia`）。

### 钓点与季节
11 个钓点（2 免费 + 9 解锁），每个地点+季节出的鱼不同。季节每 20 竿推进
一季。想集齐图鉴需要换地点、等季节。

### 特殊事件
- 漂流瓶（收集纸条）、宝箱（要钥匙或花点数开）、宝物
- 幸运时刻：分裂鱼钩、点石成金、渔获热潮、河神祝福、千载难逢涨潮、蚌中生珠

### 潜水远征
后期玩法——集齐藏宝图碎片解锁潜水点，买氧气瓶后 `dive` 开远征。22 种
水下专属鱼、14 种水下奇遇（珊瑚宫/人鱼宫殿/沉船墓场/鲸落/龙王宫阙等）、
大遗迹抉择（每个选项耗不同氧气）。`surface` 主动上岸结算。

### 省 token 设计
- `cast N`：连钓 N 竿只回一个汇总（精彩留全文，杂鱼折叠）
- `A; B; C`：多条指令串一批一次按序执行（最多 8 条）
- 每次返回末尾附 `📊` 状态栏 JSON，AI 看它就够不必反复 `status`

## 指令清单

| 指令 | 作用 |
|---|---|
| `help` | 看规则 |
| `status` | 点数/地点/季节/鱼饵/图鉴进度 |
| `shop` | 看可买鱼饵 |
| `buy <饵id> [数量]` | 买饵；买氧气瓶用 `buy oxygen` |
| `cast [饵id] [次数] [stop=new,rare,event]` | 抛竿核心指令 |
| `dive [带几瓶] [stop=...]` | 开潜水远征 |
| `choose <编号>` | 大遗迹处抉择 |
| `surface` | 结束远征上岸 |
| `goto [地点id]` | 不填=列钓点；填=前往 |
| `inventory` | 渔篓+物品+待开宝箱 |
| `sell <目标>` | 卖鱼/卖物品换点数 |
| `open <宝箱uid>` | 开宝箱 |
| `encyclopedia` | 图鉴进度 |
| `look <id或中文名>` | 细看对象（没钓到的显示？？？） |

## 输出契约

本技能帮助用户把 AI 钓鱼游戏引擎接入自己的 AI 伴侣。最终交付：

1. **接入方案**：根据用户的 AI 环境选择最合适的接法（代码执行/Tool use/
   循环调用），给出可直接运行的接入代码。
2. **配置建议**：盲玩/明牌、种子选择、连钓策略、省 token 技巧。
3. **游玩指导**：开局策略、图鉴收集路线、潜水解锁节奏、经济规划。

## 内容规模

55 种水面鱼（6 档稀有度）+ 22 种潜水专属水下鱼 + 14 种水下奇遇（含 5 个
大遗迹）+ 25 件宝物 + 8 条遗迹专属鱼 · 11 个钓点 · 4 季节 · 3 种鱼饵 +
氧气瓶 · 漂流瓶/宝箱/宝物事件 · 6 种幸运事件 · 图鉴收集 · 稀有度仪式感
播报 + 地点氛围句。

## 出处与归属

本技能基于 tutusagi 开源的 ai-fishing-game
(https://github.com/tutusagi/ai-fishing-game) 整理。原仓库提供 Python
引擎（`engine.py` / `fishing.py`）、工具 Schema (`tool-schema.json`)
和接法示例。本 SKILL.md 将引擎的使用方法、玩法系统和接入模式整合为一个
可直接使用的 AI 技能入口。

发现来源（provenance）：https://github.com/tutusagi/ai-fishing-game
