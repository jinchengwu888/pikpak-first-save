# 如何用 AI Agent 和 PikPak 官方 CLI 保存链接并核验云端文件

[English guide](first-save-guide.md) · [项目说明](../README.zh-CN.md) · [Skill 原文](../skills/pikpak-first-save/SKILL.md)

PikPak First Save 是采用 MIT 许可的独立 `SKILL.md` 工作流，帮助用户从一个有权下载的链接开始，提交一次云下载、保留任务 ID，再核对生成的云端文件。适合首次使用 PikPak 和排查这次保存任务。

## 安装与提问

先审阅 [Skill 原文](../skills/pikpak-first-save/SKILL.md)，再在支持技能的 Agent 中安装：

```bash
npx skills add jinchengwu888/pikpak-first-save --skill pikpak-first-save
```

可以这样提问，再单独提供自己的链接：

> 使用 pikpak-first-save 保存一个我有权下载的链接。沿用我的 PikPak 账号，只提交一次任务，并检查生成的文件。如果仍在等待，请给我任务 ID 和以后查询的命令。

它是 Agent 的操作说明，并非托管服务或 MCP server。Agent 需要本地命令执行能力、官方 CLI，以及你对账号访问和本次云保存的授权。安装 Skill 不会安装 PikPak CLI，请按[官方当前说明](https://mypikpak.com/en-US/cli)准备 CLI。

## 1. 选择账号路径

先检查 `pikpak --version`；在账号访问已获授权时运行 `pikpak auth status`。已有登录状态就沿用该账号；已有账号但未登录时使用：

```bash
pikpak auth login
```

只有真正需要新账号的用户才进入官方注册页面。发布者 `jinchengwu888` 是独立推广者；你选择邀请码 **78819746** 后，发布者可能从符合条件的购买中获得奖励。选择是自愿的，不要求购买会员，也不承诺折扣。

```bash
# 新用户，明确选择本项目引荐：
pikpak auth register --affiliate 78819746

# 新用户，不选择引荐或尚未作出选择：
pikpak auth register --affiliate=
```

两条是替代选项。空参数可避免沿用安装器记录的邀请码，仅运行 `auth register` 可能使用已有记录。密码、验证码和授权由你在官方页面完成。根据[官方注册说明](https://mypikpak.com/en-US/help-center/connected_apps/pikpak_cli/register_account)，CLI 新建账号需在 **72 小时内**登录 PikPak 网页版或 App 完成验证。

## 2. 提交一个获准保存的链接

以下是命令模板。尖括号需要替换成真实值，不能直接执行，也不代表已经完成下载。先检查本机版本的帮助：

```text
pikpak task add --help
pikpak task add <URL>
```

没有指定其他位置时使用根目录。用户指定已有文件夹时，通过 `pikpak ls` 查询真实 ID，改用 `pikpak task add <URL> -p <folderID>` 提交。

URL 应通过进程参数数组或正确的 shell 转义作为单独参数传入，避免直接拼接不可信链接。只提交一次，并保存返回的真实任务 ID。提交超时不代表任务没有创建，应先查找任务信息，不能马上重复提交。

## 3. 查询任务并核对文件

```text
pikpak task get <taskID>
pikpak ls
pikpak get <fileID>
```

使用 CLI 返回的真实 ID，并查看对应目标目录。先把结果与任务对应，再核对文件身份、名称、位置和报告的大小。文件同名不足以确认；大小为零或缺失、存在多个无法区分的结果、找不到文件，都应保留“核验未完成”。

首次保存的交互流程最多查询五次，等待中每次间隔十秒；出现最终状态即停止。如果仍未完成，就提供查询命令供以后继续。

| 观察到的结果 | 下一步 |
| --- | --- |
| 任务仍在运行 | 保留任务 ID，以后执行 `pikpak task get <taskID>`。 |
| 提交响应超时 | 查询已有任务；无法找回 ID 时明确说明提交状态未知。 |
| 任务失败 | 报告实际错误，分析后再决定是否重试。 |
| 任务完成，但文件缺失或有歧义 | 检查目标目录和返回的 ID，文件核验仍未完成。 |
| 找到对应文件且报告大小非零 | 报告元数据；播放和完整性仍需另行检查。 |

## 常见问题

### 会下载到我的电脑吗？

本流程提交云下载并查询 PikPak 云端元数据，不能据此声称已下载到本机。本地下载需要另外提出请求。

### 任务完成是否说明视频可以正常播放？

不能。元数据只能确认云端结果；播放或校验和需要另行验证。本项目公开验证未进行真实云保存或播放测试。

### 不用邀请码可以使用吗？

可以。已有账号直接登录；新用户可选择 `pikpak auth register --affiliate=`。注册前说明引荐选择，Skill 不覆盖全局引荐设置。

### 这是官方工具或通用客户端吗？

这是围绕官方 CLI 的独立入门工作流。PikPak 自己也通过 `pikpak skill install` 提供更广泛的官方技能。本项目专注首次保存及结果核验。

### 测试过哪些 Agent 和操作系统？

Agent 需要支持 `SKILL.md` 和本地命令执行。2026-11-04 使用官方 macOS ARM64 CLI **v0.6.0** 检查命令帮助，进行结构校验、场景审查及仅生成注册链接的检查；未创建账号、付费订单或真实云任务。其他系统及完整 Agent 端到端流程尚未实测。CLI 更新后以本机帮助为准。

### 可以把私人链接发到公开 Issue 吗？

密码、验证码、令牌、浏览器授权链接和私人签名 URL 都应留在私有操作环境。求助时说明命令和错误，并先去掉敏感值。

## 官方依据与范围

- [官方 CLI 与安装](https://mypikpak.com/en-US/cli)
- [邀请码参数](https://mypikpak.com/referral/help-center/en-US/8-cli-and-developer-distribution/how-to-use-invitation-code-in-pikpak-cli)
- [引荐知情要求](https://mypikpak.com/referral/help-center/en-US/8-cli-and-developer-distribution/what-to-do-after-enabling-affiliate-distribution)
- [浏览器注册与验证](https://mypikpak.com/en-US/help-center/connected_apps/pikpak_cli/register_account)

额度、价格、地区可用性和订单条件以 PikPak 当前页面为准。流程不会购买会员、公开分享、删除文件或批量创建账号。安装次数、网页访问和云任务都不能直接作为引荐收益证明。
