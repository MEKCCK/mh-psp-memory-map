# Resources — Monster Hunter PSP Research Projects Index

Curated index of public projects related to Monster Hunter PSP memory research,
reverse engineering, cheats, mods and save tooling. Use it to cross-verify
addresses, find structures and dig new ones. **P0** = directly fills a gap in
[memory-map.md](memory-map.md).

> Want to add/report a resource or harvest addresses from one of these?
> See [contributing.md](contributing.md) and [digging-guide.md](digging-guide.md).

## 1. Memory addresses / structures (highest value, 6)

1. **James-028/mhfu_transmog** (incl. Exceen fork) — https://github.com/James-028/mhfu_transmog
   - `FINDINGS.md` documents MHFU static-data RE results
   - Weapon table `0x089574E8` (24 bytes/entry, ~1149 entries)
   - Armor pointer table `0x08975970` + 5 slot tables (HEAD/CHEST/ARMS/WAIST/LEGS, 40 bytes/entry)
   - Model lookup `0x0893E7F0`, craft/upgrade `0x08938D1A`, weapon index `0x089A1878`
   - FUComplete TABLE_A/B/E lookups
   - Full entry-struct parsing + CWCheat format + PPSSPP caveats
2. **Exceen/mhp3rd_transmog** — https://github.com/Exceen/mhp3rd_transmog
   - MHP3RD transmog; `transmog.py` (667 lines) + `build_data.py` (493 lines)
   - MHP3RD weapon/armor data table addresses & structures — fills the MHP3RD static-data gap **P0**
3. **SiD3W4y/mhfu-re** — https://github.com/SiD3W4y/mhfu-re
   - MHFU RE project; `doc/objects.txt` (structs), `doc/functions.txt` (function addrs),
     `doc/runtime.txt`, `doc/ovl.txt`; `tools/ppsspp-py/` (WebSocket debug lib:
     memory.read_u32/search/base); `tools/binja-mhfu/` + `tools/ghidra_scripts/`
     (NID resolution); `tools/comete/` (trace viz, Go); scripts for entity/player/
     monster vtable tracing **P0**
4. **9r3i/mhp3-cheats** — https://github.com/9r3i/mhp3-cheats
   - Large CWCheat collection for MHP3 + MHFU (addresses recoverable)
   - MHP3: `NPJB40001.this.ini` (27KB, HD), `cheats.md` (all-weapon affinity etc.),
     `mhp3.js` (talisman generator w/ structure, 23KB), `mhp3.talisman.txt`, `name.txt`
   - MHFU (`mhfu/`): `ULUS10391.ini`/`.origin.ini`/`-ini.ini`, categorized files,
     `mhfu.monweak.txt` (weaknesses), `mhfu.key.quest.txt`, `mhfu.armor.skill.txt`
5. **RetroHero/MHPSP-Cheats** — https://github.com/RetroHero/MHPSP-Cheats
   - Cheat Engine `.CT` tables, e.g. *PPSSPP v1.16.6 – MHFU COMPLETE v1.4.0.CT*
   - AoB-scan Lua scripts for locating PSP memory + categorized CE entries
6. **tclamb/mhp2g-decomp** — https://github.com/tclamb/mhp2g-decomp
   - MHP2G/MHFU decompilation (WIP) — function names, structs, data-seg addresses **P0**

## 2. Cheat databases (high value, 4)

