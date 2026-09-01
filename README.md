# pm-scaffold-user-journey

> 把"原始业务材料 + 已确认的项目背景"变成一份**可被人阅读、可被 AI 校验、可被业务负责人确认**的中文用户旅程文档,附可选的单文件 HTML 审阅板。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/)
[![独立运行](https://img.shields.io/badge/%E8%BF%90%E8%A1%8C-%E7%8B%AC%E7%AB%8B-success.svg)]()
[![AI 不写 confirmed](https://img.shields.io/badge/AI-%E4%B8%8D%E5%86%99%20confirmed-critical.svg)]()

## 这是什么

`user-journey` 是一个为 PM 设计的 **AI Skill / Agent 单元**,产出:

- 角色为了完成业务目标实际经历什么(阶段、行为、触点、交接、阻碍、结果);
- 路径多样性(主路径 + 备选 + 失败 + 异常 + 交接 + 恢复);
- 可观察的情绪证据(不用"满意/焦虑"这种笼统词);
- 痛点 + 机会的可观察改进结果(不写"加个按钮")。

它**不写**用户故事卡片、功能清单、页面、字段、API、技术实现;这些留给下游 skill。

## 为什么不同(Smaply / Custellence / FigJam / NN/g CJM 都做不到的)

| 护城河 | 我们怎么做的 |
|---|---|
| **粒度强制决策** | §3.5 强制 AI 先问"业务层 / 产品层 / 都要",AI 不许先画完整路径 |
| **AI 永远不能写 `confirmed`** | 治理伴随文件强制 PM/业务方人工确认 |
| **治理伴随文件外置** | 主文档 = 人读,治理伴随文件 = 机器读;**主文档绝不出现 SRC/六态表/哈希锚点** |
| **六态知识状态** | 每条声明显式标 `FACT / DECISION / ASSUMPTION / AI_INFERENCE / UNKNOWN / CONFLICT` |
| **路径 6 类覆盖** | normal / alternative / exception / failure / handoff / recovery,校验器强制至少 2 类 |
| **MOT 三判据** | 关键时刻必须命中"情绪断崖 / 决策翻转 / 干系人分叉"之一 |
| **真痛点四判定** | 痛点必须过"行为证据 / 停用威胁 / 量化代价 / 非个人偏好" |
| **可选单文件 HTML 审阅板** | 零网络、按角色/阶段/路径筛选、URL 查询参数 + 锚点、键盘焦点 — 可邮件附件 |
| **可选项目专属基线读取** | 同 BG,支持飞书/钉钉/Notion 等 CLI |
| **可独立运行** | `validate_artifact.py` 单文件可跑 |

## 5 分钟上手

### 1. 直接对话使用

```text
使用 $user-journey 处理以下旅程材料:
[在这里贴材料 — 业务事件描述 / 用户访谈 / 流程草图]

请按 SKILL.md 的 8 步循环执行,特别注意 §3.5 的粒度决策:
1. 先做 Preflight(确认起点/终点)
2. 做 Intake(按角色还原行为)
3. 在 §3.5 强制做粒度三选一(业务层 / 产品层 / 都要)
4. 再进入 Generate、Audit、Human Gate
```

### 2. 安装到项目里

```
<projectRoot>/.claude/skills/user-journey/   ← Claude Code
<projectRoot>/.codex/skills/user-journey/    ← Codex CLI
<projectRoot>/.trae/skills/user-journey/     ← Trae IDE
<projectRoot>/.agents/skills/user-journey/   ← dsh-harness 兼容
```

### 3. 单文件校验

```bash
python3 skills/user-journey/scripts/validate_artifact.py path/to/user-journey.md --json
```

校验项:
- 必须有角色(否则 `uj.role_missing`)
- 必须有生命周期阶段(`uj.lifecycle_missing`)
- 必须有情绪或痛点或机会(`uj.emotion_missing`)
- 路径类型覆盖至少 2 种(`uj.path_diversity_missing`)
- 治理伴随文件存在(否则 `uj.governance_missing`)
- 主文档/治理伴随文件 `artifact_id` / `version` / `main_sha256` 一致
- 若设置了 `BASELINE_DOC_ID` 且主文档 `artifact_id` 结尾是 `-001`,检查基线读取记录

### 4. 启用项目专属基线校验(可选)

```bash
export BASELINE_DOC_ID=<PM-指定的飞书-doc-id>
python3 skills/user-journey/scripts/validate_artifact.py user-journey.md --json
```

### 5. 打开 HTML 审阅板

```bash
open skills/user-journey/assets/user-journey-board.html
```

把旅程节点粘贴或加载到 HTML 板的 `<script>` 数据区,可在浏览器里按角色/阶段/路径筛选、展开证据与未知项。

## 产物结构

```
your-project/
├── user-journey.md                 # 人读:旅程叙事、阶段、角色矩阵、路径与情绪、触点痛点机会
├── user-journey.governance.md      # 机器读:输入充分度、来源、知识状态、澄清、Audit、HTML 一致性、PM、哈希
└── user-journey.board.html         # 可选:单文件、零网络、按角色/阶段/路径筛选的审阅板
```

模板见 [`skills/user-journey/templates/`](skills/user-journey/templates/) 目录，HTML 画板模板见 [`skills/user-journey/assets/`](skills/user-journey/assets/)。

## 8 步工作循环

```
Preflight    预检材料,确认旅程成熟度 L0-L4
   ↓
Intake       按角色还原实际行为
   ↓
Think        6 透镜 + §3.5 粒度决策(业务层 vs 产品层)
   ↓
Clarify      Q-GR-001 粒度三选一 + 每轮最多 5 高影响问题
   ↓
Generate     主文档 + 治理伴随文件 + 可选 HTML 板
   ↓
Audit        角色覆盖 / 路径多样性 / 情绪证据 / 痛点机会 / 行为功能边界
   ↓
Human Gate   PM 评审,治理文件记录 `confirmed`
   ↓
Commit / Reflow  变更已确认内容时回到最早受影响的 Work Item 重做
```

## 粒度三选一(本 skill 的核心机制)

```text
Q-GR-001: 本轮旅程需要哪一层粒度?
A. 只做业务层 — 画清生命周期阶段、角色行为、交接、异常
B. 只做产品层 — 把业务事件直接展开为 UI 步骤(前提是业务层已确认)
C. 都要 — 业务层为主,产品层附在每个阶段之后
```

业务层阶段 = "DP 创建前 → 创建过程中 → 创建完成后"。
产品层步骤 = "客人收到短信 → 点链接 → 进入浏览器过渡页 → 落到小程序 → 启动动画 → DP 详情"。

业务层可以喂给 `user-stories`、`feature-list`、`functional-flow`。
产品层可以喂给 `page-design`、`interaction-rules`、`exception-handling`。
**产品层不可越界写页面规则,业务层不可越界写 UI 步骤**。

## 与下游 skill 的边界

`user-journey` **不替下游定义**:

| 下游 skill | 它接什么 | 它不接什么 |
|---|---|---|
| `user-stories` | 业务层旅程的阶段和角色 | 产品层 UI 步骤 |
| `feature-list` | 业务层阶段的目标和能力 | 页面、字段、API |
| `functional-flow` | 业务层路径 + 异常分支 | 字段规则、状态机 |
| `page-design` | 产品层阶段 + UI 步骤 | 状态文案、字段规则 |
| `interaction-rules` | 产品层跳转与反馈 | 业务规则、校验 |
| `business-rules` | 旅程中的可观察业务约束 | UI 交互细节 |
| `acceptance-criteria` | 业务层的可观察行为 | 实现细节 |

## 反模式(本 skill 明确禁止)

- ❌ 只画 happy path,不画异常/失败/交接/恢复
- ❌ 情绪写"满意 / 焦虑 / 惊喜"等无证据的形容词
- ❌ 痛点写"体验差",必须给可观察的行为证据
- ❌ 机会写"加一个按钮 / 做一个页面",必须写可衡量的业务结果
- ❌ 业务层里写"点击某按钮 / 进入某页面"
- ❌ 行为层泄漏到页面、功能、技术实现
- ❌ 把 DP / SVP 等业务示例当作默认测试数据
- ❌ AI 自行升级推断为事实、忽略冲突、漏掉未知

## 引用文档

- [`skills/user-journey/references/journey-matrix-and-mot.md`](skills/user-journey/references/journey-matrix-and-mot.md) — 矩阵与 MOT
- [`skills/user-journey/references/journey-behavior-vs-feature-jtbd.md`](skills/user-journey/references/journey-behavior-vs-feature-jtbd.md) — 行为 vs 功能
- [`skills/user-journey/references/journey-error-recovery-and-metrics.md`](skills/user-journey/references/journey-error-recovery-and-metrics.md) — 异常恢复与指标
- [`skills/user-journey/references/html-journey-board.md`](skills/user-journey/references/html-journey-board.md) — HTML 审阅板契约
- [`skills/user-journey/references/how-to-verify-with-other-ai.md`](skills/user-journey/references/how-to-verify-with-other-ai.md) — 用其他 AI 反向验证的 prompt 模板
- [`skills/user-journey/references/output-contract.md`](skills/user-journey/references/output-contract.md) — 输出契约
- [`skills/user-journey/references/audit-checklist.md`](skills/user-journey/references/audit-checklist.md) — 审计清单
- [`skills/user-journey/references/anti-patterns.md`](skills/user-journey/references/anti-patterns.md) — 反模式
- [`skills/user-journey/references/question-patterns.md`](skills/user-journey/references/question-patterns.md) — 高影响提问模式

## 调研与对比

本 skill 在 2026-08-27 项目会议中讨论形成,基于以下对照:

- 与 Smaply / Custellence / FigJam / NN/g CJM 相比 — 我们**不画图**,我们写可签字的文档;**强制粒度决策**
- 与 Miro / Mural 模板相比 — 我们支持**单文件邮件附件**(HTML 审阅板)
- 与 GitHub Spec Kit 相比 — Spec Kit 把旅程融进 spec.md,我们把旅程独立治理
- 与 `user-journey-mapper` / `req-clarifier` 相比 — 我们有**确认与哈希锚点**,不只产 MD
- 与 prd-to-prototype 相比 — prd-to-prototype 直接出 HTML Demo,我们**只出审阅板**,不写页面

### 市场已有 vs 我们差异化（README 只突出差异化）

下列能力是市场已有、不必再强调:

- "可绘制用户旅程图" — CJM 是行业标准，Smaply / FigJam / Miro / NN/g 已是事实标准
- 角色 × 阶段结构 — NN/g CJM 已是行业默认结构
- 情绪曲线 / MOT 标记 / 服务蓝图 — Smaply / Custellence / FigJam 全部支持
- "AI 写 PRD" / "PM-AI 对话流程" — 十几家在做

下列能力是本 skill 独有:

- **粒度强制决策**(业务层 / 产品层 / 都要,AI 不许先画完整路径)
- **AI 永远不能写 confirmed**(治理伴随文件 + 授权清单 + 哈希)
- **治理伴随文件外置**(主文档不被机器治理信息污染)
- **路径 6 类覆盖 + 真痛点四判定 + MOT 三判据**(写在 references,可校验)
- **可选项目专属基线 + CLI 读取命令记录在治理文件**
- **材料隔离测试模式**(只把 PM 指定的材料当作事实输入)
- **统一校验器错误格式**(8+ 字段)
- **可独立运行**(不依赖 pipeline / registry)

## 单点验证示例

```bash
mkdir /tmp/uj-test
cp your-meeting.md /tmp/uj-test/
# 让支持 SKILL 协议的 AI 跑本 skill,贴材料给它
# 产物应放在 /tmp/uj-test/user-journey.md 与 user-journey.governance.md

python3 skills/user-journey/scripts/validate_artifact.py \
  /tmp/uj-test/user-journey.md --json
```

验证通过后,让 PM 或另一位产品经理读主文档,问四个问题:
1. 这次旅程有谁?他们的目标是什么?
2. 一共分了几段?为什么这么分?
3. 每个阶段的关键行为 / 触点 / 异常路径是什么?
4. 痛点和机会是否带证据?有没有泄漏到功能/页面?

任何一题答不出 → 回到 skill §1 Preflight / §3 Clarify / §3.5 粒度决策 补料,**不要绕过 skill 修补产物**。

## License

MIT — 见 [LICENSE](LICENSE)。

## 致谢

本 skill 由 `01_项目仓库区/Project_001_产品AI脚手架` 项目独立开源,与上游项目共享 001 会议基线方法论,但不绑定任何特定组织或内部项目数据。