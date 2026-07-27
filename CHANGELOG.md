# Pax Bharata — changelog

Running record of player-facing changes, kept current as changes land.
Each Workshop push: turn the Unreleased section into a version entry,
copy it into the Steam changenote (BBCode-ify if long), and start a fresh
Unreleased section.

## [Unreleased] — next Workshop update

## [1.2] — 2026-07-26 — game 1.0.48/49 compat

### 1.0.48/49 compat: Independence Movements / Fall of Empires restored
- Vanilla now tags exactly two techs ["NotPostApoc"] and every vanilla
  scenario declares that tag — our scenarios don't, so both techs silently
  vanished from Pax Bharata and League campaigns ("Bad tech prereq in
  json" spam) and everything gated on them (breakaways, unions,
  fragmentation) became researchable day one. Fix: world-tagged clones
  (RW7_/RW1_IndependenceMovements, .../FallofEmpires) with vanilla display
  text; all world projects' prereqs point at the clones. Declaring
  NotPostApoc ourselves was not an option (it would resurrect the scoped-
  out vanilla political projects), nor was multi-tagging vanilla's techs
  (subset semantics would remove them from vanilla scenarios).
- The two live vanilla projects gated on those techs — Global Command
  Structure and Freedom Fighters — had the same dropped-prereq problem
  (Global Command Structure was researchable day one). They are re-gated
  via sparse merge entries listing the vanilla tech plus every world's
  clone: per scenario exactly one variant resolves and binds, the rest
  drop. Both mods ship identical entries (and clones for both worlds) so
  enabling League and Pax Bharata together stays consistent. The other
  16 vanilla projects referencing the techs are disable:true dead
  content and need nothing.


