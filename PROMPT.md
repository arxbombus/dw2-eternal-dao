# Eternal Dao Stellarity - Implementation Instructions

**Project**: Distant Worlds 2 Mod  
**Mod Name**: Eternal Dao Stellarity  
**Race**: Eternal Celestials  
**Phase**: Technical Implementation

---

## 🎯 Your Mission

Implement the **Eternal Dao Stellarity** mod for Distant Worlds 2.

**The design phase is COMPLETE.** You are the implementation agent. Your job is to create XML files, balance gameplay, and deliver a playable mod based on the complete design documentation.

---

## 📚 Start Here

### 1. Read AGENT.md First

**File**: `dw2-eternal-dao/AGENT.md`

This contains your complete instructions:

- What files to create (13 XML files)
- Implementation priorities (5 phases)
- Testing checklists
- Balance targets
- Troubleshooting guide

### 2. Reference Documentation

**WIKI.md** (532 lines) - Your Lore Bible

- Complete species lore and history
- The Wanderer (creator figure)
- Three Prodigies (Wei Jian, Shu Tianwen, Bao Meimei)
- Government structure (Imperial Mandate Senate)
- Cultural elements (dumplings, fruit baskets, merit pastries)
- Running gags and character voices
- **USE THIS for all flavor text**

**SUMMARY.md** (500 lines) - Your Technical Guide

- All 24 event specifications (10 arcs + 6 features + 8 episodic)
- 11 unique components with stats
- 9 unique facilities with effects
- 5-branch research tree (~50 projects)
- Balance targets (30-80% stronger than standard)
- Pressure valve mechanics (Bureaucracy Load, Karmic Debt, Tribulation)
- **USE THIS for all mechanical implementation**

**DESIGN_DOCUMENT.md** (placeholder)

- YOU populate this as you implement
- Document your XML structure decisions
- Track balance calculations
- Record testing results

---

## 🎮 What You're Building

### The Eternal Celestials

Imortal cultivators who blend xianxia tropes with bureaucratic comedy. They're overpowered reality-benders who treat galactic conquest as a filing exercise and solve diplomatic disputes with dumplings.

### Key Features

- **10 Story Arcs**: Following the Three Prodigies and The Wanderer mystery
- **6 Racial Features**: Unique mechanics like Tribulation Grid Subsidy, Merit Pastry Program
- **8 Episodic Events**: Replayable humor (HOA violations, sword-light ballet, etc.)
- **11 Unique Components**: Techno-dao fusion weapons, reactors, shields
- **9 Unique Facilities**: Senate Halls, Ley-Line Nexus Spires, Artifact Spirit Colleges
- **Overpowered but Balanced**: Via Bureaucracy Load, Karmic Debt, Tribulation Weather

### Tone

**Comedic but sincere.** Bureaucratic immortals with day jobs. Accessible to non-xianxia fans. Low jargon, high charm.

---

## ⚡ Quick Start

### Phase 1: Foundation (Do This First)

1. Create `Races_EternalDao.xml`
2. Create `Governments_EternalDao.xml`
3. Create `GameEvents_zEternalDaoStarGen.xml`
4. Test: Can you start a game as Eternal Celestials?

### Phase 2: Unique Content

4. Create `ComponentDefinitions_EternalDao.xml`
5. Create `FacilityDefinitions_EternalDao.xml`
6. Create `ResearchProjectDefinitions_EternalDao.xml`
7. Test: Can you build and use everything?

### Phase 3: Story

8. Create `GameEvents_EternalDao.xml` (10 arcs)
9. Test: Do arcs trigger? Do choices branch?

### Phase 4: Features

10. Create `GameEvents_RacialFeatures.xml` (6 features)
11. Create `GameEvents_Episodic.xml` (8 events)
12. Test: Do features work? Right frequency?

### Phase 5: Polish

13. Create `LOCALIZATION.yml`
14. Balance testing (3+ full games to year 100)
15. Bug fixes and final polish

---

## 📂 File Structure

