# Monster Hunter PSP Series — Memory Map (MHF · MHF2 · MHFU · MHP3RD)

All addresses are **PSP virtual addresses** (identical on real hardware and PPSSPP).

## Source Tags

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
| **[cwps]** | community CWCheat collection (user archived) | P3 original (ULJM05800) verified values |
| **[cw]** | community CWCheat (user archived) | MHF1 (ULJM05066) values |

> **Attribution:** every piece of memory knowledge in this table comes from the two
> original authors' **public projects** listed above. The PPSSPP in-emulator overlay
> only *ported, integrated and cross-checked* already-public data. No address here is
> an original discovery of this project.

## 0. Address nature & offset rules (IMPORTANT)
- All entries are **PSP virtual addresses**. The PSP memory map is fixed by hardware
  (user RAM base `0x08800000`), so **real hardware and PPSSPP are identical** —
  mhp3reload's MIPS patches use these same addresses on a real PSP.
- The "base offset problem" only exists for **external tools reading the emulator
  process memory**: PPSSPP maps PSP RAM at an arbitrary host address, so tools
  (the original Python overlay, Cheat Engine, …) must add a `base_address`
  (the original overlay obtains it via `SendMessageW(0xB118)`).
- **This in-emulator overlay reads at PSP virtual addresses directly**
  (`Memory::ReadUnchecked_*`); PPSSPP does the mapping internally — **no offset**.
- Version differences to watch:
  - Original `ULJM05800` vs HD `NPJB40001`: different EBOOT layout → different addresses
    (tables below give both columns; "N/A" = source mod only implements that version).
  - Same game + same version: same addresses on hardware and PPSSPP.

### Two addressing conventions (IMPORTANT)

Real-device cheat tools/databases often record addresses as **"RAM offsets"**
(relative to `0x08800000`), e.g. "item box slot 1 = 0x0134C244". When writing into
the emulator you **must add the base**:

```
absolute = offset + 0x08800000
0x09B4C244 = 0x0134C244 + 0x08800000     (ITEM_BOX, cross-verified ✓)
```

| Use | Address form |
|---|---|
| In-emulator code / PPSSPP memory editor | absolute `0x09xxxxxx` |
| Real-device cheat DB "offset" | absolute = offset + `0x08800000` |
| External tools reading the process | also add host base (`Memory::base`) |
| Real-device "immediate write" | uncached mirror `0x4xxxxxxx` (optional) |

### PSP virtual address space (why 0x08800000)

```
0x00000000 – 0x03FFFFFF   reserved / user area (games don't use it)
0x04000000 – 0x041FFFFF  VRAM (GE, 512KB)
0x08000000 – 0x087FFFFF   RAM region head (reserved/aliased, unusable)
0x08800000 – 0x09FFFFFF  ★ main RAM (user RAM)
                          ├ 0x08804000-ish: game ELF/EBOOT entry
                          ├ 0x0880xxxx–0x0886xxxx: game code (hooks live here)
                          ├ 0x0896xxxx: sceIo import/jump table
                          ├ 0x089Exxxx: preload data segment
                          ├ 0x09B4xxxx / 0x09BAxxxx: items / box / pouches
                          ├ 0x09DA9860: MHP3RD monster list pointer
                          └ 0x09F00000+: Slim/64MB extended RAM (loader workspace 0x09FA2100)
0x40000000 + addr          uncached mirror of main RAM
0x48000000 + addr          uncached mirror (0x08800000-based)
0x88000000 + addr          kernel-access mirror (user mode unusable)
```

Key points: the base is fixed by hardware (PPSSPP MemMap.cpp comment:
"only from 0x08800000 is it usable"); game pointers ARE absolute addresses;
real-device cheat tools may use `0x4xxxxxxx` uncached writes; the emulator-internal
mapping (host `Memory::base`) is only needed by external readers.

## 1. Monster data (ALL games)
> Tables below come from **[orig]** (Python overlay project).
> Usage: list pointer = `0x08800000 + initial` → monster list `pointer[i]` (4 bytes),
> non-zero = alive monster → monster struct; offsets are relative to the struct.
> Name byte → monster tables in `UI/monster_tables.inc` of the overlay (per game).
>
> **Types**: name `u8`; HP/MaxHP `u32`; size `u16`; status pairs `u16` (cur/max);
> rage `u16` (frames, /60 = seconds); list pointer `u32`; struct pointer chain (ptr → struct).

