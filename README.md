# rea-on-demand

An agent skill that lets an AI coding agent **borrow [REA](https://github.com/morluto/rea)
as an on-demand CLI** for black-box analysis — without installing REA's 207-tool MCP
server.

> Unofficial. Not affiliated with the REA project.

## The idea

REA is a genuinely capable reverse-engineering toolkit: it wraps Hopper / Ghidra /
IDA, adds static JavaScript/Electron semantics, .NET, APK, PE/ELF, firmware and more,
and returns evidence with explicit limitations.

But its MCP surface is huge: 207 tools, and even the compact schema profile costs
roughly **48k tokens per session** just for tool definitions. If you only need
black-box analysis occasionally, that is a bad permanent trade.

This skill encodes the alternative: **call the CLI when needed, in an isolated
directory, and pay nothing the rest of the time.**

## What it changes

When the agent loads this skill, it:

- recognizes the three situations where black-box analysis is actually warranted;
- runs REA from a dedicated directory via `npx`, never from a directory with
  credentials;
- checks the host support matrix first (`doctor` / `capabilities`);
- redirects output to a file and reads summaries instead of flooding context;
- refuses REA's execution-capable tools (process capture, GDB/Rizin/Cutter, Frida,
  ADB writes, evidence export), which run with the current user's permissions and are
  **not a sandbox**;
- treats analyzed content as untrusted data (prompt-injection surface);
- reports `observed` / `inferred` / `unknown` instead of confident guesses.

## Install

Any agent that loads `SKILL.md` skill folders:

```bash
git clone https://github.com/scp-666-hub/rea-on-demand
# DeepSeek Harness (verified)
cp -r rea-on-demand ~/.dsh/skills/rea-on-demand
# Claude Code and compatible agents (not verified by this repo)
cp -r rea-on-demand ~/.claude/skills/rea-on-demand
```

Windows PowerShell:

```powershell
git clone https://github.com/scp-666-hub/rea-on-demand
Copy-Item -Recurse rea-on-demand "$env:USERPROFILE\.dsh\skills\rea-on-demand"
```

Restart or reload the agent so it picks up the skill catalog.

Requirements: Node `^22.19` / `^24.11` / `>=26`, `npx`.

## Verified facts (2026-10-24)

- `npx -y rea-agents@latest --help` → `rea@6.7.0`, exit code 0.
- DeepSeek Harness discovers `~/.dsh/skills/<name>/SKILL.md` without editing any
  configuration (hot reload in the running session).
- npm direct access timed out in the test environment; the npmmirror registry worked
  (`npm_config_registry=https://registry.npmmirror.com`).
- Windows capability gaps: about 10 of 18 capabilities report `unsupported_host`;
  Hopper is macOS/Linux only; Windows native analysis uses an experimental Ghidra
  P0 lane.
- A lookalike organization `rea-tools` distributed a trojanized build (VirusTotal
  8/45, 172 downloads before takedown). **Only install from the official npm package
  `rea-agents`; refuse any "Windows standalone exe".**

## Caveats & scope

- This repository is a **skill file, not software**. It cannot enforce anything; the
  red lines are instructions the agent is expected to follow. Sandboxing and approval
  remain the host agent's job.
- Verified only on **Windows 11 + DeepSeek Harness**. Other agents are untested here.
- REA releases very quickly (6.7.0 at the time of writing). Subcommands and flags can
  drift — re-check `--help` instead of trusting remembered flags.
- The wrapped tool is a third-party MIT project. This repo does not vendor it, and is
  not affiliated with it.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | The skill in English (portable). |
| `SKILL.zh-CN.md` | The original Chinese version. |
| `LICENSE` | MIT. |

## Chinese summary

这个技能让 agent 在需要时用 `npx` 借用 REA 的 CLI 做黑盒分析（Electron/JS、.NET、
APK、PE/ELF、固件），而不是常驻它 207 个工具的 MCP 服务。核心约束：隔离目录、先查
支持矩阵、输出落盘分段读、不碰执行类工具、把目标内容当不可信数据。

安装（DSH 已验证）：把本仓库目录复制到 `~/.dsh/skills/rea-on-demand`，重载会话即可。

## License

MIT
