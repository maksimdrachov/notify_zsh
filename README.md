# notify

`notify` is a small Zsh helper for long-running commands. It lets command output pass through normally, waits for the command to finish, and then sends a desktop notification with the exit status and detected command line.

## Why it is useful

When you run something slow like tests, backups, builds, or database imports, it is easy to switch to another window and forget to check when it finishes. `notify` solves that with a simple pattern:

```zsh
notify some_command
```

This is useful because it:

- tells you when a long-running command is done
- shows whether it succeeded or failed
- keeps the command output visible in the terminal while it runs
- falls back to printing the status to `stderr` if `notify-send` is not available

## How it works

`notify` works best when it runs the command directly:

```zsh
notify some_command args...
```

That keeps the command attached to the terminal, so long-running programs continue printing output as they run instead of switching to pipe buffering.

If stdout is redirected but the shell is still interactive, `notify` uses `script` and `tee` to mirror output back to the controlling terminal.

The older pipeline form still works for plain passthrough:

```zsh
some_command | notify
```

But in that mode the upstream command is still writing to a pipe, so some programs may buffer output until the end.

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
notify some_command
```

Examples:

```zsh
notify npm test
notify rsync -av ~/projects ~/backup-drive
notify sleep 30
```

Pipeline passthrough is still available when you already have piped output:

```zsh
some_command | notify
```

## Notes

- `notify` is mainly intended for interactive shell use.
- Prefer `notify cmd ...` for long-running commands so output stays live.
- Use the pipeline form only when you specifically need to consume piped input.
- It is most useful for commands that take long enough that you may switch away from the terminal while they run.
