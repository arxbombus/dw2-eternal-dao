# Eternal Dao Stellarity - Design Document

**Mod Name:** Eternal Dao Stellarity  
**Race Name:** Eternal Celestials  
**Version:** 1.0.0  
**Target Game:** Distant Worlds 2

---

## 1. Mod Overview and Goals

- Portray the Eternal Celestials as a grand sect of exiled multiversal stewards whose technology and cultivation are inseparable.
- Deliver campaign humor exclusively through storyline beats, prodigy antics, and player choices while keeping every setting element majestic.
- Provide a late-game friendly faction that rewards meticulous empire management and experimentation with infinite-tier research.
- Maintain compatibility with vanilla and DW2-XL schemas; all custom XML lives under `EternalDao/` namespaces.

## 2. Race / Faction Specifications

| Attribute        | Specification                                                                           |
| ---------------- | --------------------------------------------------------------------------------------- |
| Species          | Cultivating humans adopting the title “Eternal Celestials” while in exile               |
| Empire Type      | Authoritarian-bureaucratic sect with benevolent stewardship ethos                       |
| Home Context     | Mobile worldship fleet anchored in Dimensional Cluster Gamma-74-Ω                       |
| Government       | Ascendant Dao Council (nine Pillars, five Orders)                                       |
| Cultural Tone    | Grandiose, ritualistic, sincerely courteous                                             |
| Narrative Tone   | Story-driven comedy layered on top of solemn obligations                                |
| Playstyle        | Tall-wide hybrid; strong defensive lattices, overwhelming strikes, intense upkeep       |
| Starting Bonuses | Tier I versions of governance, arsenal, and legion facilities; access to Realm 1–5 tech |
| Weaknesses       | High maintenance costs, luxury dependency, longer early research times                  |

## 3. Narrative Pillars & Comedy Mandate

1. **Stewardship First:** Every system, facility, and character answers to the prime oath of safeguarding reality.
2. **Comedy Through Storyline:** Humor appears in scripted events, prodigy dossiers, and decision popups—never in the flavor of core structures or technology.
3. **Cultivation + Technology:** Dao research and futuristic engineering reinforce one another (e.g., reactors stabilized by chants, simulations that encourage enlightenment).
4. **Exile Perspective:** Campaigns emphasize rebuilding prestige, negotiating with local heavens, and earning the right to intervene.

## 4. Campaign Story Arcs (10)

1. **Odyssey of the Thousand Worldships** – Reunite scattered arks while balancing upkeep vs. expansion.
2. **Harmony Pavilion Synod** – Resolve a multi-faction crisis using synchronized cultivation rituals instead of war.
3. **Chronoseer Audit** – Investigate temporal corruption; comedic beats involve paperwork loops that trap villains.
4. **Mandala Archive Gambit** – Decide between siphoning or harmonizing rival tech lattices (humor via Rival’s bewilderment).
5. **Infinite Kitchen Festival** – Bao Meimei’s culinary tournament accidentally powers reactors; player chooses which dish to weaponize.
6. **Flame Emperor’s Shadow** – A cult claims the Flame Emperor mantle; expose them using righteous bureaucracy.
7. **Virtual Universe Hostage Run** – Rescue citizens trapped in a simulation, referencing Virtual Universe Company structures.
8. **Chaotic Origin Quarantine** – Stabilize experimental labs without detonating half the galaxy.
9. **Paragon Narrative Trial** – Wei Jian argues a case before cosmic tribunal; comedic testimony from sentient paperwork.
10. **Silent Wanderer Beacon** – Locate a signal hinting at the Wanderer’s return; ends with cliffhanger for future expansions.

## 5. Racial Features (8)

### 5.1 Meridian Vein Metropolises

- **Trigger:** `AcquireColonyPeacefully` with `PlacementRaceId = 99`; gated per colony via `CheckEventNotTriggered` (use `GeneratedItemEventNames`).
- **Choice Event:** _Meridian Alignment Edict_.
  - **Prosperity Alignment:** `StartColonyEvent Id = CE_Meridian_Prosperity` (duration 6 years; `ColonyDevelopment +12%`, `ColonyHappiness +8%`, `ColonyMaintenance -5%`).
  - **Bastion Alignment:** `StartColonyEvent Id = CE_Meridian_Bastion` (duration 6 years; `ColonyDefenseStrength +25%`, `ShieldRechargeRate +10%` (System), `ColonyIncome -5%`).
- **Audit Loop:** When either colony event ends, queue _Meridian Audit Review_ (no bonuses) to reopen the choice; uses `EnableEvent`/`DisableEvent` for clean cycling.

### 5.2 Sacred Beast Ministries

- **Trigger:** `EmpireCashflowLevel` ≥ 0 with `ConditionEvaluation = EvaluateUntilTriggered`, `RandomComparisonNormalized <= 0.25`, `EmpireHaveAnyCharacterAvailableWithTrait = KeeperOfHarmony`, and `CheckColonyEventIsNotActiveAnywhereInEmpire (CE_BeastMinistry_Cooldown)`.
- **Choice Event:** _Ministry Appointment Ledger_ selects a host colony (≥ 5 B population).
  - **Logistics Mandate:** `StartColonyEvent Id = CE_BeastMinistry_Logistics` (540 days; `TradeIncome +10%`, `ConstructionSpeed +6%`), `GenerateCharacter` beast envoy with trait `ProtectorOfLife`.
  - **Welfare Mandate:** `StartColonyEvent Id = CE_BeastMinistry_Welfare` (540 days; `ColonyHappiness +12%`, `PopulationGrowth +8%`).
  - **Frontier Mandate:** `StartColonyEvent Id = CE_BeastMinistry_Frontier` (540 days; `ColonyDefenseStrength +18%`, `ShipRepairRate +6%` in-system).
- **Administration Cost:** Each appointment deducts 2 500 credits (`Money Value1=-2500`) and starts `CE_BeastMinistry_Cooldown` (1 year, empire scope).

### 5.3 Ascension Ledger Tribunal

- **Trigger:** `ResearchBreakthrough` while `CheckColonyEventIsNotActiveAnywhereInEmpire (CE_LedgerAudit)`.
- **Choice Event:** _Ascension Ledger Hearing_.
  - **Expedite Review:** `StartColonyEvent Id = CE_Ledger_Expedite` (360 days; `ResearchAll +18%`, `EmpireMaintenance +10%`). When the event ends it fires _Ledger Compliance Check_ (triggered via `GeneratedItemEventNames`) which performs `RandomComparisonNormalized <= 0.3`; on failure start `CE_Ledger_Reprimand` (180 days; `TaxIncome -10%`, `ResearchAll -5%`).
  - **Perfect Compliance:** `StartColonyEvent Id = CE_Ledger_Compliance` (540 days; `ResearchAll +10%`, `EmpireMaintenance -10%`, `ColonyHappiness +5%`), no risk of reprimand.
- **Cooldown:** `StartColonyEvent Id = CE_LedgerAudit` (3 years; gate only).

### 5.4 Sect Cycle Harmonics

- **Trigger:** `ResearchBreakthrough` while no cycle event is active (`CheckColonyEventIsNotActiveAnywhereInEmpire` for `CE_SectCycle_Ledger`, `CE_SectCycle_Synod`, `CE_SectCycle_Retreat`).
- **Event Chain:** Three empire events rotate automatically:
  1. **Grand Ledger Circuit:** `StartColonyEvent Id = CE_SectCycle_Ledger` (1095 days; `TaxIncome +12%`, `ColonyCorruptionReduction +10%`). On completion, triggers _Harmony Synod Directive_.
  2. **Harmony Synod:** `StartColonyEvent Id = CE_SectCycle_Synod` (1095 days; `ColonyHappiness +10%`, `ConstructionSpeed +5%`). On completion, triggers _Sutra Retreat Directive_.
  3. **Sutra Retreat:** `StartColonyEvent Id = CE_SectCycle_Retreat` (1095 days; `HyperJumpSpeed +8%`, `WarWeariness -5%`). On completion, re-enables the initial trigger.
