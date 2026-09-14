# SELF-HOSTING.md — 当 metrics 动作再次挂掉时怎么办

这份文档是 `github-metrics/*.svg` 这套东西的**故障排查与自建手册**。目的是：以后上游
再次因为 GitHub API 变更而失效时，不需要重新从零 research 一遍，照这里做即可。

---

## 0. 这套系统由什么组成

```
.github/workflows/update.yml      ← 每天 07:21 UTC 跑 17 个 step
        │  uses: gh-metrics/metrics@master   (维护中的分叉)
        ▼
github-metrics/*.svg              ← 生成后由 action 自动提交回本仓库
        │
        ▼
README.md                         ← 用相对路径 ./github-metrics/xxx.svg 内联引用
```

另有一张卡片不走 metrics，而是第三方托管服务：

```
README.md → https://github-stats-extended.vercel.app/api/top-langs?username=Hisou-Tenshi&...
```

**关键认知：两张图坏掉的原因和解法完全不同。** 前者是本仓库的 workflow（可自建），
后者是别人的服务器（只能换源或自建）。

---

## 1. 先判断「哪种坏」

| 症状 | 大概率原因 | 去哪一节 |
|---|---|---|
| 某张 SVG 变成 `480×60` 且写着 `Unexpected error` | 上游代码撞上 GitHub API 变更 | §3 |
| 某张 SVG 高度变成 40 左右、只有标题没内容 | 插件没报错但拿到的数据是空的 | §3.4 |
| 某张 SVG 内容几个月没变过 | action 没崩，但那一步本来就没数据（token 过期/API 下线） | §3.4 |
| top-langs 卡片显示裂图 | 第三方实例挂了 | §5 |
| workflow 完全没跑 | cron 被 GitHub 禁用（仓库 60 天无活动会自动停用 schedule） | §2 |

**第一步永远是看 SVG 的尺寸和内容**，比翻 Actions 日志快得多：

```powershell
Get-ChildItem github-metrics\*.svg | ForEach-Object {
  $c = Get-Content $_.FullName -Raw
  $m = [regex]::Match($c, '<svg[^>]*width="(\d+)" height="(\d+)"')
  [PSCustomObject]@{
    File   = $_.Name
    Size   = "$($m.Groups[1].Value)x$($m.Groups[2].Value)"
    Status = if ($c -match 'Unexpected error') { 'ERROR' } else { 'ok' }
    Commit = (git log -1 --format=%ad --date=short -- $_.FullName)
  }
} | Format-Table -AutoSize
```

`Commit` 那一列是**最后一次内容发生变化**的日期。如果某张图几个月没变，它就一直在坏。

---

## 2. workflow 层面

### 2.1 为什么用 `gh-metrics/metrics` 而不是 `lowlighter/metrics`

上游 `lowlighter/metrics` **最后一个 tag 是 2023-09-12 的 v3.34**，之后再没发过版本。
所以 `lowlighter/metrics@latest` 永远解析到 2023 年那份代码，GitHub API 这几年的
变更它一概不知道。

`gh-metrics/metrics` 是社区接手维护的分叉，已 cherry-pick 关键修复并持续更新
（最后一次提交 2026-09-07，`b594ca3c0c5f4312f9674afb91ca8d7619f9c77d`）。

### 2.2 换回上游 / 换到别的分叉

只改 `uses:` 一行，**17 处全部都要改**（用编辑器全局替换）：

```yaml
uses: gh-metrics/metrics@master
```

如果要锁版本（避免维护者改坏），把 `@master` 换成 commit SHA：

```yaml
uses: gh-metrics/metrics@b594ca3c0c5f4312f9674afb91ca8d7619f9c77d
```

> 分叉**没有 release tag**，所以不能用 `@v3` 之类的写法。

### 2.3 先验证 action 的输入名有没有变

分叉有时会重命名输入。换源前先核对一遍，能省掉一次失败的 CI：

