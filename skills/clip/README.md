# /clip — Content Clipper for Knowledge Inbox

**Clip any URL into a structured markdown file in your inbox, ready for later reading and research.**

Share a link from Bilibili, YouTube, Xiaohongshu, Twitter/X, WeChat, or any webpage — `/clip` extracts the content, metadata, and subtitles, then saves it to `~/workspace/inbox/` with proper frontmatter.

## The Problem

You save dozens of links daily from Feishu, WeChat, Twitter, but:
1. **Content disappears** — tweets get deleted, videos go private, articles hit paywalls
2. **No structure** — bookmarks are a graveyard of unread links
3. **Can't search later** — you remember "that AI video from last week" but can't find it

## What It Does

- Extracts full content: subtitles for videos, text + images for articles, tweets with engagement data
- Saves structured markdown with frontmatter (title, author, date, platform, metrics)
- Auto-tags with purpose labels (投资关注/工具学习/深度阅读/skill进化/产品灵感)
- Enriches short content (tweets, xiaohongshu) with context so every file is useful

## Supported Platforms

| Platform | Method | Auth Required |
|----------|--------|---------------|
| Bilibili | yt-dlp + API | Chrome cookies |
| YouTube | yt-dlp | Chrome cookies |
| Xiaohongshu | curl + __INITIAL_STATE__ | No |
| Twitter/X | fxtwitter API | No |
| WeChat | WebFetch/curl | Sometimes blocked |
| Other | WebFetch | No |

## Usage

```
/clip https://www.bilibili.com/video/BV1xxx
/clip https://x.com/someone/status/123456
/clip http://xhslink.com/xxx 投资相关，重点看估值部分
```

## Files

- `SKILL.md` — Main skill definition
- `playbook.md` — Platform-specific extraction methods
- `failures.md` — Failure log for continuous improvement
