---
name: clip
description: |
  Clip content from URLs (Bilibili, YouTube, Xiaohongshu, Twitter/X) into the
  workspace inbox for later reading and knowledge extraction.
  Use when: the user shares a link and wants to save/extract its content,
  or says "clip", "保存", "抓取", "帮我存一下", "看看这个".
  Do NOT use for: general web browsing, coding tasks, or non-content URLs.
argument-hint: "<url> [note]"
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - WebFetch
  - Glob
  - Grep
  - AskUserQuestion
  - Agent
---

# Clip Skill

Extract content from a URL → save to `~/workspace/inbox/` → report summary to user.

Before starting, read `SKILL_DIR/playbook.md` for platform-specific extraction methods.
If extraction fails, log the failure to `SKILL_DIR/failures.md` before reporting to user.

## Flow

1. **Parse input** — extract clean URL from messy share text (mobile share menus, tracking params, emojis)
2. **Detect platform** — bilibili/youtube/xiaohongshu/twitter or other
3. **Extract** — follow the method in `playbook.md` for the detected platform
4. **Enrich short content** — if body < 100 chars after extraction (common for tweets/xiaohongshu), append: author bio, hashtags/tags, image descriptions, or a 2-sentence context summary. Goal: every saved file has ≥100 chars of useful text.
5. **Save** — write to inbox using the template below
6. **Report** — filename + 2-3 sentence summary + suggest which knowledge base (investor/researcher/pm/coder)
7. **On failure** — append to `SKILL_DIR/failures.md`, then tell user what went wrong and the specific fix

## Inbox File Format

Filename: `{YYYY-MM-DD}_{platform}_{short_title}.md` (title ≤20 Chinese chars, no special chars)

```markdown
# {title}

- 来源: {platform}
- 链接: {clean_url}
- 作者: {author}
- 抓取时间: {today}
- 发布时间: {publish_date or "未知"}
- 状态: 待阅读
- 时长: {duration, video only}
- 互动: {metrics}
{- 备注: {user_note}, if provided}

---

{structured content body}
```

For long videos (>10min): organize into **section summaries in Chinese**, then append full subtitle under `## 完整字幕`. Use Agent tool for structuring.

## Archiving (user says "归档" / "存到xxx知识库")

1. Extract condensed know-how (not raw content)
2. Save to `~/workspace/agents/core/{agent}/knowledge/`
3. Update inbox file status to `已归档`

## Knowledge Bases

- `investor/knowledge/` — 投资
- `researcher/knowledge/` — 研究/技术
- `pm/knowledge/` — 产品/管理
- `coder/knowledge/` — 编程/工程