### 1.1 MHF / MHP (ULES00318 / ULUS10084 / ULJM05066)

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison cur/max | Sleep cur/max | Para cur/max | Dizzy cur/max | Rage(u16) |
|---|---|---|---|---|---|---|---|---|---|---|
| ULES00318 | 0x1254D70 | +0x210 | +0x312 | +0x43C | +0x2A4 | 0x3A8/0x46C | 0x462/0x460 | 0x474/0x472 | — | 0x580 |
| ULUS10084 | 0x1253F70 | +0x210 | +0x312 | +0x43C | +0x2A4 | 0x3A8/0x46C | 0x462/0x460 | 0x474/0x472 | — | 0x580 |
| ULJM05066 | 0x1253570 | +0x210 | +0x312 | +0x43C | +0x2A4 | 0x3A8/0x46C | 0x462/0x460 | 0x474/0x472 | — | 0x580 |

### 1.2 MHF2 / MHP2 (ULES00851 / ULUS10266 / ULJM05156)

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison | Sleep | Para | Dizzy | Rage |
|---|---|---|---|---|---|---|---|---|---|---|
| ULES00851 | 0x127AD70 | +0x1E8 | +0x2E2 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x55E | 0x564 |
| ULUS10266 | 0x12799F0 | +0x1E8 | +0x2E2 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x55E | 0x564 |
| ULJM05156 | 0x1278E70 | +0x1E8 | +0x2E2 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x55E | 0x564 |

### 1.3 MHFU / MHP2G (ULES01213 / ULUS10391 / ULJM05500)

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison | Sleep | Para | Dizzy | Rage |
|---|---|---|---|---|---|---|---|---|---|---|
| ULES01213 | 0x1412140 | +0x1E8 | +0x2E4 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x566 | 0x56C |
| ULUS10391 | 0x1412240 | +0x1E8 | +0x2E4 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x566 | 0x56C |
| ULJM05500 | 0x140D3C0 | +0x1E8 | +0x2E4 | +0x41E | +0x274 | 0x388/0x450 | 0x446/0x444 | 0x458/0x456 | 0x440/0x566 | 0x56C |

### 1.4 MHP3RD / HD (ULJM05800 / NPJB40001)

| Disc ID | List ptr (initial) | Name | HP | MaxHP | Size | Poison | Sleep | Para | Dizzy | Rage |
|---|---|---|---|---|---|---|---|---|---|---|
| ULJM05800 | 0x15A9860 | +0x62 | +0x246 | +0x288 | +0xD4 | 0x23C/0x252 | 0x24E/0x24C | 0x25A/0x258 | 0xC5C/0xC5E | 0xBC8 |
| NPJB40001 | 0x19B0AE0 | +0x62 | +0x246 | +0x288 | +0xD4 | 0x23C/0x252 | 0x24E/0x24C | 0x25A/0x258 | 0xC5C/0xC5E | 0xBC8 |

> MHP3RD offsets cross-verified with **[hpbar]**:
> list pointer `0x09DA9860 == 0x08800000 + 0x15A9860` ✅, HP `+0x246`, MaxHP `+0x288` ✅

## 2. P3-only extension: quest / game state (only P3 has public data; other versions TODO)
| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| in-quest flag (near magic 0x656D6167) | 0x09C57CA0 | 0x0A05E620 | **[hpbar]**(orig) **[dmg]** |

> Types: in-quest = `u32` value at pointer (lw-read); phase/loading = `u8` (lb-read).
| returning/quest phase byte (<3 check) | 0x09BAC044 | N/A | **[hpbar]** |
| loading screen flag | 0x08AB49EC | N/A | **[hpbar]** |

## 3. P3-only extension: player data
| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| player_area | 0x08B24979 | 0x08B2B139 | **[dmg]** |
| equipped weapon struct | 0x09B49234 | 0x09F4FCE4 | **[sharp]** |
| sharpness (in weapon struct) | 0x09B49234+0x5CC | 0x09F4FCE4+0x5DC | **[sharp]** |
| sharpness table | 0x0897D728 | 0x08983060 | **[sharp]** |
| sprite info | 0x08B268DC | 0x08B2D09C | **[sharp]** |

