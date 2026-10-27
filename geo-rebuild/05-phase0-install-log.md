# 第 0 期安装记录：家里 Linux 机部署（2026-10-27 夜，实测）

## 目标机
- 家里 Linux 电脑：Ubuntu 24.04.4 LTS，GNOME，用户 kcj，家宽出口
- 环境实测：Python 3.12.3 / git 2.43.0 / Node v18.19.1 / Firefox 已装 / **Google Chrome 未装**（which 只返回 /usr/bin/firefox）
- 磁盘 915G 余 821G；内存 7.8G

## 安装（从本 fork 克隆，非上游原仓）
```bash
cd ~ && git clone -b geo-rebuild https://github.com/ke971225-prog/creatorhub.git creatorhub-geo
cd ~/creatorhub-geo && python3 creatorhub.py install
```
- 克隆成功（2324 objects，含 geo-rebuild 目录）
- 安装成功，全程无 sudo 提示：生成 config.yaml、建 .venv、Python 依赖、Patchright Chromium 149.0.7827.55（177 MiB）+ Headless Shell + FFmpeg、npm install 48 包
- selftest.py：6/6 OK（SM3 / RC4 / 平台风控 / Patchright / Node.js / 链接下载）

## 启动验证
```bash
cd ~/creatorhub-geo && nohup python3 creatorhub.py --no-open > ~/creatorhub-geo.log 2>&1 &
curl -s http://127.0.0.1:8000/health   # {"status":"ok"}
```
- 工作台已在该机 Firefox 打开 http://127.0.0.1:8000/#overview ：平台页签 抖音/小红书/快手/视频号 齐全，右上角「服务已连接」，未登录任何平台账号（账号登录由用户本人扫码完成）

## 遗留（下一轮处理）
1. 未装 Google Chrome：小红书 auto 模式会回退 Patchright Chromium（可用但非首选）；装 Chrome 需 sudo 密码，待用户在场时走终端 apt 安装
2. 服务目前以 nohup 后台运行，重启不自启：需补 systemd --user 服务做常驻
3. 千问探针待用户本人在该机浏览器登录千问后执行（登录态/提问/引用/追问/限流五项）
