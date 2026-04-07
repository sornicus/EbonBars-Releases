# EbonBars

**Roguelike Hotbar Manager for Ebonhold Server**

EbonBars automatically saves and restores your action bar layouts across character resets — so every reroll starts with your bars already set up.

**Version:** 0.4.0  
**Compatible:** WoW 3.3.5a (Wrath of the Lich King)  
**Server:** Ebonhold

---

## Installation

1. Download `EbonBars.zip` from the [latest release](../../releases/latest)
2. Extract the `EbonBars` folder into your addons directory:
   ```
   AppData\Local\ebonhold\Interface\AddOns\EbonBars\
   ```
3. Restart WoW and verify with `/eb help`

---

## What It Does

### First Character
Arrange your bars at level 80, then save:
```
/eb save force
```
EbonBars also silently snapshots your bars during leveling as a restore point.

### After Every Reset
- **Login at level 1** → your leveling snapshot is your starting point
- **As you re-level** → saved spells fill back in as you learn them
- **Slots 1–12** → strictly managed (WoW auto-placements cleared, your saved spells restored)
- **Slots 13–120** → always under your manual control

### Per-Spec Layouts
Save separate bar setups for each talent spec. When you switch specs via the talent UI dropdown, EbonBars detects the change and restores the matching layout automatically.

---

## Key Commands

```
/eb help                  — full command list
/eb save [force]          — save current bars as your level 80 layout
/eb correct               — run a correction pass now
/eb status                — show saved layout info
/eb specstatus            — show active spec slot and saved spec layouts
/eb diagnose all          — slot-by-slot bar analysis
/eb bugreport             — generate a diagnostic snapshot
```

---

## Changelog

### 0.4.0
- **Spec detection rewritten** — now uses `SpecDropdownFrameText` matched against `SpecCustomNames` to reliably identify the active spec slot via the talent UI dropdown. Previous hook targeted the wrong UI element.
- **Cross-session reset detection** — resets that happen between sessions (log out at 80, log back in at level 1) are now correctly detected at login.
- **Pre-combat snapshot fix** — leveling layout auto-snapshot on entering combat was silently blocked by a combat lockdown guard on read-only operations. Fixed.
- **ArmChangeDetection race condition fixed** — stale timers from a prior correction pass could arm change detection during a spec switch, causing false "bars changed" popups. Generation counter prevents this.
- **Login correction pass** — now runs at all levels on login (previously skipped at max level).
- **`usingLevelingLayout` flag fix** — post-reset leveling correction now correctly targets the leveling layout instead of falling through to placeholder behavior.

### 0.3
- Initial per-spec layout system
- Leveling layout auto-snapshot
- Post-reset correction pass with level-gated spell restoration

---

## Compatibility

- WoW 3.3.5a (Interface 30300)
- Bartender4, Dominos, or default Blizzard UI

