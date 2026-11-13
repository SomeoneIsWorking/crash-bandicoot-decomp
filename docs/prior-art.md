# Prior art — Crash Bandicoot (`SCUS_949.00`)

Outside work on `SCUS_949.00` (SHA-1 `b2af088a162e046ad07f532d46b655cfcc42a05c`); a reference names addresses, our measurement decides behaviour.
Address checks ran psxport `decomp_pipeline.py --function-at` on our image (2026-10-13) unless marked "our docs";
licences, trees and push dates were re-read through the GitHub API on 2026-11-13. Paths such as `docs/issues/...` are
psx-crash paths.

Tools that cover all three titles (GOOL bytecode, NSF/NSD data) are listed here only.

## Decomp

| submodule | URL | pinned | licence | target build and match | progress (verified in tree) | what we may take |
|---|---|---|---|---|---|---|
| `external/crash-bandicoot-decomp` (psx-crash) | https://github.com/khasinski/crash-bandicoot-decomp, forked as https://github.com/SomeoneIsWorking/crash-bandicoot-decomp (this repository) | `5a4d31a8` (2026-10-20) | none (`LICENSE.md`: no licence chosen) | Crash 1 `SCUS_949.00`: `configs/USA/checksum.sha` is our SHA-1; `CORE_Main 0x80011D88`, `CORE_Loop 0x80011FC4`, `CORE_VSync 0x8003E4F0`, `GOOL_InterpretObject 0x800201DC` are Ghidra entries in our image | badge `docs/badges/functions.json` and `code.json` claim 100% matching; tree has 400 `src/**/*.c` and no `asm/` files; `docs/names.md` names 825 functions | evidence only: names, struct layouts (`docs/STRUCTURES.md`), GOOL notes; no code until it has a licence |

## Other projects

| project | URL | kind | licence | target build | build match (addresses checked) | what we may take | notes |
|---|---|---|---|---|---|---|---|
| khasinski/crash-bandicoot-pc | https://github.com/khasinski/crash-bandicoot-pc | native PC port built from the Crash 1 decomp (imported at `965a1bd`) on PsyZ | none (`LICENSE.md`: no licence chosen) | Crash 1 `SCUS-94900` | matched through its source decomp | evidence only | Verified 60 fps: `src/platform/interp.c` snapshots game memory, draws a midpoint frame with the logic held at 30 Hz, and restores memory, the same design as our replay interpolation. Last push 2026-10-20. |
| wurlyfox/c1c | https://github.com/wurlyfox/c1c | MIPS-to-C translation plus hand decomp over a PCSX-R core | none | Crash 1 `SCUS_949.00` (NTSC) | matched: `docs/FUNCTIONS.md` lists `CORE_Main 0x80011D88`, `CORE_Loop 0x80011FC4`, `0x80017A14`, `0x8001DE78`, `CORE_VSync 0x8003E4F0` | evidence only (names) | Archived; last push 2015-07-22. |
| CBHacks wiki, Game Loop | https://wiki.cbhacks.com/w/Game_Loop | RE notes | none stated | Crash 1 (region not stated) | matched (our docs): loop `0x80011FC4`, scene load `0x80015B58`, `UpdateMatrices 0x80017A14`, GPU routine `0x80016E5C`, object interpreter `0x800201DC` | evidence only | Other per-frame callees (`0x800134C8`, `0x80025928`, `0x8002B2BC`, `0x8001D5EC`) are unverified leads for issue 0023. |
| Matteo842/CrashBandicoot-Launcher | https://github.com/Matteo842/CrashBandicoot-Launcher | static recompilation (RecompOne) plus launcher | MIT | Crash 1 `SCUS-94900` | matched (our docs): `0x8003E4F0` VSync, `0x8003E638`, `0x80011FC4` loop | may copy with attribution; little fits a dynarec port | Claimed widescreen in `docs/NATIVE_WIDESCREEN.md`, unverified. Last push 2026-11-12. |
| omegakatana92/CrashBandicootRecomp | https://github.com/omegakatana92/CrashBandicootRecomp | psxrecomp config and seeds | none | Crash 1 `SCUS-94900` | matched: `game.toml` entry `0x8003E018`, text size `0x46800` equal our header | evidence only | Seeds only. Last push 2026-09-03. |
| ManDude/goocdump | https://github.com/ManDude/goocdump | GOOL script decompilation, all three games | none | per-region folders | not applicable (GOOL bytecode) | evidence only | Last push 2021-01-28. |
| cbhacks/CrashEdit | https://github.com/cbhacks/CrashEdit | NSF/NSD level editor | none for project code (GitHub NOASSERTION) | all three, data formats | not applicable | evidence only | Last push 2026-08-18. |

Not found (2026-11-13): a licensed Crash 1 decomp; any Crash 1 entry on decomp.dev. Searched GitHub repositories (titles,
serials, "decomp", "recomp", "port", "widescreen", "60fps"), decomp.dev, and the web.
