# Contributing (PRs / providing public addresses)

Any **real, verifiable, source-traceable** Monster Hunter PSP address is welcome.
Everything must meet the minimum bar below or it will be rejected.

## Two ways to contribute

### A. Pull Request (preferred)

1. Fork this repo → new branch (e.g. `add-p3-sharpness`).
2. Edit `docs/en/memory-map.md` (or `docs/zh-CN/memory-map.md`) per the entry format.
3. Open a PR and describe: which game/version, how the address was obtained, how it was verified.
4. Merged after review.

### B. Issue

Open an issue using the template below; maintainers will verify and add it (or explain what's wrong).

## Entry format (memory-map tables)

Each address entry must include: game, Disc ID, version, absolute address (or offset +
conversion), field/type, source tag, verification.

| Column | Example |
|---|---|
| Game | MHP3RD |
| Disc ID | ULJM05800 |
| Version | original / HD ("both" if shared) |
| Address | `0x09B4C244` (= 0x0134C244 + 0x08800000) |
| Field | ITEM_BOX slot 1, u16 read |
| Source | [item] (Kurogami2134/p3rd_item_sets) / self-dug (method) |
| Verified | in-emulator test / stable across restart / second source |

Use the correct source tag from Section "Source Tags" of the map
([orig]/[load]/[modman]/[hpbar]/[dmg]/[sharp]/[item]; self-dug → describe the method).

## Issue template

```markdown
### Address submission

- Game: MHP3RD
- Disc ID / version: ULJM05800 (original)
- Absolute address: (e.g. 0x09B4C244; if it is an offset, give the raw value and explain)
- Type/field: u16, item box slot 1
- Read/Write: read/write
- How obtained: repo link / digging method (route # in the digging guide)
- Verification: [] emulator-tested [] stable across restart [] second source
- Notes:
```

## Mandatory checklist (merge gate)

- [ ] Confirmed PSP virtual address (absolute, or offset with conversion stated)
- [ ] Game and version stated
- [ ] Traceable source (repo link / digging record) — no "unknown origin" entries
- [ ] Verification record (even one line: "edited HP in PPSSPP, works")
- [ ] Addressing convention not mixed (absolute vs RAM offset vs host base)

## Review principles

- **Quality over quantity**: unverifiable or untraceable entries are rejected.
- **Cross-verification encouraged**: two independent sources for one address = prefer & log it.
- **Credit**: data from other projects keeps their origin/License and the map's source tags.
- **Big changes first**: discuss section restructuring in an issue before PRing.

## Translations

The docs are bilingual (en/zh-CN); more languages tracked in
[LANGUAGES.md](../LANGUAGES.md). Translation PRs are very welcome — one language per PR,
keep the tables/addresses unchanged, only translate prose and column headers.

## Other requests

- Want a specific class of addresses? Open an issue prefixed `[REQUEST]`.
- New digging technique/tool? Extend [digging-guide.md](digging-guide.md).