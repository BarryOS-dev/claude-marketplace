# BarryOS Claude Code marketplace

Eén plek voor alle Claude Code-plugins, skills en mods die we bij BarryOS gebruiken.
Elke omgeving (laptop, Claude Code in de cloud, eigen container, CI) verwijst alleen naar deze repo
en zet één plugin aan: **`barryos-kit@barryos`**. Die installeert de rest automatisch.

- Alles staat **vast op een commit** (`sha`). Een update van een maker komt pas binnen als wij de `sha` ophogen.
- Plugins met aangepaste standaardinstellingen of ingekorte inhoud staan als kopie in `plugins/` (met hun LICENSE).
- De repo is **openbaar**, zodat ook Claude Code in de cloud hem kan ophalen (de cloud mag geen eigen GitHub-sleutels gebruiken).
  Zet hier dus **nooit** interne gegevens in: geen sleutels, project-ID's, IP-adressen, klantnamen of interne URL's.

---

## Hoe het werkt

```
claude.ai admin-console  ──►  extraKnownMarketplaces: barryos  ──►  deze repo
(managed settings)            enabledPlugins: barryos-kit@barryos        │
                                                                         ▼
                                              barryos-kit  ──dependencies──►  22 plugins
```

`barryos-kit` bevat zelf niets; het is een lijst `dependencies`. Claude Code installeert en activeert
die automatisch. Opt-in plugins staan wel in de marketplace maar niet in die lijst.

---

## Inhoud

### In `barryos-kit` (voor iedereen aan)

| Plugin | Wat | Bron (vaste commit) | Gecontroleerd |
|---|---|---|---|
| **Workflow & review** ||||
| superpowers | Brainstorm, plannen, TDD, debuggen, review | obra/superpowers@bb92a77 | al in gebruik |
| code-review | PR-review | anthropics/claude-plugins-official@f8c5e7a | Anthropic |
| code-simplifier | Vereenvoudigt gewijzigde code | anthropics/claude-plugins-official@f8c5e7a | Anthropic |
| security-guidance | Beveiligingsadvies tijdens bewerken | anthropics/claude-plugins-official@f8c5e7a | Anthropic |
| security-scan | Claude Security scanner (plugin-id `claude-security`) | anthropics/claude-plugins-official@f8c5e7a | Anthropic |
| skill-creator | Skills maken en testen | anthropics/claude-plugins-official@1aa4a4b | Anthropic |
| ponytail-skills | "Luie senior dev": hergebruik eerst, kleinste wijziging | DietrichGebert/ponytail@96608e0 | ✅ alleen skills, zonder hooks |
| thermonuclear-review | Strenge structurele code-review | jaredpalmer/kev@2074cc4 | ✅ algemeen gemaakt |
| blast-radius | Houdt riskante shell-commando's tegen ter bevestiging | anthropics/claude-code-playground@569c528 | ✅ |
| **Tokens, kosten & geheugen** ||||
| caveman | Korte antwoorden, minder output-tokens | JuliusBrussee/caveman@25d22f8 | al in gebruik |
| context-mode | Houdt grote tool-output buiten de context | mksglu/context-mode@6f6a61c | al in gebruik |
| mem | Geheugen tussen sessies (plugin-id `claude-mem`) | thedotmack/claude-mem@96ad0e2 | al in gebruik |
| cache-tax | Prompt-cache-kosten; autoWarm standaard **uit** | karanb192/cache-tax@dfe380c | ✅ |
| savvy-progress | Voortgangsbalk + agents-paneel | JohnnyVizz/claude-kit@c871c15 | ✅ |
| **Design & UI** ||||
| frontend-design | Ontwerprichtlijnen frontend | anthropics/claude-plugins-official@f8c5e7a | Anthropic |
| impeccable | Design-kritiek en polish | pbakaus/impeccable@f88b283 | al in gebruik |
| ui-ux-pro-max | Stijlen, paletten, typografie (lokaal) | nextlevelbuilder/ui-ux-pro-max-skill@b7d5353 | ✅ alleen kern-skill |
| dev-skills | Alle 12 project-skills uit webapp: vercel-react-best-practices, vercel-composition-patterns, vercel-react-view-transitions, web-design-guidelines, design-critique, landing-page-design, design-taste-frontend, image-to-code, playwright-cli, supabase, supabase-postgres-best-practices, skillui | vercel-labs/agent-skills, anthropics/knowledge-work-plugins, inference-sh/skills, Leonxlnx/taste-skill, microsoft/playwright-cli, supabase/agent-skills | al in gebruik; skillui en landing-page-design zonder automatische toestemming, web-design-guidelines vast op commit |
| skins | Thema's en tool-kaarten (`/skin`) | hellosverre/claude-skins@cf0d39f | ✅ zonder hun mod-store |
| filetree | Bestandsboom; "Follow work" standaard **uit** | data-goblin/claude-code-filetree@2b42d0f | ✅ |
| replay-theater | Bekijk de bewerkingen van de laatste beurt | anthropics/claude-code-playground@569c528 | ✅ |
| **Platform** ||||
| vercel | Vercel-skills en tools | vercel/vercel-plugin@882e66c | al in gebruik |

"Al in gebruik" = stond al in onze managed settings vóór deze marketplace; niet opnieuw gecontroleerd.
"✅" = code gelezen op deze commit (hooks, netwerk, telemetrie, scripts, verborgen instructies).

### Opt-in (in de marketplace, niet in het pakket)

Aanzetten met `/plugin install <naam>@barryos`.

