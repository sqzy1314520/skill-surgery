# Skill 自我进化架构图

```
                      ┌─────────────────────────────────────────────────────┐
                      │              Skill 自我进化闭环                      │
                      │                                                     │
                      │  执行 skill → on_use → skill_logger.db（记录）      │
                      │      │                       ↓                      │
                      │      ├─ 成功 → 统计升级  ← v_skill_stats（分析）    │
                      │      │                                              │
                      │      └─ 失败 → on_error → error-log（L3感知）      │
                      │                  ↓                                  │
                      │            重复≥3次？                                │
                      │              ├─ 否 → 继续观察                       │
                      │              └─ 是 → 自动修正写入 AGENTS.md         │
                      │                         ↓                          │
                      │                   勇哥确认 → 生效                   │
                      └─────────────────────────────────────────────────────┘
                                      │
              ┌───────────────────────┼───────────────────────┐
              │                       │                       │
              ▼                       ▼                       ▼
    ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
    │  生命周期门       │   │  技能休眠检测    │   │ 元技能封装器     │
    │  lifecycle-gate  │   │  cron 每月1号   │   │  skill-surgery   │
    │  load→use→      │   │  30天未用→报人  │   │  给任意技能       │
    │  error→unload   │   │  确认归档        │   │  套生命周期       │
    └─────────────────┘   └─────────────────┘   └─────────────────┘
```

## 文件清单

| 文件 | 用途 |
|:-----|:------|
| `~/.hermes/scripts/skill-lifecycle.py` | 生命周期引擎（on_use/on_error/on_decline） |
| `~/.hermes/scripts/lifecycle-gate.sh` | dispatch生命周期门（load/use/error/unload） |
| `~/.hermes/audit/skill_log/skill_invocations.db` | 技能调用数据库（含 v_skill_stats 视图） |
| `~/.hermes/audit/error-log.md` | 15c分类错误记录 |
| `~/.hermes/AGENTS.md §10` | 错误反射闭环纪律 |
| `~/.hermes/AGENTS.md §12` | Skill 自我进化纪律 |

## cron 任务

| cron | 周期 | 用途 |
|:-----|:------|:------|
| `cb811db23997` | 每天18:30 | 错误反射第五步（error-log扫描） |
| `c4f862a52f41` | 每月1号08:00 | 技能休眠检测 |