- **Manual Stabilization:** After any phase begins, a follow-up option allows spending 15 000 credits to extend that phase by 360 days via `StartColonyEvent Id = CE_SectCycle_Extension`; during the extension the cycle cannot advance.

### 5.5 Triune Mastery Council

- **Trigger:** `EmpireCashflowLevel` ≥ +50 000 and `CheckColonyEventIsNotActiveAnywhereInEmpire (CE_TriuneCouncil)` while not at war with pirates (`EmpireNotAtWarWithRace`, raceId 0).
- **Choice Event:** _Triune Council Convening_.
  - **Martial Directorate:** `StartColonyEvent Id = CE_Triune_Martial` (720 days; `ShipWeaponsDamage +15%`, `TroopRecruitment +10%`, `EmpireMaintenance +4%`).
  - **Formation Directorate:** `StartColonyEvent Id = CE_Triune_Formations` (720 days; `ShieldCapacity +15%`, `ShipEvasion +8%`, `RetrofitCost -10%`).
  - **Sustenance Directorate:** `StartColonyEvent Id = CE_Triune_Sustenance` (720 days; `FoodProduction +12%`, `ColonyHappiness +10%`, `TaxIncome -5%`).
- **Cooldown:** `StartColonyEvent Id = CE_TriuneCouncil` (360 days; gate only).

### 5.6 Endless Dao Insight Nodes

- **Trigger:** Completion of dedicated Eternal Dao research tiers.
- **Per-Tier Handling:** Each project (Tier I–X) has a corresponding completion event (_Dao Insight Tier {N} Affirmation_) that:
  - Applies `StartColonyEvent` with fixed bonuses (e.g., Tier I grants +2% ResearchAll/+1% EnergyCollection for 120 days, Tier II grants +4%/+2%, etc.).
  - `ResearchProjectEnable` exposes the next tier project.
- **Infinite Stage:** The repeatable project _Dao Insight Requiem_ triggers `StartColonyEvent Id = CE_DaoInsight_Repeat` (120 days; `ResearchAll +5%`, `SabotageDefense +10%`) each time it completes; no counters required as the project itself is repeatable.
- **Cooldown:** Each completion also starts `CE_DaoInsightCooldown` (240 days) to prevent simultaneous tier triggers.

### 5.7 Steward’s Oath Protocols

- **Trigger:** `EmpireRelationChange` event involving the Eternal Celestials.
- **Choice Event:** _Oath Protocol Response_ (target empire context provided via `GeneratedItemEventNames`).
  - **Affirm Protocol:** `AddEmpireIncidentFactorGeneral` (`Diplomacy +25`, `DurationDays=1080`), `StartColonyEvent Id = CE_Oath_Integrity` (720 days; `TradeIncome +8%`, `ColonyHappiness +5%`), `EnableEvent` set for cooperative story arcs.
  - **Censure Breach:** `AddEmpireIncidentFactorGeneral` (`Diplomacy -40`, `DelayDays=540`), optionally `SetDiplomaticRelationType = War` (if already Hostile), `StartColonyEvent Id = CE_Oath_Sanction` (360 days; `ShipWeaponsDamage +12%`, `ColonyHappiness -10%`), `EnableEvent` for sanction story arcs.
- **Cooldown:** `StartColonyEvent Id = CE_OathCooldown` (3 years; gate only).

### 5.8 Chronicle Harmonization Seal

- **Trigger:** `ResearchBreakthrough` when any Eternal Dao storyline flag is active (`CheckEventTriggered`) and no `CE_ChronicleCooldown` is running.
- **Choice Event:** _Chronicle Seal Directive_.
  - **Open the Seal:** `StartColonyEvent Id = CE_Chronicle_Open` (2 years; `ResearchAll +5%`) and `StartColonyEvent Id = CE_Chronicle_OpenStack` (2 years; no modifiers, used as an “open-last-cycle” marker). If the stack event is already active, the choice immediately fires _Chronicle Overwhelm Warning_ after applying the buff.
  - **Secure the Seal:** `StartColonyEvent Id = CE_Chronicle_Seal` (2 years; `ColonyHappiness +6%`, `WarWeariness -8%`) and `DisableEvent` batch for optional storylines; also fires `StartColonyEvent Id = CE_Chronicle_OpenStack_Clear` (instant duration) to cancel any active open-stack marker.
- **Cooldown:** `StartColonyEvent Id = CE_ChronicleCooldown` (2 years; gate only) begins after either choice resolves.
- **Overload Failsafe:** _Chronicle Overwhelm Warning_ starts `CE_Chronicle_Overwhelm` (180 days; `ColonyHappiness -8%`, `ResearchAll -5%`) and automatically queues the Secure option once the warning ends.

## 6. Government Features (9) - Ascendant Dao Council

Government features are game events and colony events split into 4 tiers, each with increasing bonuses but also increasing costs. Each one should last 5 years with a cooldown of 10 years.

| Tier | Max Bonuses | Cost |
| I | 10% | 100,000 |
| II | 30% | 500,000 |
| III | 60% | 1,800,000 |
| III | 75% | 3,500,000 |

### 6.1 Order of Ascendant Architects Audit (Ascendant Dao Council)

Tier I
Cost: 100,000 credits

| Bonus | Amount |
| ColonyDevelopment | 10% |
| ConstructionSpeedFacility | 10% |
| ConstructionSpeedCivilian | 10% |

### 6.2 Order of Meridian Navigators Audit (Ascendant Dao Council)

Tier I
Cost: 100,000 credits

| Bonus | Amount |
| ShipSpeed | 20% |
| HyperjumpSpeed | 30% |
| HyperDriveJumpAccuracy | 30% |
| ScannerRange | 30% |
| ScanEvasion | 10% |
| ScanFocusing | 10% |
| TradeIncome | 5% |

### 6.3 Order of Resonant Scribes Audit (Ascendant Dao Council)

Tier II
Cost: 500,000 credits

| Bonus | Amount |
| MiningRate | 30% |
| TourismIncome | 30% |
| Scenery | 15% |
| ShipMaintenanceAll | 5% |
| PlanetaryFacilityMaintenance | 10% |
| ResearchAll | 5% |

### 6.5 Order of Harmonized Hearths Audit (Ascendant Dao Council)

Tier III
Cost: 1,800,000 credits

| Bonus | Amount |
| ColonyIncome | 60% |
| ColonyHappiness | 25% |
| ColonyPopulationGrowthRate | 25% |
| ColonyCorruptionReduction | 25% |
| PlagueCuring | 100% |
| PlagueContainment | 100% |

### 6.5 Order of Wardened Steel Audit (Ascendant Dao Council)

Tier IV
Cost: 3,500,000 credits

| Bonus | Amount |
| WarWearinessReduction | 75% |
| ColonyDefense | 30% |
| TroopMaintenance | 75% |
| TroopGroundAttack | 75% |
| TroopGroundDefense | 75% |
| TroopExperienceGain | 75% |
| RecruitedTroopStrengthAll | 75% |
| ShipMaintenanceMilitary | 50% |
| ConstructionSpeedMilitary | 50% |
| WeaponsDamage | 50% |
| WeaponsRange | 50% |
| ShieldRechargeRate | 50% |
| DamageControl | 50% |
| RepairRate | 50% |
| ArmorStrength | 50% |
| BoardingAssault | 50% |
| BoardingDefense | 50% |
| BombardmentDamage | 50% |
| BombardmentResistance | 50% |
| GroundCombatResistance | 50% |
| ResearchWeapons | 50% |
| ResearchShields | 50% |
| ResearchArmor | 50% |
| MiningRate | 50% |