```
dw2-eternal-dao/
├── WIKI.md                    (✅ Complete - Read for lore)
├── SUMMARY.md                 (✅ Complete - Read for specs)
├── AGENT.md                   (✅ Complete - Your instructions)
├── PROMPT.md                  (This file)
├── DESIGN_DOCUMENT.md         (📝 YOU populate)
└── EternalDao/
    ├── OrbTypes_zEternalDao.xml                          (✅ Exists)
    ├── GameEvents_zEternalDaoStarGen.xml                 (❌ YOU CREATE)
    ├── Races_EternalDao.xml                       (❌ YOU CREATE)
    ├── Governments_EternalDao.xml                        (❌ YOU CREATE)
    ├── ComponentDefinitions_EternalDao.xml        (❌ YOU CREATE)
    ├── PlanetaryFacilityDefinitions_EternalDao.xml (❌ YOU CREATE)
    ├── ResearchProjectDefinitions_EternalDao.xml  (❌ YOU CREATE)
    ├── GameEvents_EternalDao.xml                  (❌ YOU CREATE)
    ├── GameEvents_RacialFeatures.xml                     (❌ YOU CREATE)
    ├── GameEvents_Episodic.xml                           (❌ YOU CREATE)
    ├── ColonyEventDefinitions_zEternalDao.xml     (❌ YOU CREATE - if needed)
    ├── TroopDefinitions_zEternalDao.xml           (❌ YOU CREATE)
    ├── Artifacts_zEternalDao.xml                  (❌ YOU CREATE)
    ├── CharacterAnimations_zEternalDao.xml        (❌ YOU CREATE - copy from Humans)
    ├── CharacterRooms_zEternalDao.xml             (❌ YOU CREATE - copy from Humans)
    └── LOCALIZATION.yml                                  (❌ YOU CREATE)
```

---

## ✅ Success Criteria

**Minimum Viable**: Game launches, race playable, all unique content works, at least 5 arcs functional, balanced, no crashes

**Complete Mod**: All 10 arcs + 6 features + 8 events working, branching functional, pressure valves active, localized, AI competent

**Polished**: Custom icons, balanced for multiple difficulties, no exploits, perfect tone/character voices

---

## 🚨 Critical Guidelines

### Lore Consistency

- ALWAYS reference WIKI.md for character voices and cultural elements
- Keep humor accessible (no heavy cultivation jargon)
- Maintain distinct personalities for the Three Prodigies
- Use running gags consistently

### Balance Targets

- Early game: 30-40% stronger than standard
- Mid game: 50-60% stronger
- Late game: 70-80% stronger
- MUST implement pressure valves (Bureaucracy Load, Karmic Debt, Tribulation)

### Event Implementation

- Use proper trigger types (Investigate, Build, ResearchBreakthrough, EmpireRelationChange)
- Implement flag system for tracking choices
- Set TriggerActionsAreChoices: true for branching
- Test all branches work correctly

---

## 📖 Documentation References

**In This Repo**:

- `docs/DW2_GAME_EVENTS.md` - Event mechanics reference
- `base-game/data/*.xml` - Base game examples

**External** (if needed):

- Matrix Games Forums: https://forums.matrixgames.com/viewforum.php?f=11899

---

## 🎯 Next Steps

1. **Read AGENT.md completely** (it has detailed instructions)
2. **Read SUMMARY.md** (your technical specifications)
3. **Skim WIKI.md** (understand the lore and tone)
4. **Create Phase 1 files** (Races and Governments XMLs)
5. **Test early, test often**
6. **Document your decisions in DESIGN_DOCUMENT.md**

---

## 💬 Need Help?

1. Re-read SUMMARY.md for specifications
2. Check WIKI.md for lore consistency
3. Reference docs/DW2_GAME_EVENTS.md for event mechanics (this is important to anything that will be using events)
4. Look at base game XML files for structure examples
5. Ask for clarification if specs are unclear

---

**You have everything you need. The design is complete. Now implement it!**

**May your XML be valid, your events trigger correctly, and your fruit baskets be properly filed!**

---

_Implementation Instructions_  
_Status: Design Complete, Ready for Implementation_  
_The Senate approves. Heaven has been notified._

**Keep in mind that we are working in a wsl ubuntu environment. However, due to technical limitations, the serena mcp server is running on my windows end. you are currently in the /home/harrison/programming/distant-worlds-2 folder, which is the monorepo for all my distant worlds 2 related mods and references. You are working on /home/harrison/programming/distant-worlds-2/dw2-eternal-dao. Keep in mind that because of all the weirdness with the environment, do not try to complicate things. if you need to find files with paths, understand that you are in the /home/harrison/programming/distant-worlds-2 directory already**
