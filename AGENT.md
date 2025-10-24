# Implementation Agent Instructions

**Project**: Eternal Dao Stellarity - Distant Worlds 2 Mod  
**Phase**: Technical Implementation  
**Status**: Design Complete, Ready for XML Creation

---

## Your Mission

Implement the **Eternal Dao Stellarity** mod for Distant Worlds 2 based on the completed design documentation.

**You are the implementation agent.** The design phase is complete. Your job is to create the XML files, balance the gameplay, and deliver a playable, polished mod.

---

## What You Have

### ✅ Complete Design Documentation

1. **WIKI.md** (532 lines)

   - Complete lore encyclopedia
   - USE THIS for all flavor text, character consistency, and cultural references
   - Contains: species origin, The Wanderer, cultivation realms, government structure, Three Prodigies bios, running gags

2. **SUMMARY.md** (500 lines)

   - Your primary implementation guide
   - Contains: All 24 event specifications, component/facility stats, research tree structure, balance targets
   - READ THIS FIRST for technical specifications

3. **DESIGN_DOCUMENT.md** (placeholder)

   - YOU should populate this with detailed technical notes as you implement
   - Document your XML structure decisions, balance calculations, testing results

4. **PROMPT.md**

   - Original design brief (reference for context)

5. **OrbTypes_zEternalDao.xml**
   - Already created (custom planets/stars)

---

## Your Tasks

### Phase 1: Foundation (Priority 1)

**Goal**: Get the mod loading and basic race playable

**Files to Create**:

1. `dw2-eternal-dao/EternalDao/Races_zEternalDao.xml`

   - Race ID, name, bonuses
   - Starting ships and resources
   - AI personality
   - Reference: SUMMARY.md "Race/Faction Design" section

2. `dw2-eternal-dao/EternalDao/Governments_zEternalDao.xml`

   - Imperial Mandate Senate government type
   - Base it on Republican with modifications
   - Reference: WIKI.md "Government" section

3. `dw2-eternal-dao/EternalDao/GameEvents_zEternalDaoStarGen.xml`

   - Star system generation events for custom planet types
   - Works with OrbTypes_zEternalDao.xml
   - Reference base game star generation events
   - Ensures Jade worlds, Ley-Line Nexus, etc. spawn correctly

4. `dw2-eternal-dao/EternalDao/CharacterAnimations_zEternalDao.xml`

   - Copy from base-game/data/CharacterAnimations_Human.xml
   - Update race ID references to match Eternal Celestials

5. `dw2-eternal-dao/EternalDao/CharacterRooms_zEternalDao.xml`

   - Copy from base-game/data/CharacterRooms_Human.xml
   - Update race ID references to match Eternal Celestials

6. Test: Can you start a game as Eternal Celestials?

### Phase 2: Unique Content (Priority 2)

**Goal**: Implement all unique components, facilities, research

**Files to Create**:

7. `dw2-eternal-dao/EternalDao/ComponentDefinitions_zEternalDao.xml`

   - 11 unique components (3 reactors, 2 shields, 2 hyperdrives, 4 weapons, 2 sensors, 1 armor)
   - Reference: SUMMARY.md "Components" section for stats

8. `dw2-eternal-dao/EternalDao/PlanetaryFacilityDefinitions_zEternalDao.xml`

   - 9 unique facilities
   - Reference: SUMMARY.md "Facilities" section for effects

9. `dw2-eternal-dao/EternalDao/ResearchProjectDefinitions_zEternalDao.xml`

   - 5 branches, ~50 projects total
   - Reference: SUMMARY.md "Research Tree" section

10. `dw2-eternal-dao/EternalDao/TroopDefinitions_zEternalDao.xml`

    - Unique troop types with cultivation-themed names
    - Balance: ~20-30% stronger than standard troops
    - Reference base game TroopDefinitions.xml for structure

11. `dw2-eternal-dao/EternalDao/Artifacts_zEternalDao.xml`

    - Race-specific artifacts (jade talismans, formation scrolls, etc.)
    - Tie into story arcs where appropriate
    - Reference: WIKI.md for culturally appropriate items

12. Test: Can you build and use all unique content?

### Phase 3: Story Events (Priority 3)

