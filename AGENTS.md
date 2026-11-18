# AGENTS.md — winv-nav

> 最后核对：2026-11-18 22:14（CST）。详细文档：[docs/AGENT-HANDOFF.md](docs/AGENT-HANDOFF.md) · 给人看的版本：[docs/项目说明.md](docs/项目说明.md) · 变更记录：[CHANGELOG.md](CHANGELOG.md) · 登录信息见 `CREDENTIALS.local.md`（只在本机，不进 git）

## 30 秒速览

- **项目是什么**：一个纯静态单页导航站，列出用户（gituxx）自己用 AI 做的 9 个小站。整站就一个文件 `public/index.html`。
- **线上地址**：https://nav.winv.cc ，部署成 Cloudflare Worker `winv-nav`，用的是静态资源（assets）功能，**不是 Pages**。
- **代码在哪**：本地 `/Users/bb/codex/nav`，分支 `main`；远程仓库 https://github.com/gituxx/winv-nav （公开仓库），本地**故意没有配置 git remote**，推送时临时拼带 token 的 URL。2026-11-18 核对：本地、GitHub、线上三者一致（commit `8be0dda`）。
- **当前最大的问题**：被导航的 libretv、shiying、checkin 三个 Worker 一旦被各自的项目重新 `wrangler deploy`，**它们的 `*.winv.cc` 自定义域名会被自动删掉**，因为那些项目的 wrangler 配置里只写了 oinnn.top 域名。2026-11-18 就发生过一次：`libretv.winv.cc` 掉线，已手动恢复。根治办法见 handoff 文档的“待决事项”。

## 用户偏好

- 用中文、简洁地汇报：写结论和需要用户决定的事，不要堆过程细节。
- 每次改动都要更新 `CHANGELOG.md` 和相关文档，文档要和代码保持同步。
- 授权直接做，不用问。只有花钱、用用户的个人账号、不可逆的删除，才需要先问。
- 验证必须真实：要用 `curl` 或浏览器真正打开线上页面和每个链接，“部署成功”不等于“能用”。
- 节省额度：长任务放后台，用定时器等待，不要高频轮询。
- 优先用成熟的开源方案。借鉴代码时注意许可证，GPL 或禁止商用的代码只借鉴思路、自己重写。
- 改线上之前先备份（本项目的备份就是 git 历史加 `wrangler rollback`）。
- 开发中创建的临时资源（DNS、隧道、测试文件）用完要清理，或者记进文档。
- **本项目特有的偏好**：
  - 审美要求：**不要“AI 味”**，也就是不要紫色渐变、发光、磨砂玻璃、emoji 图标、大时钟、问候语和搜索框。用户也嫌纯文字列表太素。现在这版是用户认可的：浅色、小清新、马卡龙色底加线条 SVG 图标的卡片。
  - 要求不高、越简单越好：单文件，没有构建步骤，没有框架。
  - 所有项目都用 `xxx.winv.cc` 子域名；导航里的链接一律用 winv.cc，不用 oinnn.top。
  - 分组以用户指定的为准：“影音 · 阅读”5 个，“工具”4 个。视映的定位是“视频在线解析”。
  - 不展示 OpenClaw 相关仓库（用户说没必要）。

## 红线

1. **不要把 `CREDENTIALS.local.md` 放进 `public/`、提交到 git，或者写进其他文档。** GitHub 仓库是公开的，`public/` 里的东西全都会对外公开。
2. **只有 Worker `winv-nav` 和 `nav.winv.cc` 属于本项目。** 同一个 Cloudflare 账号下的其他资源一律不要删、不要改、不要重新部署，包括：
   - Worker：libretv、shiying-video、workbuddy-checkin、edgetunnel、edge-mini、nodescope-collector、cccat-daily-checkin
   - Pages 项目：kvideo、ouonnkitv、tvbox-api-backup、nodescope-aggregator、book-oinnn-top
   - 整个 oinnn.top 域名
   - `video.winv.cc` 和 `music.winv.cc` 的 DNS 记录
   唯一的例外是：给它们**挂上或恢复** `*.winv.cc` 自定义域名（见 handoff 文档 9.5 / 9.5b）。
3. 不要在 winv.cc 的根域名或通配记录上动手。
4. 不要改回 Cloudflare Pages 部署（原因见“已否决的方案”）。
5. 不要把 token 写进 `git remote`。

## 常用命令

```bash
cd /Users/bb/codex/nav
# 设置凭据环境变量（三个变量的值见 CREDENTIALS.local.md 第 1 节）
export CLOUDFLARE_EMAIL=... CLOUDFLARE_API_KEY=... CLOUDFLARE_ACCOUNT_ID=...
npx -y wrangler deploy                         # 部署（wrangler 4.164.0）
npx -y wrangler deployments list               # 查看历史版本
npx -y wrangler rollback <version-id>          # 回滚
curl -s https://nav.winv.cc/ | diff -q - public/index.html && echo 线上与本地一致
for u in nav libretv kvideo ouonnkitv music book node checkin shiying tvbox; do echo $u $(curl -s -o /dev/null -w "%{http_code}" -m 10 https://$u.winv.cc/); done
git push https://<TOKEN>@github.com/gituxx/winv-nav.git main   # TOKEN 见 CREDENTIALS.local.md 第 2 节
```

增删项目：完整步骤见 handoff 文档 9.5b。简单说就是：改 `public/index.html` 里的 `const D=[...]`（第 43–54 行）→ 部署 → 用 curl 验证 → 推送到 GitHub → 更新 CHANGELOG。