> Types: sharpness `u16` @ weapon+offset; sharpness table `u16` array; weapon struct
> base known, full layout unverified; player_area layout unknown.

## 4. P3-only extension: items
| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| item box ITEM_BOX | 0x09B4C244 | 0x09F52CF4 | **[item]** |
| pouch 1 ITEM_POUCH1 | 0x09BA8D4A | 0x09FAF7FE | **[item]** |
| pouch 2 ITEM_POUCH2 | 0x09B4D9B4 | 0x09F54464 | **[item]** |
| give-item function GIVE_ITEM | 0x09CD0440 | 0x0A0D20A8 | **[item]** |
| button-hold check CONTROL_HOLD | 0x09BB7A64 | 0x09FBE764 | **[item]** |

> Item entries: **4 bytes LE** — hi `u16` = count, lo `u16` = item id
> (see §12 and mhfu-re itembox `+0x390` pair layout).

## 5. P3-only extension: render / camera (damage numbers, minimap, free cam)
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

## 6. P3/HD-only: filesystem / mod loader pipeline
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

## 7. MHFU static data tables [transmog] (source: FINDINGS.md of mhfu_transmog)
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

## 8. MHP3RD static data tables [transmog3] (ULJM05800, from build_data.py)
### Armor slot tables

| Slot | Base | Entries | Player offset |
|---|---|---|---|
| chest | `0x08980144` | 233 | +0x1C |
| arms | `0x0897DFFC` | 213 | +0x26 |
| waist | `0x08984DAC` | 214 | +0x30 |
| legs | `0x08986F1C` | 220 | +0x3A |
| head | `0x089825AC` | 256 | +0x44 |

### Weapon tables

| type | Weapon | Base | Entry size |
|---|---|---|---|
| 5 | Great Sword | `0x08992168` | 28 |
| 6 | Sword & Shield | `0x0898FA78` | 28 |
| 7 | Hammer | `0x0898E71C` | 28 |
| 8 | Lance | `0x08990D64` | 28 |
| 9 | Heavy Bowgun | `0x0898AB2C` | 80 |
| 11 | Light Bowgun | `0x0898C01C` | 80 |
| 12 | Long Sword | `0x08991800` | 28 |
| 13 | Switch Axe | `0x0898D5D4` | 28 |
| 14 | Gunlance | `0x089904DC` | 28 |
| 15 | Bow | `0x089891DC` | 80 |
| 16 | Dual Blades | `0x0898F164` | 28 |
| 17 | Hunting Horn | `0x0898DDB4` | 28 |

> Entry sizes known; field layout not public yet (transmog hooks the model-load
> wrappers to edit model ids, so it does not document local table fields).

### Related code addresses (model-load pipeline)

| Address | Meaning |
|---|---|
| `0x088691FC` | equipment model-load function (hook target) |
| `0x0886927C` | wrapper for 80-byte weapon types + armor (`lhu v0,0(v0)`) |
| `0x088692A0` | wrapper for 28-byte weapon types |
| `0x08868538`–`0x08868710` | model-load code region |
| `0x08966598` | slot-mapping jump table |
| `0x08966184` | jump-table handler index by type byte |


## 9. [re] MHFU runtime structures (SiD3W4y/mhfu-re `doc/objects.txt`)
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

## 10. [re] MHFU key functions
| Address | Function |
|---|---|
| `0x0884EA1C` | `decrypt_data(global_state, data, size)` |
| `0x09A6B130` | damage computation (applies all modifiers to entity `+0x3B8`) — **damage-numbers hook candidate** |
| `0x09AC8AF0` | indirect monster-state changer (vtable+0x88) |
| `0x088D72A4` | `give_item(player_state, u16 object_id, s16 count)` |

## 11. [fdx] MHF1 (FreedomDX) patches — first concrete MHF1 addresses
| Feature / anchor | Address | Version |
|---|---|---|
| EventLoader slot (EN) | `0x095079E0` | MHF1 USA (ULES00318 family) |
| EventLoader slot (JP) | `0x094F31E0` | MHF1 JP (ULJM05066) |

