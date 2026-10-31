<!--
  ⚠️ 改 README 前先读

  1) 图片地址规范
     所有 metrics 图片一律使用 jsDelivr 绝对 URL：
         https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/<path>
     · 不要写相对路径：GitHub 会把相对地址重定向到 raw.githubusercontent.com。
     · 不要直接写 raw.githubusercontent.com 的绝对 URL：GitHub 不把 raw 视为第三方域名，
       不会送进 camo 代理，浏览器只能直连——而该域名在大陆常年被 DNS 污染，图片会全灭。
     · jsDelivr 是第三方域名，GitHub 渲染时会经 camo（camo.githubusercontent.com）代取，
       浏览器全程不碰 raw，所以稳定。
     代价：jsDelivr 对 @main 有约 12h 边缘缓存，camo 同样缓存 12h，SVG 每日更新会晚几小时生效。
     所以下面所有卡片 URL 都带同一个缓存戳 ?v=20261031c；想立刻看到重新生成的图就把这个
     版本号 bump 一下（c→d…），必要时再 purge：
         https://purge.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/<path>

  2) 卡片宽度
     两列卡片统一 width="400"。原因是主页与仓库页的 README 容器宽度不同
     （主页约 980px、仓库页约 860px）：400+400 两边都放得下，430 在仓库页会掉成单列。
     改宽度前先在两种页面各看一眼。

  3) 架构、排障与自建方案见 SELF-HOSTING.md。
-->

<div align="center">

# Ciallo～(∠・ω< )⌒☆

**緋想天子 · Hisou-Tenshi**

<i>「空の天気も、地の安定も、人の気質も私の掌の上　数多の妖怪を退治してきた貴方の天気！見せて貰うわよ！」</i>

<sub>📍 幻想郷 · 非想非非想天　·　有頂天</sub>

