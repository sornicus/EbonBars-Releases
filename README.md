# EbonBars

**Roguelike Hotbar Manager for Ebonhold Server**

EbonBars automatically saves and restores your action bar layouts across character resets — so every reroll starts with your bars already set up.

**Version:** 0.4.7  
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
/eb bugreport             — open copyable diagnostic report (Ctrl+A, Ctrl+C to copy)
/eb resetstate            — reset all flags and settings, preserve your layouts
```

---

## Reporting Bugs

Type `/eb bugreport` in-game. A window will appear with a full diagnostic snapshot — hit **Ctrl+A** then **Ctrl+C** to copy it, and paste it with your bug report.

---

## Changelog

### 0.4.7
- **Leveling layout sync fix (root cause)** — eliminated the two-table sync problem between `levelingLayout` and `savedLayout`. `savedLayout` is now always the correction reference at all levels. During post-reset leveling the correction pass already skips unlearned spells, so bars restore to saved positions as spells are learned — no separate leveling snapshot needed. This resolves all variants of the "wrong bars after reset" issue without heuristics or migration logic. No user action required.

### 0.4.6
- **Leveling layout restore fix** — bars now correctly fill in during post-reset leveling. Root cause: the leveling layout (used as the correction reference while re-leveling) could be auto-snapshotted from a disorganized bar state at level 80 if `atMaxLevel` was not set correctly in older versions. This left the leveling reference out of sync with the player's actual organized layout. Fix: whenever `/eb save force` is run at max level, the leveling layout is now synced from `savedLayout` automatically. **Existing affected characters:** reach level 80, run `/eb save force` once, and future resets will restore correctly.

### 0.4.5
- **Spec switch popup fix** — the "layout changed" popup no longer fires after switching specs. Two root causes fixed: (1) the change-detection baseline snapshot was taken at correction time, before a 1.5s settle window — server-side spell placements during that window left the snapshot stale; snapshot is now taken at arm time. (2) The change comparison checked all 120 slots — server auto-places newly-learned talent spells into unmanaged slots on spec switch, triggering false positives; comparison now scopes to saved-layout managed slots only.

### 0.4.4
- **Max level in-session fix** — `atMaxLevel` is now correctly set when reaching max level via `PLAYER_LEVEL_UP`, not just on login. Previously, a player who reset mid-session and re-leveled to 80 without relogging would stay in a "not at max level" state — suppressing the spec-switch no-layout popup and change detection. A relog was the workaround; it no longer is.

### 0.4.3
- **In-session reset popup fix** — the "layout changed" popup no longer fires at level 1 immediately after a Self Terminate / roguelike reset mid-session. Root cause: `atMaxLevel` was not cleared when the reset was detected in-session, causing the next `ACTIONBAR_SLOT_CHANGED` event (bars wiped by reset) to trigger the popup incorrectly. Relogging previously worked around this since runtime rebuilds from scratch on login.
- **Max level save popup fix** — fresh characters who level to 80 mid-session now correctly receive the prompt to save their layout. Previously only showed at login.
- **Bug report** — `Ready To Track Changes` runtime flag now included in the diagnostic snapshot.

### 0.4.2
- **`/eb resetstate`** — new command that resets all flags and settings to defaults while preserving your saved layouts. Use this if the addon gets into a bad state before filing a bug report.
- **Layout changed popup redesigned** — now shows which layout changed by name (e.g. "Your Retribution layout has changed."). Buttons are now **Update Layout** and **Revert to Saved** — both do exactly what they say. Revert immediately restores your saved layout rather than leaving bars in an ambiguous state.
- **Full session logging** — debug log and event trace now capture all activity regardless of verbosity setting, so bug reports contain a complete picture even without debug mode enabled.
- **Bug report enhanced** — captures full DB state, init flags, spec layout counts, account-wide settings, and 50 log/event entries (up from 20).
- **SavedVariables cleanup** — stale event log data from debug sessions is cleared on load, keeping the SavedVariables file lean.

### 0.4.1
- **Copyable bug report** — `/eb bugreport` now opens a scrollable in-game popup instead of printing to chat. Select all and copy with Ctrl+A / Ctrl+C.
- **Version string fix** — addon version now reads from the TOC file instead of being hardcoded.

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
