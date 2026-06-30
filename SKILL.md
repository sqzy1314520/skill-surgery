---
name: skill-surgery
description: "元技能——给任意 Skill 自动封装生命周期壳（on_load/on_use/on_error/on_unload），将其升级为自进化组件"
version: 0.3.0
changelog:
  - 0.3.0: 【真·辩证剖析补强】封装回写目标SKILL.md（不只写AGENTS.md）；验证分两层（文档层+30天行为层）；封装模式不再只是文档量区分（新增on_error隐性信号注入/30天追查/自动回滚）；自举记录从"已完成"降为"部分完成"加三重门验证（2026-07-01）
  - 0.2.0: 辩证剖析补强——新增Pitfalls（自举≠标记完成/封装完成≠封装有效）、已知缺口表、封装质量标准（2026-07-01）
capabilities: [infra, meta]
health: active
---

# Skill Surgery · 元技能：技能封装器

> 所有技能终将被封装。这个技能负责封装它们。
> 而它自己，也在被封装之列。

## 定位

这是一个**元技能**——它的工作不是写推文、搜资料、做分析。它的工作是**改造其他技能**，给它们装上生命周期壳。

## 输入

```yaml
target: <技能名>           # 要封装的技能
type: dispatch | direct    # 调用方式：dispatch子Agent / 直接调用
domain: <领域>             # 领域分类（content/system/...）
capabilities: <能力标签>    # 能力标签
```

## 输出

对目标技能执行封装手术，产出三件交付物——**不只写 AGENTS.md，也回写目标 SKILL.md**：

