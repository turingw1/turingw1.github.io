---
title: "From Linux to Mac, then stitching them together with Tailscale"
date: 2026-06-22
permalink: /posts/2026/06/linux-to-mac-tailscale/
excerpt: "A beginner-friendly field guide to moving from Ubuntu to macOS without losing your terminal workflow — and using Tailscale to turn your laptop and your lab GPU box into one private network."
tags:
  - workflow
  - macOS
  - Linux
  - Tailscale
  - remote development
---

<div class="lang-switch">
  <a href="#zh">中文</a>
  <a href="#en">English</a>
</div>

> 写给和我一样、从 Linux 一路命令行长大、第一次换到 Mac 会有点慌的同学。结论先放这里：**该保留的习惯基本都能保留，剩下的用 Homebrew 补齐；再用 Tailscale 把 Mac 和实验室的 GPU 机器连成一张私有网，随时随地开发。** 一个下午就能搞定。

## 中文版 {#zh}

### 一、换到 Mac，我到底在慌什么

我之前一直用 Ubuntu 做开发，`apt`、`systemctl`、`ssh` 是肌肉记忆。换到 Mac 之前最大的担心是：**我的终端工作流会不会全废？** 真正上手之后发现，90% 的习惯是可以无缝迁移的——变的只是底层的"水管"，不是你敲命令的方式。

<figure class="ac-figure">
  <img src="/images/blog/linux-to-mac-map.svg" alt="把 Linux 常用操作映射到 macOS 的对应工具">
  <figcaption>心智迁移一张图：终端肌肉记忆基本还在，变的只是底层工具。<code>ssh / scp / rsync</code> 一个字都不用改。</figcaption>
</figure>

关键的对应关系：

