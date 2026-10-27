# CreatorHub 代码级深查存档（2026-10-27，主助手归纳子任务证据报告）

规模：app/main.py 11,303 行单体 FastAPI、app/engine/monitor.py 5,149 行、总计约 44,744 行 Python。

## 四平台判定
- 抖音 ★完整：三通道登录（扫码/创作者/协议二维码 qrlogin.py）；publish.py 浏览器发布（文件头自注实验性、首发需真机校准）；监控双通道（签名 API client.py + 浏览器拦截 fetcher.py，transport_matrix hybrid 优先）；自动评论真实（post_comment），但关键词发现不支持、只能按创作者发现；私信真实（IM protobuf + WebSocket 实时接收），但**没有私信自动回复规则引擎**（DmAutoReplyRule 执行器只有小红书）。
- 小红书 ★最完整：主站态+创作者态两套独立登录（只扫主站不能发布）；发布双模式 browser（browser_writes.py）/ api（creator_api.py 签名+Node 执行 JS）；采集面四平台最全；唯一支持“关键词→去别人帖子评论”的平台，默认先生成草稿人工审核；**唯一有完整私信自动回复闭环**（dm_automation.py：轮询+WebSocket 唤醒+规则+排队发送）。粉丝/关注抓取与官方能力矩阵有矛盾，稳定性最弱。
- 快手 ◐可用：只有 extract/publish/resolve 三个业务文件，无 client.py（无签名 API 兜底，全靠浏览器拦截，平台改版即失效）；发布/作品监控/评论监控真实；**无关键词采集**（KeywordCollector 只实现了 douyin/xhs）；自动评论 creator 模式可用、关键词模式不支持，评论 GraphQL 端点名自注需真机抓包确认；私信无专用 IM 模块、事实上缺位；粉丝抓取自注实测只抽出 1 个、需标定。
- 视频号 ◐仅本账号：数据源=创作者助手网页 channels.weixin.qq.com 拦截其自身 cgi-bin 接口，权限边界就是登录者本人，架构上做不到监控他人/下载（加密 CDN)/私信/粉丝。能做四件事：本账号作品同步、数据、自己作品评论回复、发布（publish.py 自注实验性、需真号校准；短标题限 16 字）。自动评论有执行分支但无规则生成入口（平台白名单不含 shipinhao）。

## 多账号（2-3 个抖音号无结构障碍）
- 单表 DouyinAccount 承载全平台（platform 字段区分），同行存主站态+创作态两份 storage_state、profile_dir、proxy、整组指纹字段、代理出口基线。
- profiles.py：UUID 永不复用目录防新号继承旧 Cookie；一号一代理 sticky（ProxyPool 最少占用+原子预留），代理不可用 fail-closed 不回直连。
- 串行规则：同账号串行、同出口串行（network_guard 并发=1)、发布全局串行（Semaphore 1)、同时活跃账号默认 3、小红书可见操作全机串行。**含义：多号共用一个出口 IP 会被串行化且共享熔断，一号一长效代理是设计前提。**家里机场景需注意：若多号都走家宽同一出口，就是同出口组，节奏天然被压到真人速度（合规上反而安全，但并发别指望）。
- 风控保守线：发布间隔 7200s/1 时/3 日；评论 600s/3 时/10 日；关注私信 900s/2 时/8 日；活跃时段默认 08:00-24:00；命中 403/429/验证码走 1800/7200/21600/86400 冷却阶梯+渐进恢复。

## 定时/队列
- 无 cron 库：单进程 asyncio 轮询（MonitorEngine._loop）+ SQLite 持久化，任务表本身即队列；定时发布真实（PublishTask.scheduled_at，按账号时区解析，默认 Asia/Shanghai，存量无时区预约打回草稿人工确认）；重启恢复：doing 中的写任务置 uncertain 不自动重发（防重复发布），采集任务续跑，冷却与延期跨重启保留。
- 硬限制：进程必须常驻才触发；仓库无 systemd unit，家里机部署需自加 systemd user service 或桌面常驻。错过的时间下次启动补发，不丢但不准时。

## GEO/AI 现状
- GEO 代码为零（grep 命中的 geo 全是地理指纹）。唯一的 AI 是 app/engine/compose.py 的 generate()：OpenAI 兼容 chat/completions，max_tokens 120，只服务评论短文案（≤30 字口语评论），通义千问/DeepSeek 换 base_url 即可——是 GEO 内容生成的接口雏形，但需泛化（长文/图文/视频脚本、prompt 体系、长度）。
- KeywordCollector（仅抖音/小红书）：≤20 词、作品+评论+媒体、Excel 导出、去重续跑——够做 GEO 选题素材层；缺搜索量/趋势序列与 AI 问答侧数据。
- 扩展落点明确：新表进 models.py、新周期任务挂 MonitorEngine._loop、新路由进 main.py（无 APIRouter，11k 行单体是最大工程债，加 GEO 前建议先拆 router）。

## Linux 兼容
- app 本体跨平台（Windows 分支都有 POSIX 对应：fcntl 锁、/proc 检查、SIGTERM、xdg-open、ChromeLocator 有 Linux 路径）。desktop/ 是 Windows WebView2+PyInstaller+Inno 安装包，Linux 整目录忽略，走 start.sh 源码版。
- 会卡的点：①安装脚本不装系统依赖，无桌面最小机缺 libnss3 等 Chromium 系统库；②登录/发布/小红书可见操作全部有头，无 DISPLAY 跑不了——家里机 GNOME+VNC 有桌面，不受影响；③前置条件：装稳定版 Chrome（小红书 auto 模式；家里机目前只有 Edge，需装 Chrome）、Node 18+ 仅小红书 api 模式需要；④默认只听 127.0.0.1 且无账号密码体系，本机/VNC 内使用正合设计。
