# Eileen — Proactive Requirements Elicitation Agent

> Most AI waits for clear instructions. Eileen asks the right questions.

```
User: "I want to use a computer for bookkeeping"

     ┌─────────────────────────────────────────────────────┐
     │                                                     │
     │   👤 User ──→ Agent A (Conversation Guide)          │
     │                    │                                │
     │              each round                             │
     │                    ↓                                │
     │              Agent B (Scorer)                       │
     │              ┌──────────────┐                       │
     │              │ score: 45/100│──→ "ask about         │
     │              │ tech: low    │    ideal workflow"     │
     │              │ domain: high │                       │
     │              └──────┬───────┘                       │
     │                     │                               │
     │               score < 70?                           │
     │              ╱            ╲                          │
     │           Yes              No                       │
     │            ↓                ↓                        │
     │      loop back        Agent C (Architect)           │
     │      to Agent A       → Executable Plan             │
     │                                                     │
     └─────────────────────────────────────────────────────┘
```

## What It Does

Eileen turns vague **"I want to..."** into **executable plans** through structured dialogue.

| Traditional AI | Eileen |
|---|---|
| Waits for clear instructions | Proactively asks questions |
| One-size-fits-all responses | Adapts to user's tech level |
| Dumps a generic plan | Builds understanding first, then designs |

## 3-Agent Architecture

```
┌──────────────────────┐     ┌──────────────────────┐
│  Agent A              │     │  Agent B (haiku)      │
│  Conversation Guide   │────→│  Scorer               │
│                       │←────│                       │
│  • Open-ended Qs      │     │  • 7-item checklist   │
│  • Adaptive tone      │     │  • User model         │
│  • 1 question/round   │     │  • Score 0-100        │
└──────────────────────┘     └───────────┬──────────┘
                                         │
                                    score ≥ 70
                                         ↓
                              ┌──────────────────────┐
                              │  Agent C (opus)       │
                              │  Solution Architect   │
                              │                       │
                              │  • Executable plans   │
                              │  • Dual versions for  │
                              │    low-tech users     │
                              │  • User's vocabulary  │
                              └──────────────────────┘
```

## User Adaptation

Agent B builds a live **user model** each round:

```
user_model:
  tech_literacy:      low | mid | high
  domain_knowledge:   low | mid | high
  expression_clarity: low | mid | high
  vocabulary:         ["本子", "赊账", "还款"]     ← use these words
  avoid_words:        ["数据库", "CRUD", "字段"]   ← never use these
```

This drives Agent A's behavior:

| User Type | Behavior | Rounds |
|---|---|---|
| `tech=high` | Peer-to-peer, precise terms, system-level Qs | 2-3 |
| `tech=low` | Patient, warm, daily-life framing | 4-6 |
| `engagement=low` | Concrete scenarios, then options fallback | +1-2 |

## Quick Examples

**Developer** (2 rounds → done):
```
User: I need a monitoring agent for my API endpoints
Eileen: Walk me through your current setup...
User: FastAPI, 12 endpoints, AWS ECS, want Slack alerts on 5xx spikes
→ Score 72 → Agent C → monitoring agent design
```

**Shop owner** (3-4 rounds → done):
```
User: 我想用电脑记账
Eileen: 能跟我说说您现在是怎么记账的吗？从头到尾走一遍...
User: 300多个客户，赊账，记在本子上，月底对账翻半天...
Eileen: 您理想中的记账方式是什么样的？
User: 搜名字就知道欠多少，手机能记，月底自动出总数
→ Score 75 → Agent C → 手机记账方案 (简版 + 完整版)
```

## Eval Results

Tested across **9 model combinations × 3 scenarios**:

```
Scenario     │ Models (A,B,C)          │ Rounds │ User Model │ Score
─────────────┼─────────────────────────┼────────┼────────────┼──────
developer    │ opus, sonnet, opus      │   2    │    3/3     │ 0.74 ★
bookkeeper   │ ds, ds, ds              │   6    │    3/3     │ 0.65
bookkeeper   │ sonnet, haiku, sonnet   │   3    │    3/3     │ 0.62
bookkeeper   │ opus, sonnet, opus      │   3    │    3/3     │ 0.62
```

## Install

```bash
# Add to Claude Code
claude install-skill github.com/Mossayl/skills/tree/main/skills/eileen
```

## Usage

```
/eileen I want to build a customer dashboard
/eileen 我想做个自动催款的工具
/eileen help me design a lesson plan workflow
```

Or just say `"eileen"`, `"帮我设计"`, `"I have an idea"` in conversation.

## Files

```
skills/eileen/
├── SKILL.md              ← Main skill (Agent A logic + phases)
├── agent-b-prompt.md     ← Scorer system prompt
├── agent-c-prompt.md     ← Architect system prompt
├── examples/
│   └── scenarios.md      ← 4 full conversation examples
└── LICENSE.txt           ← Apache 2.0
```

## License

Apache 2.0
