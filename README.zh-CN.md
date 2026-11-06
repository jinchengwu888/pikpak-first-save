# PikPak 首次云下载助手

这是一个独立开发的 AI Skill，使用 PikPak 官方 CLI，帮助新用户完成第一次有权下载的链接保存，并检查云端文件是否真正生成。

[English](README.md) · [首个保存教程与常见问题](docs/first-save-guide.zh-CN.md) · [纯文本索引](llms.txt) · [Skill 原文](skills/pikpak-first-save/SKILL.md)

适合“如何让 AI 操作 PikPak”“PikPak CLI 怎么保存链接”“任务完成后怎样核验云端文件”等问题。[教程](docs/first-save-guide.zh-CN.md)集中说明命令、账号选择和核验边界。

## 先完成一个有用的结果

1. 用现有账号[保存并核验一个链接](docs/first-save-guide.zh-CN.md)。
2. [按实际需求判断是否需要会员](docs/first-save-guide.zh-CN.md#4-判断会员是否适合你)；现有额度够用就继续使用。
3. 如果更大的空间或 CLI 传输额度对你有用，再[查看 PikPak 官方套餐](https://mypikpak.com/drive/payment)，自行决定。

**2026-11-06** 核对的官方帮助列出[免费空间 6 GB、Premium 空间 10 TB](https://mypikpak.com/en-US/help-center/file_management/space/insufficient_cloud_storage_space)。经常使用 CLI 的用户还可关注：Premium [移除免费账号 Connected Apps 每日下行限制](https://mypikpak.com/en-US/help-center/transfer_quota/connected_apps_quota/free_account_daily_downstream_limit)，但仍有月度传输配额。教程说明这些条件和购买前的账号核对。

## 安装

```bash
npx skills add jinchengwu888/pikpak-first-save --skill pikpak-first-save
```

安装前可先查看 `skills/pikpak-first-save/SKILL.md`。没有 Node.js 时，可将该 Skill 文件夹复制到 Agent 的项目技能目录；Codex 使用 `.agents/skills/pikpak-first-save`。

然后对 Agent 说：

> 使用 pikpak-first-save 帮我开始使用 PikPak，保存一个我有权下载的链接，并核验生成的云端文件。注册前先解释引荐关系。

它会检查官方 CLI、区分已有账号与新用户、提交一次云下载任务，并核对任务和文件状态。它补充了官方通用 Skill（`pikpak skill install`）的新手流程。

## 自愿选择引荐

发布者 `jinchengwu888` 是独立推广者。选择邀请码 **78819746** 后，发布者可能从符合条件的会员购买中获得引荐奖励。是否使用邀请码由你决定；本项目不承诺折扣、免费会员或固定价格。实际条款与价格以 PikPak 注册和购买页面为准。

安装 [PikPak 官方 CLI](https://mypikpak.com/en-US/cli) 后，根据情况选择：

```bash
# 已有账号：登录，不重复注册。
pikpak auth login

# 新用户，自愿使用本项目的邀请码：
pikpak auth register --affiliate 78819746

# 新用户，不使用安装器曾记录的邀请码：
pikpak auth register --affiliate=
```

空参数 `--affiliate=` 用于跳过安装器记录的邀请码；仅运行 `auth register` 可能沿用已有记录。你也可以在 PikPak 注册页移除引荐人。Skill 会在注册前说明选择。

密码、验证码和授权操作由你在 PikPak 官方页面完成，Skill 不收集这些信息。CLI 新建账号需在 **72 小时内**登录 PikPak 网页版或 App 完成验证，详见[官方注册说明](https://mypikpak.com/en-US/help-center/connected_apps/pikpak_cli/register_account)。

## 完成第一次保存

提供一个你拥有或获准下载的链接。助手只提交一次任务，保留任务 ID，查询任务进度，再核验云端文件。任务未完成会明确报告“等待中”；报错不会自动重复提交。

文件元数据核验只能确认云端文件存在，不能证明文件完整或视频可播放。需要时再单独检查播放或校验和。功能、额度和价格以你的账号及 PikPak 当前服务条款为准。入门流程不会购买会员、公开分享或删除文件。

请勿在公开 Issue 中粘贴令牌、密码、验证码或私人签名链接。

## 验证范围

2026-11-04 已使用官方 macOS ARM64 CLI **v0.6.0** 验证相关命令的版本和帮助输出，并比对官方 SHA-256；同时进行 Skill 结构校验及独立场景审查。本次没有创建新账号、会员订单或真实云下载任务；其他操作系统未执行测试。[英文说明](README.md#verification-and-sources)列有官方来源。

仅生成注册链接的检查已确认官方注册链接携带本项目邀请码；这项检查没有创建账号，也不代表已产生真实引荐归因。

独立项目，并非 PikPak 官方产品。MIT 许可。