### Starting-nation picker cleanup
- Fixed the starting-nation picker showing vanilla nation names ("United
  States of America", "Egypt, Turkey+"...) mixed into the scenario's
  starter options: the landless ghost nations that used to block vanilla
  projects carried vanilla picker groups. Scenario-scoping made the whole
  ghost-lock architecture redundant, so ghost codes are now pruned from
  the nation lists entirely (~138 fewer inert nation states per world;
  starter options now show exactly the scenario's own nations).


### League of Nations: suborbital-carrier boost ramp + defection crises
- World boost 1660 -> 4150 t/yr (2.3x vanilla 2026's 1789): in a world
  where the upper-middle class treats a Singapore-Belize suborbital hop as
  a layover, the carriers' pads are enormous — and the League federation
  pools that capacity to every member, so the majors ride the corporate
  boost network... until the carriers start leaving.
- Four one-shot "League Defection Crisis" events (new
  TINarrativeEventTemplate ship): as federation membership falls past
  31/29/26/21 of 32, all six majors take +0.5/+0.5/+0.75/+1.0 unrest —
  the commuter economy seizing up as the charter system unravels.


### League of Nations: corporate naming pass for the 25 free cities
- The minors are now chartered carrier corporations (the majors are barred
  from intercontinental-capable vehicles beyond their monitored ICBMs);
  every free city gets a marketing-department brand — Hong Kong Global
  Commerce, Gulf Logistics Solutions, Pan-American Aerospace, Corisco
  Launch Systems, Kigali Ascent S.A., Saharan Transship Collective,
  Irie Skies Leisure Group, Caribbean Adventures Ltd., Pacifica
  Horizons S.A., TransAtlantic Leisure Trust…


### League of Nations: fragmentation tree implemented (2026-07-26)
- 96 successor states across 24 projects / 312 gated claims: 18 T2
  "Devolution" projects (IndependenceMovements, 2500rp — every non-capital
  core economic zone charters independence, grouped 3 per major by
  longitude; Mediterranean seeds take a partner region) and 6 T3
  "Dissolution" projects (FallofEmpires + all three devolutions, 5000rp —
  every remaining region joins its planned successor; rumps keep
  capital + 1-3 regions). Plan reviewed in
  lore/league-fragmentation-plan.md.


- (League of Nations sibling mod, not this Workshop item) Fixed a
  campaign-start crash: the game's AI planner needs every continent to
  keep a pickable capital nation after adjacency sweeps, which a 32-nation
  world of continental blocs violated. The Atlantic Resort Authority is
  now seated at the Azores and the American Common Interest Zone at
  Brasilia; a new build-time audit (check_ai_targets.py) enforces the
  constraint for all worlds.

## [1.1] — 2026-07-24 — game 1.0.47 update + balance overhaul

### Terra Invicta 1.0.47 compatibility (fixes campaign-start crash)
- Fixed a hard crash on new-campaign start under game 1.0.47: the game's new
  originalCapital repair walks every landless nation's claim bilaterals with
  no null check, and our ghost/anchor nations carry vanilla claims whose
  regions don't exist in a prefixed-region world. Every affected nation
  (139 in Pax Bharata, 113 in League of Nations, incl. PRA) now gets an
  inert, permanently-gated capitalClaim seed so the repair never runs.
- Migrated to 1.0.47's alias system: the game removed scenarioPrefix
  resolution and default-localization fallback. Region and army clones now
  carry localizationAlias (fixes region/army names showing as raw keys on
  1.0.47), and project anchor nations carry referenceAlias (fixes all
  generated unification/breakaway projects being unobtainable — their
  requiresNation no longer resolved).
- New shared generator pass `compat_1047.py`, applied automatically by
  gen_world / make_pax_bharata / make_league; validator extended for the
  gate-seed claims. The disabled RandomSol build in Mods/Disabled predates
  the fix — regenerate before re-enabling.
- Rebuilding the mod re-rolled the map color assignment (the color pass
  iterates a hash-ordered set, so each rebuild is a fresh but equally valid
  graph coloring) — some nations wear different colors than before.

### Clean research browser both ways (scenario-scoped projects)
- Vanilla unification/fragmentation projects no longer appear in Pax
  Bharata or League of Nations at all — 1.0.47 scenario-scoped 20 of them
  itself (its new scenarioTags filter), and we tag the remaining 35
  (Caliphate chain, Greater Dominion, Pan-Asia chain...) with the same
  NotPostApoc tag vanilla uses, which every playable vanilla scenario
  carries — so vanilla campaigns are untouched.
- Pax Bharata's 82 political projects (and the Crossroads of Empires
  crossing) now exist only in the Pax Bharata scenario: no more RW7
  clutter in vanilla or League research browsers, and vice versa.
  New generator pass `scenario_scope.py` (wired into all three build
  scripts). Old saves that were mid-research on a now-scoped project will
  not load cleanly — start a new campaign (already required this update).

### League of Nations: boot-crash hotfix (2026-07-25)
- Fixed a game-boot crash introduced by shipping TIStartTimeTemplate.json:
  the 1.0.47 mod-merge strict-parses the VANILLA file, which ships invalid
  JSON (missing comma). The vanilla file is now patched in place (backup
  .pavonis-orig.bak beside it) and make_league.py re-applies the patch if a
  game update reverts it. Upstream bug — any mod touching start times
  crashes 1.0.47 until Pavonis fixes their file.

### League of Nations: first in-game review pass (2026-07-25)
- Federation now displays "League of Nations" (union display names are
  localization keys, not template fields — fallback name was "Mediterranean
  Federation")
- Carve fixes: Réunion + Comoros join the Indian Ocean Resort Authority;
  Greenland + Iceland to the Americas; Baku + Yerevan to the Crossroads;
  Buraydah, Jedda, Port Sudan, Gao, Nouakchott to the Mediterranean;
  Kunming to the Archipelago
- Core economic zones rebuilt: scaffold shuffle wiped, vanilla-2026
  placements restored, 49 review additions (world total 90)
- Starting research rework: Advanced Chemical Rocketry / Mission to Space /
  Space Tourism pre-researched; Reusable Rockets, Climate Lab, Tourist
  Berth, Hab Living Quarters completed for every faction; Advanced
  Electromagnetism replaces Mission to Space as a starting option;
  cryo rockets / space science lab / materials lab no longer pre-done
- Starting stations rebuilt: Unity Station (Academy) and Concord Station
  (Exodus) in equatorial LEO — solar collector, tourist berth, quarters,
  plus a climate lab / life-science lab respectively

### New sibling mod in development: The League of Nations (not part of this
Workshop item) — an alt-history federated-Earth scenario (6 treaty-bound
majors, 26 League free cities holding all of Earth's launch capacity,
one world federation). Ships separately when ready.


**NOT save-compatible with 1.0 campaigns** (project dataNames changed in
the merge pass — start a new campaign).

### Project cleanup — the tech tree is one-quarter smaller (140 → 108)
- 18 groups of mutually-contradictory "doctrine" projects merged into
  single contested projects, where every rival's claims live side by side
  (the same prize, claimed twice over): Goujian's Patience (Yue + Wu +
  Minyue + Nanyue), The Middle Kingdom (Chu + Huainan + Ba + Luoyue), The
  Mekong Mandala (Siam + Lan Xang + Khmer), The Sound Tolls (Skåneland +
  Kvenland), Pelasyikó Imperium (Danubia + Hellas + Illyria + Mazovia),
  The Wild Fields (Donetsk + Sloboda + Volgaic), The Troubadour Crowns
  (Aragon + Aquitaine + Brittany), The Seven Council Fires (Oceti Sakowin
  + Anishinaabeg + Haudenosaunee + Tsalagihi Ayeli), The Morning Star
  Rises (Tsétsėhéstȧhese + Tovaangar + Karankawa), As-Sirat (Tripolitania
  + Toubou-Nu + Ngong Fang), Great Zimbabwe (Urozvi + Batswana + Bulozi),
  Sma-Tawy (Kemet + Nobee Gurgu), The Factors' War (the West African
  dynasty coasts), Lords of the Pampas (Het Poté + Lickanantay), The
  River Sea (Pindorama + Solimões), The Lion Throne (Sauvira + Makran).
