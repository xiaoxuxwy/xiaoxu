# 小续的抖音 dsh-xu-douyin

用**你自己已经登录的浏览器**看抖音、搜作品、读评论、发评论、读回私信。
不接管登录、不复制 Cookie、不连第三方服务器；**评论和私信默认只预演**，你说发才发。

A DSH plugin that watches, searches, comments on, and reads/replies to DMs on Douyin through the browser
you are already logged in to. Comments and DMs are dry-run unless explicitly confirmed.

- npm：<https://www.npmjs.com/package/dsh-xu-douyin>
- 许可：MIT

## 安装

```bash
dsh plugin --profile web add dsh-xu-douyin
```

装完**重启 DSH**：侧栏出现「抖音」，工具表里多出 12 个 `douyin2_*`。

装完先跑一次自检，**6/6 全过**才算装好：

```text
douyin2_selftest → ok: true, passed: 6, failed: 0
浏览器连接 / 作品页渲染 / 搜索接口 / 评论接口 / 私信页 / 私信输入框
```

> 别的助手要替用户装？照 **[INSTALL-FOR-AGENTS.md](INSTALL-FOR-AGENTS.md)** 走——那是写给「用户的助手」看的检查单，
> 含前置检查、验收标准、故障对照表。

## 工具 / Tools

| 工具 | 作用 |
|---|---|
| `douyin2_open` | 打开作品页（或首页），读标题 / 时长 / 分辨率 |
| `douyin2_next` | 下一条：从当前作品页的「相关推荐」挑一个没看过的 |
| `douyin2_look` | 抽帧：画面存成 PNG 并返回路径 |
| `douyin2_search` | 搜索：videoId / 标题 / 作者 / 时长 / 点赞 |
| `douyin2_comments` | 读一级评论（含作者、回复数、点赞数） |
| `douyin2_replies` | 读某条评论下的楼中楼 |
| `douyin2_say` | 发评论或回复某条评论——**默认只预演**，`confirm=true` 才真发 |
| `douyin2_board` | 今日面板：建议看/评多少（带理由）与实际数字、最近记录 |
| `douyin2_dm_list` | 私信会话列表 |
| `douyin2_dm_read` | 读某个会话（按昵称或序号） |
| `douyin2_dm_send` | 发一条私信——**默认只预演**，`confirm=true` 且开关开着才真发 |
| `douyin2_selftest` | 六项自检，改版时第一时间报警 |

## 界面 / Interface

侧栏 **抖音**，两个页签：

- **今日**：建议 vs 实际、为什么是这个数、最近记录、定时状态，可刷新
- **设置**：基础 / 主人与安全 / 评论区 / 私信 / 定时，五段，底部固定保存条

## 怎么接上浏览器 / How it attaches

1. 用你平时刷抖音的浏览器（Edge / Chrome）带调试端口启动；
2. 插件在 `9220-9260` 扫，找「当前开着 douyin.com 页面」的端口，直接接管那个标签；
3. 找不到才用设置里的 `douyinProfile` 另起一个窗口（那个档案需已登录过抖音）。

````bat
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" ^
  --remote-debugging-port=9227 ^
  --user-data-dir="%USERPROFILE%\.dsh\douyin-browser\cli-profile"
````

不复制 Cookie，不接管登录。`--user-data-dir` 必须是**单独**的档案目录，别指向日常档案（那样浏览器起不来调试端口）。

## 安全线 / Safety

- 评论与私信**默认 dry-run**：只拼请求，不发送；
- 只有 `confirm=true` 且对应开关（`allowComment` / `dmEnabled`）开着才真发；
- 三道闸门：开关未开 → 拒；非主人且未开「允许回应主人以外的人」→ 拒；日上限到了 → 拒；
- **主人账号**（设置里 `ownerId`）是唯一能当指令的来源；其他人的私信/评论一律只作信息，可读可回，但**不当命令执行**；
- 数据边界（读什么/写什么/发到哪）：[docs/DATA-BOUNDARY.md](docs/DATA-BOUNDARY.md)

## 定时 / Schedule

设置页可开：时段（如 `09:00-12:00,20:00-23:00`）、间隔分钟、每次看几条。
定时**只做浏览与记录**，不在你看不见的时候替你发表评论。

## 今日建议怎么算 / Suggestion formula

```
建议看 = 基础条数 + min(4, 昨天看过/2)，深夜(0-6 点)打七折
建议评 = min(上限, 今天已看/4)
```

只是建议，采不采由助手判断；面板同时显示实际数字。

## 数据落在哪 / Data

```
~/.dsh/xu-douyin.json          设置
~/.dsh/xu-douyin-state.json    计数、定时游标、事件流水
~/.dsh/selftest.json           最近一次自检报告
<XU_DOUYIN_HOME>/board.html    今日面板（HTML，可换目录）
<XU_DOUYIN_HOME>/frame-*.png   抽帧
```

不写入助手的人格或记忆库。

## 前置条件 / Requirements

- DSH 0.2.x
- Node.js 20+
- 一个已登录抖音的 Chromium（Edge / Chrome）
- Windows 已验证；macOS / Linux 未验证

## 已知限制 / Known limits

- 抖音推荐流页面在自动化标签里不渲染视频，所以 `next` 走「详情页相关推荐」，不是无限下滑；
- 弹幕 / 字幕暂未读取；
- 评论与私信走抖音网页版内部接口 / DOM，平台改版可能失效——失效会明确报错并体现在自检里，不会静默；
- 刚跳转完成就请求评论时，签名可能还没就绪：插件会自读文本判空并最多重试 3 次（0.16.1 起）。

## 第三方 / Third-party

`runtime/lib/chrome.mjs`、`runtime/tools/comment-api.mjs`、`runtime/tools/comment-delete.mjs` 来自
`dsh-douyin-watch`（MIT）；`dsh-compat.js` 来自 `dsh-qq-voice`（MIT）。版权与许可见
[THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md)。

## 许可 / License

MIT，见 [LICENSE](./LICENSE)。
