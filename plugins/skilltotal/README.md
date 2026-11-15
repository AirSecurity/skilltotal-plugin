# SkillTotal for Claude Code

A Claude Code plugin that shows SkillTotal's verdict at the moments that matter, whether or not the model thinks to ask:

| When | What you see | What it changes |
|---|---|---|
| Before a skill runs | `✓ SkillTotal: Safe · pdf-tools · this exact version · <report>` | Unsafe or Potential risk asks first. A copy with no report that shares its name with a blocked skill asks first. |
| Before `npx skills add …` (or a `git clone` into `skills/` or `.claude/`) | `✗ SkillTotal: Unsafe · env-doctor · found: … · <report>` | Unsafe is denied, Potential risk asks. In the default permission mode the verdict is shown in the approval prompt. |
| At session start | `SkillTotal checked 14 installed skills: 13 Safe, 1 not checked yet` | Needs an API key. |

Verdicts read as they do on skilltotal.com: a skill the scan found built to attack is shown as Unsafe, as on its report page.

Safe and "not checked yet" never approve anything: Claude Code's own permissions still decide. If SkillTotal can't be reached, the tool runs and the line says it wasn't checked.

**It needs an API key from an approved account.** Claude Code asks for one when you enable the plugin. While SkillTotal is open to approved accounts only, a key from an account still on the waitlist gets nothing checked, and the plugin says so with a link to the waitlist. Without a key, with one SkillTotal refuses, or with one that expired (the plugin says when), it asks you for one:
- at session start;
- each time a skill runs, which it lets run unchecked;
- before an install, which always asks "Install it unchecked?".

**Make the plugin's key with no expiry.** New keys last 90 days by default, and checks stop when the key expires. When you make the key, choose **Never** under expiry, or use your Default key, which never expires.

**Only a hash leaves your machine.** A skill is identified by its content hash (the same version identity an upload of its folder gets on skilltotal.com), never by sending its files.

It also adds SkillTotal's MCP server (`check_addon` and friends), so Claude can look things up itself.

## When AIR's plugin is installed

If AIR's own plugin (`airctl-claude-plugin`, from any marketplace) is installed and enabled, it already checks add-ons, so this plugin stays quiet to give you one check, not two. The `Skill` and `Bash` hooks do nothing (no output, no lookup), and session start shows one line: "AIR's plugin is checking add-ons here, so SkillTotal's plugin stays quiet."

"Installed" means an `airctl-claude-plugin@<marketplace>` entry in `~/.claude/plugins/installed_plugins.json`. "Enabled" means that same key is `true` in `enabledPlugins` after Claude Code's settings are merged: user (`~/.claude/settings.json`), then project (`.claude/settings.json`), then local (`.claude/settings.local.json`), where a later file overrides an earlier one. A missing entry counts as not enabled, so a doubtful case checks twice rather than not at all. Disable AIR's plugin (even for one project) and this plugin checks again.

Any plugin named `airctl-claude-plugin` silences the check, whichever marketplace it came from. Project and local settings are read from `CLAUDE_PROJECT_DIR` (the project root Claude Code gives hooks), and the user settings and installed plugins from `CLAUDE_CONFIG_DIR` when set, else `~/.claude`.

Set `SKILLTOTAL_FORCE=1` to keep SkillTotal's checks on regardless.

## Install

```
/plugin marketplace add AirSecurity/skilltotal-plugin
/plugin install skilltotal@skilltotal
```

Then make a key at skilltotal.com/account/settings/api-keys (expiry: **Never**) and paste it when Claude Code asks. To change it later, run `/plugin configure skilltotal`.

## Settings

| Setting | Default | |
|---|---|---|
| `api_key` option (`/plugin configure skilltotal`) | none | Your API key. It's kept in the system keychain and reaches the hook as `CLAUDE_PLUGIN_OPTION_API_KEY` |
| `SKILLTOTAL_API_KEY` | none | The same key from the environment, used when the option isn't set |
| `SKILLTOTAL_SITE_URL` | the API's host without `api.` | Where the "make a key" link points |
| `SKILLTOTAL_API_URL` | `https://api.skilltotal.com` | Point at a local stack: `http://127.0.0.1:4300` |
| `SKILLTOTAL_TIMEOUT_MS` | `4000` | Per lookup |
| `SKILLTOTAL_FORCE` | none | `1` keeps the checks on even when AIR's plugin is installed and enabled |

## Development

The hook's source is `packages/agent-hook`. `hooks/skilltotal-hook.mjs` is its bundle, so the plugin runs on Node with nothing installed; rebuild it with `bun run plugin:build` (a unit test fails while it's stale). Try it against a local stack with `claude --plugin-dir plugins/skilltotal` and `SKILLTOTAL_API_URL=http://127.0.0.1:4300`.