- Atropaten's ambitions folded into Eranshahr (two claimants to one
  Persian restoration).
- NEW: The Great Wall (T1) — Qin, Yan, and Liang each claim the northern
  frontier; The Mandate of Heaven is now T2 and requires it. Qin's arc:
  Great Wall -> Mandate of Heaven -> All Under Heaven.

### Military & economy
- The Abbasid Caliphate finally musters one of its nine potential armies
  (Baghdad garrison), and lays a keel: 35 of 50 IPs of naval construction
  underway at start (fixed: buildNavyIPs is a fraction of the cost, not a
  raw IP count — it was rendering as an absurd 1750/50).
- The Kölgen Khanate floats a squadron home-ported at Sapporo (Hokkaido
  is not held from horseback), crossing the 4-CP line to support it
  (GDP 1,505B -> 1,720B, tribute from Huainan).
- The Gelejian Khanate can now actually field its horde (population
  3.78M -> 6.03M, over the game's 5M first-army floor; Kölgen settlers
  moved up the Amur) and lays a keel of its own (20 naval IPs; GDP
  150B -> 275B via Kurultai funding, qualifying for the 3-CP navy
  exception at 45.6k per-capita). The Bering invasion path is militarily
  achievable again.
- Navy GDP correction: the game scales every nation's GDP by 0.87 at
  load, which quietly dropped Kölgen to CP 3 and Gelejian to CP 2
  in-game — a tier short of their navies. Both boosted ~15% to clear the
  bar for real (Kölgen -> CP 4, Gelejian -> CP 3 + 45.6k per-capita),
  funded by the high-per-capita Persian merchant hybrids (Sakastan,
  Laristan, Makran).
- Army roster tuned to the in-game caps: Gurjaratra trimmed to a corps +
  a squadron; Sakastan, Laristan, and Makran keep only their colonial
  navy (naval merchant republics); the world police (Sauvira) gains an
  Atlantic squadron at São Tomé; Maharashtra fields another corps; the
  continent's peacekeeper Madhya-desa musters a corps at Patna and keeps
  a second building (70%); Bavaria raises two more.