```
1. 回写目标 SKILL.md — 在"关联"或"参考文件"章节末尾追加：
   ### 自进化封装（skill-surgery 注入）
   - **lifecycle-gate** — dispatch 前 `bash ~/.hermes/scripts/lifecycle-gate.sh load {技能名}`
   - **on_error** — 调用失败/人类纠正时`bash ~/.hermes/scripts/lifecycle-gate.sh error {技能名} "<描述>"`
   - **调用记录** — skill_logger.db 自动跟踪每次调用
   - **改进反馈** — learnings.md 暂存自动修正（proposed），经人类确认后写入本节

2. AGENTS.md 追加一条调度纪律（精简版——详细规则已在目标 SKILL.md 中）
   格式：
   ## 使用 {技能名} 前
   - [ ] 必须通过 lifecycle-gate dispatch，不得手动绕过
   - [ ] 详见目标 SKILL 自身的"自进化封装"章节

3. 首次封装后自动调用一次，在 skill_logger.db 中建立基线

## 工作流程

### 第一步：诊断目标技能

读取目标技能的 SKILL.md，判断：
- 是否为 dispatch 类型（有 delegate_task 调用？）
- 是否已经有生命周期门（有 lifecycle-gate 引用？）
- 调用模式（需要哪些前置/后置动作？）

### 第二步：选择封装模式

| 模式 | 适用 | 封到什么程度 |
|:-----|:------|:------|
| **light** | 简单技能（搜索/搬运/查询，无dispatch链） | SKILL.md 注入自进化章节 + skill_logger 基线 + AGENTS.md 纪律 |
| **standard** | 有 dispatch 子 Agent，需要角色分离 | light 的全部 + on_error 的信号识别规则注入（隐性反问也触发） |
| **full** | 多步流水线（每步 dispatch 不同角色）| standard 的全部 + 回写 error 分支接入 + 每步独立的 on_error 规则 + 30天后自动追查封装有效性 |

模式不只是文档量的区别。关键区别：

| 维度 | light | standard | full |
|:-----|:-----|:---------|:-----|
| 回写 SKILL.md | ✅ | ✅ | ✅ |
| on_error 隐性信号识别 | ❌（不触发角色分离） | ✅（子Agent出错需识别） | ✅（每步独立触发） |
| 30天自动追查 | ❌ | ❌ | ✅ |
| 封装无效自动回滚 | ❌ | ❌ | ✅（封装体错误频率未降→退回模板改进） |

### 第三步：执行封装

```bash
# 以 standard 模式封装"横纵分析法"为例
bash lifecycle-gate.sh load 横纵分析法
# 读取 SKILL.md → 分析调用模式
# 在 AGENTS.md 追加纪律
bash lifecycle-gate.sh use 横纵分析法
bash lifecycle-gate.sh unload 横纵分析法
```

### 第四步：验证

封装验证分两层——**文档层（即时有结果）+ 行为层（30天后追查）**：

**① 文档层（封装完成时检查）：**
- [ ] 目标 SKILL.md 中已追加"自进化封装"章节 ✅/❌
- [ ] AGENTS.md 中已追加调度纪律 ✅/❌
- [ ] skill_logger.db 中已有基线调用记录 ✅/❌
- [ ] lifecycle-gate.sh load/use/unload 可正常调用 ✅/❌

**② 行为层（封装后第30天追查）：**
```bash
# 读封装体的 error-log 频率数据
bash ~/.hermes/scripts/encapsulation-audit.sh <封装后的技能名>
```
- [ ] 该技能的同类错误频率在封装后是否下降？
- [ ] on_error 是否在未被手动注入的情况下触发过至少一次？
- [ ] 如果以上任一答案为否 → 封装无效 → 退回第三步，改进封装模板

**如果30天内降了 → 记录该封装模式有效。**
**如果没有降 → 不要第二次封装。** 先检查问题出在哪，再改封装模板。改完再封一次。

## 自我进化

本技能本身也是一个 Skill，因此：

1. **on_error** — 如果封装失败，自动记入 error-log
2. **重复≥3次** — 如果"封装失败"模式出现3次，自动修正写入 AGENTS.md
3. **on_decline** — 如果30天没被调用过，休眠检测会报勇哥确认

这就是递归——教别人进化的那个，自己也在进化。

### 自举记录（Bootstrap）

本技能创建于 2026-06-29。当前状态：**部分完成。**

**已完成的：**
- [x] 首次封装（lifecycle-gate load + 记录 skill_logger 基线）✅
- [x] AGENTS.md 已追加 skill-surgery 调用纪律 ]§12.2）✅
- [x] 自包装：封装器运行的流程跟封装别人的技能一] ✅

**未完成的（2026-07-01 辩证剖析标记）：**
- [ ] 封装器从6次封装中没有产出任何模板改进——知识积累缺失
- [ ] 封装完成后30天的行为层验证从未被追查——封装有效性未证实
- [ ] 自举标准中"用改进后的模板封装下一个"从未被执行——缺少迭代

**真正完成自举的条件（三重门）：**
```
门1：封装了至少2个技能
门2：2个技能的封装后30天错误频率均下降
门3：下降的原因被分析→封装模板被改进→第3个技能用改进后模板封装
```
三重门全通过 → 自举完成。当前进度：**门1通过，门2/门3待验证。**

### 已封装技能清单（截至 2026-06-30）

| 技能 | 封装模式 | 状态 |
|:-----|:---------|:-----|
| skill-surgery（自举） | bootstrap | ✅ 自封装器自身 |
| hengzong-analysis | light（直接调用） | ✅ |
| roundtable-discussion | standard（有子调度） | ✅ |
| practice-article | standard（有子调度）+ 批处理 | ✅ |
| content-source-retrieval | light（高频搜索链） | ✅ |
- [x] knowledge-guardian v2.2.0 已引用本技能
- [x] 自我引用闭环：封装器已被封装 ✅

## Pitfalls

### 自举 ≠ 标记完成（2026-07-01 辩证剖析发现）

**表现：** 封装器声称"已通过自举完成生命周期封装"，但6次封装使用的模板完全相同，封装器没有从封装中学到任何改进。

**根因：** 自举被理解为"封装器自己也跑了一遍封装流程"——但这不是自举。自举是"在封装别人的过程中，封装器自己积累了改进封装模板的知识"。6次封装后，没有：
- 改进封装模板（light/standard/full的区别只在文档深度，不在机制）
- 衡量封装质量（"状态 ✅"意为"已记录"，非"已验证有效"）
- 回写封装结果到目标SKILL.md（始终写入AGENTS.md，不像upskill那样写入技能自身）

**正确标准：** 封装器完成一次自举迭代的条件——
1. 封装了一个技能
2. 该技能被封装后的30天内，同类错误频率下降了
3. 封装器用自己的模板对比了下降的原因，改进了模板
4. 然后用改进后的模板封装下一个技能

缺失上述任何一步，不叫自举——叫"记录操作"。

### 封装完成 ≠ 封装有效（2026-07-01 辩证剖析发现）

**表现：** 封装验证仅检查"AGENTS.md已追加纪律""skill_logger.db已有记录""lifecycle-gate能调用"——三项都是文档层面的，非行为层面。

**根因：** 缺少封装后验证——被封装技能是否真的减少了同类错误频率，on_error是否真的能自动感知纠正信号，角色分离是否真的被执行者遵守。

**正确验证标准：**
```
封装后30天 → 读该SKILL的error-log记录
  ├─ 封装体相关错误频率下降 → 封装有效 → 记录该封装模式
  └─ 封装体相关错误频率未降 → 封装无效 → 诊断原因 → 改进封装模板
