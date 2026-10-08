# Validation — v1.0.0

Sources: hermes-agent branch `ko-locale` @ `313fa2eedf` — `locales/ko.yaml`, `locales/ko.tui.yaml`
(bundled Korean, copied verbatim), `locales/en.yaml` (3,432 keys), `locales/_keys.tui.json`
(1,259 keys), `locales/_keys.desktop.json` (4,987 keys), and the branch's desktop TS overlays
(`apps/desktop/src/i18n/ko*.ts`) converted into `locales/ko.desktop.yaml`.

Coverage: core 3432/3432 · TUI 1259/1259 · desktop 4888/4987 — 99 desktop keys intentionally
absent (see the list below).

## `hermes plugins validate` (run from the hermes-agent `ko-locale` worktree)

<!-- VALIDATE_BEGIN -->

```
✓ manifest — plugin.yaml parses
✓ manifest fields — name, version, description present
✓ requires_hermes — spec '>=0.22' parses
✓ config schema — not declared
✓ requires_env — all entries UPPER_SNAKE
✓ loadable — entry: locales/ (language pack)
✓ python dependencies — none declared
✓ capability probe — skipped (no __init__.py)
✓ built-in tool collisions — no tools to check
✓ security scan — caution
✓ no core override — no runtime rebinds of Hermes core
✓ locale ko.desktop — ko.desktop.yaml: 4888 key(s), 4888 match the English 
desktop catalog
✓ locale ko.tui — ko.tui.yaml: 1259 key(s), 1259 match the English tui catalog
✓ locale ko — ko.yaml: 3432 key(s), 3432 match the English core catalog
⚠ no __init__.py — capability probe skipped (manifest-only plugin)
⚠ security scan caution: curl_pipe_shell (VALIDATION.md:39), curl_pipe_shell 
(ko.desktop.yaml:3472)

Validation passed.
```

<!-- VALIDATE_END -->

The *caution* finding is translated UI text that mentions `curl | sh` (the English original
contains the same words); a language pack ships no code.

## Key-set / placeholder parity script

```
[core]     ko.yaml: 3432 keys · en.yaml: 3432 · missing: 0 · stray: 0 · placeholder_mismatch: 0
[tui]      ko.tui.yaml: 1259 keys · _keys.tui.json: 1259 · missing: 0 · stray: 0
[desktop]  ko.desktop.yaml: 4888 keys · _keys.desktop.json: 4987 · missing: 99 · stray: 0
RESULT: PASS (desktop missing = the documented skips listed below)
```

Generation-time desktop checks: every English function leaf's Korean counterpart was probed with
sentinel arguments (`⟦i⟧`, then empty-string, numeric and boolean replacements — the shapes
`apps/desktop/src/i18n/catalog-completeness.test.ts` uses) and the emitted positional
`{0}`/`{1}` template must reproduce the function's output for every shape. 613 function entries
converted this way; placeholder parity against the English templates reports `en-only: 0`,
`ko-only: 0`.

## Intentionally absent desktop keys (99)

**Branching / boolean-toggle function leaves (37)** — rendering picks a side on a
boolean/identifier argument or needs a structured argument; a positional string would pin one
branch, so English shows through:

```
assistant.catalogInstall.serverNotConnected
assistant.tool.titleTemplates.runningPrefixedTool
assistant.tool.titleTemplates.runningTool
commandCenter.maintenance.bytes
commandCenter.pets.toggleFailed
connectorsPage.tools.conflictBody
desktop.yoloSystem
install.testSucceeded
intro.custom
messaging.waitingSince
preview.noInlineBody
preview.web.finishedRestarting
preview.web.loadFailedConsole
rightSidebar.folderTip
settings.billing.charge.added
settings.billing.errors.rateLimited.message
settings.billing.errors.stripeUnavailable.message
settings.billing.plan.effectScheduled
settings.gateway.connectedTo
settings.localModels.downloadEtaHours
settings.mcp.capabilitySummary
settings.model.moaReferenceToggle
settings.plugins.installModal.catalogPinned
settings.plugins.installModal.serverNotConnected
settings.plugins.installModal.skillsReady
settings.vault.identifierShown
shell.modelMenu.limitedTip
shell.modelMenu.priceTitle
shell.modelMenu.usageLeft
shell.modelMenu.usageWindow
sidebar.projects.branchOff
sidebar.projects.toggle
skills.hub.installBlockedMessage
skills.plugins.catalogProvenance
skills.toggleToolset
ui.sidebar.toggle
webhooks.toggleFailed
```

**List/object leaves (2)** — text-only packs cannot carry them:

```
composer.followUpPlaceholders
composer.newSessionPlaceholders
```

**Korean copy identical to English (60)** — plain URLs, placeholder examples, id-like strings
(`composer.url`, `artifacts.zero`, `settings.connections.kindSsh`, …); shipping them would pin a
frozen English copy over Hermes's bundled English, and the English fallback renders the same text:

```
artifacts.colLocationLink
artifacts.zero
assistant.catalogInstall.requiresHermes
composer.themeTryPost
composer.url
composer.urlPlaceholder
composer.voiceEngineLiveShort
connectorsPage.add.authOauth
connectorsPage.add.pastePlaceholder
connectorsPage.add.typeStdio
connectorsPage.add.url
cron.deliveryLabels.discord
cron.deliveryLabels.slack
cron.deliveryLabels.telegram
desktop.yoloTitle
freeTier.providerName
install.remoteUrlPlaceholder
messaging.fieldCopy.MATRIX_HOMESERVER.placeholder
messaging.fieldCopy.MATRIX_USER_ID.placeholder
messaging.fieldCopy.MATTERMOST_URL.placeholder
messaging.fieldCopy.SIGNAL_HTTP_URL.placeholder
modelPicker.pro
onboarding.pro
preview.diff
profiles.remoteOverride.urlPlaceholder
prompts.vaultSaveIdentifierPlaceholder
sendDiagnostics.links.discord
sendDiagnostics.links.github
sendDiagnostics.links.portal
settings.appearance.installPlaceholder
settings.billing.state.autoRefill.notAvailablePill
settings.config.attachmentSizeUnit
settings.connections.kindCloud
settings.connections.kindSsh
settings.connections.urlTitle
settings.customEndpoints.namePlaceholder
settings.gateway.cloudSignInTitle
settings.gateway.cloudTitle
settings.model.moaTitle
settings.model.tasks.mcp.label
settings.nav.mcp
settings.notifications.testTitle
settings.plugins.installModal.repoPlaceholder
settings.vault.cvcField
settings.vault.originPlaceholder
settings.vault.originPlaceholderCheckout
shell.gatewayMenu.gateway
shell.statusbar.agents
shell.statusbar.contextUsagePanel.categories.mcp
shell.statusbar.cron
shell.statusbar.gateway
shell.statusbar.gatewayTitle
shell.statusbar.systemResources.ram
shell.statusbar.toggleCommandCenter
sidebar.filter.pr
updates.appName
updates.versionDetailsDistributionStore
webhooks.deliverOptions.discord
webhooks.deliverOptions.slack
webhooks.deliverOptions.telegram
```

## Note on the core and TUI catalogs

They mirror the Korean translations Hermes itself bundles (`hermes --tui`'s
`locales/ko.tui.yaml` and the core `locales/ko.yaml`, shipped on the `ko-locale` branch).
Hermes keeps updating those files upstream; this pack carries the `313fa2eedf` revision so the
declared language validates terminal-to-desktop, and a partial pack still degrades key-by-key to
the bundled English if upstream revises wording.
