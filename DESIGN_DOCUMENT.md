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