```powershell
$r = Invoke-WebRequest 'https://raw.githubusercontent.com/gh-metrics/metrics/master/action.yml' -UseBasicParsing
$r.Content -match '(?m)^\s{2}plugin_habits:'   # 逐个检查你用到的输入
```

### 2.4 关于 cron 的几个坑

- cron **只认 UTC**。本文件是 `21 7 * * *` = 16:21 JST。
- GitHub 的 cron 在高峰期可能延迟几十分钟，不要指望准点。
- **仓库连续 60 天没有任何提交，schedule 会被自动停用**，需要去 Actions 页面
  手动 re-enable。本仓库因为 action 每天提交 SVG，所以不会触发这条；但如果哪天
  所有 step 都不产生变化（连续 60 天零 diff），就会中招。
- `concurrency` 已配好，防止 cron 和手动触发同时跑导致 push 冲突。

### 2.5 手动跑一次（改完配置必做）

Actions → Metrics update UTC 07:21 → Run workflow。
跑完后按 §1 的脚本检查 SVG 尺寸，并且 `git log` 里应该出现新的
`Update github-metrics/xxx.svg - [Skip GitHub Action]` 提交。

---

## 3. 插件层面（90% 的故障在这里）

### 3.1 通用套路：GitHub 改字段 → 代码读 `undefined` → 炸

上游代码大量依赖 GitHub API 返回的**嵌套字段结构**。GitHub 一旦删字段（通常出于
隐私或弃用考虑），代码里类似这样的写法就会抛 `TypeError`：

```js
commits.flatMap(commit => commit.payload.commits.filter(...))   // ← payload.commits 没了
```

而 `lowlighter/metrics` 的 `format.error()` 会把真实异常塞进 `.error.instance`，
模板只打印 `.error.message`，于是界面上只剩一句没头没尾的 **`Unexpected error`**。
**看到这句话就去翻对应插件的 `index.mjs`，不要怀疑自己的配置。**

### 3.2 已发生过的具体案例（对照表）

| 插件 | 崩溃点 | 原因 | 时间 |
|---|---|---|---|
| `activity` | `activity/index.mjs` PushEvent 分支<br>`commits.filter(...)` | GitHub 从 `PushEvent.payload` 中移除了 `commits` 字段，只保留 `before`/`head`/`size`/`push_id` | 2025-10 |
| `habits` | `habits/index.mjs:50`<br>`flatMap(({payload}) => payload.commits)` | 同上（它自己也读 events 流） | 2025-10 |
| `achievements` | Projects (classic) 成就 | GitHub 下线旧 Projects API | 长期 |

`activity` 那个坑还有一个隐蔽点：**事件解析发生在 `plugin_activity_filter` 过滤之前**，
所以把 filter 缩到只剩 `ref/create` 也救不回来——只要 events 流里有一个 PushEvent
就整块挂掉。后来社区 PR 顺手把过滤提前了，才让「配置层面临时绕开故障类型」成为可能。

### 3.3 上游修复 PR

`https://github.com/lowlighter/metrics/pull/1760`（作者 EndBug）：
把 PushEvent 的 commit 数据改为调用
`rest.repos.compareCommitsWithBasehead({basehead: before...head})` 获取，
不再依赖 `payload.commits`。

**该 PR 至今未被上游合并**，但已被 `gh-metrics/metrics` cherry-pick（`ab54a40`、`d222f80`）。

### 3.4 不是所有「坏」都是崩溃

有些图是**没报错但没数据**，表现是内容长期不变或高度只有 40 上下：

- `youtube.music.svg` — `YOUTUBE_MUSIC_TOKENS` 需要定期刷新，过期就显示
  「No music recently listened」。这是 token 问题，不是代码问题。
- `discussions.svg` — 仓库没开 Discussions 或没有讨论时就是空的。
- `stackoverflow.svg` — `plugin_stackoverflow_user: 1` 是官方示例里的**占位数值**，
  需要换成真实的 Stack Overflow 用户 ID，否则拉到的不是本人的数据。
