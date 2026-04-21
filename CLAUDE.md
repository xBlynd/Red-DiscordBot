# Red-DiscordBot Fork — Engineering Reference

> Save this as `CLAUDE.md` in the root of the Red-DiscordBot fork when cloned.
> This file governs ALL work done inside this repo — Claude, Gemini, human.
> Cog work is NEVER done here. This repo is for Red core contributions only.

---

## Who Is Working Here

**Blynd (xBlynd)** — credited contributor to Red-DiscordBot, returning to active
contribution. PRs submitted from this fork go upstream to `Cog-Creators/Red-DiscordBot`.
The bar is high. Red's core team reviews everything and can veto without explanation.
Write code that passes review on the first pass.

---

## Repo Structure

```
github.com/xBlynd/Red-DiscordBot   ← this fork
github.com/Cog-Creators/Red-DiscordBot  ← upstream (the real repo)
```

**This fork's `V3/develop` must always track upstream's `V3/develop`.**
The fork was last synced May 2021. Before doing any work, sync it:

```bash
git remote add upstream https://github.com/Cog-Creators/Red-DiscordBot.git
git fetch upstream
git checkout V3/develop
git merge upstream/V3/develop
git push origin V3/develop
```

---

## Branch Rules

```
V3/develop          — tracks upstream, never commit directly here
V3/fix/<name>       — bug fixes, branched off V3/develop
V3/feature/<name>   — features (needs prior issue approval), off V3/develop
```

- One branch = one PR. Never bundle unrelated changes.
- All PRs target `Cog-Creators/Red-DiscordBot` `V3/develop`.
- Never PR against master or any other branch.

---

## Before Writing Any Code

**Bug fixes:**
1. Check if an issue exists in `Cog-Creators/Red-DiscordBot`. If not, create one.
2. Branch: `git checkout -b V3/fix/<short-description>`
3. Fix, test, format, PR.

**New features:**
1. Open an issue in `Cog-Creators/Red-DiscordBot` first.
2. Wait for discussion and explicit approval from the core team.
3. Do NOT write code before approval — Red's team can and will close PRs for
   unapproved features even after significant work.

**New cogs to Red core:**
- Extremely high bar. Requires extensive discussion. Do not propose without
  researching what's already in core and what's been rejected before.

---

## Code Standards — Non-Negotiable

### Python
- **Python 3.8.1+ only.** No syntax or stdlib features from 3.9, 3.10, 3.11+.
  - No `dict | dict` union syntax (3.9+)
  - No `match` statements (3.10+)
  - No `str | None` type hints inline — use `Optional[str]` from `typing`
  - Check `python_requires` in `setup.py` if unsure
- Type hints on all new/modified function signatures.
- No bare `except:` — always `except SomeSpecificException:`.
- No `print()` statements — use `logging`.
- No hardcoded Discord IDs anywhere.

### Formatting
- **Black**, 99-character line limit.
- Run before every commit: `make reformat`
- CI enforces this — a non-Black PR will fail immediately.

### Cross-Platform
- Windows + macOS + Linux. All three must work.
- No `os.system()`. No Windows-only path separators in code. Use `pathlib.Path`.
- Test mentally on all three platforms before submitting.

### i18n
- Any user-facing string in existing internationalized cogs MUST use `_()`.
- Do not add new hardcoded strings to cogs that already use i18n.
- New cogs contributed to core should support i18n from the start.

### Testing
- Write tests for new code and bug fixes. No exceptions.
- Run full test suite before submitting: `tox`
- Individual suites:
  - `tox -e tests` — unit tests
  - `tox -e docs` — documentation build
  - `tox -e style` — Black + flake8
- **PR will not be merged until all tox checks pass.**

---

## Dev Environment Setup

```bash
# Clone your fork
git clone https://github.com/xBlynd/Red-DiscordBot.git
cd Red-DiscordBot

# Add upstream
git remote add upstream https://github.com/Cog-Creators/Red-DiscordBot.git

# Set up dev environment (creates venv, installs all deps)
make newenv

# Activate venv
# Windows:
.venv\Scripts\activate
# Mac/Linux:
source .venv/bin/activate

# Reformat code
make reformat

# Run all checks
tox
```

This venv is SEPARATE from xsvStudio-Bot's venv. Never cross them.

---

## Commit Style

- Descriptive imperative sentences. Clear subject line.
- **ZERO `Co-Authored-By` lines.** No AI attribution. No tool names.
- Examples:
  - `Fix bank account balance returning negative value on transfer`
  - `Add timeout parameter to HTTP requests in streams cog`
  - `Update docs for Config.get_conf identifier requirements`
- Do not reference internal ticket numbers that upstream can't see.
- Do not write "fixes #123" unless you have confirmed the issue number in
  the upstream `Cog-Creators/Red-DiscordBot` issue tracker.

---

## PR Submission Checklist

Before opening a PR against upstream:

- [ ] Fork's `V3/develop` is synced with upstream
- [ ] Branch is based off current `V3/develop`  
- [ ] `tox` passes locally (all suites)
- [ ] All new code has type hints
- [ ] All user-facing strings use `_()` if in an i18n cog
- [ ] No bare `except:` blocks
- [ ] No hardcoded IDs
- [ ] No `print()` statements
- [ ] Cross-platform compatible
- [ ] `make reformat` was run
- [ ] PR description clearly states: what the problem is, what the fix does, how to reproduce/test

---

## Review Process Timeline

- **Bug fixes**: Core team reviews → 1 day owner approval/veto window → merge.
- **New features**: Core team reviews → 1 week owner approval/veto window → merge.
- Respond to feedback within 1 week. Unresponsive PRs get closed.
- If a core member requests changes: make them, push to the same branch. The PR updates automatically.

---

## What Does NOT Belong Here

- xsvStudio private cogs → `xsvStudio-Bot` repo
- Cogboard public cogs → `eve-cogs` repo
- Anything that is "a cog for my bot" — wrong repo entirely

If work in this repo reveals a missing API or bug that affects cog development,
fix it here and PR upstream. Then reference the upstream fix in the cog repo
with a comment and wait for the release before using it.

---

## Key Red Internals to Know

- `redbot.core.commands` — command decorators, hybrid commands, checks
- `redbot.core.Config` — persistent storage (never bypass this with raw files)
- `redbot.core.bot.Red` — the bot class (type-hint `bot: Red`)
- `redbot.core.i18n.Translator` — i18n (`_ = Translator("CogName", __file__)`)
- `redbot.core.utils.chat_formatting` — box(), pagify(), bold(), etc.
- `redbot.core.utils.menus` — pagination helpers
- Permission checks: `commands.admin_or_permissions()`, `commands.mod_or_permissions()`,
  `commands.guildowner_or_permissions()` — use these, not raw Discord perms.
