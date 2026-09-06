# claude-sessions

Tiny dependency-free CLI that lists your Claude Code sessions, live and past,
and lets you pick one by typing a few characters. Green dot = running now,
grey dot = past session you can revive with `claude --resume`.

Live sessions come from the registry Claude Code maintains in
`~/.claude/sessions/<pid>.json` (kept only while the process is alive and still
a `claude` process, to survive PID reuse). Past sessions come from the
transcripts in `~/.claude/projects/*/<session-id>.jsonl`; a small index is
cached in `~/.cache/claude-sessions/` so repeat runs are instant.

## Usage

```
cs                      interactive picker
cs corpo                picker with the filter pre-filled
cs -r                   pick, then revive the session (claude --resume, in its cwd)
cs -R                   same, adding --dangerously-skip-permissions
cs -r -- --model opus   anything after -- is passed through to claude
cs -f                   pick, then bring the live session's terminal tab to front
cs --no-focus           do not focus on Enter
cs -l                   plain table (also used automatically when not on a TTY)
cs --live               only sessions that are currently running
cs -n 20                keep only the 20 most recent past sessions
cs --json [QUERY]       sessions as JSON
cs --cwd  [QUERY]       print the chosen session's working directory
cs --name [QUERY]       print the chosen session's name
```

`cs` and `claude-sessions` are the same script.

Picker keys: type to filter, `↑`/`↓` or `^P`/`^N` to move, `Enter` to select,
`Esc`/`^C` to quit, `^U` clears, `^W` deletes a word.

Matching: each space-separated word must hit. A word matches as a contiguous
substring of the name, AI title, path, status, session ID or pid, or as a
subsequence of a single word of the name, title or path (`crp` finds `corpo`).

Enter on a **live** session brings its Terminal.app or iTerm2 tab to the
front (found by matching the session's tty; skipped when stdout is not a TTY
so command substitution stays quiet, forced with `-f`). `-r`/`-R` on a live
session also just focuses it, since a running session cannot be resumed twice.

The session ID always goes to stdout and a summary to stderr, so it composes
with other commands:

```sh
cd "$(cs --cwd)"
claude --resume "$(cs)"
```

## Display

- **Bold name**: a name you set with `/rename`.
- **`9240ca7e  (CLI for listing sessions)`**: no user-set name, so the session
  ID is shown with Claude's auto-generated title as a hint.
- `●` green = live and idle, yellow = live and busy, `○` grey = past.
- Remaining columns: last activity, status, pid (blank for past sessions).

Sessions are ordered live first, then past, most recently active on top.
Past sessions with no prompts and no name are skipped.

## Install

```sh
ln -sf "$PWD/claude-sessions" ~/.local/bin/cs
ln -sf "$PWD/claude-sessions" ~/.local/bin/claude-sessions
```

Requires Python 3.8+ (macOS/Linux; uses `termios` for the picker).