- **装软件**：`apt install` → `brew install`。先装 [Homebrew](https://brew.sh)，之后 `brew install git tmux ripgrep fzf neovim wget` 一把梭。
- **后台服务**：`systemd` → `launchd`，日常用 `brew services start <name>` 就够了。
- **终端**：GNOME Terminal + bash → 我用 **iTerm2 + zsh**（自带 oh-my-zsh 生态），也可以试新秀 Ghostty。
- **窗口管理**：习惯 i3 平铺的话，装 **Rectangle**（免费）找回键盘流；再配合 Spaces / Mission Control。
- **`ssh` / `scp` / `rsync`**：**完全不变**。这是我最想强调的一点——远程那套东西原样搬过来就行。

### 二、把 Mac 调成一台趁手的开发机

我的最小配置清单：

```bash
# 1. Homebrew（装完按提示把 brew 加进 PATH）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. 命令行主力
brew install git tmux ripgrep fzf neovim jq wget htop

# 3. Python 环境（二选一：传统 conda，或更快的 uv）
brew install --cask miniconda        # 或者： brew install uv

# 4. 终端 & 字体
brew install --cask iterm2
brew install --cask font-jetbrains-mono
```

再把 **dotfiles 用 git 管理**（`.zshrc`、`.tmux.conf`、`nvim/`），换机器时 `git clone` 一下就回到熟悉的环境。这一步是从 Linux 带过来最值钱的习惯。

### 三、真正的主角：用 Tailscale 把设备连成一张私有网

换 Mac 之后最现实的问题是：**重活还在实验室那台 GPU 机器上跑**。它蹲在校园内网、NAT 后面，没有公网 IP。传统做法是跳板机 + 端口转发 + 一堆 `ProxyJump` 配置，既麻烦又容易把端口暴露到公网。

[Tailscale](https://tailscale.com) 把这件事变得几乎不用动脑：它基于 WireGuard，在你的所有设备之间拉起一张点对点加密的 mesh 网络（叫 *tailnet*）。每台设备登录同一个账号后，自动分到一个稳定的 `100.x` 地址和一个好记的名字（MagicDNS）。

<figure class="ac-figure">
  <img src="/images/blog/tailscale-topology.svg" alt="Tailscale 把 MacBook、GPU 工作站、手机、NAS 连成一张加密 mesh">
  <figcaption>不管人在咖啡馆还是宿舍，所有设备都在同一张扁平的私有网里，直连、加密、自动穿透 NAT。</figcaption>
</figure>

三步接入：

```bash
# 在 Mac 上
brew install --cask tailscale
# 在 GPU 机器上（Ubuntu）
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up

# 两台都登录同一个账号后：
tailscale status          # 看看谁在线，各自的 100.x 地址
ssh gpu-box               # 直接用 MagicDNS 名字连，不用记 IP
```

接好之后我的日常就变成了：

- 咖啡馆里用 MacBook 写代码，**VS Code 的 Remote-SSH 直连 `gpu-box`**，编辑器在本地、算力在实验室；
- 想省去配公钥，可以开 `tailscale ssh`，用设备身份直接免密登录；
- 手机装上 Tailscale，随时连回去 `tmux attach` 看训练日志；
- NAS 也拉进 tailnet，`rsync` 自动备份 checkpoint。

### 四、为什么这套组合更安全

这点值得单独说，因为它正好和"公开访问要更安全"的思路一致：

- **不暴露公网端口**：GPU 机器不用开放 22 端口到公网，扫描器根本看不到它；
- **端到端加密**：流量走 WireGuard，中间节点看不到内容；
- **设备级授权**：每台设备一把 key，丢了手机就在后台一键下线那台设备；
- **最小权限**：可以用 ACL 限定"只有我的 Mac 能 SSH 到 GPU 机器"，其它设备连不上。

### 五、几个新手容易踩的小坑

- **MagicDNS 记得在管理后台打开**，否则只能用 `100.x` 裸 IP；
- 某些校园/公司网络会挡直连，Tailscale 会自动回退到官方 DERP relay，能用但稍慢，`tailscale ping gpu-box` 可以看是直连还是中继；
- key 默认会过期，长期跑的服务器可以在后台把该设备设成 **key expiry disabled**；
- Mac 上第一次要在"系统设置 → 隐私与安全性"里放行 Tailscale 的网络扩展。

**一句话总结**：换 Mac 没那么可怕，Homebrew 补齐工具、dotfiles 找回环境、Tailscale 连回算力——一个下午，你就有了一套"人在哪里，开发环境就在哪里"的工作流。

---

## English version {#en}

> For anyone who grew up in the Linux terminal and feels a little anxious switching to a Mac. Short version: **you keep almost all your habits, fill the gaps with Homebrew, and use Tailscale to fuse your laptop and your lab's GPU box into one private network.** An afternoon's work.

### 1. What was I actually afraid of?

I did all my development on Ubuntu — `apt`, `systemctl`, `ssh` were muscle memory. My big fear before switching was: *will my whole terminal workflow break?* It doesn't. About 90% of it transfers untouched; only the plumbing underneath changes, not the way you type commands.

<figure class="ac-figure">
  <img src="/images/blog/linux-to-mac-map.svg" alt="Mapping common Linux habits to their macOS equivalents">
  <figcaption>The mental-model map: your terminal reflexes survive. <code>ssh / scp / rsync</code> don't change at all.</figcaption>
</figure>

The key mappings: `apt install` → `brew install`; `systemd` → `launchd` (day-to-day, `brew services`); GNOME Terminal + bash → **iTerm2 + zsh**; i3 tiling → **Rectangle** + Spaces; and `ssh/scp/rsync` stay exactly the same — that last one is the part that matters most.

### 2. Turning the Mac into a real dev machine

My minimal setup:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install git tmux ripgrep fzf neovim jq wget htop
brew install --cask miniconda        # or: brew install uv
brew install --cask iterm2 font-jetbrains-mono
```

Then keep your **dotfiles in git** (`.zshrc`, `.tmux.conf`, `nvim/`). A single `git clone` on a new machine and you're home — the most valuable habit I carried over from Linux.

### 3. The real star: one private network with Tailscale

The practical problem after switching: **the heavy jobs still run on the lab GPU box**, which sits behind campus NAT with no public IP. The traditional answer is a bastion host plus port forwarding plus a pile of `ProxyJump` config — fiddly, and it tends to expose ports to the internet.

[Tailscale](https://tailscale.com) makes this almost thought-free. Built on WireGuard, it stands up a peer-to-peer encrypted mesh (a *tailnet*) between all your devices. Log each one into the same account and it gets a stable `100.x` address and a friendly name via MagicDNS.

<figure class="ac-figure">
  <img src="/images/blog/tailscale-topology.svg" alt="A Tailscale tailnet connecting a MacBook, GPU workstation, phone and NAS">
  <figcaption>Café or dorm, every device lives on the same flat private network — direct, encrypted, NAT-traversing.</figcaption>
</figure>

```bash
# on the Mac
brew install --cask tailscale
# on the GPU box (Ubuntu)
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up

tailscale status      # who's online, and their 100.x addresses
ssh gpu-box           # connect by MagicDNS name, no IPs to memorize
```

Day-to-day this becomes: write code on the MacBook with **VS Code Remote-SSH into `gpu-box`** (editor local, compute in the lab); optionally enable `tailscale ssh` for keyless, identity-based login; attach to `tmux` from my phone to watch training logs; and pull the NAS into the tailnet so `rsync` backs up checkpoints automatically.

### 4. Why this is also *safer*

Nicely aligned with "make public access safer": **no public ports** (the GPU box never exposes port 22 to the internet, so scanners can't see it); **end-to-end encryption** via WireGuard; **per-device keys** (lost your phone? revoke that one device); and **least privilege** via ACLs (e.g. only my Mac may SSH to the GPU box).

### 5. Beginner gotchas

Turn on **MagicDNS** in the admin console or you're stuck with raw `100.x` IPs; some campus networks block direct connections and Tailscale falls back to a DERP relay (`tailscale ping gpu-box` tells you which); disable **key expiry** for long-running servers; and on the first run, approve Tailscale's network extension under *System Settings → Privacy & Security*.

**In one line:** switching to a Mac isn't scary — Homebrew for the tools, dotfiles for the environment, Tailscale for the compute — and you end up with a workflow that follows you wherever you are.
