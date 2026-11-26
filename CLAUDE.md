# Notater for videre utvikling

Les denne før du endrer spillet.

## Hva spillet er
Top-down «survivor»-spill i nettleseren, laget for mobil først (logisk lerret 420×760, skaleres til skjermen).
Spilleren styrer en adblock-bot med en flytende joystick (dra hvor som helst) eller WASD. Boten skyter automatisk.

## Struktur i index.html
- Tre nivåer i `LV`: DagensKlikk (nyheter), Tilbudsjungelen (nettbutikk), Scrollr (boss @HypeHedda).
- `genPage()` lager siden: `decor` er ikke-blokkerende innhold, `blocks` er reklameflater som må skytes i stykker (sperrer på tvers + annonser i sidefeltet).
- Fiender: trackere (rask, gir sporing og treghet) og reklamemonstre (treg, gir pop-up).
- Pop-ups: maks 3 synlige (`PV`), kø bak (`PQ`). Mer enn 5 totalt = game over. Typer i `POPT` (+ merkevare-pop-ups i `BPOP`).
- Oppgraderinger: `UPG`, tre tilfeldige kort per nivå.
- Magnet slippes når siste data-pakke er plukket.
- Merkevarer i `BRANDS`: Kyuubi Creative, Nails by Jee Hee, PopUpGolf, Morokoppen, Lagkassa+ (eierens egne prosjekter). Lagkassa+ bruker aldri utropstegn.

## Arbeidsflyt
- Utvikles i Claude-chat, pushes til `main`, GitHub Pages publiserer automatisk.
- Test i headless Chromium (Playwright) før push. Ingen byggesteg.
- Ekte reklamenettverk kan IKKE brukes som skytemål/pop-ups (bryter retningslinjene deres). Kun egne eller direkte avtalte annonser i selve spillet.