- FreedomDX ships one MIPS patch per QoL feature (Event Quest Loader, Input Drop Fix,
  Instant supply box, Hold-to-Gather, …) — `build.py` assembles all of them;
  those addresses were previously blank for MHF1 in this table.
- File replacement uses `FDXDAT/NATIVEPSP/<DISC>/<fileid>` — same convention as #17.

## 12. P3HD (NPJB40001) player & items [us]  (offset style: absolute = offset + 0x08800000)

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

> Cross-verified: pouch/box absolute addresses == [item] HD table (`0x09FAF7FE` / `0x09F52CF4`) ✓
> CWCheat codes (`0x2174FCAC`) independently confirm hunter-name base (`0x09FAF7FE` / `0x09F52CF4`) ✓


## 13. [cwps] P3 original (ULJM05800): player / box / guild card

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

## 14. [cwps] P3 original: Felyne (cat) data — slot 1 base 0x09BA648F region

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

## 15. [cwps] P3 original: monster runtime fields (base list ptr 0x09DA9860)

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

## 16. [cwps] P3 original: quest / maps

| Field | Offset | Absolute | Note |
|---|---|---|---|
| Quest time left | 0x133C568 | 0x09B3C568 | u32, frames (30 = 1 s); e.g. 60min=0x1A5E0 |
| Zone/map teleport | 0x324978 | 0x08B24978 | packed (map/zone byte); near player_area |
| "No 5-min-clear" flag | 0x13AE1D8 | 0x09BAE1D8 | u32 |
| Death-no-reward-cut | 0x13AC054 | 0x09BAC054 | u32 zero |


## 17. P3HD (NPJB40001) address extrapolation (delta method, from [us]/[item]/[sharp]/[dmg] anchors)

**Method**: HD uses a relocated EBOOT; addresses shift per region. Using verified
orig↔HD pairs, each HD address is predicted as `HD = orig + delta(region)`.

### Verified anchor deltas (orig → HD)

| Region | delta |
|---|---|
| data region (box/pouch/weapon/viewmatrix/task/check…) | **+0x406AB0** (spread 0x403C68–0x406D00) |
| monster list | +0x507280 |
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


## 18. [cw] MHF1 (ULJM05066): player / guild card text

Character-set note: MHF1 name/guild-card text is **UTF-16BE** (2 bytes/char,
high byte first) — different from P3/P3HD (LE). 8 chars max for names; past that
the fields bleed into other data (unstable later in game).

| Field | Offset | Absolute | Size / layout |
|---|---|---|---|
| Character name | 0x10532C4 | 0x098532C4 | 8 chars = 16 B, UTF-16BE, 2 chars per u32 (hi first) |
| Guild-card name | 0x1057620 | 0x09857620 | 8 chars = 16 B, same encoding |
| Guild-card self intro | 0x105764C | 0x0985764C | 96 chars = 192 B; every 2 chars = +4 B (continues line-by-line) |
| Title cursor lock | 0x113A7C0 | 0x0993A7C0 | u8: fixed title-cursor slot |


## 19. Cross-validation log (all of this project's contribution)
- `0x09DA9860` (hpbar monster list) == `0x08800000 + 0x15A9860` (orig table) ✅
- Monster HP `+0x246`, MaxHP `+0x288` (hpbar) == orig MHP3RD table ✅
- sceIo import table (load/item agree) ✅
- [cwps] monster list ptr `0x09DA9860` == [orig]+[hpbar] (third source) ✅
- [cwps] monster type offset `+0x62` == [orig]/[transmog3] ✅
- [cwps] item box `0x09B4C244` == [item] original ✅
- [cwps] Felyne slot stride +0xA0 == [us] P3HD ✅

## 20. TODO
- Monster x/y/z coordinates (start near `+0xD4` / use ViewMatrix anchors)
- Quest info region (name/time/rewards)
- Player stats (attack/defense/skills)
- Hitzone/fatigue tables (file-id route + `MHP3rd-Game-FIle-List`)
- **MHF / MHF2 / MHFU player/item/render regions** (only monster tables exist so far)
- Second-source confirmation of HD monster struct offsets