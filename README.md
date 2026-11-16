# SkillTotal for Claude Code

The Claude Code plugin from [SkillTotal by AIR](https://skilltotal.com). Before a skill runs and
before `npx skills add` installs one, it shows SkillTotal's verdict for that skill, and it denies
the install of one SkillTotal found Unsafe. Only a content hash leaves your machine, never the
skill's files.

## Install

```
/plugin marketplace add AirSecurity/skilltotal-plugin
/plugin install skilltotal@skilltotal
```

Claude Code asks for your SkillTotal API key when you enable the plugin. Make one at
[skilltotal.com/account/settings/api-keys](https://skilltotal.com/account/settings/api-keys)
(choose **Never** under expiry, or use your Default key). The setup is on
[skilltotal.com/developers/plugin](https://skilltotal.com/developers/plugin), and what the plugin
sends is on [skilltotal.com/privacy](https://skilltotal.com/privacy).

## This repository

SkillTotal's release publishes it: each version is one commit, tagged with the SkillTotal release
it came from. The plugin itself is in [`plugins/skilltotal`](plugins/skilltotal); its README says what each
check shows and the settings it reads. Changes made here are overwritten by the next release.

## License

MIT, see [LICENSE](LICENSE).
