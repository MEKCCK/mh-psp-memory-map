# How to dig NEW memory addresses (methodology)

Goal: find **stable, verifiable** addresses for MHF / MHF2 / MHFU / MHP3RD, with
version and source clearly recorded.

## 0. Core concepts (read Section 0 of memory-map.md first)

- All addresses are **PSP virtual addresses** — identical on hardware and PPSSPP (same version).
- **Absolute** addresses start with `0x08`/`0x09`; real-device cheat DBs use **RAM offsets**
  (absolute = offset + `0x08800000`).
- Original `ULJM05800` and HD `NPJB40001` have **different layouts** — dig and record separately.

## 1. Tools

| Tool | Use |
|---|---|
| PPSSPP (built-in ImDebugger / memory editor) | read/write PSP memory, pause & inspect |
| WebSocket debugger (`memory.read_u32`, `memory.search`, `memory.base`) | scriptable Cheat-Engine-style search |
| Existing mod/loader asm (mhp3reload, p3rd_* series) | addresses & structural hints |
| Ghidra / IDA with MIPS + PSP support | function archaeology on the EBOOT |
| Real-device cheat DBs / CWCheat tables | cross-source (mind the offset convention!) |

## 2. Five typical routes

### Route 1: walk a known struct from a known pointer (easiest)

Using monster data (P3, proven flow):

1. Known monster list pointer `0x09DA9860` → `lw ptr0` = first monster.
2. Enter a quest, attack; observe which bytes around `ptr0` change (HP drops, status).
3. Track the "current HP" value: pause → modify → resume → confirm → record offset.
4. Extend: size, abnormal-status cur/max, rage timer.
5. Record struct base + relative offsets; **verify on another monster / another version**.

### Route 2: value hunting (no pointer to start from)

With WebSocket `memory.search` (u8/u16/u32/float, ranges, wildcards):

1. Pause (`cpu.stepping`).
2. Search current value (e.g. money `12345`) → narrow down (spend some, search again).
3. On a hit, `memory.read` the surrounding context to identify the struct.
4. **Restart the game and re-check** whether the address is stable; find a stable pointer if not.

### Route 3: pointer-chain reversal (address changes every boot)

Search for a u32 that *points at* your address (`memory.search` on the target address
value), walk up until you hit a global fixed address in `0x0880–0x09F0` holding the chain head.

### Route 4: code archaeology (leverage mod/loader hooks)

1. Collect hook addresses from existing mods (see Section 6 of the map).
2. Load the unpacked EBOOT (main program at `0x08804000+`) in Ghidra as MIPS/LE.
3. Trace back from hook addresses: who calls them, which global areas they touch — new data.
4. Example: around `READ_HOOK` you'll find the file-cache struct; `+0x246/+0x288`
   (monster HP) was proven by a mod, not guessed.

### Route 5: file-table route (structural data: hitzones, stat tables)

1. Resources are organized by a **file index table** (the loader's `FILES/<hexid>`
   mechanism — see modelloader).
2. Use `MHP3rd-Game-FIle-List` / PAC tools (`psp_mh_pac_importer`, `pmo_export`) to unpack.
3. Once you find the data file, locate where it is loaded at runtime (hook file reads →
   cache region) to get a memory address.

## 3. Pre-submit checklist

- [ ] Game (MHF/MHF2/MHFU/MHP3RD) and Disc ID stated
- [ ] Version stated (ULJM05800 / NPJB40001 / "both")
- [ ] Address is **absolute** (or marked as offset with conversion)
- [ ] Field type clear (u8/u16/u32/float, read/write)
- [ ] Verified at least once (survives a game restart)
- [ ] (Bonus) second independent source — e.g. our P3 case: list pointer `0x09DA9860`,
      HP `+0x246/+0x288` cross-verified by hpbar mod and the orig Python overlay
- [ ] Traceable source (repo/tool/session; give a link)

## 4. Record & submit

- Follow the table format in [CONTRIBUTING.md](../en/contributing.md) (or `zh-CN`).
- Mark uncertain entries `unverified` — never fake certainty.
- One entry per record, for easy review/revert.