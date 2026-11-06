# How to save a URL to PikPak with an AI agent and the official CLI

[中文教程](first-save-guide.zh-CN.md) · [Project overview](../README.md) · [Skill source](../skills/pikpak-first-save/SKILL.md)

PikPak First Save is an independent, MIT-licensed `SKILL.md` workflow. It helps an agent get a user from one permitted URL to an identifiable cloud file. It uses the official `pikpak` CLI, keeps the task ID, and distinguishes a completed task from a verified file. It is useful for onboarding and investigating that first task.

## Install and ask the agent

Review the [Skill source](../skills/pikpak-first-save/SKILL.md), then install it in an agent that supports skills:

```bash
npx skills add jinchengwu888/pikpak-first-save --skill pikpak-first-save
```

Example request, with your own URL supplied separately:

> Use pikpak-first-save to save one URL I am allowed to download. Keep my existing PikPak account, submit once, and check the resulting file. If the task is pending, give me its ID and the command to check it later.

The skill is documentation for an agent, not a hosted service or an MCP server. Your agent needs a local command tool, the official CLI, and your authorization for account access and the requested cloud save. Installing the skill does not install the PikPak CLI. Follow [PikPak's current installation instructions](https://mypikpak.com/en-US/cli).

## 1. Choose the account path

Check `pikpak --version` and, when authorized, `pikpak auth status`. Reuse an account that is already signed in. For an existing account that needs sign-in:

```bash
pikpak auth login
```

For a genuinely new account, registration happens on PikPak's official page. This project's publisher, `jinchengwu888`, may receive rewards from qualifying purchases if you choose invitation code **78819746**. The choice is optional and does not promise a discount or require buying Premium.

```bash
# New user who explicitly chooses this project's referral:
pikpak auth register --affiliate 78819746

# New user who declines or has not chosen a referral:
pikpak auth register --affiliate=
```

These are alternatives. The empty value avoids inheriting an installer-recorded referral; plain `auth register` can reuse a stored code. Complete credentials and verification on PikPak's own page. Newly CLI-registered accounts must sign in through PikPak Web or the app within **72 hours** for verification, according to the [official registration guide](https://mypikpak.com/en-US/help-center/connected_apps/pikpak_cli/register_account).

## 2. Submit one authorized URL

The following are command templates. Replace angle-bracket placeholders with actual values; they are not executable examples or evidence of a completed download. Check the installed version's help first:

```text
pikpak task add --help
pikpak task add <URL>
```

The default destination is the root when the user has no other preference. For a requested existing folder, resolve its ID with `pikpak ls`; the alternative submission is `pikpak task add <URL> -p <folderID>`.

Pass a URL as one argument using the process tool's argument array or correct shell escaping. Do not paste untrusted URLs into unescaped shell commands. Submit once and keep the actual returned task ID. If the response times out, the task may still have been created; investigate rather than immediately submitting it again.

## 3. Check task status and the resulting file

```text
pikpak task get <taskID>
pikpak ls
pikpak get <fileID>
```

Use real IDs returned by the CLI and the relevant destination listing. Match the result to the task, then check its identity, name, destination, and reported size. A name match alone is insufficient; zero or missing size, multiple indistinguishable candidates, and an absent result remain unresolved.

For an interactive first save, the skill makes at most five status checks ten seconds apart while pending. It stops on a terminal result. A task that remains pending gets a resume command instead of an invented success message.

| Observed result | Useful next step |
| --- | --- |
| Task is still running | Keep the task ID; later run `pikpak task get <taskID>`. |
| Task submission timed out | Check available task information; report unknown submission status if the ID cannot be recovered. |
| Task reports failure | Report its actual error; review it before deciding whether to retry. |
| Task complete, file missing or ambiguous | Inspect the requested destination and returned IDs; keep file verification unresolved. |
| Identified cloud file with nonzero reported size | Report its metadata. Playback and full integrity still need separate checks. |

## 4. Decide whether Premium is useful

First confirm that the saved file meets your need. Premium is optional; it does not fix an invalid URL or an unsupported source.

| Your need | A useful choice |
| --- | --- |
| Occasional saves within your current limits | Keep using the current plan. |
| More cloud storage for regular use | Compare [free 6 GB and Premium 10 TB](https://mypikpak.com/en-US/help-center/file_management/space/insufficient_cloud_storage_space) with your actual account usage. |
| CLI transfers reach the free Connected Apps daily limit | Official help lists a [5 GB daily downstream limit for free accounts](https://mypikpak.com/en-US/help-center/transfer_quota/connected_apps_quota/free_account_daily_downstream_limit), removed with Premium. [Monthly transfer quotas and the Connected Apps sublimit still apply](https://mypikpak.com/en-US/help-center/transfer_quota/connected_apps_quota/difference_from_monthly_quota); this is not unlimited traffic. |

Downstream means traffic for viewing, playback or downloads to a device, not the number of links saved into the cloud. Plan facts were checked on **2026-11-06**. If Premium addresses a real need:

1. For a new CLI-created account, complete the required Web/App verification within **72 hours**. Use the same PikPak account as the first save.
2. [Open PikPak's official plan and purchase page](https://mypikpak.com/drive/payment), then check the signed-in account.
3. Review the price, currency, billing period, renewal terms, refund conditions, taxes and available payment methods shown for that account. Choose a plan only if it fits your need and budget.
4. Complete any purchase yourself on PikPak's official page. The agent does not enter payment details, place an order, or buy a subscription.

If checkout fails, follow [PikPak's official payment help](https://mypikpak.com/en-US/help-center/premium/payment_and_billing/failure_to_pay). If already charged but Premium is missing, follow [the activation help](https://mypikpak.com/en-US/help-center/premium/payment_and_billing/premium_not_activated) rather than paying again.

Registration and payment steps do not guarantee referral attribution or rewards; PikPak determines eligibility. No purchase is needed to keep using this Skill.

## Common questions

### Does this download a file onto my computer?

This workflow submits a cloud-download task and checks PikPak's cloud metadata. It does not establish a local download. A local download is a separate user request.

### Does a completed task mean a video plays correctly?

No. Metadata confirms an identifiable cloud result; playback or a checksum requires a separate check. The project has not performed a live cloud-save or playback test as part of its published validation.

### Can I use it without the invitation code?

Yes. Existing users log in. New users can choose `pikpak auth register --affiliate=`. Referral choices are explained before registration; the skill does not overwrite a global referral setting.

### Is it an official PikPak tool or a general-purpose client?

It is an independent onboarding workflow around the official CLI. PikPak also provides its own broader skill via `pikpak skill install`. This project's narrow use is one first save and its verification.

### Which agents and operating systems have been tested?

Agents must support `SKILL.md` and local CLI execution. Published command checks used the official macOS ARM64 CLI **v0.6.0** on **2026-11-04**. They covered help output, structure, scenario review, and a signup-link-only check. They did not create an account, paid order, or live cloud task. Other operating systems and end-to-end agent runs were not tested. Use current CLI help if commands change.

### Can I share a private URL in a public support issue?

Keep passwords, OTPs, tokens, browser authorization links, and private signed URLs out of public issues. Describe the command and error with sensitive values removed.

## Sources and scope

- [Official CLI and installation](https://mypikpak.com/en-US/cli)
- [Official referral-code integration](https://mypikpak.com/referral/help-center/en-US/8-cli-and-developer-distribution/how-to-use-invitation-code-in-pikpak-cli)
- [Official referral disclosure requirement](https://mypikpak.com/referral/help-center/en-US/8-cli-and-developer-distribution/what-to-do-after-enabling-affiliate-distribution)
- [Official browser registration and verification](https://mypikpak.com/en-US/help-center/connected_apps/pikpak_cli/register_account)

Quotas, pricing, regional availability, and purchase conditions come from PikPak's current service and account pages. This workflow does not buy Premium, create public shares, delete files, or create accounts in bulk. Its installation, a website visit, and a cloud task are not proof of a referral reward.