## 6. Episodic Events (8)

### 6.1 Celestial Concord Inquest

- **Trigger:** `EmpireCashflowLevel` < 0 or voluntary check when `CheckColonyEventIsNotActiveAnywhereInEmpire (CE_Inquest)`; evaluation loops until triggered.
- **Choice Event:** _Imperial Concord Inquest_.
  - **Submit Comprehensive Ledgers:** `StartColonyEvent Id = CE_Inquest_Success` (360 days; `TaxIncome +12%`, `ColonyCorruptionReduction +10%`).
  - **Restructure Administrations:** `StartColonyEvent Id = CE_Inquest_Reform` (360 days; `TaxIncome -5%`, `ResearchAll +8%`).
- **Cooldown:** `StartColonyEvent Id = CE_Inquest` (540 days; gate only).

### 6.2 Tribulation Alignment Rite

- **Trigger:** Active disaster forecast `CheckColonyEventIsActiveAnywhereInEmpire (CE_TribulationForecast)`.
- **Choice Event:** _Tribulation Alignment Rite_.
  - **Channel to Forge Grounds:** `StartColonyEvent Id = CE_Rite_Forge` (180 days; `TroopExperienceGain +25%`, `ColonyPopulation -5%` at target).
  - **Diffuse Through Shield Lattice:** `StartColonyEvent Id = CE_Rite_Shield` (240 days; `ColonyHappiness +10%`, `ShipSpeed -10%` empire-wide).
- **Follow-up:** Alignment outcome queues `EnableEvent` for post-rite inspection; non-response defaults to diffusion.

### 6.3 Spirit Furnace Conclave

- **Trigger:** `Build` Origin Flame Warfoundry tier ≥ II with `CheckColonyEventIsNotActiveAnywhereInEmpire (CE_FurnaceConclave)`.
- **Choice Event:** _Spirit Furnace Conclave_.
  - **Harmonic Tap Directive:** `StartColonyEvent Id = CE_Furnace_Harmony` (540 days; `EnergyCollection +15%`, `ColonyHappiness +10%`).
  - **Martial Overflow Directive:** `StartColonyEvent Id = CE_Furnace_Martial` (540 days; `ShipWeaponsDamage +20%`, `EmpireMaintenance +5%`).
- **Cooldown:** `StartColonyEvent Id = CE_FurnaceConclave` (3 years) and `EnableEvent` for later calibration prompt.

### 6.4 Virtual Nexus Exchange

- **Trigger:** `Encounter` Virtual Universe artifact or `GainAllianceMember` with a Virtual Universe empire.
- **Choice Event:** _Virtual Nexus Exchange_.
  - **Host Scholastic Delegation:** `StartColonyEvent Id = CE_Nexus_Scholar` (720 days; `ResearchAll +18%`, `CounterEspionage -5%`).
  - **Host Strategic Delegation:** `StartColonyEvent Id = CE_Nexus_Strategist` (720 days; `FleetManeuver +10%`, `TradeIncome +10%`).
- **Diplomacy:** Apply `AddEmpireIncidentFactorGeneral` vs the delegation empire (respectively +15 or +10).

- **Trigger:** `Destroy` Flame cult hero ship or `EmpireRelationChange` involving a Flame cult empire (first significant contact).
- **Choice Event:** _Flame Emperor Vigil_.
  - **Stage Vigil:** `StartColonyEvent Id = CE_Vigil_Honour` (360 days; `TroopMorale +15%`, `Diplomacy +10` with lawful empires).
  - **Issue Imperial Edict:** `StartColonyEvent Id = CE_Vigil_Edict` (360 days; `ShipWeaponsDamage vs cult factions +20%`, `ColonyHappiness -5%`).
- **Story Flag:** Edict path enables subsequent cult suppression storyline nodes.

### 6.6 Infinite Fleet Gauntlet

- **Trigger:** `DestroyShipRole` (Pirate CapitalShip/Carrier) while `CheckColonyEventIsNotActiveAnywhereInEmpire (CE_GauntletCooldown)`.
- **Choice Event:** _Infinite Fleet Gauntlet_.
  - **Offensive Trials:** `StartColonyEvent Id = CE_Gauntlet_Offense` (180 days; `ShipWeaponsDamage +30%`, `FuelUse +10%`).
  - **Defensive Trials:** `StartColonyEvent Id = CE_Gauntlet_Defense` (180 days; `ShieldCapacity +25%`, `ShipSpeed +5%`).
- **Cooldown:** `StartColonyEvent Id = CE_GauntletCooldown` (360 days).

### 6.7 Chrono-Rift Safeguard

- **Trigger:** `EncounterLocation` flagged as time anomaly or `Destroy` anomaly hazard.
- **Choice Event:** _Chrono-Rift Safeguard_.
  - **Seal the Rift:** `StartColonyEvent Id = CE_Rift_Seal` (540 days; `ColonyDevelopment +12%`, `ResearchAncientKnowledge +10%`).
  - **Exploit the Rift:** `StartColonyEvent Id = CE_Rift_Exploit` (270 days; `ConstructionSpeed +35%`, `ColonyHappiness -10%`), 20% chance to start `CE_Rift_Backlash` (90 days; `ShipDamageTaken +10%`).
- **Resolution:** Backlash auto-clears with narrative message; seal option boosts relations with research-oriented empires (+10 diplomacy via incident factor).

- **Trigger:** `EmpireRelationChangeIndependentColony` positive result or `EmpireRelationChange` establishing alliance/trade agreement with any major empire while diplomacy bias ≥ +50.
- **Choice Event:** _Mandate of Harmony Symposium_.
  - **Diplomacy Track:** `StartColonyEvent Id = CE_Symposium_Diplomacy` (720 days; `Diplomacy +20`, `TradeIncome +10%`).
  - **Prosperity Track:** `StartColonyEvent Id = CE_Symposium_Prosperity` (720 days; `ColonyDevelopment +15%`, `TaxIncome +8%`).
  - **Enlightenment Track:** `StartColonyEvent Id = CE_Symposium_Insight` (720 days; `ResearchAll +18%`, `ColonyHappiness +5%`).
- **Cooldown:** `StartColonyEvent Id = CE_SymposiumCooldown` (5 years). Each choice writes a storyline flag for bespoke dialogue.

## 7. Unique Components

- Seven core component families: Lance Arrays, Petalstorm Defense Webs, Spirit Aegis Plating, Dao Halo Shields, Meridian Core Reactors, Infinite Rift Drives, Dao Insight Command Bridges.
- Each family includes Tiers I–X with a capstone ∞ project for infinite research. Final tiers require unique luxury resources and sect storyline completions.
- XML: store under `EternalDao/ComponentDefinitions_zEternalDao.xml`; infinite research handled via repeatable `ResearchProject` entries with incremental bonuses.

## 8. Unique Facilities

- **Azure Meridian Pavilion → Dao Zenith Sanctum** (governance line).
- **Star-Refining Arsenal → Origin Ascension Forge** (fleet yard line).
- **Celestial Legion Dojo → Eternal Vanguard Citadel** (troop line).
- Tier I granted at start; higher tiers unlocked through research rows 487–489, matching WIKI names.

## 9. Research Tree Structure

- Three dedicated rows (487–489) in the Eternal Dao research screen.
- Additional rows assign cultivation realms, component tiers, and storyline unlocks (e.g., Mandala Archive, Infinite Kitchen Festival).
- Infinite research nodes use `Repeatable='true'` and rely on event-supplied flavour text to reinforce narrative cadence.

## 10. Technical Implementation Notes

