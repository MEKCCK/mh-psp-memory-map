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
| **[cheats]** | 9r3i/[mhp3-cheats](https://github.com/9r3i/mhp3-cheats) | MHP3/MHFU cheat codes, talisman skill table |
| **[mhef]** | svanheulen/[mhef](https://github.com/svanheulen/mhef) `mhef/psp.py` | PSP MH save crypto |
| **[val]** | This project (MEKCCK in-emulator integration) — cross-validation only, no new addresses |

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

## 4. P3-only extension: items

| Field | ULJM05800 | NPJB40001 | Source |
|---|---|---|---|
| item box ITEM_BOX | 0x09B4C244 | 0x09F52CF4 | **[item]** |
| pouch 1 ITEM_POUCH1 | 0x09BA8D4A | 0x09FAF7FE | **[item]** |
| pouch 2 ITEM_POUCH2 | 0x09B4D9B4 | 0x09F54464 | **[item]** |
| give-item function GIVE_ITEM | 0x09CD0440 | 0x0A0D20A8 | **[item]** |
| button-hold check CONTROL_HOLD | 0x09BB7A64 | 0x09FBE764 | **[item]** |

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

## 9. MHFU static data tables [transmog] (source: FINDINGS.md of mhfu_transmog)

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

**Caution (FINDINGS.md):** runtime addresses are unstable across equipment loads —
e.g. `0x08A35890` (current head model file_id), `0x0912F54C`. Only for live tools,
**not** for static cheat codes.

## 10. MHP3RD static data tables [transmog3] (ULJM05800, from build_data.py)

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

### Related code addresses (model-load pipeline)

| Address | Meaning |
|---|---|
| `0x088691FC` | equipment model-load function (hook target) |
| `0x0886927C` | wrapper for 80-byte weapon types + armor (`lhu v0,0(v0)`) |
| `0x088692A0` | wrapper for 28-byte weapon types |
| `0x08868538`–`0x08868710` | model-load code region |
| `0x08966598` | slot-mapping jump table |
| `0x08966184` | jump-table handler index by type byte |


## 11. [re] MHFU runtime structures (SiD3W4y/mhfu-re `doc/objects.txt`)

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

## 12. [re] MHFU key functions

| Address | Function |
|---|---|
| `0x0884EA1C` | `decrypt_data(global_state, data, size)` |
| `0x09A6B130` | damage computation (applies all modifiers to entity `+0x3B8`) — **damage-numbers hook candidate** |
| `0x09AC8AF0` | indirect monster-state changer (vtable+0x88) |
| `0x088D72A4` | `give_item(player_state, u16 object_id, s16 count)` |

## 13. [re] OVL format & runtime sections (`runtime.txt` / `ovl.txt`)

- Overlays live in `psp_game/usrdir/data.bin` (encrypted archive; unpack with
  svanheulen/mhff or mhef).
- `struct OVLFile`: magic `"MWo3"`, u32 load_base (== section vaddr), load_end,
  name[0x60], then data; **the header stays in memory while mapped**.
- Runtime section list shows e.g. demo/edit/movie/install/arcade tasks loaded at
  `0x09A5F300+` (see doc/runtime.txt for the full list).

## 14. [cheats] MHP3 talisman skill table (excerpt) + CWCheat conversion

`mhp3.talisman.txt` (175 lines): 2-skill-combo id → skills, e.g.
`0001 = Torso Up`, `0101 = Torso Up / Poison`, `0202 = Poison / Sleep`,
`0404 = Sleep / Health`, `0F0F = Guard / Bomb Boost`, `1010 = Guard Up / Gluttony`, …
Full table in the repo (`9r3i/mhp3-cheats`).

CWCheat → absolute conversion example (codes in `cheats.md`):
Attack multiplier `_L 0x200AD964 …` → absolute **`0x088AD964`** (u32 code write,
`+0x08800000`). Use the same rule for any code in the DB.

## 15. [mhef] PSP save crypto constants (`mhef/psp.py`)

- MHP2G JP salt `b'S)R?Bf8xW3#5h9lGU8wR'`, NA `b'3Nc94Hq1zOLh8d62Sb69'`,
  MHP3 JP `b'VQ(DOdIO9?X3!2GmW#XF'`; SHA-1 over data+salt.
- Per-version keys: MHP `b'>\r\xb2\xef…'`, MHP2 `b'\xe3\xb5\xce…'`,
  MHP2G `b'\xcd\x1f Y…'`, MHP3 `b"\xe3\x05\xce…"` (see file for full bytes).
- Encryption key defaults `(0x2345, 0x7f8d)`; MHP2G/MHP3 exception byte lists.
- Use for save-file data mining & validation (not runtime memory).

## 16. Additional data sources (cross-ref, no runtime addrs)

- Saramagrean/CWCheat-Database-Plus- — Thai CWCheat DB incl. tested P3 name-edit
  (PR #62); offset→absolute rule applies.
- `mhfu.monweak.txt` (monster weaknesses), `mhfu.armor.skill.txt` (armor skills),
  `cheats.md` attack codes — data references for verifying table semantics.
- tclamb/mhp2g-decomp: WIP, nothing public yet (see TODO).

## 7. Cross-validation log (all of this project's contribution)

- `0x09DA9860` (hpbar monster list) == `0x08800000 + 0x15A9860` (orig table) ✅
- Monster HP `+0x246`, MaxHP `+0x288` (hpbar) == orig MHP3RD table ✅
- sceIo import table (load/item agree) ✅

## 8. TODO

- Monster x/y/z coordinates (start near `+0xD4` / use ViewMatrix anchors)
- Quest info region (name/time/rewards)
- Player stats (attack/defense/skills)
- Hitzone/fatigue tables (file-id route + `MHP3rd-Game-FIle-List`)
- **MHF / MHF2 / MHFU player/item/render regions** (only monster tables exist so far)
- Second-source confirmation of HD monster struct offsets