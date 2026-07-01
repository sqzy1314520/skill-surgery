# Skill Surgery · 元技能封装器

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Hermes Agent](https://img.shields.io/badge/Hermes-v0.2%2B-ff6b6b)](https://github.com/NousResearch/hermes-agent)
[![agentskills.io](https://img.shields.io/badge/agentskills.io-compatible-blue)](https://agentskills.io)
![GitHub last commit](https://img.shields.io/github/last-commit/sqzy1314520/skill-surgery)

> **All skills will be encapsulated. This skill does the encapsulating. And it too, is being encapsulated.**

Inject self-evolution lifecycles (`on_load` / `on_use` / `on_error` / `on_unload`) into any Hermes Agent SKILL.md — turning static instruction sets into self-improving components.

**skill-surgery is not an alternative to [hermes-agent-self-evolution](https://github.com/NousResearch/hermes-agent-self-evolution), [hermes-dojo](https://github.com/Yonkoo11/hermes-dojo), or [curator-evolver](https://github.com/pingchesu/hermes-curator-evolver).**  
Self-evolution = GEPA mutates skill parameters. Dojo = finds which skills are weak. Curator = decides whether to approve changes.  
**skill-surgery = opens the SKILL.md itself and implants a nervous system.** It changes how a skill runs, not what it says.

## What it does

Not a skill that writes articles or searches data. Its target is **other SKILLs**:

```
Input:  a plain SKILL.md (no self-evolution capability)
Output: the same SKILL.md + injected "self-evolution encapsulation" section
        + AGENTS.md dispatch discipline
        + skill_logger.db call baseline
```

An encapsulated skill can sense its own errors, log invocations, trigger auto-correction, and report back validation results.

## How it's different

| Project | What it does | Relation to skill-surgery |
|:--------|:-------------|:--------------------------|
| [EvoSkill](https://github.com/sentient-agi/EvoSkill) | Generates new skills from failure trajectories | It's the "midwife" — births new skills. skill-surgery is the "surgeon" — implants evolution nerves into existing ones |
| [upskill](https://github.com/yungbose/upskill) | Adds learnings.md staging area to a SKILL | It mounts an external module. skill-surgery does **injection** — opens SKILL.md and writes the evolution chapter inside |
| [SkillForge](https://github.com/dwickyfp/skillforge) | Q-value ranking + effectiveness tracking | It's the "inspector" — tells you which skill works. skill-surgery is the "modifier" — makes bad skills improve themselves |

**skill-surgery uniqueness:**
1. **Meta-skill** — operates not on user tasks, but on other skills
2. **Injection-based encapsulation** — writes back into target SKILL.md, not external decoration
3. **Recursive bootstrapping** — the encapsulator must pass a "triple gate" before it can encapsulate others

## Three encapsulation modes

| Mode | When | Depth |
|:-----|:-----|:------|
| **light** | Simple skills (search/fetch/query) | SKILL.md injection + call baseline |
| **standard** | Skills with dispatch sub-agents | + on_error signal recognition |
| **full** | Multi-step pipelines | + 30-day behavioral validation + error branch wiring + auto-rollback |

## Bootstrap status (Triple Gate)

```
Gate 1: Encapsulated at least 2 skills ✅ (6 skills done)
Gate 2: Error frequency dropped in both within 30 days ⏳ (pending)
Gate 3: Root cause analyzed → template improved ⏳ (pending)
```

**Current: Gate 1 passed, Gates 2/3 pending.**

## When to use

- ✅ 10+ skills in production with recurring error patterns
- ✅ Need for "every SKILL has its own evolution loop"
- ❌ Beginners with few skills (manual fixes are enough)
- ❌ Light users with 3-5 skills

## Install

```bash
# Clone to Hermes skills directory
cp -r skill-surgery/ ~/.hermes/skills/skill-surgery/

# First use — encapsulate itself
# See SKILL.md bootstrap guide for details
```

## Quick demo

```bash
# Encapsulate a skill with light mode
hermes skills dispatch skill-surgery "encapsulate hengzong-analysis light"

# Check encapsulation status
bash lifecycle-gate.sh status hengzong-analysis
```

## Links

- [Hermes Agent](https://hermes-agent.nousresearch.com/) — underlying agent framework
- [Full Skill Doc](SKILL.md) — complete operation manual

## License

MIT