- `languages.indepth.svg` — 标题写 "11 Languages" 但列表只有 4-5 种：`11` 来自各仓库
  `languages.edges` 的去重并集（包含 0 字节的语言），而 indepth 分析器统计出的
  0 行/0 字节语言会被静默丢弃。属于上游行为，不是配置错误。

### 3.5 两个「生成了但没展示」的文件

workflow 每天会生成 17 张图，其中这两张**没有被 README 引用**（历史上也从未引用过）。
它们不是坏了才藏起来的，而是本来就没放上去：

| 文件 | 状态 | 为什么先别放上去 |
|---|---|---|
| `achievements.compact.svg` | ❌ 内容是 `Unexpected error` | 展示出来只会多一张裂图 |
| `discussions.svg` | ⚪ 内容是「No discussions」 | 仓库没开 Discussions 或没有讨论 |

想清理的话有两个选择：把它们从 workflow 里删掉（省两次 API 调用），
或者修好后加进 README。加之前**先跑 §1 的脚本确认内容正常**。

### 3.6 本地复现排错

本机 `D:\users\hinanawitenshi\documents\GitHub\_metrics-src` 有一份上游源码副本
（v3.34，即 2023 年那份）。虽然它不含分叉的修复，但**用来看崩溃点非常方便**：

```powershell
# 找所有依赖已消失字段的地方
Select-String -Path "_metrics-src\source\plugins\*\*.mjs" -Pattern 'payload\.commits'
```

想跟最新代码对比，直接取分叉的原文：

```powershell
(Invoke-WebRequest 'https://raw.githubusercontent.com/gh-metrics/metrics/master/source/plugins/activity/index.mjs' -UseBasicParsing).Content
```

---

## 4. 真·自建：完全脱离第三方 action

如果哪天所有分叉都死了，或者你想要 100% 可控，按下面做。

### 4.1 方案 A：fork 分叉 + 改 `uses:`

1. fork `https://github.com/gh-metrics/metrics` 到自己的账号
2. 把 workflow 里 17 处 `uses: gh-metrics/metrics@master` 改成
   `uses: <你的账号>/metrics@master`
3. 想打补丁就直接在自己的 fork 上改 `source/plugins/*/index.mjs`

**优点**：改动最小，`git push` 即生效。
**注意**：action 是 `composite` 类型，内部会读 `${{ github.action_repository }}`
来解析自己的源码仓库，所以 fork 之后不需要改 Dockerfile 之类的东西。

### 4.2 方案 B：本地 Docker / Node 跑

`_metrics-src` 里有完整的运行环境（Dockerfile / package.json）：

```bash
git clone https://github.com/gh-metrics/metrics
cd metrics
npm ci

# 用低权限 token 跑单张图
node source/app/action/index.mjs \
  --filename github-metrics/activity.svg \
  --token "$GH_TOKEN" \
  --plugin_activity yes \
  --plugin_activity_limit 5 \
  --plugin_activity_days 0 \
  --plugin_activity_filter "issue, pr, release, fork, review, ref/create" \
  --base ""
```

跑通以后，写一个最简单的 PowerShell / bash 脚本把 17 步串起来，
用系统定时任务替代 GitHub Actions：

```powershell
# 思路示意，不是可直接运行的成品
$steps = @(
  @{ file='base.svg';             args=@('--plugin_introduction','yes', '--plugin_lines','yes') },
  @{ file='habits.charts.svg';    args=@('--plugin_habits','yes', '--plugin_habits_charts','yes') }
  # ... 其余 15 步照抄 workflow
)
foreach ($s in $steps) {
  node source/app/action/index.mjs --filename "github-metrics/$($s.file)" --token $env:GH_TOKEN --base "" @($s.args)
}
git add github-metrics; git commit -m "Update metrics"; git push
```

**优点**：完全自主，GitHub API 再变也只是改几行代码。
**代价**：需要自己维护一套 `npm ci` 环境和 token 轮换。

### 4.3 生成 SVG 后怎么提交