- Global inequality push softened: multiplier 1.3 -> 1.225 (75% of the
  original). The world's fuses burn a little slower; the flagship
  extremes (the Caliphate's 7.02) are unchanged.
- New in-repo economy report (NATION_ECONOMY.md, regenerate with
  generator/nation_report.py) lists every nation's in-game GDP,
  per-capita GDP, inequality, and control points.
- Population relocation: the North African windfall had left Cyrenaica
  ($353k/head) and Tripolitania ($151k) grotesquely rich per-capita on
  tiny populations. Cyrenaica tripled and Tripolitania +30% bring them to
  4th and 5th in per-capita GDP, just behind Moko-uku; Iceland (+50%),
  Kvenland (+25%), and Taurida (+25%) also come down from their outlier
  highs. All 10M emigrants come from Wu's 231M — a ripple.
- Second population wave: Te Waipounamu, Miskitu Nani, Oceti Sakowin, and
  Lefatshe la Batswana each doubled (19M total from Nao Sauvira's 229M).
- Puelmapu cedes half its GDP to Khmer Janapada (115B transfer); the
  Pampas shrink, the Mekong rises.
- World population and GDP conserved.
- Space funding rebuilt from the ground up: funding is no longer tied to
  having a space program. Every nation except the five khanates now pays
  into space, proportional to GDP weighted by equality — the fairer the
  society, the more of its economy reaches orbit (a nation at inequality
  2.8 pays ~3.9x its GDP share; the Abbasid Caliphate at 7.1 pays a
  quarter of its). Global total drops 4,980 -> 2,000/yr (~85% of
  vanilla-2026's 2,343): 165 funders instead of 18, top funder Oyo Mesi
  at 81/yr instead of Baghdad at 800. Control points everywhere are worth
  something; nowhere is a money fountain.

### Research & education
- The research crown moves to Baghdad: the House of Wisdom never burned.
  Caliphate education 9.29 -> 10.0 (best on Earth), Sauvira 9.07 -> 7.34
  (guns before schools). The Caliphate is now the world's top research
  economy, 10% ahead of Sauvira.
- The learning gradient: colonized Europe's schools serve the factors'
  counting-houses. 29 European nations lose 1.0-2.0 education (Iceland
  and Brittany fall hardest, from world-class to middling; Aquitaine hits
  the continental floor at 4.2), leaving Europe barely ahead of the
  khanates. The eastern Mediterranean is exempt — Baghdad's light reaches
  Basileia, Hellas, Shqiperia, Sicily, Rome, and the Volga. Every point
  drained flows east: 38 nations across India, China, Persia, Indonesia,
  and Oceania below Akitsushima's 7.9 rise in proportion to how far they
  lag (Liang climbs most, 4.7 -> 7.6; Viet Janapada barely moves). Vanga's
  share is capped — 456M literate Bengalis would have contested Baghdad's
  crown outright; it settles for third on the research board, just ahead
  of Maharashtra. Education compounds — it drives research, GDP growth,
  and miltech, so the gradient deepens over a campaign.

### Inequality
- The inequality summit flattened: the five most unequal nations
  (Andhrapatha, Kalinga, Maharashtra, Gurjaratra, Nava Gurjaratra, all
  7.7-8.1 with a cliff below them) each shed a full point, spread as half
  a point over ten courts across the Indosphere and Persia (the
  Caliphate, Karunadu, Tamilakam, Laristan, Sauvira, Kharaveladesha,
  Kumarapalabhumi, Mayurasharmapura, Daharraje, Ashokanagar). The wealth
  gap is a civilizational feature, not five nations' pathology — and the
  Abbasid Caliphate now tops the board at 7.12.

### The Mountain Accord — Kyrgyzstan, the Switzerland of Central Asia
- Khorasan peacefully cedes the Dushanbe valley: the Tian Shan and the
  Pamirs become one highland confederation — isolationist, pacifist,
  democratic, literate, and armed with exactly one deterrent bomb.
- Kyrgyzstan sheds all its expansion claims (hostile paper claims on two
  khanates' lands were a standing war invitation) and is exempted from the
  border-disputes engine in both directions: the fortress republic disputes
  nothing, and nothing disputes it. Only the khanates covet the mountains —
  Tolui's claims on Bishkek and the valley are live from turn 1; Chagatai
  claims the valley outright but must dream the Chagatai Dream to claim
  Bishkek.
- The republic stays small by choice: Bishkek trims to 6M, the valley to
  5M, and 6.94M emigrants resettle in the Silk Road lowlands (Tajiks to
  Persian-speaking Khorasan, Fergana Turks to Tashkent, caravan families
  to Urumqi).
- A development levy (an equal ~7% cut across eight mid-rich periphery
  states: Kvenland, Skåneland, Brittany, Wabanakik, Quitu, Ingria, Jochi,
  Miskitu Nani) funds the union: GDP 1,189B -> 1,721B, holding the #2
  per-capita spot ($136k in-game, closer to #1 Outremer-Kala than #3
  Moko-uku) at CP 3.
- NEW PROJECT: Crossroads of Empires (T1, 4000 research) — a development
  consortium spanning five rival courts reopens the old Silk Road passes:
  a friendly-only Dushanbe-Peshawar crossing over the mountains, and a
  one-time GDP boom in all six corridor regions (Dushanbe +20%; Bishkek,
  Kabul, Peshawar, Urumqi, Tashkent +10%) — the vanilla bridge-project
  mechanics, first modded content in this world to use them.

### Diplomacy & flavor
- The two Japans: Tsukushi and Akitsushima are now rivals, each with a
  foreign patron — Korea backs Akitsushima, Maharashtra backs Tsukushi
  (and quietly claims it in The Dynasty Coasts).
- The UN survived the Concord — distributed across three rivals so no one
  chairs it alone: UN Office for Outer Space Affairs in New Delhi, UNESCO
  and the IMF in Mumbai, the WHO in Islamabad.

### Under the hood
- Federation-leadership claim gates retargeted to the merged projects.
- Validator now understands sparse vanilla-org overrides.

## [1.0] — 2026-07-19 — initial Workshop release

Workshop item 3767900104. 170 nations, 47 armies, 18 nuclear powers,
~107+33 projects, the Sauvira–Madhya-desa war, the powder keg, the claim
webs, and one free city with a plan. See the full history at
https://demeisen6.github.io/pax-bharata/
