# CreatorHub × 国内 GEO 工具 — Codex/Pi 商量简报（工作稿，随证据补全）

日期：2026-10-27（Asia/Shanghai）
目标机器：用户家里 Linux 电脑（Ubuntu 24.04.4，GNOME 桌面，用户 kcj，7.6G 内存，家宽住宅 IP；经 VNC 真人式操作是既定铁律，登录态账号禁止程序化浏览器直开深层链接）

## 用户目标（已复述确认）
基于开源项目 CreatorHub 改造出一个系统，同时做两件事：
1. 国内社媒日常运营：小红书 / 抖音 / 视频号 / 快手，多账号（抖音可能 2-3 个号）
2. 国内 GEO 优化：① 在豆包、通义千问等国内 AI 问答平台登录并自动提问，监测品牌提及/引用；② 在社媒平台生产图文/视频内容并发布，为 GEO 服务
改造完成后上传 GitHub（用户自有仓库开源发布），再由家里 Linux 电脑从 GitHub 下载安装。

## CreatorHub 已实查事实（主助手亲验，2026-10-27）
- 仓库：https://github.com/3441293738/creatorhub ，MIT，Python 3.10+ / FastAPI / SQLModel/SQLite / Patchright（Playwright 兼容）/ 系统 Chrome CDP（小红书）
- 本地克隆：~/workspace/creatorhub-geo-tool/creatorhub（HEAD a1eb771，仓库显示 2026-10-26 有新提交，活跃）
- 平台能力（README 平台表）：四平台均可登录+发布；抖音/小红书/快手可自动评论回复、本账号作品/关注/粉丝/私信同步；视频号仅本账号作品/数据/评论，无自动评论、无他人监控/下载
- 多账号：每账号独立浏览器 Profile + 可绑独立代理（HTTP/HTTPS/SOCKS5），同出口账号串行，风控 conservative 默认：写间隔/小时日额度/冷却阶梯（1800/7200/21600/86400 秒）持久化
- 任务队列：采集/发布/自动评论/账号动作/下载统一队列，重启恢复，登录失效保留任务等重登
- 小红书关键词采集、抖音/小红书关键词批量采集（≤20 词）已有，可复用做 GEO 选题/监测原料
- 数据全本地：data/creatorhub.db + data/profiles/ + data/media/

## 试装实测（本机 Linux，与家里机同为 Ubuntu 系，Python 3.12.3 / Node v24）
- `python3 creatorhub.py install --skip-browser` 成功（pip 依赖全装 + npm install 48 包）
- `patchright install chromium` 成功（Chromium 149）
- `selftest.py` 6/6 通过
- Uvicorn 实起：GET /health = 200 {"status":"ok"}；GET / = 200，301587 字节
- 结论：Linux 源码路线可装可跑；家里机有桌面，扫码登录条件更好。Chrome 在家里机需另行确认（小红书建议系统 Chrome；该机已装 Edge，Chrome 待装/待核）

## 主助手补充实查（2026-10-27 晚）
- 平台模块厚度不均：douyin 11 个文件（auth/client/publish/qrlogin/signing/web_session 等）、xhs 15 个、kuaishou 只有 5 个（extract/publish/resolve）、channels 只有 5 个——快手与视频号实现明显更薄，改造评估要重点看这两家。
- 代码里没有 GEO/豆包/千问业务代码（grep 命中的 geo 是 IP 地理指纹 ip_fingerprint 之类），GEO 监测是全新模块，不是改现成的。
- Windows 专属代码都有 sys.platform 守卫（windowing.py、cdp.py 的 powershell/taskkill 分支）；desktop/ 只是 Windows 安装包分发（WebView2 启动中心），Linux 走 start.sh + Web 工作台，不受影响。

## GEO 资料（本地已存，待盘点子任务补全细节）
- ~/workspace/research/yao-geo-skills：姚金刚 24 个 GEO skill，含 doubao-crawler / deepseek-crawler / chatgpt-crawler（重复采样问答监测）、geo-douyin / geo-xiaohongshu（平台相关词采集）、geoflow-cli/design/template、effect-monitor、tracking
- ~/workspace/blog-pipeline/geo-yao-integration-20261022.md：已定案 v2 的测量纪律（核心问×3、提及率/引用率/事实正确率、评分卡）
- ~/workspace/geoinflow-eval：GEOInflow = 网站+AI 爬虫分析（补监测面，不做提问/打分）
- 待补：GEOFlow / GEOHub 仓库实查、国内 GEO 白皮书来源与要点、千问监测的现成开源实现

## 给 Codex 的商量问题（待资料齐后正式发出）
1. GEO 提问监测模块放哪：复用 CreatorHub 的浏览器 Profile/风控/任务队列框架新增 app/platforms/ai_qa/{doubao,qianwen,deepseek,...}，还是独立服务？各自代价。
2. 豆包/千问网页端登录态与提问：用 Patchright 独立 Profile 扫码登录是否可行，验证码/风控的真人接管点怎么设计（家里机 VNC 真人式操作铁律）。
3. 内容生产链：关键词采集（已有）→ GEO 选题/问题图谱 → 图文/视频生成 → CreatorHub 发布队列，这条链每一环接什么、哪些先做 MVP。
4. 多账号与 GEO 的数据模型：品牌/项目 → 问题集 → 采样记录（提及/引用/名次/原文）→ 内容任务 → 发布记录，怎么建表才不推翻现有 SQLite 迁移机制。
5. 发布与分发：fork 到用户 GitHub 仓库的改造分支策略、版本号、家里 Linux 一键更新脚本（沿用 creatorhub.py install/check 模式）。
6. 分期：MVP 先做哪两个平台 + 哪两个 AI 问答，视频号受限能力怎么处理。