| Plugin | Waarom opt-in |
|---|---|
| graphify | Kennisgraaf van je codebase. Vereist `uv tool install 'graphifyy==0.9.103'`. Managed env zet `GRAPHIFY_BACKEND=none`, dus er gaat niets naar externe AI-diensten. |
| terminal-browser | Browser in de terminal. Vereist een los programma via `curl \| bash`, stuurt standaard telemetrie (managed env zet `TERMINAL_BROWSER_NO_TELEMETRY=1`) en laat de agent sites bedienen waar je ingelogd bent. |
| ui-ux-design-studio | Logo's, iconen, huisstijl (`design`, `brand`, `design-system`). Beeldgeneratie stuurt je prompt naar Gemini / Atlas Cloud / MuAPI en leest keys uit `~/.claude/.env`. Zet daar alleen die keys in. |
| swift-lsp | Swift language server; alleen nuttig voor Swift-projecten. |

### Bewust niet opgenomen

- **OmniRoute**: stuurt code en prompts naar externe AI-aanbieders.
- **Addy Osmani agent-skills**: dubbel met superpowers.
- **claude-reflect**: dubbel met mem en de ingebouwde auto-memory.
- **"You Should Know"**: bestaat niet in Claude Code 2.1.319.
- Skills staan **niet meer** in `webapp/.agents/skills` of `.claude/skills`: alles komt uit deze marketplace (`dev-skills`), zodat ze niet dubbel laden.
- Je claude.ai-account-skills (`~/.claude/skills/synced`) volgen je account al automatisch.

---

## Installeren per omgeving

### 1. Laptops (automatisch)

Geregeld via de admin-console: **claude.ai → Organization settings → Claude Code → Managed settings**
(alleen Owner / Primary Owner). Het relevante deel:

```json
{
  "extraKnownMarketplaces": {
    "barryos": {
      "source": { "source": "github", "repo": "BarryOS-dev/claude-marketplace" },
      "autoUpdate": true
    }
  },
  "enabledPlugins": { "barryos-kit@barryos": true },
  "env": {
    "GRAPHIFY_BACKEND": "none",
    "TERMINAL_BROWSER_NO_TELEMETRY": "1"
  }
}
```

Laat de bestaande sleutels in de admin-console (zoals `autoMode`, `pluginConfigs`, `channelsEnabled`) staan;
voeg dit toe in plaats van te vervangen. Herstart Claude Code daarna.

Handmatig (zonder admin-console):

```bash
claude plugin marketplace add BarryOS-dev/claude-marketplace
claude plugin install barryos-kit@barryos
```

### 2. Claude Code in de cloud (claude.ai/code)

Het setup-script van de cloud-omgeving (claude.ai → Admin → Cloud environments) installeert het pakket:

```bash
claude plugin marketplace add BarryOS-dev/claude-marketplace || true   # zelfde vorm als in managed settings, anders genegeerd
claude plugin install barryos-kit@barryos --scope user -y || true
```

Daarnaast staat in `.claude/settings.json` van een project-repo alleen `barryos-kit@barryos` aan, zodat
lokaal en in de cloud hetzelfde pakket geldt.

### 3. Eigen container / VPS / CI

Nodig in de image:

- **Claude Code** ≥ 2.1.287 (mods laden standaard vanaf die versie).
- **GitHub-toegang** tot de private repo via een credential helper, bijvoorbeeld `gh auth login` of
  `gh auth setup-git` met `GH_TOKEN`. Een losse `GITHUB_TOKEN`-variabele alleen is niet genoeg.
- **Runtimes** voor de plugins met hooks: `node`, `python3`, `git`; `bun` voor mem (claude-mem) en context-mode.
- **Instellingen**: log in met het Team-account (dan gelden de managed settings) **of** zet de JSON hierboven in
  `/etc/claude-code/managed-settings.json`.

Voorbeeld:

```dockerfile
RUN npm install -g @anthropic-ai/claude-code
COPY managed-settings.json /etc/claude-code/managed-settings.json
# runtime: GH_TOKEN meegeven, daarna:
#   gh auth setup-git && claude plugin install barryos-kit@barryos
```

---

## Beheer

### Een plugin bijwerken

1. Bekijk de wijzigingen tussen de huidige `sha` en de nieuwe commit (`git diff <oud>..<nieuw>` in de bronrepo).
2. Pas de `sha` aan in `.claude-plugin/marketplace.json`, of kopieer de nieuwe bestanden naar `plugins/<naam>`.
3. Verhoog `version` in `plugins/barryos-kit/.claude-plugin/plugin.json`.
4. `claude plugin validate .`, PR openen, mergen. Iedereen krijgt het bij de volgende auto-update.

Wijs **nooit** naar een branch (`ref: main`): dan komt elke upstream-wijziging ongezien binnen.

### Een skill toevoegen

Niet meer via `npx skills add` in een project-repo. Kopieer de skillmap naar `plugins/dev-skills/skills/<naam>/`,
zet de LICENSE van de bron in `plugins/dev-skills/licenses/`, verhoog `version` in `dev-skills` en `barryos-kit`, en open een PR.
Let op `allowed-tools` in de SKILL.md: automatische toestemming voor commando's alleen als je die bewust wilt.

Licentie-noot: `landing-page-design` (inference-sh/skills) heeft upstream geen licentiebestand.

### Een plugin toevoegen

1. Controleer de code (hooks, netwerkverkeer, telemetrie, scripts, instructies in SKILL.md).
2. Voeg een entry toe in `marketplace.json` met een vaste `sha`.
3. Voeg de naam toe aan `dependencies` van `barryos-kit` (of laat hem opt-in).
4. Noteer hem in de tabel hierboven.

Namen die beginnen met `claude-`, `anthropic-` of `cc-plugin-` zijn gereserveerd voor Anthropic; geef de entry
dan een andere naam (zie `security-scan`, `mem`).

### Lokaal testen

```bash
claude plugin validate .
claude --plugin-dir ./plugins/<naam>          # één plugin voor één sessie
```
