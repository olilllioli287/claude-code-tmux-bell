# tmux-bell

> **已經搬到 [tmux-kit](https://github.com/olilllioli287/tmux-kit)（2026-10-15）。** 這裡不再更新，新安裝請用 tmux-kit：裝它的第 3 項「Claude 回完反白」，會自動換掉這裡的舊版本。已經裝了這裡的版本也能繼續用。
>
> **Moved to [tmux-kit](https://github.com/olilllioli287/tmux-kit).** This repo is no longer updated; installing tmux-kit's tmux-bell replaces this one automatically.

**繁體中文** | [English](#english)

Claude Code 的 mod（外掛）。在 tmux 裡開好幾個分頁、每個分頁跑一個 Claude Code 時，哪個 Claude **回覆完了**或**在等你允許**，下面狀態列的那個分頁就會反白，切過去看之後自動恢復。

```
 Session 0 |[1] 聊天  [2] 遊戲  [3] 作業  ▌[4] zsh▐   ← 4 號分頁的 Claude 回完了
```

## 為什麼需要

tmux 本來就會把「響了一聲（BEL）」的分頁反白，但 Claude Code 在 iTerm2 裡預設只送 iTerm2 自己的通知（OSC 9），不會響，所以 tmux 不知道哪個分頁的 Claude 回完了。這個外掛在 Claude 停下來的時候，替它在自己那格響一聲。

## 安裝

在 Claude Code 的輸入框打：

```
/plugin install tmux-bell --marketplace olilllioli287/claude-code-tmux-bell
```

問要不要加入來源時按 `y`，範圍選 user。已經開著的 Claude Code 可能要重開才會用上。

## 移除

```
/plugin uninstall tmux-bell@tmux-bell
```

想連來源一起移除：`/plugin marketplace remove tmux-bell`。

## 什麼時候會反白

- Claude 回覆完（`Stop` hook）
- Claude 要你允許某個動作，或有問題要你回答（`Notification` hook 的 `permission_prompt`、`elicitation_dialog`）

你正在看的那個分頁不會響，所以不會每次回覆都嗶一聲。不在 tmux 裡的 Claude Code 完全不受影響。

## 需求

- tmux（3.0 以後都可以），Claude Code 要在 tmux 的格子裡執行
- macOS 或 Linux；不需要編譯，只用到 `sh` 和 `tmux`

## 沒有反白的話

tmux 的預設值就會反白。如果你的 `~/.tmux.conf` 改過下面這些設定，檢查一下：

```
set -g monitor-bell on                 # 要開著(預設 on)
set -g bell-action any                 # any 或 other(預設 any);none 會整個忽略
set -g window-status-bell-style reverse  # 反白的樣子,可以改成例如 fg=black,bg=yellow
```

另一個分頁響的時候，tmux 也會把響聲傳給終端機，iTerm2 可能會發出聲音或閃一下。不想要聲音的話，在 tmux 加 `set -g visual-bell on`（改成在狀態列顯示一行訊息），或在 iTerm2 的 Settings → Profiles → Terminal 勾選 Silence bell。

## 原理

`hooks/tmux-bell.sh` 用 `$TMUX_PANE` 找到 Claude Code 所在的格子，讀出那格的終端機裝置（`#{pane_tty}`），寫一個 `\a` 進去。tmux 會把它當成那格的程式響了一聲，照 `monitor-bell` 和 `window-status-bell-style` 標記分頁。如果那個分頁就是目前畫面上的分頁（`#{window_active}`），而且有人在看這個工作階段（`#{session_attached}`），就不送。

## 授權

MIT

---

<a id="english"></a>

# tmux-bell (English)

A Claude Code plugin. When you run several Claude Code sessions in different tmux windows, the window whose Claude **just finished replying** or **is waiting for your permission** gets highlighted in the tmux status line, until you switch to it.

## Why

tmux already highlights windows that ring the terminal bell (BEL), but Claude Code in iTerm2 only sends iTerm2's own notification (OSC 9) by default and never rings the bell, so tmux can't tell which window's Claude is done. This plugin rings the bell in Claude Code's own pane when it stops.

## Install

In Claude Code:

```
/plugin install tmux-bell --marketplace olilllioli287/claude-code-tmux-bell
```

Answer `y` to adding the marketplace and pick the user scope. Sessions that were already running may need a restart.

## Uninstall

```
/plugin uninstall tmux-bell@tmux-bell
```

To also remove the marketplace: `/plugin marketplace remove tmux-bell`.

## When it rings

- Claude finishes a reply (`Stop` hook)
- Claude asks for permission or asks you a question (`Notification` hook: `permission_prompt`, `elicitation_dialog`)

It stays quiet for the window you are looking at, and does nothing outside tmux.

## Requirements

- tmux (3.0 or later), with Claude Code running inside a tmux pane
- macOS or Linux; only `sh` and `tmux` are used, nothing to compile

## No highlight?

The tmux defaults work. If your `~/.tmux.conf` changes any of these, check them:

```
set -g monitor-bell on                   # must be on (default)
set -g bell-action any                   # any or other (default any); none ignores bells
set -g window-status-bell-style reverse  # how the tab looks, e.g. fg=black,bg=yellow
```

tmux also passes the bell on to your terminal, so iTerm2 may beep or flash. To avoid the sound, add `set -g visual-bell on` to tmux (shows a status-line message instead), or tick Silence bell in iTerm2 → Settings → Profiles → Terminal.

## How it works

`hooks/tmux-bell.sh` finds Claude Code's pane from `$TMUX_PANE`, reads its terminal device (`#{pane_tty}`) and writes a `\a` to it. tmux treats that as the pane's program ringing the bell and marks the window according to `monitor-bell` and `window-status-bell-style`. Nothing is sent when that window is the current one (`#{window_active}`) of a session someone is attached to (`#{session_attached}`).

## License

MIT
