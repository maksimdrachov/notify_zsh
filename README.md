# notify

`notify` is a small Zsh helper for long-running commands. It lets command output pass through normally, waits for the command to finish, and then sends a desktop notification with the exit status and detected command line.

## Why it is useful

When you run something slow like tests, backups, builds, or database imports, it is easy to switch to another window and forget to check when it finishes. `notify` solves that with a simple pattern:

```zsh
some_command | notify
```

This is useful because it:

- tells you when a long-running command is done
- shows whether it succeeded or failed
- keeps the command output visible in the terminal while it runs
- falls back to printing the status to `stderr` if `notify-send` is not available

## How it works

`notify` is designed to be the last step in a pipeline. It reads the command output with `cat`, so the output still appears in your terminal, then sends a notification after the pipeline finishes.

If the command exits with a non-zero status, the notification urgency is raised to `critical`.

## Installation

1. Copy the function to your home directory:

```zsh
cp .notify_function ~/.notify_function
```

2. Add this line to your `~/.zshrc`:

```zsh
[[ -f "$HOME/.notify_function" ]] && source "$HOME/.notify_function"
```

3. Reload your shell:

```zsh
source ~/.zshrc
```

## Requirements

- `zsh`
- `notify-send` for desktop notifications on Linux

If `notify-send` is missing, `notify` still works, but it prints the status and command to `stderr` instead of showing a desktop popup.

## Usage

Basic usage:

```zsh
some_command | notify
```

Examples:

```zsh
npm test | notify
rsync -av ~/projects ~/backup-drive | notify
sleep 30 | notify
```

## Notes

- `notify` is mainly intended for interactive shell use.
- Put it at the end of the command you want to watch.
- It is most useful for commands that take long enough that you may switch away from the terminal while they run.
