# Eternal Dao Stellarity - Design Document

**Mod Name:** Eternal Dao Stellarity  
**Race Name:** Eternal Celestials  
**Version:** 1.0.0  
**Target Game:** Distant Worlds 2

---

## Table of Contents

1. [Mod Overview and Goals](#mod-overview-and-goals)
2. [Race/Faction Specifications](#racefaction-specifications)
3. [10 Main Story Arcs](#10-main-story-arcs)
4. [8 Racial Features](#racial-features-raceid-99--detailed-implementation)
5. [8 Episodic Events](#8-episodic-events)
6. [Unique Components](#unique-components)
7. [Unique Facilities](#unique-facilities)
8. [Research Tree Structure](#research-tree-structure)
9. [Technical Implementation Notes](#technical-implementation-notes)
10. [Balance and Tuning](#balance-and-tuning)

---

For the complete design document content, please see the full document in the repository.

This file has been created as a placeholder. The implementation team should populate it with:

- Detailed race specifications
- Complete story arc implementations
- All racial features and episodic events
- Component and facility stats
- Research tree structure
- Technical implementation guidelines
- Balance parameters

Refer to WIKI.md for lore consistency.

---

_Document prepared by the Ministry of Polite Exiles, Technical Standards Division_

---

## Racial Features (RaceId 99) – Detailed Implementation

This section defines the eight Eternal Celestials racial features as concrete [GameEvents](../docs/DW2_GAME_EVENTS.md) and related Colony Events to be implemented in GameEvents_zEternalDaoRacialFeatures.xml and ColonyEventDefinitions_zEternalDao.xml.

Conventions

- Event names double as ids; keep the plain-English titles shown below when authoring XML.
- PlacementRaceID: 99 (Eternal Celestials) or 255 (player empire) when appropriate.
- TriggerActionsAreChoices: true where player choices are presented.
- Use StartColonyEvent to apply time-limited effects (colony and empire-level) per base-game convention (compare Great Art Exposition, Quameno Contemplation, Zenox Shadow Council). Cooldowns are handled with companion colony events rather than timers or stored “charges”.
- Use GenerateCharacter, ClearCharacterTraits, AddCharacterTrait, and SetCharacterTraitsKnown to mint unique Dao Tech paragons (define bespoke trait ids in `CharacterTraitDefinitions_zEternalDao.xml`).
- Whenever disabling or re-enabling a follow-up, rely on DisableEvent/EnableEvent hooks fired from the end of a colony event.
- All content must remain compatible with DW2-XL (see `DW2-XL/XL/`). When introducing new ship hulls or sizes, cross-reference `dw2-eternal-dao/SHIPHULLS.csv` so we stay within established size brackets (e.g., Zenox capital hull size 11).

Dao Tech Synergy Flow

- Empyreal Ascension births Celestial Paragons (CE_AscensionResonance / CE_AscensionArmada / CE_AscensionLegions) who establish temporary research or combat lattices.
- While Paragons are active, Sovereign Decree Engine selections (CE*Decree*\*) can be layered safely; vent events kick in through CE_Decree_PressureVent to prevent runaway stacking.
- Mandala Archive Overmind’s first-contact branch populates empire modifiers (CE_Mandala_Siphon or CE_MandalaAttunement) that extend decree uptime and inform Chrono-Refraction decisions.
- Stellar Crucible Forges then channels those empire-wide buffs into capital hull specialisations aligned with XL size tiers and research unlocks.
- Horizonfold Armada Doctrine, Dao Gate Exodus Networks, and Chrono-Refraction Protocols are tactical outlets: each fires immediately when triggered, forcing the player to convert empire-wide tempo into fleet strikes, fortified bastions, or accelerated construction.
- Celestial Tributary Accord captures the economic fallout of those manoeuvres, stabilising maintenance and trade while the Senate’s Dao Tech bureaucracy audits karmic flow.

Flags/Counters

- eternal_pressure_index: increments with each Sovereign Decree; vents knock the value back down to prevent runaway stacking.

Note: ColonyEventDefinitions_zEternalDao.xml must include the referenced ColonyEventIds with the specified effects and durations.

---

## Government Features (GovernmentId 32000 – Eternal Dao Ascendancy)

All mechanics are single-step and timer based—no stacking counters, no hidden states. Each feature enters play via a colony event or direct government hook and expires automatically when its timer ends.

### Celestial Praxis Ledger
- **Gameplay:** Every completed research project grants the empire +8% Research Speed for 3 years.
- **Implementation:**
  - `GameEvents_zEternalDaoGovernment.xml` → `Celestial Praxis Ledger Trigger (Eternal Dao)` (`TriggerType=ResearchBreakthrough`, `PlacementRaceId=99`).
  - Trigger action: `StartColonyEvent` (`Id=CE_PraxisWave`; `DurationMinimum/Maximum=3`; `Bonuses → ResearchAll Amount=0.08`, `AppliesTo=Empire`).
  - Because the colony event fully replaces itself, no separate cooldown is required.

### Mandate Concord Hearings
- **Gameplay:** Manual colony action that costs 15,000 credits and grants +6 colony happiness plus +5% empire-wide corruption reduction for 2 years.
- **Implementation:**
  - `ColonyEventDefinitions_zEternalDao.xml` entry `CE_MandateConcord` with `CanBeManuallyInitiated=true`, `ManualInitiationCost=15000`, `Duration=2`, `MinimumIntervalPerEmpireYears=3`.
  - Apply `ColonyHappiness=6` and `Bonuses → ColonyCorruptionReduction Amount=0.05` (`AppliesTo=Empire`).
  - Add `GovernmentFilterIds` containing `32000` so only Eternal Dao colonies can initiate it.

### Tribunal of Cascading Laws
- **Gameplay:** When war begins, gain +10% weapons damage empire-wide and -5 diplomacy bias for 3 years.
- **Implementation:**
  - In `Governments_zEternalDao.xml`, set `WarStartColonyEventDefinitionId` to `CE_TribunalCascadingLaws`.
  - `ColonyEventDefinitions_zEternalDao.xml`: `Duration=3`, `Bonuses → WeaponsDamage Amount=0.10`, `Bonuses → Diplomacy Amount=-0.05`, `ValuesDecayGradually=false`.

### Ley Tide Auditor Swarms
- **Gameplay:** Random colony inspection that arrives roughly once every 10 years, boosting mining output by 15% for 2 years while skimming 10% of colony income to pay the auditors.
- **Implementation:**
  - `ColonyEventDefinitions_zEternalDao.xml` entry `CE_LeyTideAudit` with `RandomInitiationChance=0.1`, `MinimumIntervalPerEmpireYears=8`, `Duration=2`.
  - Apply `Bonuses → MiningRate Amount=0.15` (`AppliesTo=Empire`) and `Bonuses → ColonyIncome Amount=-0.10` (`AppliesTo=Colony`).
  - Include `GovernmentFilterIds=32000` so other governments never roll it.

### Karmic Vent Festivals
- **Gameplay:** Manual colony action costing 10,000 credits that reduces corruption by 10% for 1 year at the price of -5 colony happiness.
- **Implementation:**
  - `ColonyEventDefinitions_zEternalDao.xml` entry `CE_KarmicVent` with `CanBeManuallyInitiated=true`, `ManualInitiationCost=10000`, `Duration=1`, `MinimumIntervalPerEmpireYears=2`.
  - Set `ColonyHappiness=-5` and `Bonuses → ColonyCorruptionReduction Amount=0.10` (`AppliesTo=Empire`).

### Chronicle of Wandering Gates
- **Gameplay:** Colonising any new world triggers a 1-year reduction to survey and scan times by 20% empire-wide.
- **Implementation:**
  - Game events `Chronicle of Wandering Gates (Peaceful)` (`TriggerType=AcquireColonyPeacefully`) and `Chronicle of Wandering Gates (Conquest)` (`TriggerType=ConquerColony`), both restricted to `PlacementRaceId=99`.
  - Trigger action: `StartColonyEvent Id=CE_ChronicleWanderingGates` (Duration 1 year; `Bonuses → ScanSurveyTimeReduction Amount=0.20`, `AppliesTo=Empire`).

### Astral Tax Immunities
- **Gameplay:** Signing any trade treaty grants +12% trade income and -5% ship maintenance for 3 years.
- **Implementation:**
  - Game events for each treaty type: `Astral Tax Immunity (Restricted)`, `(Limited)`, `(Free)`, each with `TriggerType=EmpireRelationChange` and the corresponding `TriggerEmpireRelationType`.
  - Action: `StartColonyEvent Id=CE_AstralTaxImmunity` (Duration 3 years; `Bonuses → TradeIncome Amount=0.12`, `Bonuses → ShipMaintenanceAll Amount=-0.05`, `AppliesTo=Empire`).

### Sovereign Quietus Protocol
- **Gameplay:** Peace treaties trigger a 2-year period that accelerates war-weariness recovery (+25% War Weariness Reduction) while nudging colony happiness down by 5.
- **Implementation:**
  - Game event `Sovereign Quietus Protocol Trigger` (`TriggerType=EmpireRelationChange`, `TriggerEmpireRelationType=Peace`, `PlacementRaceId=99`).
  - Action: `StartColonyEvent Id=CE_SovereignQuietus` (Duration 2 years; `Bonuses → WarWearinessReduction Amount=0.25`, `ColonyHappiness=-0.05`, `ValuesDecayGradually=false`).

_Existing content:_ `Dao Tech Exhibition` remains as a manual colony festival (already implemented) and can be highlighted alongside the new toolkit where needed.

---

## Government Facilities — Eternal Mandate Infrastructure

All facilities reside in `EternalDao/PlanetaryFacilityDefinitions_zEternalDao.xml`. Dedicated `FacilityFamilyId` values prevent multiple tiers from coexisting; upgrades always replace the previous structure. Tier I facilities are created during empire initialization, while higher tiers require Eternal Dao-exclusive research.

| Line | Family Id | Tier | Facility Id | Name | Cost | Maintenance | Key Effects |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Governance | 320 | I | 32000 | Eternal Mandate Outpost Nexus | 0 (starting build) | 8 000 | Colony: -30% corruption, +10 happiness, +8 development, +6 income; 30k corruption aura. |
|  |  | II | 32001 | Eternal Mandate Concord Lattice | 75 000 | 11 000 | Aura radius 45k; Empire: +5% diplomacy, +5% trade; colony income +8%. |
|  |  | III | 32002 | Eternal Mandate Steward Bastion | 95 000 | 14 000 | Colony development +12%; Empire: +4% corruption reduction, +8% counter-espionage; aura 60k. |
|  |  | IV | 32003 | Eternal Mandate Harmonic Zenith | 120 000 | 18 000 | Empire: min colony happiness 12, +10% research all, +10% diplomacy; colony income +10%; aura 75k. |
| Fleet | 321 | I | 32010 | Origin Dao Command Spire | 0 (starting build) | 12 000 | Colony: +25% ship build, +15% retrofit, +10% targeting; −5% tourism. |
|  |  | II | 32011 | Origin Dao Stellar Aerie | 110 000 | 16 000 +2 Zenite/yr | Colony dock/repair +15%; Empire: +8% maneuver, +5% weapons damage. |
|  |  | III | 32012 | Origin Dao Meridian Citadel | 140 000 | 20 000 | Colony: +1 carrier hangar, +20% shield recharge; Empire: +10% command range, +10% engagement speed; −5% trade. |
|  |  | IV | 32013 | Origin Dao Ascendant Throne | 180 000 | 26 000 | Empire: -10% ship maintenance, +15% weapons damage, annual 30-day rapid deployment buff; +3 empire corruption. |
| Legion | 322 | I | 32020 | Eternal Dao Muster Forge | 0 (starting build) | 12 000 | Colony: +25% troop recruitment, +15% experience, +10% defense; consumes 5 000 Steel/yr. |
|  |  | II | 32021 | Eternal Dao Phalanx Citadel | 95 000 | 15 000 | Colony boarding +15%; Empire: +10% ground strength, +10% troop recovery. |
|  |  | III | 32022 | Eternal Dao Legion Sanctum | 120 000 | 18 000 | Colony happiness −4; grants elite troop every 3 years; Empire: siege bonus +15%, garrison upkeep −10%. |
|  |  | IV | 32023 | Eternal Dao Apex Cohort | 150 000 | 22 000 | Empire: +15% assault capacity, +15% troop damage reduction, wartime Mandate Rally (180-day +25% ground strength); consumes 1 Glacium +1 Chromium/yr. |

Implementation notes:

- Upgrades fire through a dedicated colony event that destroys the prior tier before building the new one, preventing double maintenance.
- Resource upkeep may use negative `AddCargo` actions; if stability issues appear we fall back to equivalent `Money` drains tied to commodity prices.
- Corruption aura leverages `ColonyCorruptionReductionProjectionRange`, scaling 30k → 45k → 60k → 75k.
- Maintenance magnitudes presume late-game Eternal Dao economy; revisit after integration testing with other government bonuses.

## Government Research — Exclusive Unlock Tree

`EternalDao/ResearchProjectDefinitions_zEternalDao_Government.xml` houses three four-step chains (rows 487–489). Each project carries `GovernmentId=32000` and references vanilla research to stay inside the canonical progression.

| Row | Column | Project Id | Name | Unlocks | Prerequisites |
| --- | --- | --- | --- | --- | --- |
| 487 | 0 | 348700 | Eternal Mandate Concord Schematics | Governance Tier II | System Governance (`ProjectId=1042`). |
| 487 | 1 | 348701 | Eternal Mandate Steward Protocols | Governance Tier III | Governance Tier II + Sector Governance (`ProjectId=1043`). |
| 487 | 2 | 348702 | Eternal Mandate Harmonic Doctrine | Governance Tier IV | Governance Tier III + Quadrant (`ProjectId=1044`) + Galactic Governance (`ProjectId=1045`). |
| 488 | 0 | 348710 | Origin Dao Stellar Doctrine | Fleet Tier II | Advanced Command (`ProjectId=1272`). |
| 488 | 1 | 348711 | Origin Dao Meridian Charter | Fleet Tier III | Fleet Tier II + Strategic Command (`ProjectId=1273`). |
| 488 | 2 | 348712 | Origin Dao Ascendant Mandate | Fleet Tier IV | Fleet Tier III + Supreme Command (`ProjectId=1274`). |
| 489 | 0 | 348720 | Eternal Dao Phalanx Treatise | Legion Tier II | Improved Crew Compartments (`ProjectId=1110`) + Shipboard Marines (`ProjectId=1111`). |
| 489 | 1 | 348721 | Eternal Dao Legion Codex | Legion Tier III | Legion Tier II + Elite Shipboard Marines (`ProjectId=1113`). |
| 489 | 2 | 348722 | Eternal Dao Apex Edict | Legion Tier IV | Legion Tier III + Ultimate Shipboard Garrisons (`ProjectId=1114`). |

Additional implementation details:

- Projects beyond Tier I include `AdditionalRequirements` pointing to the previous Eternal Dao research ID to enforce linear progression.
- Each project uses `UnlocksFacilityId` where supported; otherwise we trigger a `TriggerEventOnResearchCompletion` that presents the upgrade prompt.
- Tier I prerequisites (Planetary Administration, Improved Command, Improved Crew Compartments, Shipboard Marines) are granted during the government start event so players begin with full baseline infrastructure.
- Column placement (0–2) keeps the three chains compact; adjust in the editor if tree overlap occurs with vanilla techs.

---

## Luxury Resource Suite – Eternal Dao Exclusives

Sixty cultivation-grade luxury resources power Eternal Dao economies, morale, research lattices, and martial formations. They remain exclusive to Eternal Dao start conditions, reinforcing the faction's bespoke supply chains.

Design guardrails:

- **Tier distribution:** 10 resources per tier (1 through 6). Base costs scale 10/20/30/40/50/100.
- **Category cadence:** Within Tiers 1–5 each of the four colony categories appears twice, with one ship-focused military resource, one troop-focused military resource (dual-purposed with Research where noted), and one dedicated Research effect. Tier 6 ascends to super luxuries that stack three synergistic bonuses.
- **Bonus bands:** Each modifier respects the per-tier bonus windows (1–3 %, 3–6 %, 6–10 %, 10–15 %, 15–20 %, 20–30 %). Tier 6 individual bonuses stay inside 20–30 %.
- **Lore touchstones:** Every name references naturally occurring Dao phenomena—spirit flora, beast harvests, mineral veins, astral delicacies—sprinkled with rare easter eggs like Meimei's famed street food.

### Tier 1
| # | Resource Name | Category | Base Cost | Bonuses | Description | Image |
| - | ------------- | -------- | --------- | ------- | ----------- | ----- |
| 1 | Meridian Basalt Nodes | Colony Development | 10 | ColonyDevelopment +3% (Colony) | Hexagonal basalt nodules saturated with steady qi currents; settlers hammer them into new foundations to anchor city grids. | UserInterface/Placeholder |
| 2 | Glowmoss Cartographer | Colony Development | 10 | ColonyDevelopment +2% (Colony) | Bioluminescent moss that maps ley-line contours as it creeps, giving survey crews a living topographical guide. | UserInterface/Placeholder |
| 3 | Amberfen Spice Resin | Colony Income | 10 | ColonyIncome +3% (Colony) | A fragrant resin tapped from amber marsh reeds; caravans prize it for elevating everything from noodles to nebula tea. | UserInterface/Placeholder |
| 4 | Stargrazer Jerky Strips | Colony Income | 10 | ColonyIncome +2% (Colony) | Slow-dried slices of migratory stargrazer flank that keep traders lingering—and spending—at frontier waystations. | UserInterface/Placeholder |
| 5 | Astralseed Millet | Colony Growth | 10 | ColonyPopulationGrowthRate +3% (Colony) | Millet raised in low-gravity paddies so each grain hums with astral vitality, strengthening young families. | UserInterface/Placeholder |
| 6 | Shellfoam Broth Clams | Colony Growth | 10 | ColonyPopulationGrowthRate +2% (Colony) | Foam-shelled clams gathered from dao reefs; their broth restores exhausted settlers after long shifts. | UserInterface/Placeholder |
| 7 | Lanternfruit Citrus | Colony Happiness | 10 | ColonyHappiness +3% (Colony) | Citrus gourds that glow like lanterns and brighten communal feasts during protracted frontier nights. | UserInterface/Placeholder |
| 8 | Songmist Bloom Petals | Colony Happiness | 10 | ColonyHappiness +2% (Colony) | Petals that release chime-soft vapor when steeped, smoothing tempers in even the busiest quarter. | UserInterface/Placeholder |
| 9 | Skywhorl Ray Fins | Military (ships) | 10 | ShipManeuvering +2% (Empire) | Shed fins from skywhorl rays, bonded to hull canards to let cutters tack cleanly through grav eddies. | UserInterface/Placeholder |
| 10 | Ironpaw Tusk Fibers | Military (troops) / Research | 10 | TroopRecoveryRate +2% (Empire), ResearchTroops +1% (Empire) | Resilient fibers teased from ironpaw boar tusks; drill sages weave them into wraps that hasten recovery and inspire new formations. | UserInterface/Placeholder |

### Tier 2
| # | Resource Name | Category | Base Cost | Bonuses | Description | Image |
| - | ------------- | -------- | --------- | ------- | ----------- | ----- |
| 11 | Skyforge Anchorite | Colony Development | 20 | ColonyDevelopment +6% (Colony) | High-altitude ore that crystallizes along turbine cliffs and locks aerial platforms into precise alignments. | UserInterface/Placeholder |
| 12 | Leyburrow Heartwoods | Colony Development | 20 | ColonyDevelopment +4% (Colony) | Heartwood roots that tunnel along ley-rivers, knitting entire districts against seismic shocks. | UserInterface/Placeholder |
| 13 | Meimei's Pork Buns | Colony Income | 20 | ColonyIncome +6% (Colony) | Beloved buns stuffed with lotus-fed pork; gourmet pilgrims queue for hours and flood nearby stalls with coin. | UserInterface/Placeholder |
| 14 | Sunbraid Pepper Pods | Colony Income | 20 | ColonyIncome +4% (Colony) | Slender peppers ripened in braided sun nets whose aroma alone sparks bidding wars between spice guilds. | UserInterface/Placeholder |
| 15 | Moonquill Kelp Spores | Colony Growth | 20 | ColonyPopulationGrowthRate +5% (Colony) | Drifting kelp spores that settle in nursery pools and quicken infant qi circulation. | UserInterface/Placeholder |
| 16 | Glaciant Marrow Pearls | Colony Growth | 20 | ColonyPopulationGrowthRate +4% (Colony) | Frost-born marrow pearls dissolved into restorative tonics that harden constitutions against brutal climates. | UserInterface/Placeholder |
| 17 | Festival Thunder Plums | Colony Happiness | 20 | ColonyHappiness +6% (Colony) | Plums that crackle with celebratory thunder qi when peeled, igniting laughter across auditing halls. | UserInterface/Placeholder |
| 18 | Cloudrest Velvetdown | Colony Happiness | 20 | ColonyHappiness +4% (Colony) | Down shed by cloud yaks; plush cushions stuffed with it cradle exhausted clerks in weightless comfort. | UserInterface/Placeholder |
| 19 | Starstream Rudder Coral | Military (ships) | 20 | ShipSpeed +5% (Empire) | Coral branches that grow along nebula currents; ground to slurry they coat thrusters for sharper acceleration. | UserInterface/Placeholder |
| 20 | Thunderhorn Vanguard Spurs | Military (troops) / Research | 20 | TroopGroundAttack +5% (Empire), ResearchWeapons +4% (Empire) | Electrified spurs harvested from thunderhorn beasts empower shock troops while weapon scholars reverse-engineer the charge. | UserInterface/Placeholder |

### Tier 3
| # | Resource Name | Category | Base Cost | Bonuses | Description | Image |
| - | ------------- | -------- | --------- | ------- | ----------- | ----- |
| 21 | Heavenforge Quartz Veins | Colony Development | 30 | ColonyDevelopment +10% (Colony) | Prismatic quartz seams that resonate structural fields, letting architects suspend sprawling terraces with confidence. | UserInterface/Placeholder |
| 22 | Terrashift Magnetite | Colony Development | 30 | ColonyDevelopment +7% (Colony) | Magnetized ore drawn from tectonic shear zones that keeps orbital shipyards balanced atop lift platforms. | UserInterface/Placeholder |
| 23 | Dragoncloud Tea Sprigs | Colony Income | 30 | ColonyIncome +9% (Colony) | Tea sprigs nurtured in dragoncloud fog whose brewed steam fetches fortunes at celestial auctions. | UserInterface/Placeholder |
| 24 | Starbrine Salt Lattices | Colony Income | 30 | ColonyIncome +7% (Colony) | Crystalline lattices formed by evaporating comet tides, a culinary marvel chefs chase across the quadrant. | UserInterface/Placeholder |
| 25 | Auroral Nectar Gourds | Colony Growth | 30 | ColonyPopulationGrowthRate +9% (Colony) | Gourds that absorb auroral light and distill into prenatal tonics for families taming harsh moons. | UserInterface/Placeholder |
| 26 | Warmspire Broodfish | Colony Growth | 30 | ColonyPopulationGrowthRate +6% (Colony) | River fish raised in steam vents whose roe accelerates recovery throughout mining towns. | UserInterface/Placeholder |
| 27 | Serenity Cloud Lotus | Colony Happiness | 30 | ColonyHappiness +9% (Colony) | Lotus blooms suspended in perpetual mist that, when steeped, calm entire administrative wards. | UserInterface/Placeholder |
| 28 | Joyflare Lychee | Colony Happiness | 30 | ColonyHappiness +7% (Colony) | Iridescent lychee bursting with laughter qi that dissolves unrest faster than proclamations. | UserInterface/Placeholder |
| 29 | Voidwing Spine Feathers | Military (ships) | 30 | WeaponsRange +8% (Empire) | Midnight feathers molted by voidwings and forged into targeting vanes that stretch beam arcs across battles. | UserInterface/Placeholder |
| 30 | Titanbane Myco Husk | Military (troops) / Research | 30 | TroopGroundDefense +8% (Empire), ResearchBioWarfare +6% (Empire) | Fungal husks gathered from titanbane groves fortify legion armor while researchers decode their anti-bioweapon secrets. | UserInterface/Placeholder |

### Tier 4
| # | Resource Name | Category | Base Cost | Bonuses | Description | Image |
| - | ------------- | -------- | --------- | ------- | ----------- | ----- |
| 31 | Starforge Obsidian Pillars | Colony Development | 40 | ColonyDevelopment +15% (Colony) | Obsidian pillars grown within solar furnaces that brace megacities against orbital shear. | UserInterface/Placeholder |
| 32 | Leyglow Aragonite | Colony Development | 40 | ColonyDevelopment +12% (Colony) | Iridescent aragonite clusters that absorb geomantic shocks and keep skyscraper districts level. | UserInterface/Placeholder |
| 33 | Phoenixcrest Saffron | Colony Income | 40 | ColonyIncome +14% (Colony) | Saffron strands plucked from phoenixcrest lilies; imperial chefs bid entire treasuries for a handful. | UserInterface/Placeholder |
| 34 | Starreef Truffle Coral | Colony Income | 40 | ColonyIncome +11% (Colony) | Reef coral that buds truffle-like pearls coveted by gourmand sects across the spiral. | UserInterface/Placeholder |
| 35 | Sunwell Terraced Grain | Colony Growth | 40 | ColonyPopulationGrowthRate +14% (Colony) | Terraced grain bathed in mirrored suns that sustains explosive settlement booms. | UserInterface/Placeholder |
| 36 | Cradlewyrm Bone Marrow | Colony Growth | 40 | ColonyPopulationGrowthRate +11% (Colony) | Gentle cradlewyrms yield nutrient-rich marrow distilled into tonics for overburdened colonies. | UserInterface/Placeholder |
| 37 | Jade Serenade Resin | Colony Happiness | 40 | ColonyHappiness +14% (Colony) | Aromatic resin that burns with jade-blue fire and blankets public plazas in tranquil harmonics. | UserInterface/Placeholder |
| 38 | Starlit Opera Pearls | Colony Happiness | 40 | ColonyHappiness +11% (Colony) | Pearls that hum ancient operas when warmed, enchanting festival crowds with shared nostalgia. | UserInterface/Placeholder |
| 39 | Stormglass Leviathan Scales | Military (ships) | 40 | ShieldRechargeRate +12% (Empire) | Scales flensed from stormglass leviathans and inlaid into hulls to accelerate shield regeneration. | UserInterface/Placeholder |
| 40 | Dragonbone Ward Shards | Military (troops) / Research | 40 | TroopGroundAttack +13% (Empire), ResearchAncientKnowledge +10% (Empire) | Runic dragonbone shards radiate martial wards while scholars decode the battle doctrine etched within. | UserInterface/Placeholder |

### Tier 5
| # | Resource Name | Category | Base Cost | Bonuses | Description | Image |
| - | ------------- | -------- | --------- | ------- | ----------- | ----- |
| 41 | Celestine Anchor Monoliths | Colony Development | 50 | ColonyDevelopment +20% (Colony) | Towering celestine monoliths that lock megacities into synchronous orbit-stabilized formations. | UserInterface/Placeholder |
| 42 | Chronotwine Elderwood | Colony Development | 50 | ColonyDevelopment +17% (Colony) | Elderwood vines that twist through time seams to knit entire districts against tectonic drift. | UserInterface/Placeholder |
| 43 | Sunsilk Tribute Pelts | Colony Income | 50 | ColonyIncome +19% (Colony) | Radiant pelts shed by sunsilk vulpines and traded as ceremonial tributes at staggering premiums. | UserInterface/Placeholder |
| 44 | Voidcurrant Amberwine | Colony Income | 50 | ColonyIncome +16% (Colony) | Amberwine fermented from vacuum-grown berries; monks guard each vintage like a treasury bond. | UserInterface/Placeholder |
| 45 | Eonheart Seedstones | Colony Growth | 50 | ColonyPopulationGrowthRate +19% (Colony) | Seedstones that pulse with ancient heartbeats, planted beneath nurseries to hasten communal recovery. | UserInterface/Placeholder |
| 46 | Heavenfeast Leviathan Roe | Colony Growth | 50 | ColonyPopulationGrowthRate +17% (Colony) | Luminescent roe from heavenfeast leviathans reserved for colonies racing through expansion. | UserInterface/Placeholder |
| 47 | Aurora Dream Incense | Colony Happiness | 50 | ColonyHappiness +20% (Colony) | Incense cones that project auroral visions overhead and dissolve civic unrest into awe. | UserInterface/Placeholder |
| 48 | Laughing Moon Petrogems | Colony Happiness | 50 | ColonyHappiness +16% (Colony) | Petrogems that ring with silvery laughter when struck, sealing reconciliation ceremonies with joy. | UserInterface/Placeholder |
| 49 | Chronosea Leviathan Tendons | Military (ships) | 50 | ShipSpeed +18% (Empire) | Elastic tendons that retain chronosea momentum; engineers braid them into engines for blitz-speed sorties. | UserInterface/Placeholder |
| 50 | War-Oracle Obsidian Eyes | Military (troops) / Research | 50 | TroopExperienceGain +18% (Empire), ResearchPsychic +15% (Empire) | Obsidian eye-stones carved from war-oracle statues enlighten generals while advancing psychic warfare studies. | UserInterface/Placeholder |

### Tier 6
| # | Resource Name | Category | Base Cost | Bonuses | Description | Image |
| - | ------------- | -------- | --------- | ------- | ----------- | ----- |
| 51 | Empyreal Mandala Geode | Super Luxury | 100 | ColonyDevelopment +26% (Colony), ResearchAll +23% (Empire), TradeIncome +22% (Empire) | Mandala-shaped geodes cracked open to reveal harmonic crystals that uplift infrastructure, scholarship, and trade. | UserInterface/Placeholder |
| 52 | Phoenixheart Dawn Nectar | Super Luxury | 100 | ColonyHappiness +25% (Colony), ColonyDevelopment +23% (Colony), ColonyPopulationGrowthRate +22% (Colony) | Nectar gathered at sunrise from phoenixheart blossoms; a single draught sparks civic zeal and flourishing families. | UserInterface/Placeholder |
| 53 | Starwyrm Aurora Heart | Super Luxury | 100 | ShipManeuvering +23% (Empire), WeaponsDamage +24% (Empire), TroopGroundAttack +22% (Empire) | Crystalline starwyrm hearts pulse with aurora fire, syncing fleets and legions into ferocious harmony. | UserInterface/Placeholder |
| 54 | Chronotidal Empyrean Pearl | Super Luxury | 100 | ColonyIncome +24% (Colony), TradeIncome +23% (Empire), Diplomacy +21% (Empire) | Pearls grown where time tides meet let merchants sense profitable futures and diplomats read the room. | UserInterface/Placeholder |
| 55 | Eclipsepetal Lotus Crown | Super Luxury | 100 | ColonyDevelopment +23% (Colony), ResearchAncientKnowledge +22% (Empire), ColonyHappiness +21% (Colony) | Lotus crowns that bloom only during eclipses, weaving ancient schematics into urban hearts while enthralling citizens. | UserInterface/Placeholder |
| 56 | Dragonwhorl Tempest Scale | Super Luxury | 100 | ShieldRechargeRate +23% (Empire), ShipSpeed +24% (Empire), WeaponsRange +21% (Empire) | Tempest scales shed by dragonwhorl leviathans mid-storm supercharge admiralty cores and fleet responses. | UserInterface/Placeholder |
| 57 | Karmic Aurora Veil | Super Luxury | 100 | ColonyCorruptionReduction +25% (Empire), ColonyHappiness +22% (Colony), Diplomacy +21% (Empire) | Veils spun from auroral silk filter karmic smog, brighten populations, and make every negotiation gleam. | UserInterface/Placeholder |
| 58 | Voidjade Oracle Husk | Super Luxury | 100 | ResearchPsychic +24% (Empire), CounterEspionage +22% (Empire), Espionage +21% (Empire) | Fossilized voidjade oracle husks sharpen allied clairvoyance while confounding rival agents. | UserInterface/Placeholder |
| 59 | Heaven's Ember Lychee | Super Luxury | 100 | ColonyPopulationGrowthRate +24% (Colony), TroopRecruitment +23% (Empire), ColonyDevelopment +22% (Colony) | Lychee ripened within volcano monasteries kindle fertile booms, eager recruits, and rapid civic expansion. | UserInterface/Placeholder |
| 60 | Daoheart Ocean Pearl | Super Luxury | 100 | ColonyIncome +25% (Colony), ResearchHighTech +22% (Empire), TradeIncome +23% (Empire) | Ocean pearls grown around daoheart currents bankroll research foundries and interstellar trade hubs alike. | UserInterface/Placeholder |


### 1) Empyreal Ascension Cycle

Summary

- Research and megaproject breakthroughs trigger the creation of Celestial Paragons—legendary Dao Tech prodigies who coordinate research, fleets, or ground forces through synchronized cultivation networks.

Events

- Name: Empyreal Ascension Trigger (Eternal Celestials)

  - TriggerType: ResearchBreakthrough (TriggerResearchProjectID = -1)
  - AdditionalPlacement: Build (TriggerBuildFacilityID = <EternalCelestialsMegaprojectIds>, e.g., Stellar Crucible, Dao Gate Nexus)
  - PlacementRaceID: 99
  - Conditions: CheckColonyEventIsNotActiveAnywhereInEmpire (CE_AscensionCooldown)
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Empyreal Ascension Choice (Eternal Celestials)
    - Delay 2-5 seconds for presentation

- Name: Empyreal Ascension Choice (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - TriggerActions (choices):
    - ChoiceButtonText: Elevate a Star-Seer
      - TriggerEvent -> GeneratedItemName: Empyreal Ascension Scientist (Eternal Celestials)
    - ChoiceButtonText: Forge a Celestial Strategist
      - TriggerEvent -> GeneratedItemName: Empyreal Ascension Admiral (Eternal Celestials)
    - ChoiceButtonText: Crown a Heavenward Marshal
      - TriggerEvent -> GeneratedItemName: Empyreal Ascension General (Eternal Celestials)

- Name: Empyreal Ascension Scientist (Eternal Celestials)

  - TriggerActions:
    - GenerateCharacter (Id1=-1, CharacterRole=Scientist, ActionLocationHint=HomeColony)
    - ClearCharacterTraits (Id1=-1)
    - AddCharacterTrait (Id1=<TraitCelestialParagonResearch>, repeat to add top-tier research traits)
    - SetCharacterTraitsKnown (Id1=-1)
    - StartColonyEvent Id = CE_AscensionResonance (duration 5 years; empire +20% research, +5% happiness as dao-lattice morale uplift)
    - StartColonyEvent Id = CE_AscensionCooldown (duration 5 years; no modifiers, used solely as gating flag)
    - GeneralStoryMessageToEmpire (MessageTitle="Empyreal Ascension", include lorem tie-in)

- Admiral and General variants mirror the above but focus on fleet damage/defense and ground assault strength respectively (use dedicated CE_AscensionArmada, CE_AscensionLegions).

Notes

- Ensure generated traits remain permanently revealed; set SetCharacterTraitsKnown.
- Cooldown is handled entirely by CE_AscensionCooldown, so no date-based timers are required.
- Pair the Paragon colony events with the Sovereign Decree buffs for compounding-but-temporary advantages; overstretch is prevented by the shared cooldown.

### 2) Mandala Archive Overmind

Summary

- Upon first contact or espionage successes, the Eternal Celestials decide whether to copy a rival’s Dao Tech lattice or weave it into a shared research accord, producing measured boosts rather than free breakthroughs.

Events

- Name: Mandala Archive Deployment Trigger (Eternal Celestials)

  - TriggerType: EmpireRelationChange (TriggerEmpireRelationType=FirstContact)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Mandala Archive Decision (Eternal Celestials)

- Name: Mandala Archive Decision (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Siphon Their Insights
      - TriggerEvent -> GeneratedItemName: Mandala Archive Siphon (Eternal Celestials)
    - ChoiceButtonText: Harmonize the Patterns
      - TriggerEvent -> GeneratedItemName: Mandala Archive Attune (Eternal Celestials)

- Name: Mandala Archive Siphon (Eternal Celestials)

  - TriggerActions:
    - StartColonyEvent Id = CE_Mandala_Siphon (duration 5 years; empire +22% research, +12% espionage offense, -5% diplomacy bias with the contacted empire; narratively framed as Dao Tech lattice extraction)
    - DisableEvent (Mandala Archive Deployment Trigger (Eternal Celestials) applied to the contacted empire via GeneratedItemEventNames)
    - GeneralStoryMessageToEmpire (explain cultivation arrays copying their data lattice)

- Name: Mandala Archive Attune (Eternal Celestials)

  - TriggerActions:
    - StartColonyEvent Id = CE_MandalaAttunement (duration 6 years; empire +18% research, +10% counter-intelligence, +8% trade income with the contacted empire; Dao Tech treaty flavour)
    - DisableEvent (Mandala Archive Deployment Trigger (Eternal Celestials) applied to the contacted empire via GeneratedItemEventNames)
    - GeneralStoryMessageToEmpire (noting harmonious Dao Tech sharing)

Notes

- After the choice, rely on a paired `Mandala Archive Cooldown (Eternal Celestials)` event attached via `GeneratedItemEventNames`; once its colony event ends, use `EnableEvent` to restore the initial trigger if later content requires it.
- Mirrors Zenox infiltration potency but frames it through Dao Tech cultural exchange rather than raw theft.

### 3) Sovereign Decree Engine

Summary

- Building the Eternal Mandate Senate unlocks stacked empire-wide decrees that push every economic lever into overdrive while building celestial pressure; when pressure spikes, automatic “vent” events unleash collateral effects.

Events

- Name: Sovereign Decree Engine Trigger (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <EternalMandateSenateId>)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Sovereign Decree Selection (Eternal Celestials)

- Name: Sovereign Decree Selection (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices (each StartColonyEvent applies empire modifiers for 6 years and adds +1 eternal_pressure_index):
    - ChoiceButtonText: Supreme Mobilization Decree
      - StartColonyEvent Id = CE_Decree_Mobilization (+25% ship build speed, +15% troop strength, +4% empire maintenance)
      - IncrementGlobalVariable (eternal_pressure_index, +1)
      - TriggerEvent -> Sovereign Decree Vent (Eternal Celestials) [Conditions: VariableComparison (eternal_pressure_index >= 3)]
    - ChoiceButtonText: Limitless Insight Decree
      - StartColonyEvent Id = CE_Decree_Insight (+30% research, +15% colony development rate, +5% corruption)
      - IncrementGlobalVariable (eternal_pressure_index, +1)
      - TriggerEvent -> Sovereign Decree Vent (Eternal Celestials) [Conditions: VariableComparison (eternal_pressure_index >= 3)]
    - ChoiceButtonText: Absolute Concord Decree
      - StartColonyEvent Id = CE_Decree_Concord (+20 diplomacy bias to all empires, +15% trade income, -10% war weariness accumulation)
      - IncrementGlobalVariable (eternal_pressure_index, +1)
      - TriggerEvent -> Sovereign Decree Vent (Eternal Celestials) [Conditions: VariableComparison (eternal_pressure_index >= 3)]

- Name: Sovereign Decree Vent (Eternal Celestials)

  - TriggerActions:
    - StartColonyEvent Id = CE_Decree_PressureVent (duration 540 days; empire -6% happiness, +6% weapons damage)
    - IncrementGlobalVariable (eternal_pressure_index, -2)
    - GeneralStoryMessageToEmpire (flavor text on stability restored)
    - TriggerEvent -> Sovereign Decree Vent (Eternal Celestials) [Conditions: VariableComparison (eternal_pressure_index >= 3)] (auto-chain until pressure falls below threshold)

Notes

- Players can intentionally stack decrees for outrageous power but must absorb periodic vent fallout.
- Mandala Attunement bonuses extend decree uptime, while Paragon cooldowns limit how often all three pillars align; Chrono-Refraction and Dao Gate events should look for active decree buffs before offering their own prompts for maximal synergy.

### 4) Stellar Crucible Forges

Summary

- Activating the Stellar Crucible facility unlocks Celestial-class capital hulls, flawless components, and empire-wide ship bonuses that dwarf vanilla capitals.

Events

- Name: Stellar Crucible Activation Trigger (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <StellarCrucibleId>)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Stellar Crucible Activation (Eternal Celestials)

- Name: Stellar Crucible Activation (Eternal Celestials)

  - TriggerActions:
    - ResearchProjectEnable (Id1=<CelestialCoreReactorProjectId>) — queues the Dao Tech reactor research without granting progress
    - UnlockFactionSpecialShipTech (Id1=<CelestialHullTechId>) — exposes Celestial hull templates sized within Zenox capital limits (size class 11 per `SHIPHULLS.csv`)
    - StartColonyEvent Id = CE_StellarCrucible (duration 6 years; empire +20% ship durability, +15% weapon damage, +10% capital-ship build speed)
    - GeneralStoryMessageToEmpire

Notes

- The unique hull sits on the Zenox capital chassis scale (size 11) to preserve XL balance while reframing stats through Dao Tech plating.
- Consider tying the project unlock to prior decree or ascension milestones so the buff cadence remains deliberate.
- The capital-ship build speed bonus stacks meaningfully with Chrono-Refraction’s acceleration branch, so timing both events rewards careful Senate planning.

### 5) Horizonfold Armada Doctrine

Summary

- Crushing a major enemy fleet lets the Eternal Celestials bend space-time around their armadas, granting catastrophic strike capabilities or impenetrable defenses on command.

Events

- Name: Horizonfold Doctrine Trigger (Eternal Celestials)

  - TriggerType: DestroyShipRole
  - TriggerShipRoles: CapitalShip, Carrier
  - Conditions: CheckColonyEventIsNotActiveAnywhereInEmpire (CE_HorizonfoldCooldown)
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Horizonfold Doctrine Choice (Eternal Celestials)

- Name: Horizonfold Doctrine Choice (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Phase-Lance Offensive
      - StartColonyEvent Id = CE_Horizonfold_Offense (duration 360 days; fleets gain +40% damage, +40% hyperjump range, ignore nebula penalties)
      - StartColonyEvent Id = CE_HorizonfoldCooldown (duration 720 days; no bonuses, only blocks repeat triggers)
    - ChoiceButtonText: Aegis of Stillness
      - StartColonyEvent Id = CE_Horizonfold_Defense (duration 360 days; fleets gain +50% shield capacity, +25% evasion, +15% repair rate)
      - StartColonyEvent Id = CE_HorizonfoldCooldown (duration 720 days; no bonuses, only blocks repeat triggers)

Notes

- Colony events should be empire-scoped; configure the corresponding `ColonyEventDefinitions` entries to apply fleet damage/defense modifiers targeted at RaceId 99 ships.
- `CE_HorizonfoldCooldown` should enable the trigger after it ends via `EnableEvent`, forcing an immediate doctrine choice after each qualifying kill and preventing stockpiling.
- Pairing the offensive stance with Sovereign Decree Mobilization maximises burst damage, while the defensive stance cushions decree vents or Chrono-Refraction downtime.
- The trigger can fire again only after `CE_HorizonfoldCooldown` ends, ensuring each eligible victory demands an immediate doctrinal choice.

### 6) Celestial Tributary Accord

Summary

- Every first major diplomatic contact or vassalization opportunity can be converted into either immediate tribute extraction or permanent loyal client-states through irresistible psionic decrees.

Events

- Name: Celestial Tributary Ultimatum Trigger (Eternal Celestials)

  - TriggerType: EmpireRelationChange (TriggerEmpireRelationType=FirstContact)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Celestial Tributary Ultimatum (Eternal Celestials)

- Name: Celestial Tributary Ultimatum (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Offer Ascension Accord
      - TriggerEvent -> GeneratedItemName: Celestial Tributary Accord (Eternal Celestials)
    - ChoiceButtonText: Demand Immediate Tribute
      - TriggerEvent -> GeneratedItemName: Celestial Tributary Extraction (Eternal Celestials)

- Name: Celestial Tributary Accord (Eternal Celestials)

  - TriggerActions:
    - SetDiplomaticRelationType (EmpireRelationType=Subjugation, TargetEmpire=-1)
    - AddEmpireIncidentFactorGeneral (Value1=+35, DurationDays=1440) to lock positive relations
    - StartColonyEvent Id = CE_AscensionAccord (duration 8 years; empire +18% tax income, +10% population growth from tributaries, +5% trade income with the subjugated empire)

- Name: Celestial Tributary Extraction (Eternal Celestials)

  - TriggerActions:
    - Money (Value1=+60000 credits instantly)
    - AddEmpireIncidentFactorGeneral (Value1=-20 after 540 days to simulate resentment)
    - StartColonyEvent Id = CE_AscensionTribute (duration 4 years; empire +15% trade income, -10 diplomacy bias with target, +10% corruption)

Notes

- Use Conditions to prevent repeating on the same empire unless relations reset below Neutral.
- Provides a binary choice between long-term power consolidation and explosive economic spikes.
- Tributary accords stabilize the maintenance penalties introduced by Sovereign Decree Mobilization, keeping the Dao Tech bureaucracy solvent.

### 7) Dao Gate Exodus Networks

Summary

- Mature Celestial worlds open Dao Gates that duplicate entire populations into newly seeded colonies or fortify frontier worlds with instant infrastructure.

Events

- Name: Dao Gate Resonance Online (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <DaoGateResonanceSpireId>)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> Dao Gate Exodus Choice (Eternal Celestials)

- Name: Dao Gate Exodus Choice (Eternal Celestials)

  - TriggerType: Undefined (invoked by other events)
  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Seed a New Star
      - TriggerEvent -> GeneratedItemName: Dao Gate Exodus Colonize (Eternal Celestials)
    - ChoiceButtonText: Reinforce the Bastion
      - TriggerEvent -> GeneratedItemName: Dao Gate Exodus Fortify (Eternal Celestials)

- Name: Dao Gate Exodus Colonize (Eternal Celestials)

  - TriggerActions:
    - GenerateShipBase (ShipRole=ColonyShip, Id1=<DaoGateColonyShipDesignId>, ActionLocationHint=NearestColony) — use the Zenox colonizer size brackets (size 14) to keep XL compatibility
    - Money (Value1=+15000 to cover immediate warp-gate costs)
    - StartColonyEvent Id = CE_DaoGate_Colonize (duration 3 years; empire +25% colony ship construction speed, +15% development on newly founded colonies)
    - DisableEvent (Dao Gate Resonance Online (Eternal Celestials))
    - EnableEvent (Dao Gate Resonance Online (Eternal Celestials)) — fired from CE_DaoGate_Colonize when it ends via GeneratedItemEventNames

- Name: Dao Gate Exodus Fortify (Eternal Celestials)

  - TriggerActions:
    - StartColonyEvent Id = CE_DaoGate_Fortify (duration 5 years; target colony +35% defensive strength, +15% resource extraction, +10 happiness)
    - DisableEvent (Dao Gate Resonance Online (Eternal Celestials))
    - EnableEvent (Dao Gate Resonance Online (Eternal Celestials)) — fired from CE_DaoGate_Fortify on completion

Notes

- The Dao Gate Resonance Spire facility should require high-population colonies, providing an in-game gating mechanism without needing direct population-change triggers.
- Because the choice fires immediately on facility completion, players cannot stockpile uses; they must decide whether the Dao Tech lattice supports expansion or consolidation right away.
- Colonize pairs naturally with Chrono-Refraction acceleration windows, while Fortify offsets the maintenance and happiness pressure created by Sovereign Decree vents.

### 8) Chrono-Refraction Protocols

Summary

- The Celestials briefly warp their Dao Tech time lattices, forcing a choice between raw acceleration or stabilised prosperity.

Events

- Name: Chrono-Refraction Relay Online (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <VoidPrismRelayId>)
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Chrono-Refraction Activation Prompt (Eternal Celestials)

- Name: Chrono-Refraction Activation Prompt (Eternal Celestials)

  - TriggerType: Undefined (invoked by other events)
  - TriggerActionsAreChoices: true
  - Conditions: CheckColonyEventIsNotActiveAnywhereInEmpire (CE_ChronoRefraction)
  - Choices:
    - ChoiceButtonText: Accelerate the Lattice
      - StartColonyEvent Id = CE_ChronoRefraction (duration 150 days; empire +60% construction speed, +45% research, +20% ship maneuverability, -20% sabotage success)
      - DisableEvent (Chrono-Refraction Relay Online (Eternal Celestials))
      - EnableEvent (Chrono-Refraction Relay Online (Eternal Celestials)) — fired from CE_ChronoRefraction upon completion
    - ChoiceButtonText: Stabilize Heaven’s Clock
      - StartColonyEvent Id = CE_ChronoRefractionStability (duration 180 days; empire +15% construction speed, +15% research, +10% happiness, +10% trade income)
      - DisableEvent (Chrono-Refraction Relay Online (Eternal Celestials))
      - EnableEvent (Chrono-Refraction Relay Online (Eternal Celestials)) — fired from CE_ChronoRefractionStability upon completion

Notes

- Future content can add additional triggers (e.g., research breakthroughs or tribulation events) by reusing the same prompt pattern.
- Cooldown is handled implicitly by the active colony event, so players must commit to an option each time a Void Prism Relay comes online—no stockpiling.
- Pair the acceleration option with Mandala Siphon windows for peak research spikes, or choose stability to smooth empire happiness before triggering Sovereign Decree vents.

Balancing Summary (initial values)

- Empyreal Ascension Cycle: Paragon promotions roughly every 6–8 years; each delivers a 5-year +20% research (or equivalent military boost) plus minor happiness increases before cooldown.
- Mandala Archive Overmind: 5–6 year research/espionage surges (either +22%/+12% with diplomatic strain or +18%/+10% with trade bonuses) without granting free tech.
- Sovereign Decree Engine: Decrees offer +25% build / +30% research / +20 diplomacy packages for 6 years, but vent events trigger once pressure reaches 3 stacks and apply -6% happiness.
- Stellar Crucible Forges: Six-year empire buff of +20% durability, +15% weapon damage, +10% capital ship build speed while unlocking XL-compatible Celestial hull research.
- Horizonfold Armada Doctrine: Post-kill decisions grant 360-day offensive (+40% damage) or defensive (+50% shields) fleet boosts, followed by a 720-day cooldown colony event.
- Celestial Tributary Accord: Long-term accord grants +18% tax, +10% growth, +5% trade for 8 years; extraction option pays 60k credits and +15% trade for 4 years at the cost of diplomacy.
- Dao Gate Exodus Networks: Each Resonance Spire triggers an immediate colonize-or-fortify decision—either a one-off colonizer spawn with +25% ship output or a 5-year +35% defense/+15% extraction buff.
- Chrono-Refraction Protocols: Every Void Prism Relay forces a choice between a 150-day +60% construction/+45% research surge or a 180-day +15% balanced stability buff; no stockpiling.
