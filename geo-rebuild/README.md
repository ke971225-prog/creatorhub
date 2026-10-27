# CreatorHub × 国内 GEO 改造（geo-rebuild）

本目录记录把 CreatorHub 改造成「国内社媒运营 + 国内 GEO 优化」一体工具的全过程。流程纪律：**先开源发布，后逐步改造，每一步改造都以 Git 提交留痕在本仓库。**

## 基线
- 上游：https://github.com/3441293738/creatorhub （MIT），本仓库为其 fork
- 基线版本：上游 2026-10-26/27 迭代（仓库内版本 1.0.6 / Windows 发行 v0.2.5）

## 目标
1. 国内社媒日常运营：抖音 / 小红书 / 快手 / 视频号（多账号），外加 GEO 信源层平台 头条号 / 微信公众号 / 微博 / 知乎 的账号注册与后续接入
2. 国内 GEO 优化：在豆包、通义千问、DeepSeek、Kimi、腾讯元宝、文心一言等 AI 问答平台进行登录采样监测，并以社媒内容生产与发布为 GEO 服务
3. 双系统可用：Linux（源码安装）与 Windows（安装包）双通道发布

## 文档
- `01-project-brief.md` 项目简报与已实查事实
- `02-code-audit.md` 上游代码级能力审计（四平台真实实现边界、多账号、定时、Linux 兼容）
- `03-geo-inventory.md` 国内 GEO 开源资产盘点（yao-geo-skills / GEOFlow / GEOHub / GEOInflow / 国内 GEO 白皮书与团体标准）
- `04-codex-discussion-ai-crawlers.md` 与 Codex 的设计讨论定版：两层架构（CreatorHub 执行层 + 每 AI 平台专用适配器/skill 方法层）、统一采样契约 v2、开发顺序 千问 → Kimi → 元宝 → 文心、真机探针与账号分离红线

## 路线
- 第 0 期：原版部署到家里 Linux 机跑通日常运营；千问真机探针
- 第 1 期：采样契约 v2 + 千问适配器
- 第 2 期：Kimi、元宝适配器 + GEO 内容生产链接入发布队列
- 第 3 期：文心适配器 + 按团体标准的报告体系