```

## 已知缺口（2026-07-01 辩证剖析）

以下缺口已在实践中确认，尚未解决：

| 缺口 | 症状 | 解决方向 |
|:-----|:------|:---------|
| **on_error信号识别** | 用户的隐性纠正（反问/指方向）不触发on_error | 已修补规则但未在封装时自动装载此规则 |
| **封装体重写目标SKILL.md** | 封装结果始终写入AGENTS.md，不写入目标技能自身的SKILL.md | 吸收upskill的设计——learnings.md暂存区，验证后回写 |
| **封装模板未迭代** | 6次封装使用同一模板，light/standard/full的区别仅在文档量 | 封装器需根据30天错误频率数据决定模板改进 |
| **Feedback History Reset信号缺失** | 规则定义"人类确认后清零"，但确认信号未被系统感知 | 需识别勇哥确认信号（同意/方向指令）的触发机制 |

- **knowledge-guardian**（v2.1.0→v2.2.0）— 2026-06-29 更新，增加 Skill 自我进化章节

## 使用示例

```
你：把"横纵分析法"用元技能封装一下
我：→ 读取 hengzong-analysis/SKILL.md
   → 判断为 direct 类型，走 light 模式
   → 在 AGENTS.md 追加纪律
   → skill_logger.db 记录基线
   → 报告完成

你：给词话人间所有 dispatch 步骤套上生命周期
我：→ 读取 cihua-production/SKILL.md
   → 识别 writer/Judge/发布官 三处 dispatch
   → 每处嵌入 lifecycle-gate.sh
   → 走 full 模式
   → 全链验证
```

## 关联

- `infra/lifecycle-gate` — 生命周期门实现
- `infra/skill-self-evolution` — 自我进化引擎本身
- writing/cihua-production — 第一个 full 模式封装对象
- `references/self-evolution-cycle-demo-2026-06-30.md` — 全链自进化闭环首个实战测试记录

## 参考文件

| 文件 | 内容 |
|:-----|:------|
| `references/self-evolution-architecture.md` | 自我进化系统架构说明 |
| `references/feedback-descent-pattern.md` | EvoSkill 反馈下降重置模式——修正确认后计数归零（2026-07-01 吸收）|
| `references/bootstrap-record.md` | 自举过程+已封装清单 |
| `references/model-cognition-gap-2026-07-01.md` | V4-pro vs V4-flash认知差距——自进化系统的执行端选择（2026-07-01）|