7. **Saramagrean/CWCheat-Database-Plus-** — https://github.com/Saramagrean/CWCheat-Database-Plus-
   - Large CWCheat DB incl. tested MHP3RD (ULJM05800) name-editing codes (PR #62, 118 lines)
8. **gamehacking.org** (not GitHub) — https://gamehacking.org
   - CWCheat DB covering MHF (US v1.01), MHF2, MHFU, … (author/format/`.db` downloads)
9. **etherealgames.com** (not GitHub) — https://etherealgames.com
   - CWCheat pages per version: money/farm pts/guild pts/items/HP addresses
10. **wikiwiki.jp/cwcheat/** (Japanese wiki, not GitHub) — https://wikiwiki.jp/cwcheat/
    - MHP2G page: weapon-type address tables etc.

## 3. Mods / patches (mid-high value, hooks, 7)

11. **FUComplete/Patch** — https://github.com/FUComplete/Patch
    - FUComplete patch v1.4.0 (MHP2G enhancement mod) — many MIPS patches/hooks → function & struct addresses
12. **FUComplete/FUCTool** — https://github.com/FUComplete/FUCTool
    - Manager tool (Python, GPL-3.0): Patcher/Configuration/File Replacer/Custom Quests; file replacement (`nativePSP` folder) knowledge
13. **SilverJolteon/FreedomDX** — https://github.com/SilverJolteon/FreedomDX
    - MHF1 (MHP1 JPN / MHF1 USA & EUR) all-in-one patch — Event Quest Loader, Input Drop Fix,
      Instant supply box, Hold-to-Gather etc., each a MIPS patch → MHF1 function addresses **P0**
14. **Immortalcripple/Monster-Hunter-Freedom-DX** — https://github.com/Immortalcripple/Monster-Hunter-Freedom-DX
    - Another MHF DX project; `build.py` ISO patch logic (overlaps/complements #13)
15. **Kethen/mhp3_patcher** (orig. codestation/mhp3) — https://github.com/Kethen/mhp3_patcher
    - MHP3RD English-patch loader (PSP homebrew, works in PPSSPP + CFW); loader hook addresses + file replacement
16. **darkpigyummy/GePatch_mh** — https://github.com/darkpigyummy/GePatch_mh
    - MHP3RD HD-fication plugin (Adrenaline/PSVita), GePatch-based → render hooks
17. **nuzcraft/FreedomUniteColorRandomizer** — https://github.com/nuzcraft/FreedomUniteColorRandomizer
    - MHFU monster color randomizer — monster texture data structs

## 4. Saves / crypto / data tools (mid value, 5)

18. **svanheulen/mhef** — https://github.com/svanheulen/mhef
    - MH encryption lib; `mhef/psp.py` has save enc/dec for MHP/MHP2/MHP2G/MHP3RD (Disc ID consts, salts, algorithms)
19. **svanheulen/mhsc** — https://github.com/svanheulen/mhsc
    - Save converter (PPSSPP plugin), save-structure knowledge
20. **hzhreal/save-decrypters** — https://github.com/hzhreal/save-decrypters
    - `monsterhunter-psp-decrypter/main.c` — MHP2G/MHFU/MHP3RD save decrypt; hash salts (MHP2G_JP/NA…)
21. **viciousShadow/MH-PSP-DLC** (KouyaOsamu fork) — https://github.com/viciousShadow/MH-PSP-DLC
    - PSP MH DLC archive (MHP/MHF1, MHP2/MHF2, MHP2G/MHFU, MHP3rd) — DLC quest data structs
22. **amaillo/MH-Pointers-Tool** — https://github.com/amaillo/MH-Pointers-Tool
    - File-internal string pointer-table tool (translation), MIT; MH1…MHP3RD/MHTri — file-pointer-table knowledge

## 5. Game databases (low-mid, cross-ref, 3)

23. **Kolyn090/mhfu-db** (+ asset/image db) — https://github.com/Kolyn090/mhfu-db
    - MHFU full DB JSON (weapons/armor/items/skills/quests/locations)
24. **ar-tur-33/MH-Portable-3rd-Data** — https://github.com/ar-tur-33/MH-Portable-3rd-Data
    - MHP3RD data (CSV, locations etc.)
25. **gaugustini/MHFUDatabase** — https://github.com/gaugustini/MHFUDatabase
    - MHFU DB Android app (data from #23); SQLite structure

## 6. HD texture/UI packs (low value, texture-replace addrs, 3)

26. **HolographicWings/Monster-Hunter-Portable-3rd-HD-Pack-Enhanced** — https://github.com/HolographicWings/Monster-Hunter-Portable-3rd-HD-Pack-Enhanced
    - MHP3RD HD textures (ULJM05800 & NPJB40001); README's "missing addresses" → hash maps
27. **David-vz/MonsterHunterPortable3rdHDRemake** (StormieVN / rivenirvana forks) — https://github.com/David-vz/MonsterHunterPortable3rdHDRemake
    - MHP3RD HD remake pack — texture replacement tutorial & hash maps
28. **alien038/MHFU-HD** — https://github.com/alien038/MHFU-HD
    - MHFU HD texture/UI pack — UI texture replacement knowledge

## 7. Other (low, 2)

29. **YNDZX/CheatGuildCardMhp3rd** — https://github.com/YNDZX/CheatGuildCardMhp3rd
    - MHP3RD guild-card cheat (GPL-2.0), HD (NPJB40001) only — guild-card data addresses
30. **GReinoso96/XXModding Wiki QoL Codes** — https://github.com/GReinoso96/XXModding/wiki/Quality-of-Life-Codes
    - QoL codes incl. MHF2 (EUR ULES00851) True Raw/Ele/Status; mostly PS2/3DS content

---

## Summary

| Category | Count | P0 items |
|---|---|---|
| Memory/structures | 6 | mhfu_transmog, mhp3rd_transmog, mhfu-re, mhp3-cheats, mhp2g-decomp |
| Cheat DBs | 4 | — |
| Mods/patches | 7 | FreedomDX (MHF1 coverage) |
| Saves/crypto | 5 | — |
| Game DBs | 3 | — |
| HD packs | 3 | — |
| Other | 2 | — |

## How to use this list

- **Cross-verify**: before adding an address from one source, confirm with a second
  (e.g. mhp3-cheats vs etherealgames vs mhp3rd_transmog).
- **Dig**: P0 repositories (transmog/fucomplete/decomp) contain the highest-quality
  structs — follow [digging-guide.md](digging-guide.md) Route 4 (code archaeology).
- **Contribute**: after verification, submit entries via
  [contributing.md](contributing.md) — always with the source repo linked.