**Goal**: Implement the 10 main story arcs with branching

**Files to Create**:

13. `dw2-eternal-dao/EternalDao/GameEvents_zEternalDao.xml`

    - All 10 story arcs
    - Implement flag system for choices
    - Reference: SUMMARY.md "10 Main Story Arcs" section
    - Reference: docs/DW2_GAME_EVENTS.md for trigger types

14. Test: Do arcs trigger correctly? Do choices branch?

### Phase 4: Features & Episodic (Priority 4)

**Goal**: Implement racial features and replayable events

**Files to Create**:

15. `dw2-eternal-dao/EternalDao/GameEvents_zEternalDaoRacialFeatures.xml`

    - 6 racial features
    - Reference: SUMMARY.md "6 Racial Features" section

16. `dw2-eternal-dao/EternalDao/GameEvents_zEternalDaoEpisodic.xml`

    - 8 episodic events
    - Reference: SUMMARY.md "8 Episodic Events" section

17. `dw2-eternal-dao/EternalDao/ColonyEventDefinitions_zEternalDao.xml` (optional)

    - Only create if racial features require colony-specific events
    - Check if Imperial Edicts or other features need colony event hooks
    - Reference base game ColonyEventDefinitions.xml

18. Test: Do features trigger? Are cooldowns working?

### Phase 5: Localization & Polish (Priority 5)

**Goal**: All text strings, balance pass, bug fixes

**Files to Create**:

19. `LOCALIZATION.yml`

    - All flavor text in YAML format (key: value pairs)
    - Comedic tone throughout
    - Reference: WIKI.md for character voices and running gags
    - Reference: base-game localization files for format examples

20. Balance Testing

    - Play 3+ full games to year 100+
    - Verify: 30-80% power advantage vs standard races
    - Check: Bureaucracy Load, Karmic Debt, Tribulation systems functional

21. Bug Fixes

    - Ensure no crashes
    - Fix trigger issues
    - Verify all branches work

---

## Critical Guidelines

### Lore Consistency

**ALWAYS reference WIKI.md for**:

- Character personalities (Wei Jian, Shu Tianwen, Bao Meimei)
- Cultural elements (dumplings, fruit baskets, merit pastries)
- Government committees and agencies
- Running gags (HOA violations, sword spirit unionizing, Shu optimizing things)
- Tone: Comedic but sincere, low jargon, bureaucratic immortals

**DO**:

- Keep humor accessible to non-xianxia fans
- Maintain distinct character voices
- Use cultural touchstones consistently
- Reference the committees by name
- Keep technical jargon minimal

**DON'T**:

