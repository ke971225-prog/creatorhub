# 国内 GEO 资料盘点（2026-10-27 子任务报告，主助手存档）

## 一句话结论
- 豆包监测有现成开源实现（yao-doubao-crawler）；千问/Kimi/元宝/文心没有专用开源 crawler，必须照豆包/DeepSeek 的同构骨架自研。
- 用户说的 "Geo follow" 基本确认 = GEOFlow（github.com/yaojingang/GEOFlow，AGPL-3.0，Laravel 网站内容分发系统，不含社媒运营、不含 AI 提问采样）。
- “中国 GEO 白皮书”不止一份，须区分四份（见下）。

## 本地资产
1. yao-geo-skills：~/workspace/research/yao-geo-skills（源 github.com/yaojingang/yao-geo-skills，24 个 skill）
   - yao-doubao-crawler：豆包重复采样。Web 后端 OpenCLI Browser Bridge 复用已登录豆包网页会话（scripts/doubao_batch_crawl.mjs）；Mobile 后端安卓模拟器+Appium。输出 doubao-crawl.json + summary + 报告；指标=提及率/平均排名/Top1/3/5 概率/情感/信源构成。不绕登录/验证码/风控。
   - yao-deepseek-crawler：同构，chat.deepseek.com 已登录会话，随机间隔 1-3 分钟（安全模式 5-20 分钟）。是自研千问版的最佳仿写模板。
   - yao-chatgpt-crawler：同骨架海外版。
   - yao-geo-douyin / yao-geo-xiaohongshu：只是搜索联想词拓词+截图+评分 Excel，不是发帖/运营工具。可作 GEO 问题库/选题前置。
   - yao-geo-effect-monitor：监测规格层（指标体系、五平台采样口径、纠偏任务表、DB/API 草案），不执行采样。五平台口径：豆包看口语问答/图文；千问看引用源/追问路径/阿里生态信源；Kimi 看长文引用；元宝看微信生态/公众号/视频号；DeepSeek 看结论稳定性/证据链。
   - yao-geo-tracking：国内归因靠“来源补丁”（口令/企微/电话/问卷/CRM 补录），数据模型设计期就要预留字段。
   - 其余 14 个为内容生产/诊断方法包（标题/对比/榜单/科普/旧文改造/品牌图谱等），可作内容模块规则库。
   - 许可：根 LICENSE 是 MIT，但部分 docs 文件头写 All rights reserved，沿用既定口径：只借方法、用自己的表述重写。
2. GEOInflow：~/workspace/geoinflow-eval（github.com/yaojingang/GEOInflow，MIT）= 网站+AI 爬虫分析，不提问、不打分；与社媒无关，二期再议（可借“内容目录 vs 被引覆盖→盲区清单”概念）。
3. 整合定版：~/workspace/blog-pipeline/geo-yao-integration-20261022.md（评分卡 100 分、核心问×3、提及率/引用率/事实正确率）。当时因面向海外“明确不采”的国内五平台口径，正是这次国内工具的主线。
4. 自有早期资产：~/workspace/geo-tracking/（GEO测试方法.md + rounds/*.json，最小字段集 {date, engine, query, cited_domains[], ours_cited, answer_structure, notes}）。

## 远程仓库
- GEOFlow：github.com/yaojingang/GEOFlow，3940 star，AGPL-3.0，Laravel/PHP8.3+PostgreSQL+Redis。能力=知识库→AI 生产→质量门禁→人工审核→多站点分发（网站/WordPress，不含社媒账号）；Chrome 运营助手模式（工单+填草稿+人确认发布+不保存平台密码）值得借鉴，正合真人式运营铁律。
- GEOHub：github.com/yaojingang/GEOHub，165 star，AGPL-3.0，Python CLI。只消费离线观察包做测量/策略，不做实时采样；测量公式与证据台账协议可借鉴。

## 白皮书（四份，勿混淆）
1. 姚金刚《GEO 内容工程操作手册与评估标准》doc.laoyao.cn/9fl0bc：最具操作性，六层架构+12 步+100 分评分卡+Selection/Absorption 分开诊断+核心问 fan-out 5-20 变体+每问 3-5 次采样留原文/引用/截图/版本。
2. 《2026 中国 GEO 产业发展白皮书》：锚定中国商务广告协会 2026-10-14 五项团体标准 T/CAACCHINA 001-005-2026；核心指标=可见率/准确率/推荐率。行业口径：效果验证须 ≥3 个时间窗口重复采样、单指标 ≥30 次有效观测、至少两种采集方式交叉验证，不得以单次截图为证据（T/CAAAD 046-048-2026，2026-10-23 实施）。
3. 《证据型 GEO 白皮书》豆智语义：证据四维（存在性/一致性/可信度/时效性），方法可借、无代码。
4. 其他同名（弯弓研究院实战白皮书、增长超人全意图白皮书）：登记备查，关系弱。

## 平台信源生态（决定社媒运营即 GEO 的排兵）
- 豆包 ← 头条号/抖音/西瓜（字节生态）：抖音账号就是豆包 GEO 信源阵地
- 元宝 ← 公众号/视频号/腾讯新闻：视频号直接喂元宝
- 千问 ← 知乎/微博/淘宝评价/UC：重引用源与追问路径、真实体验内容
- DeepSeek ← 知乎/公众号/长文站：深度长文+数据
- Kimi ← 公众号深度长文/知乎专栏；文心 ← 百科/知道/百家号

## 缺口总表（自研清单）
- 千问/Kimi/元宝/文心 crawler：按豆包/DeepSeek 骨架仿写（OpenCLI 复用已登录会话→新会话提问→随机延迟低频→raw JSON→分析提及/排名/引用→同契约输出）
- 社媒发布/评论/私信/多账号运营：GEO 资料里全缺，这是 CreatorHub 本体的主场
- 面向抖音/小红书/视频号的 GEO 内容形态适配