保留 workflow 里现在的形态即可——action 自己会 commit + push，
提交信息带 `[Skip GitHub Action]` 标记，所以**不会**触发 workflow 的 `push:` 造成死循环。
自己写脚本时也要保留这个标记，否则会无限递归触发。

---

## 5. top-langs 卡片（第三方服务）

### 5.1 现状

README 里这张卡片用的是 `github-stats-extended.vercel.app`，即
`github-readme-stats` 的官方继任项目（前者已被作者归档）。

历史上踩过的坑：

- `github-readme-stats.vercel.app` 公共实例 **返回 HTTP 503**（限额耗尽），
  且项目已归档。已弃用。
- 更早的参考项目 `anuraghazra/github-readme-stats` 同理。

### 5.2 换源清单

所有同类服务参数基本兼容，换域名即可：

| 服务 | top-langs 路径 | 备注 |
|---|---|---|
| `github-stats-extended.vercel.app` | `/api/top-langs` | 当前使用，官方继任项目 |
| `github-readme-stats.vercel.app` | `/api/top-langs` | ❌ 已归档 + 503 |
| 自建 Vercel 部署 | `/api/top-langs` | 最稳，见下 |

### 5.3 自建部署（推荐用于长期稳定）

1. fork `https://github.com/stats-organization/github-stats-extended`
2. 在 Vercel 里 Import 这个 fork
3. 在 Vercel 项目设置里加环境变量 `PAT_1`（一个 classic PAT，`repo` + `read:user` 即可）
   - 不配 token 也能跑，但会更容易撞上 GitHub 的匿名限额
4. 部署完把 README 里的域名替换成你自己的 `<project>.vercel.app`

### 5.4 数据质量说明

迁移后实测这张卡片渲染正常（500×190），能列出 8 种语言：
HTML 33.7% / Python 24.9% / JavaScript 13.2% / CSS 8.5% / TeX 5.9% /
BibTeX Style 5.6% / C 4.4% / R 3.9%。

需要留意的是**它和 `languages.indepth.svg` 统计口径不同**，两个数字对不上是正常的：

- 这张卡：统计各仓库的**语言字节占比**，把 README、SVG、`colors/` 里的图片等
  都算进 HTML 里，所以 HTML 占比偏高
- `languages.indepth.svg`：只统计**新增代码行**，并用 linguist 按文件类型识别

如果觉得 HTML 占比失真，可以加 `&exclude_repo=<仓库名>` 排除某些仓库。

### 5.5 更彻底的替代方案

commit SVG 到仓库（本仓库其它 16 张图就是这么做的）比依赖在线服务可靠得多，
因为服务什么时候挂你无法预知，而仓库里的文件永远在。

---

## 6. 快速排查清单

出问题时按这个顺序走：

1. 跑 §1 的脚本，看**哪张图坏了、什么时候开始坏的**
2. 记住坏掉的日期 → `git log` 找那个时间点前后有什么变化
3. 看 SVG 里的文字：
   - `Unexpected error` → 代码崩了，去查对应插件的 `index.mjs`，找 `undefined`
   - 只有标题没内容 → 数据源为空（token 过期 / API 下线 / 仓库无数据）
   - 内容陈旧但没报错 → 该 step 的输入参数配错了
4. 拿最新的分叉源码跟 `_metrics-src` 本地那份 diff，定位被删的字段
5. 修不动就先在 workflow 里**注释掉那一个 step**，保证其它 16 张图正常
   （不要因为一张图坏掉就让整个 job 失败）
6. 修不了又等不到上游 → 走 §4 自建

---

## 7. 参考链接

- upstream（停更）：https://github.com/lowlighter/metrics
- 当前使用的分叉：https://github.com/gh-metrics/metrics
- PushEvent 修复 PR：https://github.com/lowlighter/metrics/pull/1760
- 相关讨论：https://github.com/lowlighter/metrics/discussions/1761
- GitHub Events API 文档：https://docs.github.com/en/rest/activity/events
- top-langs 服务：https://github.com/stats-organization/github-stats-extended
- 已归档的旧服务：https://github.com/anuraghazra/github-readme-stats
