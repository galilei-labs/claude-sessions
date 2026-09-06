# claude-sessions

Tiny dependency-free CLI that lists the Claude Code sessions currently running
on this machine and lets you pick one by typing a few characters.

It reads the registry Claude Code maintains in `~/.claude/sessions/<pid>.json`
and keeps only entries whose process is still alive (and still a `claude`
process, to survive PID reuse).

## Usage

```
cs                      interactive picker
cs corpo                picker with the filter pre-filled
cs -l                   plain table (also used automatically when not on a TTY)
cs --json [QUERY]       live sessions as JSON
cs --cwd  [QUERY]       print the chosen session's working directory
cs --name [QUERY]       print the chosen session's name
cs --all                include stale registry entries (dead processes)
```

`cs` and `claude-sessions` are the same script.

Picker keys: type to filter, `↑`/`↓` or `^P`/`^N` to move, `Enter` to select,
`Esc`/`^C` to quit, `^U` clears, `^W` deletes a word.

Matching: each space-separated word must hit. A word matches as a contiguous
substring of the name, path, status, session ID or pid, or as a subsequence
of a single word of the name or path (`crp` finds `corpo`).

On selection the session ID goes to stdout and a summary to stderr, so it
composes with other commands:

```sh
cd "$(cs --cwd)"
claude --resume "$(cs)"
```

## Display

- **Bold name**: a name you set with `/rename`.
- **`9240ca7e  (projects-81)`**: no user-set name, so the session ID is shown
  with Claude's auto-derived name as a hint.
- `●` green = idle, yellow = busy; columns are last activity, status, pid.

## Install

```sh
ln -sf "$PWD/claude-sessions" ~/.local/bin/cs
ln -sf "$PWD/claude-sessions" ~/.local/bin/claude-sessions
```

Requires Python 3.8+ (macOS/Linux; uses `termios` for the picker).