[🌐 Website](https://hisou-tenshi.github.io/)　·　[📝 Blog](https://tenshi.qzz.io/)　·　[💻 GitHub](https://github.com/Hisou-Tenshi?tab=repositories)

<sub>Preference：<a href="https://www.gov.cn/">简体中文</a>｜<a href="http://big5.www.gov.cn/gate/big5/www.gov.cn/">繁體中文</a>｜<a href="https://www.kunaicho.go.jp/">日本語</a>｜<a href="https://www.gov.uk/">English</a></sub>

<p>
<kbd>Python</kbd> <kbd>HTML</kbd> <kbd>JavaScript</kbd> <kbd>CSS</kbd> <kbd>TeX</kbd> <kbd>C</kbd> <kbd>R</kbd>
</p>

</div>

<h3 align="center">📊 数据概览 · Stats</h3>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/base.svg?v=20261031c" alt="GitHub 个人概览"></a>
  <a href="https://www.yuzu-soft.com/"><img width="360" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/ciallo.gif" alt="Ciallo"></a>
</p>

<h3 align="center">🧠 语言 · Languages</h3>

<!--
  languages.svg 走 metrics 的 languages 插件「默认算法」（不开 indepth）：
  直接汇总各仓库 languages.edges 的字节占比，等于「全部仓库的语言占比」，
  秒级完成、不会超时。之前开 indepth 时，Tenshi / Hisou-Tenshi.github.io 等
  巨型仓库会吃光分析预算，卡片长期带 "Reached maximum execution time ...
  partial results" 警告，所以 2026-10-31 改回默认算法。
  标题里的 "N Languages" 是各仓库语言并集的去重数（含 0 字节语言），
  可能略多于列表条数（limit 8），属上游行为，不是配置错误。
-->
<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/languages.svg?v=20261031c" alt="最常用的语言"></a>
  <a href="https://github.com/stats-organization/github-stats-extended"><img width="400" src="https://github-stats-extended.vercel.app/api/top-langs?username=Hisou-Tenshi&layout=compact&title_color=E25577&hide=jupyter%20notebook,makefile&langs_count=8&size_weight=0.1&count_weight=0.9&card_width=500" alt="Top languages"></a>
</p>

<!--
  右图是第三方托管服务（github-stats-extended，github-readme-stats 的官方继任项目）。
  它和左边 metrics 卡现在都按各仓库语言字节占比统计，口径接近；差别是它会按仓库数量
  加权（size_weight=0.1&count_weight=0.9），所以数字略有出入属正常。
  若该实例挂掉，可换源或在 SELF-HOSTING.md §5 里找自建方案。
-->

<h3 align="center">🎮 习惯与生活 · Habits &amp; Life</h3>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/habits.charts.svg?v=20261031c" alt="编程习惯图表"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/steam.full.svg?v=20261031c" alt="Steam 游戏记录"></a>
</p>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/habits.facts.svg?v=20261031c" alt="编程习惯文字结论"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/youtube.music.svg?v=20261031c" alt="最近在听的音乐"></a>
</p>

<h3 align="center">📅 贡献活动 · Activity</h3>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/isocalendar.fullyear.svg?v=20261031c" alt="全年贡献"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/calendar.full.svg?v=20261031c" alt="贡献日历"></a>
</p>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/followup.indepth.svg?v=20261031c" alt="Issue 与 PR 状态"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/activity.svg?v=20261031c" alt="最近活动"></a>
</p>

<h3 align="center">🧩 社区与成就 · Community &amp; Achievements</h3>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/stackoverflow.svg?v=20261031c" alt="Stack Overflow 数据"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/achievements.compact.svg?v=20261031c" alt="GitHub 成就"></a>
</p>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/reactions.svg?v=20261031c" alt="评论表情反应"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/people.followers.svg?v=20261031c" alt="关注者"></a>
</p>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/repositories.pinned.svg?v=20261031c" alt="置顶仓库"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/licenses.overview.svg?v=20261031c" alt="许可证概览"></a>
</p>

<h3 align="center">⭐ 星标 · Stars</h3>

<p align="center">
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/stars.svg?v=20261031c" alt="最近点星"></a>
  <a href="https://github.com/lowlighter/metrics"><img width="400" src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/github-metrics/stargazers.chartist.svg?v=20261031c" alt="Star 趋势"></a>
</p>

<h3 align="center">🎨 カラーコード · Colors</h3>

<p align="center"><sub>Vocaloid 主题色 · 同时用于上方卡片：h1 天依蓝 · h2 重音红 · h3 橙 · 正文初音绿</sub></p>

<table align="center">
  <thead>
    <tr><th align="center">V</th><th align="center">HEX</th><th align="center">Λ</th></tr>
  </thead>
  <tbody>
    <tr><td align="center">初音ミク</td><td align="center">#39C5BB</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/39C5BB.png" alt="#39C5BB" height="15"></td></tr>
    <tr><td align="center">洛天依</td><td align="center">#66CCFF</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/66CCFF.png" alt="#66CCFF" height="15"></td></tr>
    <tr><td align="center">重音テト</td><td align="center">#E25577</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/E25577.png" alt="#E25577" height="15"></td></tr>
    <tr><td align="center">鏡音リン</td><td align="center">#FFA500</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/FFA500.png" alt="#FFA500" height="15"></td></tr>
    <tr><td align="center">鏡音レン</td><td align="center">#FFE211</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/FFE211.png" alt="#FFE211" height="15"></td></tr>
    <tr><td align="center">巡音ルカ</td><td align="center">#FFC0CB</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/FFC0CB.png" alt="#FFC0CB" height="15"></td></tr>
    <tr><td align="center">桃音モモ</td><td align="center">#FFC7C8</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/FFC7C8.png" alt="#FFC7C8" height="15"></td></tr>
    <tr><td align="center">唄音ウタ</td><td align="center">#CB85FF</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/CB85FF.png" alt="#CB85FF" height="15"></td></tr>
    <tr><td align="center">KAITO</td><td align="center">#0000FF</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/0000FF.png" alt="#0000FF" height="15"></td></tr>
    <tr><td align="center">MEIKO</td><td align="center">#D80000</td><td align="center"><img src="https://cdn.jsdelivr.net/gh/Hisou-Tenshi/Hisou-Tenshi@main/colors/D80000.png" alt="#D80000" height="15"></td></tr>
  </tbody>
</table>

<div align="center">

<sub>Copyright © 2025–2026 緋想天子 (Hisou-Tenshi) · 卡片由 GitHub Actions 每日自动更新</sub>

<sub>Powered by <a href="https://github.com/gh-metrics/metrics">gh-metrics/metrics</a> · 维护与排障见 <a href="SELF-HOSTING.md">SELF-HOSTING.md</a></sub>

</div>
