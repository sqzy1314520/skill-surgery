# Skill Surgery · 元技能封装器

> **所有技能终将被封装。这个技能负责封装它们。而它自己，也在被封装之列。**

给任意 Hermes Agent SKILL 注入生命周期壳（on_load / on_use / on_error / on_unload），将其从一个静态指令集升级为**自进化组件**。

## 它做什么

不是帮你写文章、搜资料、出图。它的操作对象是**其他 SKILL**：

```
输入：一个普通的 SKILL（没有自我进化能力）
输出：同一个 SKILL + 回写的"自进化封装"章节
       + AGENTS.md 调度纪律
       + skill_logger.db 调用基线
```

封装后的 SKILL 能自己感知错误、记录调用、触发自动修正、反馈验证结果。

## 跟其他自我进化项目的区别

| 项目 | 做什么 | 跟 skill-surgery 的关系 |
|:------|:------|:------|
| [EvoSkill](https://github.com/sentient-agi/EvoSkill) | 从失败轨迹自动生成新 SKILL | 它是"产婆"——生出新技能。skill-surgery 是"外科医生"——给已有技能植入进化神经 |
| [upskill](https://github.com/yungbose/upskill) | 给 SKILL 加 learnings.md 暂存区 | 它装一个附加模块。skill-surgery 做的是**注入式改造**——打开 SKILL.md，往里写入自进化章节 |
| [SkillForge](https://github.com/dwickyfp/skillforge) | Q值排序 + 有效性追踪 | 它是"质检员"——告诉你哪个技能好用。skill-surgery 是"改造师"——让不好用的技能自己变好用 |

**skill-surgery 独特性：**
1. **元技能**——操作对象不是用户任务，是其他技能
2. **注入式封装**——回写目标 SKILL.md，不是外挂附加文件
3. **递归自举**——封装器必须先通过"三重门"验证才能去封装别人

## 三种封装模式

| 模式 | 适用 | 封装深度 |
|:-----|:------|:------|
| **light** | 简单技能（搜索/搬运/查询） | SKILL.md 注入 + 调用基线 |
| **standard** | 有 dispatch 子Agent | + on_error 隐性信号识别 |
| **full** | 多步流水线 | + 30天行为层验证 + 封装无效自动回滚 |

## 自举条件（三重门）

封装器不是"写完 README"就能用的——它必须先证明自己：

```
门1：封装了至少 2 个技能 ✅（已封装 6 个）
门2：2 个技能封装后 30 天内错误频率均下降（待验证）
门3：下降原因被分析 → 封装模板改进 → 下一个技能用改进模板封装（待验证）
```

当前状态：**门1通过，门2/门3待验证。**

## 适用对象

- ✅ 已有 10+ 个 SKILL 在生产环境跑、同类错误反复出现的团队
- ✅ 需要"每个 SKILL 都有自己的进化回路"的系统
- ❌ 刚开始建 SKILL 的新手（缺实践积累）
- ❌ 只有 3-5 个 SKILL 的轻量用户（手动修就够了）

## 安装

```bash
# 复制到 Hermes skills 目录
cp -r skill-surgery/ ~/.hermes/skills/skill-surgery/

# 首次使用——封装自己
# 见 AGENTS.md §12.2 调度纪律
```

## 关联

- [Hermes Agent](https://hermes-agent.nousresearch.com/) — 底层 Agent 框架
- [智正行动](https://github.com/) — 方法论来源体系

## 许可

MIT
