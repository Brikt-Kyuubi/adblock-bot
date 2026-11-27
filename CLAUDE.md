# Notater for videre utvikling

Les denne før du endrer spillet.

## Hva spillet er
Top-down «survivor»-spill i nettleseren, laget for mobil først (logisk lerret 420×760, skaleres til skjermen).
Spilleren styrer en adblock-bot med en flytende joystick (dra hvor som helst) eller WASD. Boten skyter automatisk.

## Struktur i index.html
- Tre nivåer i `LV`: DagensKlikk (nyheter), Tilbudsjungelen (nettbutikk), Nettverket (boss LagkassaBrikt: eierens selvironiske, ensporede «influencer» som bare poster om Lagkassa+; alt på boss-siden er Lagkassa+).
- `genPage()` lager siden: `decor` er ikke-blokkerende innhold, `blocks` er reklameflater som må skytes i stykker (sperrer på tvers + annonser i sidefeltet).
- Fiender: trackere (rask, gir sporing og treghet) og reklamemonstre (treg, gir pop-up).
- Pop-ups: maks 3 synlige (`PV`), kø bak (`PQ`). Mer enn 5 totalt = game over. Typer i `POPT` (+ merkevare-pop-ups i `BPOP`).
- Oppgraderinger: `UPG`, tre tilfeldige kort per nivå.
- Magnet slippes når siste data-pakke er plukket.
- Merkevarer i `BRANDS`: Kyuubi Creative, Nails by Jee Hee, PopUpGolf, Morokoppen, Lagkassa+ (eierens egne prosjekter). Lagkassa+ bruker aldri utropstegn.
- ALL reklame i spillet er disse merkevarene: reklameflater, reklamemonstre (med merkets symbol), boss-skudd og reklame-pop-ups. Ingen generiske/anonyme annonser.
  Nettsidens egne pop-ups (cookie-banner, varsler, «Skal du gå allerede?», betalingsmur) tilhører den fiktive nettsiden.
- Logoer ligger inline i `LOGO_SRC` (full logo + `_s` = symbol). PNG fra eierens filer, SVG for PopUpGolf og Lagkassa+.
- Spillet er også Kyuubi Creative sitt 5-årsjubileum (meny og sluttskjerm). Spilleren er Kyuubi-revesymbolet (`LOGO_SRC.kyuubi_s`, tegnet i `drawFox` med hvit/rosa tint ved treff/tracker), menyene bruker Kyuubi-solnedgangen og Poppins.
- Målet er raskeste totaltid gjennom alle tre sidene (`tot.time`). Mislykkede forsøk teller med i tiden. Beste tid lagres lokalt (`ab_best`).
- Toppliste: `LB = {url, key, table}` (Supabase REST). Ligger i Supabase-prosjektet AddUp (gammelt spill, forgjengeren til SumoSan), tabell `adblock_scores`. Opprinnelig skisse:
  ```sql
  create table public.scores (
    id bigint generated always as identity primary key,
    name text not null check (char_length(name) between 1 and 20),
    time_ms integer not null check (time_ms between 60000 and 3600000),
    version text,
    created_at timestamptz not null default now()
  );
  alter table public.scores enable row level security;
  create policy "alle kan lese" on public.scores for select using (true);
  create policy "alle kan sende inn" on public.scores for insert with check (true);
  ```
  Bruk publishable/anon-nøkkelen (den er laget for å ligge i nettleseren). Uten innlogging kan hvem som helst sende inn falske tider.

- Hemmelig nivå (`LV[SECRET]`, `secret:true`, kind `block`): KornyKloss, laget rundt interessene til en 11-åring (håndball, fotball, samlekort, tegninger). Låses opp når en ferdig runde havner på topp 10 (`secretCheck`, `localStorage.ab_secret`), og kan da også startes fra menyen. Egen tid (`ab_kbest`), teller ikke i totaltiden eller topplisten.
  - Boss BigBoss Korny (`drawKorny`, `kornyState`): egen lilla blokk-spøkelsesfigur med caps. Syklus synlig → forsvinner → usynlig (kan ikke treffes) → dukker opp ved siden av spilleren → slikk (treghet hvis innen 105 px).
  - Klossbomber (`drawKloss`) erstatter reklamemonstre: tenner lunta nær spilleren og eksploderer. Baller (håndball/fotball) erstatter trackere. Klossvegger (`cube`) er skytbare hindre.
  - Pop-ups: tegning, kort, maal, fotball (`KIDP`, klasse `pt-kid`).
  - IKKE bruk Pokémon-, Gengar- eller Minecraft/creeper-figurer, navn eller logoer. Alt skal være egne design.

- Vanskelighet: angrepsoppgraderinger (`OFF` = rate, multi, pierce, dmg) gir fiendene en motoppgradering (`enemyBuff`, `EB`): +20 % helse, +12 % spawn eller +8 % fart (fart maks ca. 1,35×). Kortene viser «Reklamen svarer med en oppgradering», og et rødt banner vises etter valget. `EB` nullstilles i `newRun` og lagres i `snap` ved nytt nivå.
- 25 % av pop-upene legges oppå spilleren i stedet for lengst unna (`showPop`).
- Side 3 (Nettverket) snur cookie-veggen: «Godta alle» gir et level-up, «avvis» gir sporing.

## Arbeidsflyt
- Utvikles i Claude-chat, pushes til `main`, GitHub Pages publiserer automatisk.
- Test i headless Chromium (Playwright) før push. Ingen byggesteg.
- Ekte reklamenettverk kan IKKE brukes som skytemål/pop-ups (bryter retningslinjene deres). Kun egne eller direkte avtalte annonser i selve spillet.
