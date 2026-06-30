# Skill Surgery 封装记录

## 已封装技能清单（截止 2026-06-30）

| 技能 | 封装时间 | 模式 | 基线ID |
|:-----|:---------|:-----|:-------|
| skill-surgery（自举） | 2026-06-29 | bootstrap | BOOTSTRAP |
| hengzong-analysis | 2026-06-29 | light | SURGERY-BOOTSTRAP |
| roundtable-discussion | 2026-06-29 | standard | SURGERY-BOOTSTRAP |
| practice-article | 2026-06-29 | standard | BOOTSTRAP |
| content-source-retrieval | 2026-06-30 | standard | SURGERY-BOOTSTRAP |

## 封装步骤（可复现）

```
1. 读目标 SKILL.md → 判断调用模式（light/standard/full）
2. 在 AGENTS.md §12.2 追加纪律条目
3. 调用 lifecycle-gate.sh load/unload 记录基线
4. 调用 skill-lifecycle.py use 写入 skill_logger.db
5. 验证：AGENTS.md 有记录 + skill_logger.db 有调用
```

## 验证命令

```bash
grep "自动封装" ~/.hermes/AGENTS.md
python3 -c "import sqlite3; db=sqlite3.connect('~/.hermes/audit/skill_log/skill_invocations.db'); print(db.execute('SELECT skill_name,COUNT(*) FROM invocations GROUP BY skill_name').fetchall())"
```
