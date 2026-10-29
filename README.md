# ai-usage

A small CLI that prints how much of your Claude Code and Codex plan usage is left.

```
Claude Code wk [██│░░░░░░░░]  82% left (5d 19h)   5h [█████░░░░░]  50% left (3h 44m)
Codex       wk [██│███░░░░░]  49% left (5d 12h)
Use         claude-code (claude-code weekly usage is 0.8 pts ahead of week elapsed; codex is +30.0 pts)
```

- Filled blocks show usage spent. Light blocks show usage left.
- `│` on a weekly bar marks how far through the week you are. Filled blocks past the marker mean you are using quota faster than an even pace.
- Claude Code shows the weekly and 5-hour windows. Codex shows the weekly window.
- Output is coloured on a terminal. Set `NO_COLOR=1` to turn colour off. Piped output and `--agent` are never coloured.
- `Use` names the recommended harness, picked by the same rule as `--agent` (see below).

## Requirements

- macOS (Claude Code stores its login in the macOS keychain).
- Python 3.9 or newer. No other dependencies.
- You are logged in to Claude Code (`claude`) and Codex (`codex login`) with a subscription plan.

## Install

```sh
curl -o ~/.local/bin/ai-usage https://raw.githubusercontent.com/nazreen/ai-usage/main/ai-usage
chmod +x ~/.local/bin/ai-usage
```

Make sure `~/.local/bin` is on your `PATH`.

## Usage

```sh
ai-usage           # one line per tool, with bars
ai-usage --agent   # JSON for agents choosing a harness
```

### `--agent`

Prints JSON with `recommended_harness`, a `reason`, the `rule` used, and the raw numbers for each window.

The recommended harness is the one with the lowest weekly `pace_delta_pct` (weekly % used minus % of the week elapsed). A harness is skipped if it has 0% weekly quota left, or if Claude Code has under 10% of its 5-hour quota left. If no harness has usable data, `recommended_harness` is `null`.

An agent rule might say: "Before picking a harness, run `ai-usage --agent` and use `recommended_harness`."

## How it works

It calls the same endpoints the official clients use:

- Claude Code: `GET https://api.anthropic.com/api/oauth/usage`, with the OAuth token read from the keychain item `Claude Code-credentials`.
- Codex: `GET https://chatgpt.com/backend-api/wham/usage`, with the token read from `~/.codex/auth.json`.

Tokens are read at run time and sent only to those two hosts. Nothing is written or cached.

These endpoints are not documented public APIs. They can change or break without notice.

## License

MIT
