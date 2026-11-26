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
- Spillet er også Kyuubi Creative sitt 5-årsjubileum (meny og sluttskjerm). Spilleren er en rev i Kyuubi-stil (`drawFox`), menyene bruker Kyuubi-solnedgangen og Poppins.
- Målet er raskeste totaltid gjennom alle tre sidene (`tot.time`). Mislykkede forsøk teller med i tiden. Beste tid lagres lokalt (`ab_best`).
- Toppliste: `LB = {url, key}` (Supabase REST). Tomt = av. Tabell:
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

## Arbeidsflyt
- Utvikles i Claude-chat, pushes til `main`, GitHub Pages publiserer automatisk.
- Test i headless Chromium (Playwright) før push. Ingen byggesteg.
- Ekte reklamenettverk kan IKKE brukes som skytemål/pop-ups (bryter retningslinjene deres). Kun egne eller direkte avtalte annonser i selve spillet.