- Maintain compatibility with DW2-XL by mirroring `ComponentFamilyId` ranges and overriding via higher priority XML.
- Use `EventAction` cooldown flags (`CE_*`) to manage unique storyline triggers (Ascension, Mandala Archive, etc.).
- Facilities and character generators reference new stewardship titles—no “Mandate” terminology remains.
- Localization keys collected under `localization/eternal_dao_en.txt`; include comedic text variants flagged with `StorylineHumor`.
- Automation scripts: extend `scripts/testdw2mod.sh` with component/facility validation and research tree integrity checks.

## 11. Balance and Tuning

- Base maintenance assumes mid-to-late game economies; monitor credit drain during testing and adjust upkeep multipliers.
- Even with doubled stats, maintain vanilla ranges to preserve counterplay. Increase energy, fuel, and luxury consumption instead of free power.
- Storyline buffs stack multiplicatively only when narratively justified; otherwise cap at +30% to avoid runaway spirals.
- Early game pacing intentionally slower—players stabilize upkeep before unleashing infinite research loops.
- Comedy events should grant tangible benefits so players feel rewarded for engaging with narrative.

## Luxury Resource Suite – Eternal Dao Exclusives

Sixty cultivation-grade luxury resources power Eternal Dao economies, morale, research lattices, and martial formations. They remain exclusive to Eternal Dao start conditions, reinforcing the faction's bespoke supply chains.

Design guardrails:

- **Tier distribution:** 10 resources per tier (1 through 6). Base costs scale 10/20/30/40/50/100.
- **Category cadence:** Within Tiers 1–5 each of the four colony categories appears twice, with one ship-focused military resource, one troop-focused military resource (dual-purposed with Research where noted), and one dedicated Research effect. Tier 6 ascends to super luxuries that stack three synergistic bonuses.
- **Bonus bands:** Each modifier respects the per-tier bonus windows (1–3 %, 3–6 %, 6–10 %, 10–15 %, 15–20 %, 20–30 %). Tier 6 individual bonuses stay inside 20–30 %.
- **Lore touchstones:** Every name references naturally occurring Dao phenomena—spirit flora, beast harvests, mineral veins, astral delicacies—sprinkled with rare easter eggs like Meimei's famed street food.

### Tier 1

| #   | Resource Name           | Category                     | Base Cost | Bonuses                                                     | Description                                                                                                                         | Image                     |
| --- | ----------------------- | ---------------------------- | --------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| 1   | Meridian Basalt Nodes   | Colony Development           | 10        | ColonyDevelopment +3% (Colony)                              | Hexagonal basalt nodules saturated with steady qi currents; settlers hammer them into new foundations to anchor city grids.         | UserInterface/Placeholder |
| 2   | Glowmoss Cartographer   | Colony Development           | 10        | ColonyDevelopment +2% (Colony)                              | Bioluminescent moss that maps ley-line contours as it creeps, giving survey crews a living topographical guide.                     | UserInterface/Placeholder |
| 3   | Amberfen Spice Resin    | Colony Income                | 10        | ColonyIncome +3% (Colony)                                   | A fragrant resin tapped from amber marsh reeds; caravans prize it for elevating everything from noodles to nebula tea.              | UserInterface/Placeholder |
| 4   | Stargrazer Jerky Strips | Colony Income                | 10        | ColonyIncome +2% (Colony)                                   | Slow-dried slices of migratory stargrazer flank that keep traders lingering—and spending—at frontier waystations.                   | UserInterface/Placeholder |
| 5   | Astralseed Millet       | Colony Growth                | 10        | ColonyPopulationGrowthRate +3% (Colony)                     | Millet raised in low-gravity paddies so each grain hums with astral vitality, strengthening young families.                         | UserInterface/Placeholder |
| 6   | Shellfoam Broth Clams   | Colony Growth                | 10        | ColonyPopulationGrowthRate +2% (Colony)                     | Foam-shelled clams gathered from dao reefs; their broth restores exhausted settlers after long shifts.                              | UserInterface/Placeholder |
| 7   | Lanternfruit Citrus     | Colony Happiness             | 10        | ColonyHappiness +3% (Colony)                                | Citrus gourds that glow like lanterns and brighten communal feasts during protracted frontier nights.                               | UserInterface/Placeholder |
| 8   | Songmist Bloom Petals   | Colony Happiness             | 10        | ColonyHappiness +2% (Colony)                                | Petals that release chime-soft vapor when steeped, smoothing tempers in even the busiest quarter.                                   | UserInterface/Placeholder |
| 9   | Skywhorl Ray Fins       | Military (ships)             | 10        | ShipManeuvering +2% (Empire)                                | Shed fins from skywhorl rays, bonded to hull canards to let cutters tack cleanly through grav eddies.                               | UserInterface/Placeholder |
| 10  | Ironpaw Tusk Fibers     | Military (troops) / Research | 10        | TroopRecoveryRate +2% (Empire), ResearchTroops +1% (Empire) | Resilient fibers teased from ironpaw boar tusks; drill sages weave them into wraps that hasten recovery and inspire new formations. | UserInterface/Placeholder |

### Tier 2

| #   | Resource Name              | Category                     | Base Cost | Bonuses                                                      | Description                                                                                                                 | Image                     |
| --- | -------------------------- | ---------------------------- | --------- | ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| 11  | Skyforge Anchorite         | Colony Development           | 20        | ColonyDevelopment +6% (Colony)                               | High-altitude ore that crystallizes along turbine cliffs and locks aerial platforms into precise alignments.                | UserInterface/Placeholder |
| 12  | Leyburrow Heartwoods       | Colony Development           | 20        | ColonyDevelopment +4% (Colony)                               | Heartwood roots that tunnel along ley-rivers, knitting entire districts against seismic shocks.                             | UserInterface/Placeholder |
| 13  | Meimei's Pork Buns         | Colony Income                | 20        | ColonyIncome +6% (Colony)                                    | Beloved buns stuffed with lotus-fed pork; gourmet pilgrims queue for hours and flood nearby stalls with coin.               | UserInterface/Placeholder |
| 14  | Sunbraid Pepper Pods       | Colony Income                | 20        | ColonyIncome +4% (Colony)                                    | Slender peppers ripened in braided sun nets whose aroma alone sparks bidding wars between spice guilds.                     | UserInterface/Placeholder |
| 15  | Moonquill Kelp Spores      | Colony Growth                | 20        | ColonyPopulationGrowthRate +5% (Colony)                      | Drifting kelp spores that settle in nursery pools and quicken infant qi circulation.                                        | UserInterface/Placeholder |
| 16  | Glaciant Marrow Pearls     | Colony Growth                | 20        | ColonyPopulationGrowthRate +4% (Colony)                      | Frost-born marrow pearls dissolved into restorative tonics that harden constitutions against brutal climates.               | UserInterface/Placeholder |
| 17  | Festival Thunder Plums     | Colony Happiness             | 20        | ColonyHappiness +6% (Colony)                                 | Plums that crackle with celebratory thunder qi when peeled, igniting laughter across auditing halls.                        | UserInterface/Placeholder |
| 18  | Cloudrest Velvetdown       | Colony Happiness             | 20        | ColonyHappiness +4% (Colony)                                 | Down shed by cloud yaks; plush cushions stuffed with it cradle exhausted clerks in weightless comfort.                      | UserInterface/Placeholder |
| 19  | Starstream Rudder Coral    | Military (ships)             | 20        | ShipSpeed +5% (Empire)                                       | Coral branches that grow along nebula currents; ground to slurry they coat thrusters for sharper acceleration.              | UserInterface/Placeholder |
| 20  | Thunderhorn Vanguard Spurs | Military (troops) / Research | 20        | TroopGroundAttack +5% (Empire), ResearchWeapons +4% (Empire) | Electrified spurs harvested from thunderhorn beasts empower shock troops while weapon scholars reverse-engineer the charge. | UserInterface/Placeholder |

### Tier 3

