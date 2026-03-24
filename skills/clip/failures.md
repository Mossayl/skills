# Clip Failures Log

每次抓取失败时追加记录到此文件，用于持续改进 skill 和 playbook。

格式：
```
## {date} {platform} — {一句话描述}
- URL: {url}
- 方法: {尝试了什么}
- 错误: {具体报错}
- 原因: {根因分析}
- 解决: {最终怎么解决的 / 待解决}
- 改进: {是否需要更新 playbook}
```

---

## 2026-03-16 bilibili — WebFetch 无法获取字幕
- URL: https://www.bilibili.com/video/BV1dKwbzZEcH/
- 方法: WebFetch 直接抓页面
- 错误: 只拿到标题和描述，无字幕内容
- 原因: B站是 SPA，字幕通过异步 API 加载且需要登录态
- 解决: 改用 yt-dlp + --cookies-from-browser chrome
- 改进: playbook 已标注 "WebFetch 无法获取字幕"

## 2026-03-16 bilibili — Player API 字幕为空
- URL: https://www.bilibili.com/video/BV1dKwbzZEcH/
- 方法: Bilibili player API (无 cookies)
- 错误: subtitles: [] 空数组
- 原因: AI 生成字幕需要登录态，匿名请求不返回
- 解决: yt-dlp 带 Chrome cookies 成功获取 ai-zh 字幕
- 改进: playbook 明确 "cookies required"

## 2026-03-16 youtube — Bot 检测拦截
- URL: https://www.youtube.com/watch?v=DSKO9ZtbHFA
- 方法: yt-dlp 无 cookies
- 错误: "Sign in to confirm you're not a bot"
- 原因: YouTube 加强了 bot 检测，几乎强制要求 cookies
- 解决: 加 --cookies-from-browser chrome 解决
- 改进: playbook 标注 YouTube 必须带 cookies

## 2026-03-16 xiaohongshu — WebFetch 返回空壳
- URL: https://www.xiaohongshu.com/discovery/item/69b690a1000000001e00cd24
- 方法: WebFetch
- 错误: 只拿到 minified JS/CSS，无内容
- 原因: 纯 SPA，服务端不渲染内容，但 __INITIAL_STATE__ 包含数据
- 解决: curl + 解析 window.__INITIAL_STATE__ JSON
- 改进: playbook 标注 "WebFetch 返回空壳 JS"

## 2026-03-16 xiaohongshu — JSON 解析失败
- URL: 同上
- 方法: JSON.loads(__INITIAL_STATE__)
- 错误: json.JSONDecodeError
- 原因: JS 对象中有 undefined 值，不是合法 JSON
- 解决: 预处理 re.sub(r':undefined', ':null', raw)
- 改进: playbook 代码已包含此处理

## 2026-03-16 twitter — yt-dlp 文字推文报错
- URL: https://x.com/neethanwu/status/2029216273515565293
- 方法: yt-dlp
- 错误: "No video could be found in this tweet"
- 原因: yt-dlp 只支持视频推文，文字推文无法处理
- 解决: 改用 fxtwitter API
- 改进: playbook 标注 yt-dlp 仅限视频推文

## 2026-03-16 twitter — WebFetch 返回空
- URL: 同上
- 方法: WebFetch
- 错误: "JavaScript is not available" 空页面
- 原因: X.com 纯 JS 渲染，无 SSR
- 解决: fxtwitter API
- 改进: playbook 标注 "WebFetch 不可用"

## 2026-03-16 twitter — Nitter 镜像全挂
- URL: 同上
- 方法: nitter.poast.org / nitter.privacydev.net / xcancel.com
- 错误: HTTP 000（连接超时/拒绝）
- 原因: Nitter 项目大面积停运，公共镜像不再可靠
- 解决: fxtwitter API 作为替代
- 改进: playbook 不再推荐 nitter

## 2026-03-16 twitter — oEmbed 文本截断
- URL: 同上
- 方法: publish.twitter.com/oembed
- 错误: 长推文被截断为 "…"
- 原因: oEmbed 设计限制，不返回完整文本
- 解决: fxtwitter API 返回完整文本
- 改进: oEmbed 降级为 fallback

## 2026-03-21 wechat — 安全验证拦截（3条）
- URL: https://mp.weixin.qq.com/s/enk3GFTSY8srX6Na5qUacQ
- URL: https://mp.weixin.qq.com/s/IrEPAZnJWl9xwZ4xTYgfhw
- URL: https://mp.weixin.qq.com/s/sg5NnmQ7Lx9ffyyLiB_AxA
- 方法: WebFetch + curl with User-Agent
- 错误: 返回 secitptpage/verify.html，"环境异常，完成验证后即可继续访问"
- 原因: 微信公众号对非微信客户端（尤其服务器 IP）访问进行安全验证拦截，WebFetch 和 curl 均触发
- 解决: 未解决。需要在微信客户端内打开，或使用已登录微信的 cookie
- 改进: playbook 需增加微信公众号说明：必须带微信登录 cookie 或通过微信 in-app browser 访问
