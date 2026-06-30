# Feedback Descent 重置模式

> 来源：EvoSkill (sentient-agi/evoskill) — arXiv 2603.02766
> 2026-07-01 吸收

## 核心算法

```python
if candidate_better_than_current:
    current_best = candidate
    feedback_history = []   # ← 重置
```

**当一次修正被人类确认有效后，该模式的历史错误计数应清零。** 旧错误基于旧的 baseline，新 baseline 下不相关。

## 在我们系统中的对应

| EvoSkill | 我们的实现 | 当前行为 | 应有行为 |
|:---------|:----------|:---------|:---------|
| feedback_history reset | error-log 同类错误计数 | 持续累加（×19、×16） | 人类确认修正后，对应模式计数归零 |
| saturation detection | 自动修正通用化 | 每次修正内容一致但不自知 | 检测到"修正内容与上次相同"→停止迭代 |
| evaluation gate | AGENTS.md 写入验证 | 直接写入，不验证效果 | 写入后用同一任务重跑验证 |

## 落地规则

### 1. 计数重置条件

满足以下全部条件时，对应模式的 error-log 计数归零：
- 自动修正已写入 AGENTS.md §12.2
- 人类已确认并细化了修正内容
- 该修正已被执行验证（用同一个任务重跑通过）

### 2. 饱和度检测

`lifecycle-gate.sh error` 调用时，先检测：
- 本次错误描述，与最近3次同类错误描述是否高相似度
- 如果相似且最近3次修正内容相同（未被人类细化改动）
- 说明已达到当前能力的改进上限 → 写入"已饱和"标记，不再触发修正

### 3. 评估门前置

自动修正写入 AGENTS.md 前，应该：
1. 生成修正草案
2. 用原失败任务重跑一次
3. 如果重跑失败同一模式 → 修正有效，落地
4. 如果重跑失败新模式 → 再修正
5. 如果重跑同样失败 → 回滚，标记「建议人类介入」

## 参考

- EvoSkill paper: https://arxiv.org/abs/2603.02766
- GEPA: https://github.com/sentient-agi/gepa-plus
- Feedback Descent: https://arxiv.org/abs/2511.07919
