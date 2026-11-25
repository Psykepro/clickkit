# ClickKit

Click a file path in Claude Code's terminal output and it opens in your editor at the right line. Copy any path, command or code block with one button instead of selecting it by hand.

> Part of the upcoming **Agentic Engineering Operating System (AEOS)**, a toolkit for working with coding agents that I'm building and will release. This plugin works on its own today.

## Install

```
/plugin install clickkit --marketplace Psykepro/clickkit
```

Or from a shell: `claude plugin marketplace add Psykepro/clickkit`, then `claude plugin install clickkit@clickkit`. Needs a Claude Code version with terminal mod support.

## Behavior

- Matches absolute, `~/`, and relative paths with a `/` (`src/a.ts`, `../x/y.py`), optionally `:line`. Relative paths resolve against the session working directory.
- Only paths that exist get buttons. Table rows are left as text.
- An inline code span with two or more words (a command) gets no path buttons; it shows as written with one `⧉` button that copies the whole command, pipes and quotes included.
- Any multi-line text gets one `⧉ copy` button that copies the whole body, whatever it holds (a Slack message, a prompt, a config, a command list): a fenced block (backticks or `~~~`, any length, any language label or none), or an inline backtick span that runs over several lines. The message does not need to contain a path.
- Open runs `<editor> <project root> -g <file>[:line]`.
- Copy asks the host clipboard (`$.ui.copy`) first, then tries `pbcopy` (then `/usr/bin/pbcopy` for hosts with a stripped PATH), `wl-copy`, `xclip -selection clipboard`, then `clip.exe`; the first that succeeds wins.

## Settings (environment variables)

| variable | default | meaning |
|---|---|---|
| `CLICKKIT_EDITOR` | `code` | editor command; must accept `<root> -g <file>:<line>` (VS Code, Cursor, Windsurf, VSCodium) |
| `CLICKKIT_ROOT_MARKERS` | `.git` | comma list of file/folder names marking a project root, tried in order; no match opens the file's folder |
| `CLICKKIT_COPY_CMD` | unset | clipboard command (reads the path on stdin), replaces the built-in list, e.g. `xsel -ib` |

## What it runs and what it sends

- **Reads:** the text of assistant messages (to find file paths and commands) and checks on disk whether each path exists. Nothing else is read.
- **Runs, on a button press only:** your editor (`CLICKKIT_EDITOR`, default `code`) as `<editor> <project root> -g <file>[:line]`, and a clipboard tool (`pbcopy`, `wl-copy`, `xclip -selection clipboard`, `clip.exe`, or the command you set in `CLICKKIT_COPY_CMD`) with the path or command text on stdin. The editor and clipboard commands come only from those fixed names and your own environment variables; no other program is started.
- **Sends:** nothing. The mod makes no network calls, has no telemetry and stores nothing. Data leaves the mod only as the text handed to your own clipboard tool or the path handed to your own editor.

## License

MIT

## Renamed from open-file-links

This plugin was called `open-file-links` up to 0.4.2. The name changed in 0.5.0 because it now copies commands and text blocks as well as opening files. The settings changed with it: `OPEN_FILE_EDITOR`, `OPEN_FILE_ROOT_MARKERS` and `OPEN_FILE_COPY_CMD` are now `CLICKKIT_EDITOR`, `CLICKKIT_ROOT_MARKERS` and `CLICKKIT_COPY_CMD`.
