# Clip Playbook — Platform Extraction Methods

**Do NOT try WebFetch on SPA sites** (Bilibili/Xiaohongshu/Twitter). Use methods below.

## SRT Cleanup (shared by Bilibili & YouTube)

```python
import re
prev = ''
for line in content.strip().split('\n'):
    line = line.strip()
    if not line or re.match(r'^\d+$', line) or re.match(r'\d{2}:\d{2}:\d{2}', line): continue
    clean = re.sub(r'<[^>]+>', '', line)
    if clean and clean != prev: print(clean); prev = clean
```

---

## Bilibili (`bilibili.com` / `b23.tv`)

**Cookies required.**

```bash
curl -sLI "{url}" | grep -i location          # resolve b23.tv
curl -s "https://api.bilibili.com/x/web-interface/view?bvid={BVID}"  # metadata
rm -f /tmp/clip_subtitle*
yt-dlp --cookies-from-browser chrome --write-sub --sub-lang ai-zh --skip-download -o "/tmp/clip_subtitle" "{url}"
```

Fail: "请确认 Chrome 已登录 B站"

---

## YouTube (`youtube.com` / `youtu.be`)

**Cookies required.**

```bash
yt-dlp --cookies-from-browser chrome --print title --print description --print duration --print channel --print upload_date --print view_count --print like_count "{url}"
rm -f /tmp/clip_subtitle*
yt-dlp --cookies-from-browser chrome --write-auto-sub --sub-lang en --skip-download --convert-subs srt -o "/tmp/clip_subtitle" "{url}"
```

Fail: "请确认 Chrome 已登录 YouTube"

---

## Xiaohongshu (`xiaohongshu.com` / `xhslink.com`)

**No login needed.**

```bash
curl -sL "{url}" -H "User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36" -H "Accept-Language: zh-CN,zh;q=0.9"
```

```python
import re, json
match = re.search(r'window\.__INITIAL_STATE__\s*=\s*({.*?})\s*</script>', html, re.DOTALL)
raw = re.sub(r'[,:](undefined)', lambda m: m.group(0)[0] + 'null', match.group(1))
note = json.loads(raw)['note']['noteDetailMap'][note_id]['note']
```

Fail: "xsec_token 过期，请重新分享"

---

## Twitter/X (`twitter.com` / `x.com`)

**No login needed.**

```bash
curl -sL "https://api.fxtwitter.com/{handle}/status/{tweet_id}" -H "User-Agent: Mozilla/5.0"
```

Fallback: `curl -sL "https://publish.twitter.com/oembed?url={url}"` (may truncate)
Video tweets: add `yt-dlp --cookies-from-browser chrome` for subtitles.

---

## Other URLs

Use WebFetch. If fails (SPA/login), try `curl` + content parsing.