- Use heavy cultivation terminology
- Break character consistency
- Make power levels explicit (keep Meimei's power subtle)
- Forget running gags
- Use generic fantasy language

### Balance Targets

**Power Curve**:

- Early game (years 1-20): 30-40% stronger
- Mid game (years 20-50): 50-60% stronger
- Late game (years 50+): 70-80% stronger

**Pressure Valves** (MUST IMPLEMENT):

1. **Bureaucracy Load**

   - Track as hidden colony event or modifier
   - Range: 0-100
   - Accumulates from: rapid expansion, edicts, major decisions
   - Decays: 5 points/year
   - Effects: +1-2% maintenance per point

2. **Karmic Debt**

   - Track as flag-based counter
   - Range: -100 (credit) to +100 (debt)
   - Affects: Random event frequency, Heaven's approval
   - Thresholds: -50 (blessings), +30 (problems), +60 (wrath)

3. **Tribulation Weather**
   - Chance: 2% base + 0.5%/10 colonies + 1%/50 ships
   - Can harvest for energy (risky, profitable)
   - Reduced by: Karmic Credit, Heaven-appeasing actions

### Event Implementation

**Trigger Types to Use**:

- `Undefined` (time-based or manual)
- `Investigate` (ruins on planets)
- `Build` (facilities, stations)
- `ResearchBreakthrough` (tech completions)
- `EmpireRelationChange` (first contact, treaties)

**Flag System**:

- Name format: `eternal_arc[N]_complete`
- Choice tracking: `eternal_[category]_[choice]`
- Counters: `eternal_wanderer_clues` (need 3 for Arc 10)

**Branching Choices**:

- Set TriggerActionsAreChoices: true
- Use ChoiceButtonText for options
- Create separate follow-up events per choice
- Set flags to track decisions

---

## File Structure

```
dw2-eternal-dao/
├── WIKI.md                          (✅ Complete - Lore Bible)
├── SUMMARY.md                       (✅ Complete - Your Guide)
├── DESIGN_DOCUMENT.md               (📝 YOU populate with technical notes)
├── PROMPT.md                        (Reference only)
├── AGENT.md                         (This file)
├── LOCALIZATION.md                         (Reference for our event text)
└── EternalDao/
    ├── OrbTypes_zEternalDao.xml                       (✅ Already exists)
    ├── GameEvents_zEternalDaoStarGen.xml              (Partially Initiated)
    ├── Races_zEternalDao.xml                          (Partially Initiated)
    ├── Governments_zEternalDao.xml                    (❌ YOU CREATE)
    ├── ComponentDefinitions_zEternalDao.xml     (❌ YOU CREATE)
    ├── FacilityDefinitions_zEternalDao.xml      (❌ YOU CREATE)
    ├── ResearchProjectDefinitions_zEternalDao.xml (❌ YOU CREATE)
    ├── GameEvents_zEternalDao.xml               (❌ YOU CREATE)
    ├── GameEvents_zEternalDaoRacialFeatures.xml                  (❌ YOU CREATE)
    ├── GameEvents_zEternalDaoEpisodic.xml                        (❌ YOU CREATE)
    ├── ColonyEventDefinitions_zEternalDao.xml   (❌ YOU CREATE)
    ├── OrbTypes_zEternalDao.xml                       (✅ Already exists)
```

---

## Testing Checklist

After each phase, verify:

**Phase 1 - Foundation**:

- [ ] Game launches without errors
- [ ] Can select Eternal Celestials race
- [ ] Refugee fleet start scenario works
- [ ] Can establish first colony
- [ ] Starting bonuses are active

**Phase 2 - Content**:

- [ ] All components buildable and functional
- [ ] All facilities buildable with stated effects
- [ ] Research tree has no broken prerequisites
- [ ] Unique content provides appropriate bonuses
- [ ] Balance feels OP but not broken

**Phase 3 - Story**:

- [ ] Arc 1 triggers at game start
- [ ] Arc 2 triggers when first colony founded
- [ ] Arc 3 triggers on first contact
- [ ] All choices present proper options
- [ ] Flags set correctly for tracking
- [ ] Branches lead to different outcomes
- [ ] Arc 10 requires 3 clues to unlock

**Phase 4 - Features**:

- [ ] Enlightened Leadership triggers after battles/crises
- [ ] Imperial Edicts unlockable with Senate Hall
- [ ] Tribulation Grid Subsidy offers during storms
- [ ] Merit Pastry Program tracks karma
- [ ] Artifact Spirit Unionization after College built
- [ ] Three Courtesies Protocol active on first contact
- [ ] Episodic events fire with appropriate frequency

**Phase 5 - Polish**:

- [ ] All text strings localized
- [ ] No placeholder text remains
- [ ] Tone consistent throughout
- [ ] Character voices distinct
- [ ] Running gags referenced appropriately
- [ ] Full playthrough to year 100+ successful
- [ ] No crashes or major bugs

---

## Balance Testing Protocol

### Test Game 1: Standard Playthrough

- **Settings**: Normal difficulty, standard galaxy
- **Goal**: Play to year 100 or victory
- **Track**: Expansion rate, research speed, military power, economy
- **Compare**: Against standard races in same game
- **Target**: 30-80% advantage depending on phase

### Test Game 2: Pressure Valve Focus

- **Settings**: Higher difficulty, aggressive AI
- **Goal**: Test Bureaucracy Load, Karmic Debt, Tribulation
- **Track**: How often pressure valves trigger, effectiveness
- **Verify**: They actually limit player, not just cosmetic

### Test Game 3: Story Arc Coverage

- **Settings**: Standard
- **Goal**: Trigger all 10 arcs and test all branches
- **Track**: Flag system working, choices matter
- **Verify**: Can reach Arc 10, all endings accessible

---

## Common Issues and Solutions

### Issue: Events Not Triggering

**Solutions**:

- Check TriggerType matches actual game state
- Verify PlacementRaceID is correct (255 = player race)
- Check Conditions are properly set
- Verify prerequisite flags exist
- Check TriggerPriority for multiple events on same trigger

### Issue: Balance Too Strong/Weak

**Solutions**:

- Adjust bonuses by 5-10% increments
- Increase/decrease Bureaucracy Load accumulation
- Adjust pressure valve thresholds
- Modify component stat advantages
- Change research costs/times

### Issue: Flags Not Tracking

**Solutions**:

- Ensure flag events are actually firing
- Check flag names for typos (case-sensitive)
- Verify flag set before checked
- Use GeneratedItemName correctly for flag creation
- Test flag existence with Conditions

### Issue: Branching Not Working

**Solutions**:

- Verify TriggerActionsAreChoices: true
- Check ChoiceButtonText present
- Ensure each choice triggers different event
- Verify follow-up events have correct names
- Test that GeneratedItemName matches event names

---

## Documentation Requirements

As you implement, document in **DESIGN_DOCUMENT.md**:

1. **XML Structure Decisions**

   - Why you structured events a certain way
   - ID numbering scheme used
   - Any deviations from SUMMARY.md specs

2. **Balance Calculations**

   - Actual component stat values chosen
   - Reasoning for facility effect strengths
   - Research cost calculations

3. **Testing Results**

   - Game performance data
   - Balance observations
   - Bug tracking and fixes

4. **Known Issues**
   - Unresolved bugs (if any)
   - Limitations of implementation
   - Future improvement areas

---

## Success Criteria

### Minimum Viable Mod (Must Have)

✅ Game launches and loads mod  
✅ Race is playable start to finish  
✅ All unique content works  
✅ At least 5 story arcs functional  
✅ Balance is OP but not game-breaking  
✅ No major crashes or bugs

### Complete Mod (Should Have)

✅ All 10 story arcs working  
✅ All 6 racial features working  
✅ All 8 episodic events working  
✅ Branching choices functional  
✅ Pressure valves active and effective  
✅ Full localization complete  
✅ AI can play race competently

### Polished Mod (Nice to Have)

✅ Custom icons/graphics  
✅ Balanced for multiple difficulty levels  
✅ No exploits or cheese strategies  
✅ All running gags properly referenced  
✅ Character voices perfectly distinct  
✅ Extensive playtesting completed

---

## Final Checklist

Before declaring mod complete:

**Documentation**:

- [ ] DESIGN_DOCUMENT.md fully populated
- [ ] All technical decisions documented
- [ ] Installation guide written
- [ ] Known issues listed

**Functionality**:

- [ ] All 13 XML files created
- [ ] All content tested and working
- [ ] Balance verified through playtesting
- [ ] No crashes or game-breaking bugs

**Quality**:

- [ ] Lore consistency maintained
- [ ] Tone appropriate throughout
- [ ] No typos in player-facing text
- [ ] Running gags properly used
- [ ] Character voices distinct

**Polish**:

- [ ] Localization complete
- [ ] Placeholder text removed
- [ ] Clean folder structure
- [ ] Commented XML where helpful

---

## Getting Help

**If Stuck**:

1. Re-read SUMMARY.md for specifications
2. Check WIKI.md for lore consistency
3. Reference docs/DW2_GAME_EVENTS.md for event mechanics
4. Look at base game XML files for structure examples
5. Test incrementally - don't implement everything before testing

**Remember**:

- You have complete design documentation
- Specifications are clear and detailed
- Your job is implementation, not design
- Test early, test often
- Ask for clarification if specs are unclear

---

## Good Luck!

You have everything you need:

- Complete lore (WIKI.md)
- Detailed specifications (SUMMARY.md)
- Clear priorities and testing protocols
- Balance targets and pressure valves

**May your XML be valid, your events trigger correctly, and your fruit baskets be properly filed!**

---

_Instructions prepared by the Design Completion Division_  
_For the Technical Implementation Agent_  
_Status: Ready to Begin Implementation_  
_The Senate approves of this handoff. Heaven has been notified._