| #   | Resource Name            | Category                     | Base Cost | Bonuses                                                          | Description                                                                                                             | Image                     |
| --- | ------------------------ | ---------------------------- | --------- | ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| 21  | Heavenforge Quartz Veins | Colony Development           | 30        | ColonyDevelopment +10% (Colony)                                  | Prismatic quartz seams that resonate structural fields, letting architects suspend sprawling terraces with confidence.  | UserInterface/Placeholder |
| 22  | Terrashift Magnetite     | Colony Development           | 30        | ColonyDevelopment +7% (Colony)                                   | Magnetized ore drawn from tectonic shear zones that keeps orbital shipyards balanced atop lift platforms.               | UserInterface/Placeholder |
| 23  | Dragoncloud Tea Sprigs   | Colony Income                | 30        | ColonyIncome +9% (Colony)                                        | Tea sprigs nurtured in dragoncloud fog whose brewed steam fetches fortunes at celestial auctions.                       | UserInterface/Placeholder |
| 24  | Starbrine Salt Lattices  | Colony Income                | 30        | ColonyIncome +7% (Colony)                                        | Crystalline lattices formed by evaporating comet tides, a culinary marvel chefs chase across the quadrant.              | UserInterface/Placeholder |
| 25  | Auroral Nectar Gourds    | Colony Growth                | 30        | ColonyPopulationGrowthRate +9% (Colony)                          | Gourds that absorb auroral light and distill into prenatal tonics for families taming harsh moons.                      | UserInterface/Placeholder |
| 26  | Warmspire Broodfish      | Colony Growth                | 30        | ColonyPopulationGrowthRate +6% (Colony)                          | River fish raised in steam vents whose roe accelerates recovery throughout mining towns.                                | UserInterface/Placeholder |
| 27  | Serenity Cloud Lotus     | Colony Happiness             | 30        | ColonyHappiness +9% (Colony)                                     | Lotus blooms suspended in perpetual mist that, when steeped, calm entire administrative wards.                          | UserInterface/Placeholder |
| 28  | Joyflare Lychee          | Colony Happiness             | 30        | ColonyHappiness +7% (Colony)                                     | Iridescent lychee bursting with laughter qi that dissolves unrest faster than proclamations.                            | UserInterface/Placeholder |
| 29  | Voidwing Spine Feathers  | Military (ships)             | 30        | WeaponsRange +8% (Empire)                                        | Midnight feathers molted by voidwings and forged into targeting vanes that stretch beam arcs across battles.            | UserInterface/Placeholder |
| 30  | Titanbane Myco Husk      | Military (troops) / Research | 30        | TroopGroundDefense +8% (Empire), ResearchBioWarfare +6% (Empire) | Fungal husks gathered from titanbane groves fortify legion armor while researchers decode their anti-bioweapon secrets. | UserInterface/Placeholder |

### Tier 4

| #   | Resource Name               | Category                     | Base Cost | Bonuses                                                                 | Description                                                                                            | Image                     |
| --- | --------------------------- | ---------------------------- | --------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------- |
| 31  | Starforge Obsidian Pillars  | Colony Development           | 40        | ColonyDevelopment +15% (Colony)                                         | Obsidian pillars grown within solar furnaces that brace megacities against orbital shear.              | UserInterface/Placeholder |
| 32  | Leyglow Aragonite           | Colony Development           | 40        | ColonyDevelopment +12% (Colony)                                         | Iridescent aragonite clusters that absorb geomantic shocks and keep skyscraper districts level.        | UserInterface/Placeholder |
| 33  | Phoenixcrest Saffron        | Colony Income                | 40        | ColonyIncome +14% (Colony)                                              | Saffron strands plucked from phoenixcrest lilies; imperial chefs bid entire treasuries for a handful.  | UserInterface/Placeholder |
| 34  | Starreef Truffle Coral      | Colony Income                | 40        | ColonyIncome +11% (Colony)                                              | Reef coral that buds truffle-like pearls coveted by gourmand sects across the spiral.                  | UserInterface/Placeholder |
| 35  | Sunwell Terraced Grain      | Colony Growth                | 40        | ColonyPopulationGrowthRate +14% (Colony)                                | Terraced grain bathed in mirrored suns that sustains explosive settlement booms.                       | UserInterface/Placeholder |
| 36  | Cradlewyrm Bone Marrow      | Colony Growth                | 40        | ColonyPopulationGrowthRate +11% (Colony)                                | Gentle cradlewyrms yield nutrient-rich marrow distilled into tonics for overburdened colonies.         | UserInterface/Placeholder |
| 37  | Jade Serenade Resin         | Colony Happiness             | 40        | ColonyHappiness +14% (Colony)                                           | Aromatic resin that burns with jade-blue fire and blankets public plazas in tranquil harmonics.        | UserInterface/Placeholder |
| 38  | Starlit Opera Pearls        | Colony Happiness             | 40        | ColonyHappiness +11% (Colony)                                           | Pearls that hum ancient operas when warmed, enchanting festival crowds with shared nostalgia.          | UserInterface/Placeholder |
| 39  | Stormglass Leviathan Scales | Military (ships)             | 40        | ShieldRechargeRate +12% (Empire)                                        | Scales flensed from stormglass leviathans and inlaid into hulls to accelerate shield regeneration.     | UserInterface/Placeholder |
| 40  | Dragonbone Ward Shards      | Military (troops) / Research | 40        | TroopGroundAttack +13% (Empire), ResearchAncientKnowledge +10% (Empire) | Runic dragonbone shards radiate martial wards while scholars decode the battle doctrine etched within. | UserInterface/Placeholder |

### Tier 5

| #   | Resource Name               | Category                     | Base Cost | Bonuses                                                          | Description                                                                                                    | Image                     |
| --- | --------------------------- | ---------------------------- | --------- | ---------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------- |
| 41  | Celestine Anchor Monoliths  | Colony Development           | 50        | ColonyDevelopment +20% (Colony)                                  | Towering celestine monoliths that lock megacities into synchronous orbit-stabilized formations.                | UserInterface/Placeholder |
| 42  | Chronotwine Elderwood       | Colony Development           | 50        | ColonyDevelopment +17% (Colony)                                  | Elderwood vines that twist through time seams to knit entire districts against tectonic drift.                 | UserInterface/Placeholder |
| 43  | Sunsilk Tribute Pelts       | Colony Income                | 50        | ColonyIncome +19% (Colony)                                       | Radiant pelts shed by sunsilk vulpines and traded as ceremonial tributes at staggering premiums.               | UserInterface/Placeholder |
| 44  | Voidcurrant Amberwine       | Colony Income                | 50        | ColonyIncome +16% (Colony)                                       | Amberwine fermented from vacuum-grown berries; monks guard each vintage like a treasury bond.                  | UserInterface/Placeholder |
| 45  | Eonheart Seedstones         | Colony Growth                | 50        | ColonyPopulationGrowthRate +19% (Colony)                         | Seedstones that pulse with ancient heartbeats, planted beneath nurseries to hasten communal recovery.          | UserInterface/Placeholder |
| 46  | Heavenfeast Leviathan Roe   | Colony Growth                | 50        | ColonyPopulationGrowthRate +17% (Colony)                         | Luminescent roe from heavenfeast leviathans reserved for colonies racing through expansion.                    | UserInterface/Placeholder |
| 47  | Aurora Dream Incense        | Colony Happiness             | 50        | ColonyHappiness +20% (Colony)                                    | Incense cones that project auroral visions overhead and dissolve civic unrest into awe.                        | UserInterface/Placeholder |
| 48  | Laughing Moon Petrogems     | Colony Happiness             | 50        | ColonyHappiness +16% (Colony)                                    | Petrogems that ring with silvery laughter when struck, sealing reconciliation ceremonies with joy.             | UserInterface/Placeholder |
| 49  | Chronosea Leviathan Tendons | Military (ships)             | 50        | ShipSpeed +18% (Empire)                                          | Elastic tendons that retain chronosea momentum; engineers braid them into engines for blitz-speed sorties.     | UserInterface/Placeholder |
| 50  | War-Oracle Obsidian Eyes    | Military (troops) / Research | 50        | TroopExperienceGain +18% (Empire), ResearchPsychic +15% (Empire) | Obsidian eye-stones carved from war-oracle statues enlighten generals while advancing psychic warfare studies. | UserInterface/Placeholder |

