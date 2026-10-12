# hermes-lang-ko — Hermes Agent 한국어 언어팩

**한국어** for Hermes Agent: CLI·게이트웨이 메시지(코어), Hermes TUI, Hermes Desktop.

This is a *language pack* plugin for [Hermes Agent](https://github.com/NousResearch/hermes-agent),
modeled on the reference pack [hermes-lang-pl](https://github.com/teknium1/hermes-lang-pl): a
**text-only** plugin — no Python, no JavaScript, no network. It ships three YAML catalogs that
Hermes layers over its bundled English:

| File | Surface | Source it mirrors | Keys |
| --- | --- | --- | --- |
| `locales/ko.yaml` | core (Python `t()`: approval prompts, gateway/slash replies, CLI, tips, tool verbs…) | `locales/en.yaml` | 3432 / 3432 |
| `locales/ko.tui.yaml` | Hermes TUI (`hermes --tui`) | `ui-tui/src/i18n/en.ts` (`locales/_keys.tui.json`) | 1259 / 1259 |
| `locales/ko.desktop.yaml` | Hermes Desktop | `apps/desktop/src/i18n/en.ts` (`locales/_keys.desktop.json`) | 4888 / 4987 (99 skipped, see below) |

The core and TUI catalogs are the Korean translations already bundled with Hermes
(`locales/ko.yaml`, `locales/ko.tui.yaml` on the `ko-locale` branch, commit `313fa2eedf`); the pack
carries them so `hermes plugins validate` sees a complete declared language and older installs keep
working. The Desktop catalog is the pack's added value: the Korean desktop overlays from the
`ko-locale` branch (`apps/desktop/src/i18n/ko*.ts`), converted into the YAML pack convention.

## Install

```
hermes plugins install https://github.com/ymshin-dev/hermes-lang-ko
hermes config set display.language ko
```

Restart the gateway / Desktop / TUI afterwards. `hermes plugins validate <path>` on a checkout of
this repo confirms the pack parses and its keys are known. Requires Hermes `>= 0.21.6` (pluggable
i18n, `9bcbe7b5df`; it shipped in the `v0.21.4`-line canaries from 2026-09-29 onward, and 0.21.6
is the latest stable build that carries it).

To go back: `hermes config set display.language en`; to remove:
`hermes plugins uninstall hermes-lang-ko`.

## How it works

- `plugin.yaml` declares `provides_locales: [{id: ko, endonym: 한국어, rtl: false}]`. The plugin
  loader registers every `locales/ko[.tui|.desktop].yaml` automatically (`ctx.register_locale_dir`)
  — a manifest-only pack needs no `__init__.py`.

- Resolution order for a key: plugin pack → user overlay (`$HERMES_HOME/locales/ko.yaml`) →
  bundled → English. Packs may be partial; missing keys fall back to English, so this pack keeps
  working when Hermes adds strings.

- Registering the pack never changes `display.language`; you opt in with the config command above.

## Pack conventions (apply to any language pack)

- **Keys** are copied verbatim from the English catalogs and nested the same way (leaf keys that
  contain dots, e.g. `fieldLabels."display.personality"`, stay a single quoted key). YAML reserved
  words used as keys (`yes`, `no`, `on`, `off`) are quoted.

- **Core placeholders** are named and must match English exactly: `{count}`, `{error}`, `{model}` …
  (`str.format`). The parity script for this release reports `placeholder_mismatch: 0`.

- **Desktop/TUI function-valued entries** — English catalogs contain entries like
  `` (name, count) => `Install ${name} (${count})` ``. A pack expresses those as a plain string
  with **positional placeholders in argument order**:
  `"{0} 설치 ({1})"`. The renderer wraps the string back into a function. Only arguments the
  English text actually interpolates need to appear; arguments used purely as booleans/discriminators
  are dropped.

- **Plural forms**: positional strings cannot branch on the count, so Korean keeps the numeral-based
  phrasing ({0}개 etc.) exactly as the source translations do.

- **Left in English on purpose**: product names (Hermes, Nous, Skills Hub, Kanban), slash-command
  and tool names, config keys (`display.language`), paths, model/provider ids, code in backticks,
  keyboard key names.

### Not translated (fall back to English)

99 Desktop entries are intentionally absent from `ko.desktop.yaml` (the app shows English for them):

- **Editorial intro content (`intro.custom`)** — the English catalog entry is a shell
  (`intro: { stock: {}, custom: () => [] }`); the introduction copy lives in JSONL that packs
  cannot carry (the same reason hermes-lang-pl skips `intro.custom`).
- **Boolean-toggle and branching labels** — 36 further function entries whose rendering branches on
  a boolean/identifier argument instead of echoing it (`desktop.yoloSystem`,
  `commandCenter.pets.toggleFailed`, `ui.sidebar.toggle`, …). A positional `{0}` string would
  render the wrong side of the toggle, so they are left to the bundled English.
- **2 list/object leaves** (`composer.newSessionPlaceholders`, `composer.followUpPlaceholders`) —
  the same text-only limitation.
- **60 leaves whose Korean copy is identical to the English one** (plain URLs, numeric strings,
  `composer.url`, `artifacts.zero`…). Shipping them would pin stale English text over Hermes's
  bundled English, so they are dropped; the fallback renders the same text.

The full list is appended to [VALIDATION.md](/VALIDATION.md). New Hermes strings → add the key
under the same path as English; unknown keys are reported by `hermes plugins validate` as warnings,
never errors.

## Provenance

- Forked from [teknium1/hermes-lang-pl](https://github.com/teknium1/hermes-lang-pl) (structure,
  MIT license).
- `locales/ko.yaml`, `locales/ko.tui.yaml` — Korean core/TUI translations from the
  [hermes-agent](https://github.com/NousResearch/hermes-agent) `ko-locale` branch (`313fa2eedf`).
- `locales/ko.desktop.yaml` — generated from the same branch's Desktop TS overlays
  (`apps/desktop/src/i18n/ko.ts`, `ko_chrome.ts`, `ko_settings.ts`, `ko_command_center.ts`,
  `ko_capabilities.ts`, `ko_fn.ts`) by converting each function leaf into its positional template
  (613 function entries) and dropping the shapes YAML cannot express. The dashboard (`web/src`)
  Korean strings from that branch ship with Hermes itself and are not part of a pack surface.

## Contributing

Fix a string → edit the YAML, keep the key and placeholders, open a PR. New Hermes strings → add
the key under the same path as English; unknown keys are reported by `hermes plugins validate` as
warnings, never errors.

## License

MIT — see `LICENSE`.
