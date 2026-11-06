---
name: skillui
description: "Reverse-engineer een designsysteem uit een website, git-repo of lokale map en verpak het als een Claude-skill met DESIGN.md, tokens, screenshots en fonts. Pure statische analyse, geen AI, geen API-keys. Gebruik dit als iemand vraagt om het designsysteem van een site over te nemen, een referentie-UI na te bouwen, of tokens uit een bestaande codebase te trekken. Triggers: designsysteem extraheren, design system van een site, tokens uit een repo halen, huisstijl van een website overnemen, skillui."
metadata:
  upstream: https://github.com/amaancoderx/npxskillui
  package: skillui (npm, MIT)
---

# SkillUI

CLI die kleuren, typografie, spacing, animaties en componenten uit een bestaande
UI haalt en er een map van maakt die Claude Code direct kan lezen.

## Aanroepen

**Altijd via `npx`, nooit globaal installeren.** Een globale install (`npm i -g
skillui`) faalt op deze machine met een permissiefout op de npm-prefix.

```bash
npx --yes skillui --url  https://linear.app        # live website
npx --yes skillui --repo https://github.com/org/repo   # git-repo klonen en scannen
npx --yes skillui --dir  ./pad/naar/app              # lokale map
```

Vlaggen:

| Vlag | Betekenis |
|---|---|
| `--out <pad>` | Uitvoermap (standaard de huidige map) |
| `--name <naam>` | Projectnaam in de uitvoer overschrijven |
| `--format design-md\|skill\|both` | Standaard `both` |
| `--no-skill` | Alleen `DESIGN.md`, geen `.skill`-pakket |
| `--mode ultra` | Volledige visuele extractie met screenshots |
| `--screens <n>` | Aantal screenshots in ultra-modus |

Ultra-modus heeft Playwright nodig:

```bash
npm install playwright && npx playwright install chromium
```

## Uitvoer

Een map met `SKILL.md`, `CLAUDE.md`, `DESIGN.md`, `references/` (animaties,
layout, componenten, interacties), `screens/`, `tokens/` (colors, spacing,
typography als JSON) en `fonts/`. Open die map met `claude` en de skill wordt
automatisch geladen.

## Regels voor dit project

- **Schrijf de uitvoer nooit in de repo.** Gebruik `--out` naar je
  scratchpad-map. De uitvoer is enkele megabytes inclusief fonts en screenshots
  en hoort niet in git.
- **Richt `--url` niet op onze eigen app.** Die zit achter een login; de
  crawler krijgt alleen de inlogpagina. Het eigen designsysteem staat al als
  designtokens in de app-repo — dat is de bron van waarheid. Gebruik hooguit
  `--dir <pad-naar-app>` om te zien wat de code in de praktijk gebruikt.
- **Gebruik het voor referenties, niet voor de eigen huisstijl.** De zinvolle
  toepassing is een site die je mooi vindt uitpluizen als inspiratie, niet
  BarryOS' eigen stijl opnieuw afleiden.
- **Draai het niet ongevraagd op de site van een klant of concurrent.** Het is
  een crawler; vraag het eerst.
- Het pakket is MIT en doet pure statische analyse, maar `npx` haalt elke keer
  code van npm op. Bij twijfel over een versie: eerst
  `npm view skillui versions` en dan een vaste versie pinnen met
  `npx --yes skillui@1.3.4`.