### Tier 6

| #   | Resource Name              | Category     | Base Cost | Bonuses                                                                                                   | Description                                                                                                            | Image                     |
| --- | -------------------------- | ------------ | --------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| 51  | Empyreal Mandala Geode     | Super Luxury | 100       | ColonyDevelopment +26% (Colony), ResearchAll +23% (Empire), TradeIncome +22% (Empire)                     | Mandala-shaped geodes cracked open to reveal harmonic crystals that uplift infrastructure, scholarship, and trade.     | UserInterface/Placeholder |
| 52  | Phoenixheart Dawn Nectar   | Super Luxury | 100       | ColonyHappiness +25% (Colony), ColonyDevelopment +23% (Colony), ColonyPopulationGrowthRate +22% (Colony)  | Nectar gathered at sunrise from phoenixheart blossoms; a single draught sparks civic zeal and flourishing families.    | UserInterface/Placeholder |
| 53  | Starwyrm Aurora Heart      | Super Luxury | 100       | ShipManeuvering +23% (Empire), WeaponsDamage +24% (Empire), TroopGroundAttack +22% (Empire)               | Crystalline starwyrm hearts pulse with aurora fire, syncing fleets and legions into ferocious harmony.                 | UserInterface/Placeholder |
| 54  | Chronotidal Empyrean Pearl | Super Luxury | 100       | ColonyIncome +24% (Colony), TradeIncome +23% (Empire), Diplomacy +21% (Empire)                            | Pearls grown where time tides meet let merchants sense profitable futures and diplomats read the room.                 | UserInterface/Placeholder |
| 55  | Eclipsepetal Lotus Crown   | Super Luxury | 100       | ColonyDevelopment +23% (Colony), ResearchAncientKnowledge +22% (Empire), ColonyHappiness +21% (Colony)    | Lotus crowns that bloom only during eclipses, weaving ancient schematics into urban hearts while enthralling citizens. | UserInterface/Placeholder |
| 56  | Dragonwhorl Tempest Scale  | Super Luxury | 100       | ShieldRechargeRate +23% (Empire), ShipSpeed +24% (Empire), WeaponsRange +21% (Empire)                     | Tempest scales shed by dragonwhorl leviathans mid-storm supercharge admiralty cores and fleet responses.               | UserInterface/Placeholder |
| 57  | Karmic Aurora Veil         | Super Luxury | 100       | ColonyCorruptionReduction +25% (Empire), ColonyHappiness +22% (Colony), Diplomacy +21% (Empire)           | Veils spun from auroral silk filter karmic smog, brighten populations, and make every negotiation gleam.               | UserInterface/Placeholder |
| 58  | Voidjade Oracle Husk       | Super Luxury | 100       | ResearchPsychic +24% (Empire), CounterEspionage +22% (Empire), Espionage +21% (Empire)                    | Fossilized voidjade oracle husks sharpen allied clairvoyance while confounding rival agents.                           | UserInterface/Placeholder |
| 59  | Heaven's Ember Lychee      | Super Luxury | 100       | ColonyPopulationGrowthRate +24% (Colony), TroopRecruitment +23% (Empire), ColonyDevelopment +22% (Colony) | Lychee ripened within volcano monasteries kindle fertile booms, eager recruits, and rapid civic expansion.             | UserInterface/Placeholder |
| 60  | Daoheart Ocean Pearl       | Super Luxury | 100       | ColonyIncome +25% (Colony), ResearchHighTech +22% (Empire), TradeIncome +23% (Empire)                     | Ocean pearls grown around daoheart currents bankroll research foundries and interstellar trade hubs alike.             | UserInterface/Placeholder |

## Government Features

### 1) Paragon Steward Elevation Rite

Summary

- Research and megaproject breakthroughs trigger the creation of Celestial Paragons—legendary Dao Tech prodigies who coordinate research, fleets, or ground forces through synchronized cultivation networks.

Events

- Name: Paragon Steward Elevation Trigger (Eternal Celestials)

  - TriggerType: ResearchBreakthrough (TriggerResearchProjectID = -1)
  - AdditionalPlacement: Build (TriggerBuildFacilityID = <EternalCelestialsMegaprojectIds>, e.g., Stellar Crucible, Dao Gate Nexus)
  - PlacementRaceID: 99
  - Conditions: CheckColonyEventIsNotActiveAnywhereInEmpire (CE_AscensionCooldown)
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Paragon Steward Elevation Choice (Eternal Celestials)
    - Delay 2-5 seconds for presentation

