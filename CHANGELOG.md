# CHANGELOG

## 2026-11-18
- 文档：新增 `AGENTS.md`、`docs/AGENT-HANDOFF.md`、`docs/项目说明.md`、`CREDENTIALS.local.md`（只在本机，权限 600，已加入 git 忽略），以及 `.gitignore`。
- 运维：发现 `libretv.winv.cc` 掉线（libretv Worker 在 2026-11-18T01:13Z 重新部署时删掉了自定义域名），已通过 API 恢复。
- 验证：让一个只看文档的新子 Agent 回答 8 个接手问题，按它找出的漏洞补了文档（部署顺序、Token 权限、新增站点流程、DoH 检查脚本、Pages 子域名查法）。
- 清理：删除了 `/tmp/r` 下只读查看源码用的临时克隆（`.git/config` 里含 token）。

## 2026-11-16
- `8be0dda` 按用户要求重新分组：影音 · 阅读 5 个，工具 4 个；视映的简介改成“视频在线解析”。
- `58c9e8c` 改成小清新风格：马卡龙色底加线条 SVG 图标。
- `9c7c506` 改成纯文字极简风（被否：太素）。
- `df9f1b4` 改成渐变玻璃风（被否：AI 味太浓）。
- `8012445` 加入 Any Listen（music.winv.cc）。
- `d670089` / `3be0019` 所有链接改用 winv.cc，并修复误替换出来的 `kshiying` 链接；给目标站挂上 winv.cc 域名，修复 tvbox 的 403。
- `d477f9e` 去掉 OpenClaw 分组。
- `1de221d` 部署方式从 Pages 改为 Workers 静态资源。
- `4ed5376` 初版上线 nav.winv.cc。
