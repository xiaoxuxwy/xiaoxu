# dsh-xu-douyin

DSH（DeepSeek Harness）的抖音插件：用**你已经登录的浏览器**看抖音、搜作品、发评论、读回私信。

- npm：<https://www.npmjs.com/package/dsh-xu-douyin>
- 许可：MIT

## 安装

```bash
dsh plugin --profile web add dsh-xu-douyin
```

装完重启 DSH，侧栏出现「抖音」即为成功。

## 它能做什么

| 工具 | 作用 |
|---|---|
| `douyin2_open` / `next` / `look` | 打开作品、看下一条（走相关推荐）、抽帧 |
| `douyin2_search` | 搜作品（id/标题/作者/时长/点赞） |
| `douyin2_comments` / `replies` | 读一级评论、读楼中楼 |
| `douyin2_say` | 发评论，或 `replyCid` 回复某条评论——**默认只预演**，`confirm` 才真发 |
| `douyin2_board` | 今日面板：建议看几条·评几条（含理由）、实际数字、最近记录 |
| `douyin2_dm_list` / `dm_read` / `dm_send` | 读会话列表、读某会话、发私信（同样默认预演） |
| `douyin2_selftest` | 六项自检：浏览器 / 作品页 / 搜索 / 评论 / 私信页 / 输入框 |

界面：侧栏「抖音」里两个页签——**今日**（建议 vs 实际、进度、最近记录、定时状态）和**设置**（基础 / 主人与安全 / 评论区 / 私信 / 定时）。

## 它怎么连浏览器

不自己开浏览器、不复制 Cookie：在 `9220-9260` 扫一遍，接管**已经在运行、且开着 douyin.com 页面**的那个 Chromium（Edge/Chrome）调试端口。

## 安全线

- 评论与私信默认 **dry-run**，只有 `confirm=true` 且对应开关打开才真发
- 设置里登记的**主人账号**是唯一能当指令的来源；其他人的私信/评论一律标记为不可信信息
- 三道闸门：开关未开 → 拒绝；非主人且未开「允许回应主人以外的人」→ 拒绝；日上限到了 → 拒绝
- 数据只落本机：设置、定时状态、事件流水、自检报告；不连第三方服务器

## 前置条件

- Windows（评论/私信走页面内请求，其它平台未验证）
- Node.js 20+
- 一个已登录抖音的 Chromium 浏览器

## 已知限制

- 抖音推荐流页面在自动化标签里不渲染，`next` 走「详情页相关推荐」
- 依赖页面 DOM 与网页版内部接口，抖音改版可能失效——先跑 `douyin2_selftest` 看哪条挂了
- 定时只做浏览：写评论需要助手在场

## 第三方代码

见 [THIRD-PARTY-NOTICES.md](https://github.com/xiaoxuxwy/xiaoxu/blob/main/THIRD-PARTY-NOTICES.md)（打包自 npm 包内）。