- Name: Paragon Steward Elevation Choice (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - TriggerActions (choices):
    - ChoiceButtonText: Elevate a Star-Seer
      - TriggerEvent -> GeneratedItemName: Paragon Steward Elevation Scientist (Eternal Celestials)
    - ChoiceButtonText: Forge a Celestial Strategist
      - TriggerEvent -> GeneratedItemName: Paragon Steward Elevation Admiral (Eternal Celestials)
    - ChoiceButtonText: Crown a Heavenward Marshal
      - TriggerEvent -> GeneratedItemName: Paragon Steward Elevation General (Eternal Celestials)

- Name: Paragon Steward Elevation Scientist (Eternal Celestials)

  - TriggerActions:
    - GenerateCharacter (Id1=-1, CharacterRole=Scientist, ActionLocationHint=HomeColony)
    - ClearCharacterTraits (Id1=-1)
    - AddCharacterTrait (Id1=<TraitCelestialParagonResearch>, repeat to add top-tier research traits)
    - SetCharacterTraitsKnown (Id1=-1)
    - StartColonyEvent Id = CE_AscensionResonance (duration 5 years; empire +20% research, +5% happiness as dao-lattice morale uplift)
    - StartColonyEvent Id = CE_AscensionCooldown (duration 5 years; no modifiers, used solely as gating flag)
    - GeneralStoryMessageToEmpire (MessageTitle="Paragon Steward Elevation", include lorem tie-in)

- Admiral and General variants mirror the above but focus on fleet damage/defense and ground assault strength respectively (use dedicated CE_AscensionArmada, CE_AscensionLegions).

Notes

- Ensure generated traits remain permanently revealed; set SetCharacterTraitsKnown.
- Cooldown is handled entirely by CE_AscensionCooldown, so no date-based timers are required.
- Pair the Paragon Steward colony events with the Council Edict buffs for compounding-but-temporary advantages; overstretch is prevented by the shared cooldown.

### 2) Dao Archive Concordance

Summary

- Upon first contact or espionage successes, the Eternal Celestials decide whether to copy a rival’s Dao Tech lattice or weave it into a shared research accord, producing measured boosts rather than free breakthroughs.

Events

- Name: Dao Archive Concordance Trigger (Eternal Celestials)

  - TriggerType: EmpireRelationChange (TriggerEmpireRelationType=FirstContact)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Dao Archive Concordance Decision (Eternal Celestials)

- Name: Dao Archive Concordance Decision (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Siphon Their Insights
      - TriggerEvent -> GeneratedItemName: Dao Archive Siphon (Eternal Celestials)
    - ChoiceButtonText: Harmonize the Patterns
      - TriggerEvent -> GeneratedItemName: Dao Archive Attunement (Eternal Celestials)

- Name: Dao Archive Siphon (Eternal Celestials)

  - TriggerActions:
    - StartColonyEvent Id = CE_DaoArchive_Siphon (duration 5 years; empire +22% research, +12% espionage offense, -5% diplomacy bias with the contacted empire; narratively framed as Dao Tech lattice extraction)
    - DisableEvent (Dao Archive Concordance Trigger (Eternal Celestials) applied to the contacted empire via GeneratedItemEventNames)
    - GeneralStoryMessageToEmpire (explain cultivation arrays copying their data lattice)

- Name: Dao Archive Attunement (Eternal Celestials)

  - TriggerActions:
    - StartColonyEvent Id = CE_DaoArchive_Attunement (duration 6 years; empire +18% research, +10% counter-intelligence, +8% trade income with the contacted empire; Dao Tech treaty flavour)
    - DisableEvent (Dao Archive Concordance Trigger (Eternal Celestials) applied to the contacted empire via GeneratedItemEventNames)
    - GeneralStoryMessageToEmpire (noting harmonious Dao Tech sharing)

Notes

- After the choice, rely on a paired `Dao Archive Cooldown (Eternal Celestials)` event attached via `GeneratedItemEventNames`; once its colony event ends, use `EnableEvent` to restore the initial trigger if later content requires it.
- Mirrors Zenox infiltration potency but frames it through Dao Tech cultural exchange rather than raw theft.

### 3) Council Edict Resonator

Summary

- Building the Eternal Mandate Senate unlocks decree modes that supercharge the empire for six years; each decree automatically resolves with a vent period before the council permits another directive.

Events

- Name: Council Edict Resonator Trigger (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <EternalMandateSenateId>)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Council Edict Resonator Selection (Eternal Celestials)

- Name: Council Edict Resonator Selection (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices (each starts a six-year decree buff and disables the selection event until completion):
    - ChoiceButtonText: Supreme Mobilization Decree
      - StartColonyEvent Id = CE_Decree_Mobilization (duration 6 years; `ShipBuildSpeed +25%`, `TroopStrength +15%`, `EmpireMaintenance +4%`)
      - DisableEvent (Council Edict Resonator Selection (Eternal Celestials))
      - TriggerEvent -> GeneratedItemName: Council Edict Resonator Vent (Eternal Celestials)
    - ChoiceButtonText: Limitless Insight Decree
      - StartColonyEvent Id = CE_Decree_Insight (duration 6 years; `ResearchAll +30%`, `ColonyDevelopmentRate +15%`, `Corruption +5%`)
      - DisableEvent (Council Edict Resonator Selection (Eternal Celestials))
      - TriggerEvent -> GeneratedItemName: Council Edict Resonator Vent (Eternal Celestials)
    - ChoiceButtonText: Absolute Concord Decree
      - StartColonyEvent Id = CE_Decree_Concord (duration 6 years; `Diplomacy +20`, `TradeIncome +15%`, `WarWearinessAccumulation -10%`)
      - DisableEvent (Council Edict Resonator Selection (Eternal Celestials))
      - TriggerEvent -> GeneratedItemName: Council Edict Resonator Vent (Eternal Celestials)

- Name: Council Edict Resonator Vent (Eternal Celestials)

  - TriggerActions:
    - Delay 2190 days (fires when the active decree ends)
    - StartColonyEvent Id = CE_Decree_PressureVent (duration 540 days; `Happiness -6%`, `WeaponsDamage +6%`)
    - EnableEvent (Council Edict Resonator Selection (Eternal Celestials)) — fired after the vent completes via `GeneratedItemEventNames`.
    - GeneralStoryMessageToEmpire (reports the vent and reinstatement of decree deliberations)

Notes

- Only one decree may run at a time; players time the six-year buff knowing a 540-day vent will follow before a new decree may be issued.
- Pair decree windows with Dao Archive Attunement, Paragon Steward promotions, Time-Thread Harmonization, or Ethereal Gate choices to maximise combined uptime.

### 4) Origin Flame Warfoundry

Summary

- Activating the Origin Flame Warfoundry unlocks Celestial-class capital hulls, flawless components, and empire-wide ship bonuses that dwarf vanilla capitals.

Events

- Name: Origin Flame Warfoundry Trigger (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <StellarCrucibleId>)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Origin Flame Warfoundry Activation (Eternal Celestials)

- Name: Origin Flame Warfoundry Activation (Eternal Celestials)

  - TriggerActions:
    - ResearchProjectEnable (Id1=<CelestialCoreReactorProjectId>) — queues the Dao Tech reactor research without granting progress
    - UnlockFactionSpecialShipTech (Id1=<CelestialHullTechId>) — exposes Celestial hull templates sized within Zenox capital limits (size class 11 per `SHIPHULLS.csv`)
    - StartColonyEvent Id = CE_OriginFlame_Warfoundry (duration 6 years; empire +20% ship durability, +15% weapon damage, +10% capital-ship build speed)
    - GeneralStoryMessageToEmpire

Notes

- The unique hull sits on the Zenox capital chassis scale (size 11) to preserve XL balance while reframing stats through Dao Tech plating.
- Consider tying the project unlock to prior decree or ascension milestones so the buff cadence remains deliberate.
- The capital-ship build speed bonus stacks meaningfully with Time-Thread Harmonization’s acceleration branch, so timing both events rewards careful Senate planning.

### 5) Rift-Sundering Armada Codex

Summary

- Crushing a major enemy fleet lets the Eternal Celestials bend space-time around their armadas, granting catastrophic strike capabilities or impenetrable defenses on command.

Events

- Name: Rift-Sundering Codex Trigger (Eternal Celestials)

  - TriggerType: DestroyShipRole
  - TriggerShipRoles: CapitalShip, Carrier
  - Conditions: CheckColonyEventIsNotActiveAnywhereInEmpire (CE_RiftSundering_Cooldown)
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Rift-Sundering Codex Choice (Eternal Celestials)

- Name: Rift-Sundering Codex Choice (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Phase-Lance Offensive
      - StartColonyEvent Id = CE_RiftSundering_Offense (duration 360 days; fleets gain +40% damage, +40% hyperjump range, ignore nebula penalties)
      - StartColonyEvent Id = CE_RiftSundering_Cooldown (duration 720 days; no bonuses, only blocks repeat triggers)
    - ChoiceButtonText: Aegis of Stillness
      - StartColonyEvent Id = CE_RiftSundering_Defense (duration 360 days; fleets gain +50% shield capacity, +25% evasion, +15% repair rate)
      - StartColonyEvent Id = CE_RiftSundering_Cooldown (duration 720 days; no bonuses, only blocks repeat triggers)

Notes

- Colony events should be empire-scoped; configure the corresponding `ColonyEventDefinitions` entries to apply fleet damage/defense modifiers targeted at RaceId 99 ships.
- `CE_RiftSundering_Cooldown` should enable the trigger after it ends via `EnableEvent`, forcing an immediate doctrine choice after each qualifying kill and preventing stockpiling.
- Pairing the offensive stance with Council Edict Mobilization maximises burst damage, while the defensive stance cushions decree vents or Time-Thread Harmonization downtime.
- The trigger can fire again only after `CE_RiftSundering_Cooldown` ends, ensuring each eligible victory demands an immediate doctrinal choice.

### 6) Stewardship Vassalage Ultimatum

Summary

- Every first major diplomatic contact or vassalization opportunity can be converted into either immediate tribute extraction or permanent loyal client-states through irresistible psionic decrees.

Events

- Name: Stewardship Vassalage Ultimatum Trigger (Eternal Celestials)

  - TriggerType: EmpireRelationChange (TriggerEmpireRelationType=FirstContact)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Stewardship Vassalage Ultimatum (Eternal Celestials)

- Name: Stewardship Vassalage Ultimatum (Eternal Celestials)

  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Offer Ascension Accord
      - TriggerEvent -> GeneratedItemName: Stewardship Vassalage Accord (Eternal Celestials)
    - ChoiceButtonText: Demand Immediate Tribute
      - TriggerEvent -> GeneratedItemName: Stewardship Vassalage Extraction (Eternal Celestials)

- Name: Stewardship Vassalage Accord (Eternal Celestials)

  - TriggerActions:
    - SetDiplomaticRelationType (EmpireRelationType=Subjugation, TargetEmpire=-1)
    - AddEmpireIncidentFactorGeneral (Value1=+35, DurationDays=1440) to lock positive relations
    - StartColonyEvent Id = CE_Vassalage_Accord (duration 8 years; empire +18% tax income, +10% population growth from tributaries, +5% trade income with the subjugated empire)

- Name: Stewardship Vassalage Extraction (Eternal Celestials)

  - TriggerActions:
    - Money (Value1=+60000 credits instantly)
    - AddEmpireIncidentFactorGeneral (Value1=-20 after 540 days to simulate resentment)
    - StartColonyEvent Id = CE_Vassalage_Extraction (duration 4 years; empire +15% trade income, -10 diplomacy bias with target, +10% corruption)

Notes

- Use Conditions to prevent repeating on the same empire unless relations reset below Neutral.
- Provides a binary choice between long-term power consolidation and explosive economic spikes.
- Vassalage accords stabilize the maintenance penalties introduced by Council Edict Mobilization, keeping the Dao Tech bureaucracy solvent.

### 7) Ethereal Gate Transit Lattice

Summary

- Mature Celestial worlds open Dao Gates that duplicate entire populations into newly seeded colonies or fortify frontier worlds with instant infrastructure.

Events

- Name: Ethereal Gate Resonance Online (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <DaoGateResonanceSpireId>)
  - PlacementRaceID: 99
  - TriggerActions:
    - TriggerEvent -> Ethereal Gate Transit Choice (Eternal Celestials)

- Name: Ethereal Gate Transit Choice (Eternal Celestials)

  - TriggerType: Undefined (invoked by other events)
  - TriggerActionsAreChoices: true
  - Choices:
    - ChoiceButtonText: Seed a New Star
      - TriggerEvent -> GeneratedItemName: Ethereal Gate Transit Colonize (Eternal Celestials)
    - ChoiceButtonText: Reinforce the Bastion
      - TriggerEvent -> GeneratedItemName: Ethereal Gate Transit Fortify (Eternal Celestials)

- Name: Ethereal Gate Transit Colonize (Eternal Celestials)

  - TriggerActions:
    - GenerateShipBase (ShipRole=ColonyShip, Id1=<DaoGateColonyShipDesignId>, ActionLocationHint=NearestColony) — use the Zenox colonizer size brackets (size 14) to keep XL compatibility
    - Money (Value1=+15000 to cover immediate warp-gate costs)
    - StartColonyEvent Id = CE_EtherealGate_Colonize (duration 3 years; empire +25% colony ship construction speed, +15% development on newly founded colonies)
    - DisableEvent (Ethereal Gate Resonance Online (Eternal Celestials))
    - EnableEvent (Ethereal Gate Resonance Online (Eternal Celestials)) — fired from CE_EtherealGate_Colonize when it ends via GeneratedItemEventNames

- Name: Ethereal Gate Transit Fortify (Eternal Celestials)

  - TriggerActions:
    - StartColonyEvent Id = CE_EtherealGate_Fortify (duration 5 years; target colony +35% defensive strength, +15% resource extraction, +10 happiness)
    - DisableEvent (Ethereal Gate Resonance Online (Eternal Celestials))
    - EnableEvent (Ethereal Gate Resonance Online (Eternal Celestials)) — fired from CE_EtherealGate_Fortify on completion

Notes

- The Ethereal Gate Resonance Spire facility should require high-population colonies, providing an in-game gating mechanism without needing direct population-change triggers.
- Because the choice fires immediately on facility completion, players cannot stockpile uses; they must decide whether the Dao Tech lattice supports expansion or consolidation right away.
- Colonize pairs naturally with Time-Thread Harmonization acceleration windows, while Fortify offsets the maintenance and happiness pressure created by Council Edict vents.

### 8) Time-Thread Harmonization

Summary

- The Celestials briefly warp their Dao Tech time lattices, forcing a choice between raw acceleration or stabilised prosperity.

Events

- Name: Time-Thread Relay Online (Eternal Celestials)

  - TriggerType: Build (TriggerBuildFacilityID = <VoidPrismRelayId>)
  - TriggerActions:
    - TriggerEvent -> GeneratedItemName: Time-Thread Harmonization Prompt (Eternal Celestials)

- Name: Time-Thread Harmonization Prompt (Eternal Celestials)

  - TriggerType: Undefined (invoked by other events)
  - TriggerActionsAreChoices: true
  - Conditions: CheckColonyEventIsNotActiveAnywhereInEmpire (CE_TimeThread)
  - Choices:
    - ChoiceButtonText: Accelerate the Lattice
      - StartColonyEvent Id = CE_TimeThread_Acceleration (duration 150 days; empire +60% construction speed, +45% research, +20% ship maneuverability, -20% sabotage success)
      - DisableEvent (Time-Thread Relay Online (Eternal Celestials))
      - EnableEvent (Time-Thread Relay Online (Eternal Celestials)) — fired from CE_TimeThread_Acceleration upon completion
    - ChoiceButtonText: Stabilize Heaven’s Clock
      - StartColonyEvent Id = CE_TimeThread_Stability (duration 180 days; empire +15% construction speed, +15% research, +10% happiness, +10% trade income)
      - DisableEvent (Time-Thread Relay Online (Eternal Celestials))
      - EnableEvent (Time-Thread Relay Online (Eternal Celestials)) — fired from CE_TimeThread_Stability upon completion

Notes

- Future content can add additional triggers (e.g., research breakthroughs or tribulation events) by reusing the same prompt pattern.
- Cooldown is handled implicitly by the active colony event, so players must commit to an option each time a Void Prism Relay comes online—no stockpiling.
- Pair the acceleration option with Dao Archive Siphon windows for peak research spikes, or choose stability to smooth empire happiness before triggering Council Edict vents.

Balancing Summary (initial values)

- Paragon Steward Elevation Rite: Paragon promotions roughly every 6–8 years; each delivers a 5-year +20% research (or equivalent military boost) plus minor happiness increases before the cooldown expires.
- Dao Archive Concordance: 5–6 year research/espionage surges (either +22%/+12% with diplomatic strain or +18%/+10% with trade bonuses) without granting free breakthroughs.
- Council Edict Resonator: Decrees provide +25% build / +30% research / +20 diplomacy packages for 6 years, followed by an automatic 540-day vent (-6% happiness, +6% weapons damage) before the council authorises another decree.
- Origin Flame Warfoundry: Six-year empire buff of +20% durability, +15% weapon damage, +10% capital ship build speed while unlocking XL-compatible Celestial hull research.
- Rift-Sundering Armada Codex: Post-kill decisions grant 360-day offensive (+40% damage) or defensive (+50% shields) fleet boosts, followed by a 720-day cooldown colony event.
- Stewardship Vassalage Ultimatum: Long-term accord grants +18% tax, +10% growth, +5% trade for 8 years; extraction option pays 60k credits and +15% trade for 4 years at the cost of diplomacy.
- Ethereal Gate Transit Lattice: Each Resonance Spire triggers an immediate colonize-or-fortify decision—either a one-off colonizer spawn with +25% ship output or a 5-year +35% defense/+15% extraction buff.
- Time-Thread Harmonization: Every Void Prism Relay forces a choice between a 150-day +60% construction/+45% research surge or a 180-day +15% balanced stability buff; no stockpiling.
