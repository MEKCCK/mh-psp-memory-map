# Monster Hunter PSP Series — Memory Map (MHF · MHF2 · MHFU · MHP3RD)

All addresses are **PSP virtual addresses** (identical on real hardware and PPSSPP).

## Source Tags

> This is a **memory reference table**. Cheat/cheat-code databases are only a
> *discovery & verification* source (see digging-guide); entries are recorded as
> memory addresses, not as codes.

| Tag | Source |
|---|---|
| **[orig]** | Alexander-Lancellott / **[MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator)** (Python) — the real origin of the monster pointer tables & offsets; this project only ported/integrated them |
| **[load]** | Kurogami2134/mhp3reload loader asm (`src/*.asm`) |
| **[modman]** | Kurogami2134/p3rdml_modman |
| **[hpbar]** | Kurogami2134/mhp3rd_monster_hp_bar |
| **[dmg]** | Kurogami2134/mhp3rd_dmg_numbers |
| **[sharp]** | Kurogami2134/p3rd_sharpness_indicator |
| **[item]** | Kurogami2134/p3rd_item_sets |
| **[transmog]** | James-028/[mhfu_transmog](https://github.com/James-028/mhfu_transmog) `FINDINGS.md` |
| **[transmog3]** | Exceen/[mhp3rd_transmog](https://github.com/Exceen/mhp3rd_transmog) `build_data.py` |
| **[re]** | SiD3W4y/[mhfu-re](https://github.com/SiD3W4y/mhfu-re) `doc/*.txt` | MHFU runtime structures/functions/OVL |
| **[fdx]** | SilverJolteon/[FreedomDX](https://github.com/SilverJolteon/FreedomDX) | MHF1 patches (event loader slots etc.) |
| **[val]** | This project (MEKCCK in-emulator integration) — cross-validation only, no new addresses |
| **[us]** | MEKCCK (repo maintainer), measured on device/emulator | P3HD verified values |
| **[cwps]** | community CWCheat (user archived) — used as a *verification source* | P3 original (ULJM05800) values |
| **[cw]** | community CWCheat (user archived) — *verification source* | MHF1 (ULJM05066) values |

> **Attribution:** every piece of memory knowledge in this table comes from the two
> original authors' **public projects** listed above. The PPSSPP in-emulator overlay
> only *ported, integrated and cross-checked* already-public data. No address here is
> an original discovery of this project.

## 1. MHF1 (ULJM05066 · ULES00318 · ULUS10084)
> Tables below come from **[orig]** (Python overlay project).
> Usage: list pointer = `0x08800000 + initial` → monster list `pointer[i]` (4 bytes),
> non-zero = alive monster → monster struct; offsets are relative to the struct.
> Name byte → monster tables in `UI/monster_tables.inc` of the overlay (per game).
>
> **Types**: name `u8`; HP/MaxHP `u32`; size `u16`; status pairs `u16` (cur/max);
> rage `u16` (frames, /60 = seconds); list pointer `u32`; struct pointer chain (ptr → struct).

### Monster data table [orig]

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison cur/max | Sleep cur/max | Para cur/max | Dizzy cur/max | Rage(u16) |
|---|---|---|---|---|---|---|---|---|---|---|
| ULES00318 | 0x1254D70 | +0x210 | +0x312 | +0x43C | +0x2A4 | 0x3A8/0x46C | 0x462/0x460 | 0x474/0x472 | — | 0x580 |
| ULUS10084 | 0x1253F70 | +0x210 | +0x312 | +0x43C | +0x2A4 | 0x3A8/0x46C | 0x462/0x460 | 0x474/0x472 | — | 0x580 |
| ULJM05066 | 0x1253570 | +0x210 | +0x312 | +0x43C | +0x2A4 | 0x3A8/0x46C | 0x462/0x460 | 0x474/0x472 | — | 0x580 |

### Player / guild-card text [cw]

Character-set note: MHF1 name/guild-card text is **UTF-16BE** (2 bytes/char,
high byte first) — different from P3/P3HD (LE). 8 chars max for names; past that
the fields bleed into other data (unstable later in game).

| Field | Offset | Absolute | Size / layout |
|---|---|---|---|
| Character name | 0x10532C4 | 0x098532C4 | 8 chars = 16 B, UTF-16BE, 2 chars per u32 (hi first) |
| Guild-card name | 0x1057620 | 0x09857620 | 8 chars = 16 B, same encoding |
| Guild-card self intro | 0x105764C | 0x0985764C | 96 chars = 192 B; every 2 chars = +4 B (continues line-by-line) |
| Title cursor lock | 0x113A7C0 | 0x0993A7C0 | u8: fixed title-cursor slot |


### Patch anchors (FreedomDX) [fdx]
| Feature / anchor | Address | Version |
|---|---|---|
| EventLoader slot (EN) | `0x095079E0` | MHF1 USA (ULES00318 family) |
| EventLoader slot (JP) | `0x094F31E0` | MHF1 JP (ULJM05066) |

- FreedomDX ships one MIPS patch per QoL feature (Event Quest Loader, Input Drop Fix,
  Instant supply box, Hold-to-Gather, …) — `build.py` assembles all of them;
  those addresses were previously blank for MHF1 in this table.
- File replacement uses `FDXDAT/NATIVEPSP/<DISC>/<fileid>` — same convention as #17.

## 2. MHF2 (ULES00851 · ULUS10266 · ULJM05156)
### Monster data table [orig]

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison | Sleep | Para | Dizzy | Rage |
|---|---|---|---|---|---|---|---|---|---|---|
| ULES00851 | 0x127AD70 | +0x1E8 | +0x2E2 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x55E | 0x564 |
| ULUS10266 | 0x12799F0 | +0x1E8 | +0x2E2 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x55E | 0x564 |
| ULJM05156 | 0x1278E70 | +0x1E8 | +0x2E2 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x55E | 0x564 |

## 3. MHFU (ULES01213 · ULUS10391 · ULJM05500)
### Monster data table [orig]

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison | Sleep | Para | Dizzy | Rage |
|---|---|---|---|---|---|---|---|---|---|---|
| ULES01213 | 0x1412140 | +0x1E8 | +0x2E4 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x566 | 0x56C |
| ULUS10391 | 0x1412240 | +0x1E8 | +0x2E4 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x566 | 0x56C |
| ULJM05500 | 0x140D3C0 | +0x1E8 | +0x2E4 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x566 | 0x56C |

### Runtime structures [re]
| Structure | Address / offset | Field |
|---|---|---|
| Game state | ptr @ `0x09C122B0` (game_task.ovl loaded) | huge global; +0x1220 entity ptr list (0x14 pters), +0x1270 entity count u32 |
| Player state | vtable `0x089BB3CC` | see offsets below |
| — pos | `+0x200 / +0x204 / +0x208` | x / y / z (f32) — **player coordinates!** |
| — map entry | `+0x2A4` | u8 (1/2/3) |
| — HP | `+0x2E4` | u16 |
| — damage this frame | `+0x3B8` | i32 (negated before deal_damage) |
| — item box index | `+0x55D` | u8/u16 |
| Monster state | same pos/HP layout as player | |
| — entity id | `+0x1E4` | u8 |
| — next id | `+0x1E6` | u16 |
| — type | `+0x1E8` | u8 — **== [orig] MHFU name offset `+0x1E8` (cross-verified ✓)** |

Player vtable slots: [4] quest start `0x09A65498`; [16] map change `0x09A655F0`
(also sets position); [26] `deal_damage` `0x088D6594` (damage>0 heal, <0 hurt;
call sites `0x09A6B77C` dmg / `0x09A69BCC` regen); [27] player action `0x09A67630`.
Monster vtable: [34] `change_state` (called at `0x09AC8B4C`); [58] hit handler
`0x09AD4C58`; Rajang vtable `0x089BCD64`. Entity type 0..175 table = MHFU monster
table (see [orig] `monsters_mhfu`). Item box: ptr @ `0x089CC558`, item array at
`+0x390` (pairs of u16 object_id, s16 count).

### Key functions [re]
| Address | Function |
|---|---|
| `0x0884EA1C` | `decrypt_data(global_state, data, size)` |
| `0x09A6B130` | damage computation (applies all modifiers to entity `+0x3B8`) — **damage-numbers hook candidate** |
| `0x09AC8AF0` | indirect monster-state changer (vtable+0x88) |
| `0x088D72A4` | `give_item(player_state, u16 object_id, s16 count)` |

### Static data tables [transmog]
> Verified via FUComplete patches / savestate diffs; CWCheat examples confirm the mapping.

| Table | Address | Notes |
|---|---|---|
| Weapon table | `0x089574E8` | 24 bytes/entry, ~1149 entries (ends ~`0x0895E0A0`); model id @ `+0x10` (1 byte) |
| Armor pointer table | `0x08975970` | `type2`-indexed pointers to slot tables |
| — legs | `0x08970D30` | 40 bytes/entry |
| — head | `0x08960750` | 40 bytes/entry |
| — chest | `0x08964B70` | 40 bytes/entry |
| — arms | `0x08968D10` | 40 bytes/entry |
| — waist | `0x0896CD48` | 40 bytes/entry (ptr index 5/6 are flags `0x02030007`/`0x04030002`, not pointers) |
| Model file lookup | `0x0893E7F0` | SHARED across equipment types — do NOT patch |
| Craft / upgrade table | `0x08938D1A` | |
| Weapon index table | `0x089A1878` | |
| FUComplete TABLE_A | `0x089972AC` | u16 array (not used at model-load) |
| FUComplete TABLE_B | `0x08997BA8` | u16 array (matches modelIdMale) |
| FUComplete TABLE_E | `0x0899851C` | u16 array |

**Entry layout (partial, from FINDINGS.md code examples):**
- Weapon entry (24B): model id `u8` @ `+0x10` (verified via 8-bit CWCheat writes);
  remaining fields unverified.
- Armor entry (40B): model fields near `+0x10` (male/female `u16` pair per examples),
  rest unverified.

**Caution (FINDINGS.md):** runtime addresses are unstable across equipment loads —
e.g. `0x08A35890` (current head model file_id), `0x0912F54C`. Only for live tools,
**not** for static cheat codes.

## 4. MHP3RD original ULJM05800
### Monster data table [orig]

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison | Sleep | Para | Dizzy | Rage |
|---|---|---|---|---|---|---|---|---|---|---|
| ULJM05800 | 0x15A9860 | +0x62 | +0x246 | +0x288 | +0xD4 | 0x23C/0x252 | 0x24E/0x24C | 0x25A/0x258 | 0xC5C/0xC5E | 0xBC8 |
| NPJB40001 | 0x19B0AE0 | +0x62 | +0x246 | +0x288 | +0xD4 | 0x23C/0x252 | 0x24E/0x24C | 0x25A/0x258 | 0xC5C/0xC5E | 0xBC8 |

> MHP3RD offsets cross-verified with **[hpbar]**:
> list pointer `0x09DA9860 == 0x08800000 + 0x15A9860` ✅, HP `+0x246`, MaxHP `+0x288` ✅


> (HD monster data: see §5 — HD list pointer `0x0A1B0AE0`, struct offsets identical)
### Quest / game state [hpbar][dmg]
| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| in-quest flag (near magic 0x656D6167) | 0x09C57CA0 | 0x0A05E620 | **[hpbar]**(orig) **[dmg]** |

> Types: in-quest = `u32` value at pointer (lw-read); phase/loading = `u8` (lb-read).
| returning/quest phase byte (<3 check) | 0x09BAC044 | N/A | **[hpbar]** |
| loading screen flag | 0x08AB49EC | N/A | **[hpbar]** |

### Player data [dmg][sharp]
| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| player_area | 0x08B24979 | 0x08B2B139 | **[dmg]** |
| equipped weapon struct | 0x09B49234 | 0x09F4FCE4 | **[sharp]** |
| sharpness (in weapon struct) | 0x09B49234+0x5CC | 0x09F4FCE4+0x5DC | **[sharp]** |
| sharpness table | 0x0897D728 | 0x08983060 | **[sharp]** |
| sprite info | 0x08B268DC | 0x08B2D09C | **[sharp]** |

> Types: sharpness `u16` @ weapon+offset; sharpness table `u16` array; weapon struct
> base known, full layout unverified; player_area layout unknown.

### Items [item]
| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| item box ITEM_BOX | 0x09B4C244 | 0x09F52CF4 | **[item]** |
| pouch 1 ITEM_POUCH1 | 0x09BA8D4A | 0x09FAF7FE | **[item]** |
| pouch 2 ITEM_POUCH2 | 0x09B4D9B4 | 0x09F54464 | **[item]** |
| give-item function GIVE_ITEM | 0x09CD0440 | 0x0A0D20A8 | **[item]** |
| button-hold check CONTROL_HOLD | 0x09BB7A64 | 0x09FBE764 | **[item]** |

> Item entries: **4 bytes LE** — hi `u16` = count, lo `u16` = item id
> (see §12 and mhfu-re itembox `+0x390` pair layout).

### Render / camera [dmg]
| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| ViewMatrix (world→screen) | 0x09B486B0 | 0x09F4F120 | **[dmg]** |
| main render hook / ret | 0x088E6D64 / 0x088EBAB8 | 0x088E881C / 0x088EE410 | **[dmg]** |
| printf (on-screen text) | 0x088EAA64 | 0x088EC51C | **[dmg]** |
| damage-number ADD hook / ret | 0x09C750FC / 0x09C953E0 | 0x0A07BA7C / 0x0A09BD60 | **[dmg]** |
| print settings PRINT_SETTINGS | 0x09ADB910 | 0x09EE2350 | **[dmg]** |
| damage check CHECK | 0x09C1EC70 | 0x0A025608 | **[dmg]** |
| sceGeListEnQueue | 0x08960CF8 | N/A | **[hpbar]** |
| HP-bar mod staging area | 0x08800FF0 | N/A | **[hpbar]** |

> Types: ViewMatrix = `f32[16]` (4×4); printf = code func; CHECK/ADD values `u32`;
> matrix element layout unverified.

### Player / guild card / box data [cwps]

| Field | Offset | Absolute | Note |
|---|---|---|---|
| Player HP (current/green) | 0x0D41556 | 0x09541556 | in-task u8/u16 |
| Player HP (recover/red) | 0x0D41596 | 0x09541596 | in-task |
| Player HP max | 0x0D41598 | 0x09541598 | in-task |
| Player action region | 0x0D4149C | 0x0954149C | used by button-codes |
| Move speed (f32) | 0x0D41FC4 | 0x09541FC4 | +0x40 per extra ×1 |
| Item box slot 1 | 0x134C244 | **0x09B4C244** | u32 id@+0 count@+2 — == [item] original ✓ |
| Item pouch ∞ region | 0x1CAFA2 | 0x089CAFA2 | codes "Item Infinity" |
| Item pouch 99 region | 0x13A8D4C | 0x09BA8D4C | codes "Item Infinity99" |
| Armor slots 3-hole region | 0x117E036 | 0x09A7E036 | code "Equip Slot 3" |
| Weapon slots 3-hole region | 0x118D602 | 0x09A8D602 | |
| Melee element region | 0x118D5FE | 0x09A8D5FE | fire/water/thunder/dragon/ice/status |
| Play time (s, u32) | 0x134DA94 | 0x09B4DA94 | guild card |
| Guild pts / affinity | 0x13A182C | 0x09BA182C | |
| Village hunts | 0x13A1820 | 0x09BA1820 | u16 |
| Guild-hall low/high | 0x13A1822 / 0x13A1824 | 0x09BA1822 / 0x09BA1824 | |
| Training arena / bath | 0x13A1826 / 0x13A1828 | 0x09BA1826 / 0x09BA1828 | |
| Weapon-use counts (GS) | 0x13A1C10 | 0x09BA1C10 | see list below |
| Medals | 0x13A1894 | 0x09BA1894 | bitfield |
| Card bg color | 0x13A188B | 0x09BA188B | 0–4 |
| Record clear region | 0x13A3A30 | 0x09BA3A30 | |

Weapon-use count offsets from table base 0x09BA1C10: GS +0, DB +2, Hammer +4, Lance +6,
HBG +8, LBG +0xC, LS +0xE, SA +0x10, Gunlance +0x12, Bow +0x14, SnS +0x16, HH +0x18.

### Felyne (cat) data [cwps]

| Field | Offset | Absolute | Note |
|---|---|---|---|
| Cat level | 0x13A648F | 0x09BA648F | u8, max 20 |
| Bond (亲密度) | 0x13A6493 | 0x09BA6493 | max 30 |
| Attack mode | 0x13A64E8 | 0x09BA64E8 | 0–5 |
| Attack tendency | 0x13A64E9 | 0x09BA64E9 | 0–4 |
| Personality | 0x13A64EA | 0x09BA64EA | 0–6 |
| Color | 0x13A64EB | 0x09BA64EB | 0–0x21 |
| Sortie count | 0x13A64E6 | 0x09BA64E6 | u16 |
| ATK / DEF | 0x13A6527 / 0x13A6528 | 0x09BA6527 / 0x09BA6528 | u8 |
| Skills (3 slots u32×2) | 0x13A64F4 | 0x09BA64F4 | 2×(u32) |
| Waterfall skill | 0x13A6525 | 0x09BA6525 | 0–5 |
| Owner name | 0x13A656C | 0x09BA656C | u16 |
| Cat 2 waterfall/owner | 0x13A65C5 / 0x13A660C | 0x09BA65C5 / 0x09BA660C | **stride +0xA0 (== [us] HD ✓)** |
| Drink (猫饭) skills | 0x13A5E40 | 0x09BA5E40 | 5×u16+u8 |

### Monster runtime fields [cwps]

| Offset | Field | Source code |
|---|---|---|
| +0x62 | monster type u8 (== [orig]/[transmog3] ✓) | "062 モンスター種類" |
| +0x18C | motion value u16 | motion codes |
| +0xBCA | rage flag u8 (00/01) | "BCA 怒り判定" |
| +0xBC2 | stamina/Hp-zero field | "怪物体力为0" |
| +0x258 | paralysis apply | "[L+R+X]怪麻痺" |

Body-part resistance base `0x08B24A10` (code semantics, unverified): +0xB32/+0xB34
part-resist=1 (1st/2nd monster at +0x4), +0x280 at `0x08B24A64` = corpse-despawn fast
(large continuous quests).

### Quest / maps [cwps]

| Field | Offset | Absolute | Note |
|---|---|---|---|
| Quest time left | 0x133C568 | 0x09B3C568 | u32, frames (30 = 1 s); e.g. 60min=0x1A5E0 |
| Zone/map teleport | 0x324978 | 0x08B24978 | packed (map/zone byte); near player_area |
| "No 5-min-clear" flag | 0x13AE1D8 | 0x09BAE1D8 | u32 |
| Death-no-reward-cut | 0x13AC054 | 0x09BAC054 | u32 zero |


## 5. MHP3RD HD NPJB40001
### Player / items (measured) [us]

| Field | Offset | Absolute | Size / layout |
|---|---|---|---|
| Quest-instance entry detect | `0x00015FA8` | `0x08815FA8` | task value `0x0185E610` written on entering a quest |
| Psychic (千里眼) cooldown | `0x00E4A496` | `0x0964A496` | u16 |
| Pouch slot 1 | `0x017AF7FE` | `0x09FAF7FE` | 4B: hi u16 = count, lo u16 = item id |
| Item box slot 1 | `0x01752CF4` | `0x09F52CF4` | 4B, same layout as pouch |
| Equipment box slot 1 | `0x0174FE14` | `0x09F4FE14` | 12B (layout unverified) |
| Hunter name | `0x0174FCAC` | `0x09F4FCAC` | 24B, **UTF-16LE**, first char = low u16 at base |
| Guild card text | `0x017A8280` | `0x09FA8280` | UTF-16LE text (3×u32 write sample) |
| Felyne slot 1 | `0x017ACF44` | `0x09FACF44` | first u32 = name (2×u16 LE); slot stride +0xA0 |
| Felyne slot 2 | `0x017ACFE4` | `0x09FACFE4` | same layout, +0xA0 from slot 1 |
| Money (candidate) | `0x17AC8D4` | `0x09FAC8D4` | u32, 9,999,999 = 0x0098967F — code `0x2…` = 32-bit write; awaits verification |

> Cross-verified: pouch/box absolute addresses == [item] HD table (`0x09FAF7FE` / `0x09F52CF4`) ✓
> CWCheat codes (`0x2174FCAC`) independently confirm hunter-name base (`0x09FAF7FE` / `0x09F52CF4`) ✓


### Data areas (verified via community codes) [cw]

> Monster struct offsets HD == P3 orig ([orig]): +0x246 HP, +0x288 maxHP, +0x23C/0x252 poison, +0x24E/0x24C sleep, +0x25A/0x258 para, +0xC5C/0xC5E dizzy, +0xBC2/+0xBC0 stamina, +0xD4 size (read directly by the HP-display cheat) — cross-verified ✓✓
> Felyne slots: name 0x09FACF44, stride +0xA0 (5 cats) ✓

| Field | Offset | Absolute | Note |
|---|---|---|---|
| Farm/Yukumo pts | 0x17AC8CC | 0x9fac8cc | u32, max 9999999 |
| Guild points | 0x17AC8D0 | 0x9fac8d0 | u32 (contiguous) |
| Money | 0x17AC8D4 | 0x9fac8d4 | u32 (9999999=0x98967F) — verified by code ✓ |
| Box slot 1 | 0x1752CF4 | 0x9f52cf4 | id@+0 (0xCE=bitterbug), count@+2 — == [us]/[item] ✓ |
| Box slot1 count | 0x1752CF6 | 0x9f52cf6 | u16 |
| Pouch slot 1 | 0x17AF800 | 0x9faf800 | = [us] pouch1 0x09FAF7FE count field (+2) ✓ |
| Pouch slot 2 | 0x17AF804 | 0x9faf804 | +4 |
| Mix list unlock | 0x1754548 | 0x9f54548 | u32 bitflags ×4+ |
| Monster list unlock | 0x1754588 | 0x9f54588 | u32 |
| All materials area | 0x1752CF8 | 0x9f52cf8 | box slot array start |
| Item rare-1 area | 0x01D0FB9 | 0x89d0fb9 | item-flag region (candidate) |
| Hunter sex | 0x174FCC7 | 0x9f4fcc7 | u8 (0 M / 1 F); name+0x1B |
| HP current | 0x0E49B16 | 0x9649b16 | in-task u8/u16 |
| HP recover | 0x0E49B56 | 0x9649b56 | in-task |
| HP max | 0x0E49B58 | 0x9649b58 | in-task, 150=0x96 |
| Poison invalid | 0x0E49B26 | 0x9649b26 | u8 |
| Stamina max | 0x0E4A49A | 0x964a49a | u16 900=0x384 |
| Sharpness | 0x0E4ACF4 | 0x964acf4 | 2×u16 (color) |
| LS gauge | 0x0E4AD18 | 0x964ad18 | u16 ×3 |
| SA gauge | 0x0E4AE0C | 0x964ae0c | u8 |
| Ammo no-reload | 0x0E4A4F0 | 0x964a4f0 | u8 |
| Ammo +9 | 0x0E4A4F1 | 0x964a4f1 | u8 |
| GL overheat | 0x0E4AD1C | 0x964ad1c | u16 |
| Game speed | 0x0E4A594 | 0x964a594 | f32 1.5/2/4 |
| Move speed | 0x0D41FC4 | 0x9541fc4 | f32 — same addr as P3 orig |
| Cat level | 0x17ACF3F | 0x9facf3f | u8 (20=0x14), 5 cats stride 0xA0 |
| Cat bond | 0x17ACF43 | 0x9facf43 | u8 (0x1A) |
| Cat skill pts | 0x17ACFB6 | 0x9facfb6 | u16 (250=0xFA) |
| Cat skills 1/2 | 0x17ACF9C | 0x9facf9c | u32 bitfields |
| Cat comment | 0x17ACF5E | 0x9facf5e | 0x1A bytes |
| Cat master name | 0x17ACF7C | 0x9facf7c | u16 |
| Monster list ptr | 0x19B0AE0 | 0xa1b0ae0 | **0x0A1B0AE0** (corrected) ✓ |
| Quest time | 0x17B4E68 | 0x9fb4e68 | ±0x4650 u16/u32; NOTE: differs from delta-prediction |
| Clear quest | 0x17B2CD4 | 0x9fb2cd4 | 0x03 |
| Quest phase block | 0x13AC044 | 0x9bac044 | == [hpbar] 0x09BAC044 ✓ |
| Reward infinite | 0x13AC054 | 0x9bac054 | == P3 orig same addr |
| HUD toggle | 0x17F5C96 | 0x9ff5c96 | u8 |
| Change-equip toggle | 0x1349364 | 0x9b49364 |  |
| Yukumo farm unlock | 0x17ADF4C | 0x9fadf4c |  |
| Hotsprings upgrade | 0x17AEF1D | 0x9faef1d |  |
| Armor 3-slot region | 0x018396E | 0x898396e | repeats |
| Weapon 3-slot region | 0x0192F3A | 0x8992f3a |  |
| Bow attr region | 0x018EB74 | 0x898eb74 |  |
| Equip skill +32 | 0x017D9BF | 0x897d9bf |  |

### P3HD monster struct field map (from the HP-display main code, struct-relative)

| Offset | Field | Read | Note |
|---|---|---|---|
| +0x62 | type (monster id) | lb | == [orig] ✓ |
| +0x246 | current HP | lhu | == [orig] ✓ |
| +0x288 | max HP | lhu | == [orig] ✓ |
| +0xD4 | size (f32) | qe | == [orig] ✓ |
| +0xBC0 / +0xBC2 | stamina / fatigue | lhu/lhu | new |
| +0x23C / +0x252 | poison cur/max | lhu | == [orig] ✓ |
| +0x24E / +0x24C | sleep cur/max | lhu | ✓ |
| +0x25A / +0x258 | para cur/max | lhu | ✓ |
| +0xC5C / +0xC5E | dizzy cur/max | lhu | ✓ |
| +0xB32..+0xB6C | part-resistance pairs (8 zones) | lhu/lhu | +0xB32/B34, B3A/B3C, B42/B44, B4A/B4C, B52/B54, B5A/B5C, B62/B64, B6A/B6C |

Drawn via a staging area at `0x08801FF0`–`0x08801FFC` (6 u32) and a HUD
text/color region at `0x08800FFA`; the display code itself lives at `0x08801000`.

### DMG numbers / target camera (code areas, cross-checks)

| Item | Absolute | Note |
|---|---|---|
| DMG hook (write at 0x200E881C) | **0x088E881C** | == [dmg] HD MAIN_HOOK ✓ |
| DMG code cave (0x227F0000) | **0x0AFF0000** | == [dmg] HD LOAD_ADD ✓ |
| Monster list used by DMG code | **0x0A1B0AE0** | third confirmation ✓ |
| Target-camera code cave (0x22900000) | 0x0B100000 | extended RAM; reads monster list |
| Drink skill slot 9/10 | 0x0964A4EE / 0x09F32EDE (task/non-task) | +1 = slot 10 |

### Address extrapolation (delta method)

**Method**: HD uses a relocated EBOOT; addresses shift per region. Using verified
orig↔HD pairs, each HD address is predicted as `HD = orig + delta(region)`.

### Verified anchor deltas (orig → HD)

| Region | delta |
|---|---|
| data region (box/pouch/weapon/viewmatrix/task/check…) | **+0x406AB0** (spread 0x403C68–0x406D00) |
| monster list | +0x407280 |
| player runtime (0x08B2xxxx) | +0x67C0 |
| code 0x088Exxxx | +0x1AB8 |
| sharpness/data 0x0897xxxx | +0x5938 |

### Predicted HD addresses (nearest verified anchor)

| Field (P3 orig) | Orig addr | Predicted HD | Anchor |
|---|---|---|---|
| Item box slot 1 | 0x09B4C244 | **0x09F52CF4** (verified) | item box |
| 99-pouch region | 0x09BA8D4C | 0x09FAF800 (≈ measured 0x09FAF7FE ✓) | pouch1 |
| Play time | 0x09B4DA94 | 0x09F54544 | pouch2 |
| Village hunts | 0x09BA1820 | 0x09FA82D4 | pouch1 |
| Guild-hall high | 0x09BA1824 | 0x09FA82D8 | pouch1 |
| Weapon-use GS | 0x09BA1C10 | 0x09FA86C4 | pouch1 |
| Medals | 0x09BA1894 | 0x09FA8348 | pouch1 |
| Card bg color | 0x09BA188B | 0x09FA833F | pouch1 |
| Record clear | 0x09BA3A30 | 0x09FAA4E4 | pouch1 |
| Cat level | 0x09BA648F | 0x09FACF43 (≈ measured slot 0x09FACF44 ✓) | pouch1 |
| Cat skills | 0x09BA64F4 | 0x09FACFA8 | pouch1 |
| Cat owner name | 0x09BA656C | 0x09FAD020 | pouch1 |
| Cat drink skills | 0x09BA5E40 | 0x09FAC8F4 | pouch1 |
| Quest time | 0x09B3C568 | 0x09F42FD8 | view matrix |
| No-5min-flag | 0x09BAE1D8 | 0x09FB4C8C | pouch1 |
| Death-no-cut | 0x09BAC054 | 0x09FB2B08 | pouch1 |
| Part-resist base | 0x08B24A10 | 0x08B2B1D0 (low) | player_area |
| Zone teleport | 0x08B24978 | 0x08B2B138 (low) | player_area |

**Self-validation**: cat-level prediction 0x09FACF43 ≈ measured HD felyne slot
0x09FACF44 (off by 1 B); 99-pouch prediction ≈ measured pouch1 ±2 B. Both confirm
the +0x406ABx cluster.

> ⚠️ Predictions are for **prioritized testing only** — verify per digging-guide,
> then promote to a verified row. HD splits regions differently (loads of NPJB40001
> differ); outside these clusters the delta method is unreliable.


## 6. Mod loader pipeline (P3 original / HD) [load][modman]
| Name | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| EBOOT_LOAD | 0x0880134C | 0x0880134C | **[load]** |
| PRELOAD_HOOK | 0x088215D4 | 0x08821818 | **[load]** |
| PRELOAD_INIT | 0x089E02A0 | 0x089DFE60 | **[load]** |
| file read hook | 0x0886242C | 0x0886365C | **[load]** |
| file seek hook | 0x08864390 | 0x088655C0 | **[load]** |
| crypto hook | 0x088641F0 | 0x08865420 | **[load]** |
| mod table workspace | 0x09FA2100 (+0x800 table) | 0x083B5600 | **[load]** |
| sceIo import table | 0x08960A00+ | 0x08965690+ | **[load]** **[item]** |
| replacement files dir | ms0:/P3RDML/FILES/ | ms0:/P3RDHDML/FILES/ | **[load]** **[modman]** |
| animation data region | 0x099C0000 | — | **[modman]** |

## 7. Cross-validation log
- `0x09DA9860` (hpbar monster list) == `0x08800000 + 0x15A9860` (orig table) ✅
- Monster HP `+0x246`, MaxHP `+0x288` (hpbar) == orig MHP3RD table ✅
- sceIo import table (load/item agree) ✅
- [cwps] monster list ptr `0x09DA9860` == [orig]+[hpbar] (third source) ✅
- [cwps] monster type offset `+0x62` == [orig]/[transmog3] ✅
- [cwps] item box `0x09B4C244` == [item] original ✅
- [cwps] Felyne slot stride +0xA0 == [us] P3HD ✅
- HP-display main code reads HD monster offsets == [orig] full map ✓✓
- DMG hook 0x088E881C == [dmg] HD MAIN_HOOK; cave 0x0AFF0000 == [dmg] LOAD_ADD ✓
- Monster list 0x0A1B0AE0 third confirmation (DMG/target-cam codes) ✓

## 8. TODO
- Monster x/y/z coordinates (start near `+0xD4` / use ViewMatrix anchors)
- Quest info region (name/time/rewards)
- Player stats (attack/defense/skills)
- Hitzone/fatigue tables (file-id route + `MHP3rd-Game-FIle-List`)
- **MHF / MHF2 / MHFU player/item/render regions** (only monster tables exist so far)
- Second-source confirmation of HD monster struct offsets