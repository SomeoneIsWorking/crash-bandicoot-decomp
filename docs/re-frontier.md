# RE frontier — Crash Bandicoot (`SCUS_949.00`)

The ordered evidence chain the psx-crash port follows toward faithful behaviour, run with re-harness
`re_frontier.py` from this repository's root (from the port: `RE_FRONTIER_ROADMAP=external/crash-bandicoot-decomp/docs/re-frontier.md`).
`where` and evidence paths (`game/...`, `titles/...`, `tools/...`, `docs/issues/...`) are psx-crash paths. A dependency
on a step kept in another title's roadmap is named in `notes` as `Cross-title dep`, not in `deps`. The trilogy's
roadmaps are in `crash-bandicoot-decomp` (Crash 1, and SHARED-01, the cross-title ownership step), `crash2-decomp` and
`crash3-decomp`.

The execution-method migration is ordered in psx-crash `docs/migration.md`. Static-path observations below are frozen
evidence for the frontier that Lightrec must reproduce; they are not instructions to regenerate, build, or run that
path, and they do not make it a product fallback.

Statuses: ✅ `re-verified` · 🟡 `re-partial` · 🔬 `in-progress` · ⬜ `todo` · ⏸ blocked upstream ·
➖ `skip-by-design` · ⚠️ `hack`.

## Target identity

### CRASH1-01 — select and measure the Crash Bandicoot executable
- status: re-verified
- deps:
- evidence: Real `SCUS_949.00` measured as 290,816 bytes, SHA-1 `b2af088a162e046ad07f532d46b655cfcc42a05c`, SHA-256 `aabf1464f90b2e0b81e712b77aebbdb88f303b16ce830535e2b0cd886ee280f2`. Its PS-X EXE header declares entry `0x8003E018`, text `[0x80010000,0x80056800)` (`0x46800` bytes), gp `0`, and stack `0x801FFFF0`; the entry lies inside the declared text. Embedded strings `Sony Computer Entertainment Inc. for North America area` and `BASCUS-94900` establish the North American `SCUS-94900` target. `python3 tools/verify_executable.py --check --exe <SCUS_949.00>` matches 11/11 tracked facts, including the VSync body fingerprint.
- where: `titles/crash1/executable.json`, `tools/verify_executable.py`
- gap: This step proves executable identity and header only; CRASH1-02 separately binds it to the supplied disc's boot target. No whole-disc authentication, runtime execution, or gameplay is claimed.
- notes: The executable remains outside git. The manifest contains only derived metadata and embedded text evidence.

## Provisioning and oracle

### CRASH1-02 — provision the selected disc without tracking copyrighted data
- status: re-verified
- deps: CRASH1-01
- evidence: `python3 tools/provision_title.py --title crash1 <real USA CHD>` extracted `SYSTEM.CNF` and its boot target through psxport `discdump`; `SYSTEM.CNF` selects `SCUS_949.00`, and the extracted 290,816-byte executable matched 11/11 manifest facts including SHA-256 `aabf1464f90b2e0b81e712b77aebbdb88f303b16ce830535e2b0cd886ee280f2`. The real Crash 2 USA disc refused because it boots `SCUS_941.54`. Nine production-seam tests cover every resolution source, precedence, invalid configured-path refusal, ambiguous drop-ins, successful publish, and identity disagreement without publish.
- where: `titles/crash1/executable.json`, `tools/provision_title.py`, `tools/verify_executable.py`, `tests/test_provision_titles.py`
- gap: Provisioning proves which executable the supplied disc boots and that executable's identity; it does not authenticate every disc sector or prove the game boots.
- notes: Resolution is CLI argument, per-title/generic environment, per-title/generic `.env`, then exactly one repository-root CHD. Invalid configured paths and ambiguous drop-ins refuse rather than selecting another image. Extracted data remains gitignored under `scratch/bin/crash1/`.

### CRASH1-03 — deterministic psxport/oracle boot harness
- status: re-verified
- deps: CRASH1-02
- evidence: Against pinned psxport `7f5d3f13`, the independent CPU fixture passed 39/39 positive, hardware-stop negative, instruction-stepping, RAM-mirroring, and modeled-return checks. `oracle_trace` independently captured the first real crt0 call at step 57,910, target `0x80011A18`; `crossvalidate_crt0.py` compared 6 fields and agreed 6/6 on gp, call target, a0, planned sp, planned a0, and planned a1. The bounded crossvalidator selftest passed 5/5, including unreached-call refusal and wrong-target disagreement; the canonical tracer CLI selftest passed 8/8, including insufficient-window refusal. CMake `crash1_oracle_boot_check` reproduces provisioning, the fixture, and the real comparison from one asset-gated target when a disc is configured.
- where: CMake `crash1_oracle_boot_check`; framework `oracle_spike`, `oracle_trace`, `crt0_extract`, and `crossvalidate_crt0.py`; gitignored boundary trace
- gap: This proves the first independent crt0 window only. BIOS, GPU, SPU, CD, DMA, timers, native product execution, and gameplay remain outside this window.
- notes: The call capture is independent of the symbolic expected target: it records the first executed jal after the entry prologue. Do not call this a full oracle boot or a PC port boot.

## Preserved execution and ownership evidence

### CRASH1-04 — record the resident execution boundary
- status: re-partial
- deps: CRASH1-03
- evidence: Against clean psxport `54af32cb`, the verified USA executable and deliberately empty title seed additions produce 115 static candidate seeds and 666 emitted candidates with zero configured overlays under emitter version `2026-08-26.14`. Generated execution with the direct Crash 1 runtime installed starts at entry `0x8003E018` and agrees 34/34 with the independent Mednafen CPU at eight calls through first syscall `0x8003E1F8`. At step 62,083 the independent CPU records Cause `0x20` and EPC `0x8003E1FC`; the controlled shipping path executes the exact generated wrapper through `rec_syscall`, retains the same Cause/EPC, returns prior IRQ 1, and disables delivery. Both CPUs pop the Status mode stack once and resume at `0x8003E200`; the next dynamic-dispatch state agrees 34/34 at PC `0xB0`, with `t1=0x56` and `ra=0x800431B8`, identifying BIOS B(56h). The complete gate passes 18/18, including manifest-altered syscall and BIOS facts, short 7/8 trace, out-of-text seed, repeated target, wrong syscall target, wrong selector, and altered-register controls. Ghidra 12.0.4 disassembly of the same verified bytes at `0x8004319C..0x80043248` proves the continuation reads C0 slot `+0x18`, copies fourteen words over `[0x80043204,0x8004323C)`, and tail-dispatches A(44h) with `ra=0x800431E8`. PSX-SPX's retail-BIOS analysis identifies slot 6 as C(06h) ExceptionHandler at `0x00000C80`. Against clean pushed psxport `99a42aa3`, the shipping-HLE consumer CTest passes that slot contract. A real ordered independent trace then applies selector-1 return, B(56h) returning `0x8000F800`, and seed `0x8000F818=0x00000C80`; at its next boundary `v0=0x00000CB8`, proving the fourteen-word destination loop completed. `crash1_boot_frontier.*` composes the product to execute B(56h) through shipping HLE and stop at the A(44h) pre-HLE boundary.
- where: `titles/crash1/executable.json`, `titles/crash1/bios_contract.json`, CMake target `crash_port`; the static translator, seeds, and recomp boundary tests were deleted by the execution migration
- gap: Equality is proven from the executable entry through the first post-syscall B(56h) dispatch boundary. Product composition extends to A(44h), but no serialized run has yet proven it reaches that boundary. The ordered real oracle now preserves CPU/RAM through the syscall, B(56h), C0 seed, and fourteen-word copy, but its first-post-model-call policy captures intermediate local wrapper `0x8004323C` with `ra=0x800431E8`. That wrapper tail-dispatches A(44h) via non-link `jr`, and modeled returns cannot currently combine with exact target capture. The A(44h) register file and fourteen destination words therefore lack an independent comparison. Of 666 emitted candidates, eight bodies execute and one further candidate is observed as a call target without executing its body; 657 candidates lack execution provenance. Static table discovery also emits data-like fragments, so neither the 115-seed nor 666-candidate count is a correctness denominator.
- notes: C0 slot 6 is fixed in the shared framework, not overridden by Crash. The unresolved A(44h) oracle boundary remains valid evidence debt. Do not extend or rerun the static path; reproduce the recorded state through Lightrec and use the independent oracle for new comparison evidence. This is neither a rendered frame nor a playable-game claim.

### CRASH1-VSYNC — identify and bound guest VSync
- status: re-partial
- deps: CRASH1-01
- evidence: Verified SCUS_949.00 contains canonical libetc VSync at `[0x8003E4F0,0x8003E638)`. The tracked body SHA-256 is `915c96a114984906cdf7e9db7a82f361d9a716a593f5f8afead14b9f45edb8c7`. The executable gate retains altered entry/body controls. The exact-pinned asset-free runtime test initializes the production platform owner, proves the two GpuUpdate JAL sites remain guest instructions rather than native-function overrides, and verifies that the VSync host service returns a typed `FrameBoundary` while preserving its guest continuation.
- where: `titles/crash1/executable.json`, `game/frame/frame_driver.*`, `crash::Runtime::registerOverrides` (`StockVSync`)
- gap: A real Lightrec run has not yet reached either GpuUpdate VSync and resumed at its measured continuation.
- notes: The host advances its own clock at each typed boundary; it never executes the retail busy-wait body.

### CRASH1-FRAME — recover one complete native-owned frame step
- status: re-verified
- deps: CRASH1-04, CRASH1-VSYNC
- evidence: Verified SCUS_949.00 identifies CoreLoop `[0x80011FC4,0x80012560)`, its next-iteration entry `0x80012030`, GpuUpdate `[0x80016E5C,0x800172E8)`, two display waits at `0x800170F4` and `0x80017124`, and its CNT2 increment event `0x80034504`. `crash::FrameDriver` enters only through the host shell, services pad/audio and the measured counter clock, resumes the two exact post-wait continuations, and commits one presentation fence. Real headless product launches reached three earlier boot-time guest-VSync callers and made each fatal rather than answering it. Ghidra identifies the first two as libcd initialization `[0x80044E8C,0x8004519C)` and callback/event/pad initialization `[0x8003CB9C,0x8003CD3C)`; title-local runtime owners retain both generated bodies, preserve the state-producing initialization, and issue no guest VSync. The third caller `[0x80042864,0x80042A04)` is the GPU command-queue watchdog: its native owner reads the host-owned compatibility field counter directly and retains both the poll deadline and timeout recovery. Focused Clang tests exercise all three production transitions; their touched sources pass clang-tidy and `crash_port` rebuilds with them.
- evidence: Ghidra identifies the next startup chain as disc-index reader `[0x8002F8C4,0x8002FD30)` calling stock-libcd CdControl `[0x80043520,0x80043668)`, CdControlF `[0x80043668,0x80043984)`, CdRead `[0x800439A4,0x80043A24)`, CdReadSync `[0x80043984,0x800439A4)`, and the CdSync wrapper/body at `0x80043498`/`0x800440EC`. A serialized real-disc run proved reads stayed sequential after the NSD because only CdControl, not the page loader's CdControlF, updated the native head. Extracted NSD entry 6 requires LBA 51468 while guest RAM came from LBA 51376/NSF offset 34. `crash1_disc_index_io.*` now authenticates and binds CdControlF through a no-result ABI adapter to psxport's existing synchronous command owner, preserving every generated body. An isolated rerun then read LBA 51468 and delivered the exact extracted entry-6 payload, SHA-256 `ec3f010049a1b98cf0561ce1163c45992ca4b10bcdeb5bd792df46aaad22c937`; the retail decompressor advanced through later pages. Static inspection of the next refusal found the manifest's done flag transposed as `0x80036428`; the authenticated transition LUI/LW pair loads `0x80056428`. The corrected manifest records both address and load site, and the executable gate decodes and cross-checks that relationship. Against pinned psxport `3c342ec3`, a fully isolated PSX-render run reconciled 120/120 host frame fences through frame 119, returned normally, and emitted no guest-VSync, fatal, error, or watchdog-timeout line. Presented frames 30, 60, and 119 were visually inspected as the centered SCEA Presents splash. The focused Clang test proves Setloc and stale-result-pointer isolation; clang-tidy is clean and the product links.
- evidence: Ghidra: the scene loader `FUN_80015B58` calls `FUN_800322CC` (0x80015D10), which uploads the scene's loading picture in 8-line strips and calls `FUN_800172E8` `[0x800172E8,0x80017358)` twice; that helper swaps the buffer pointers, calls VSync(0) at 0x80017320 (returns to 0x80017328), PutDispEnv and PutDrawEnv. `0x8005C54C = 2` then makes GpuUpdate skip its swap and DrawOTag for two frames. The frame driver delivers these waits as `FrameProgram::loadingSwap`.
- where: `game/frame/frame_driver.*`, `game/boot/{libcd_init,callback_boot,gpu_watchdog}.*`, `game/runtime/runtime.*`, `titles/crash1/frame/frame_driver.*`
- gap: The native-owned frame-step objective is verified for 120 consecutive intro frames. Gameplay progression remains unverified, and native graphics producers and interpolation remain separate ungrounded frontiers; the widescreen projection owner is built but no widened frame is proven (issue 0023).
- notes: The host shell invokes only this driver. The boot owners are not fake VSync answers: the libcd owner keeps the host controller and reproduces measured library state, the callback owner preserves 21 non-delay calls and removes four hardware-settle delays owned by the host, and the GPU watchdog reads `Timing::vblank` without dispatching the guest leaf. The stable-source page candidate is not in the product; CdControlF fixes the upstream seek instead.

### CRASH1-POOL — recover the size-class block pool that the product faults in
- status: re-verified
- deps: CRASH1-04
- evidence: The product's stop address was a FAULT, not a budget exit: the product's own log said
  `frame 0 left guest execution at 0x800159A8 with fault after 8177050 cycles (Lightrec execution
  fault)`, and `runtime/cpu/lightrec_executor.cpp:624` is the only site that returns that detail.
  `0x800159A8` is the word `0x8C620004` = `lw $v0,0x4($v1)`, the second class-field read of the
  size-class cell lookup whose entry is `0x80015978`; the guest printed `invalid load/store at address
  PC 0x00800004` immediately before, so `$v1` was `0x00800000`, which is unmapped. The image settles
  the class rule: `beq $2,$4` at `0x8001599C` and `bne $2,$4` at `0x800159B0` both test `$a0`, the
  bounded sibling at `0x800159E8` does the same, and the allocate path writes and compares the
  unshifted word at `0x80012FFC` — so the class a cell is compared against is the request word WHOLE,
  not `request >> 13`. The bucket mask is `0x3FC`, the cell stride 8, the class field +4, and the
  bounded sibling `0x800159C4` has **0** call sites. `crash1::BlockPool` owns the lookup natively.
  Correcting the class rule turned a fault into a complete run: unservable classes 5 -> 0, executed
  blocks 1,689,262 -> 3,461,249, `fallback_blocks` 0 in both arms of the controlled pair.
- where: `titles/crash1/render/block_pool.*`, `titles/crash1/executable.json` ->
  `runtime.block_pool`, issue 0020
- gap: The allocate path at `0x80012F10` is recovered as a readable model but is deliberately NOT
  installed as an override, because nothing establishes that the guest reaches it: every lookup in
  the disc-backed run came from `ra = 0x80015164` (`FUN_80015118`), never from `0x80013140`. The
  callers' request word is not established either — `0x80015118` passes a tagged handle, not a byte
  count.
- notes: the engine's bound at `0x800159C4` was applied and **measured to break a path retail
  completes** (a disc-backed run faulted at 320,508 cycles with it, and reached the display wait
  without it), so the owner stops at the edge of main RAM instead — a host-memory fact, true for every
  cell retail can read. A controlled pair with the override OFF reaches the same boundary, so the
  owner is transparent. `0x8005C534` is the pool's LOW end, not its high end, and getting that
  backwards rejects the walk on its first step; the test pins cells walked as well as the answer
  because a comment could not have caught it. `[producers] run-end: OtAttr spans recorded 0` is a dead
  counter for this title — its window is the legacy `GameConfig` packet pool, which a typed
  `GameRuntime` never fills — so the guest's real submission is 239,549 GP0 primitives over 400
  frames, and 6 of 6 captured presented frames carry a picture.

### SHARED-01 — prove cross-title shared-engine ownership
- status: re-partial
- deps: CRASH1-04
- evidence: The callback initializer is word-identical across the trilogy apart from leaf addresses and gp slots; Crash 3's libcd init body is Crash 2's; all three CoreLoops resume at a loop top and wait twice in GpuUpdate; the GPU watchdog check matches in Crash 1 and 2 only.
- where: `game/boot/`, `game/frame/`, `game/runtime/`
- gap: Every shared owner is parameterised by `TitleFacts`; engine behaviour above the boot and frame seams is still per title.
- notes: Similar directory roles or franchise lineage are leads, not ownership proof. Cross-title dep: CRASH2-01, CRASH3-01.

### CRASH1-05 — identify camera state and graphics submitters
- status: in-progress
- deps: CRASH1-FRAME
- evidence: Static retail/Ghidra and address-aligned reference source identify GfxUpdateMatrices 0x80017A14 camera inputs and GoolObjectTransform 0x8001DE78 pre-GTE object submitter. Against exact psxport `fb08d30f`, one isolated real-disc run reconciled 1,172/1,172 frame fences with no dropped layer and visually advanced from SCEA through Universal and Naughty Dog to the 3D Crash title/main menu. Fntrace measured CamUpdate, GfxUpdateMatrices, and GfxLoadWorlds on all 1,172 frames; GoolObjectTransform first ran at frame 324 and totaled 13,156 calls, compressed vertices first ran at frame 324 and totaled 9,253 calls, and static vertices first ran at frame 630 and totaled 543 calls, all with zero ABI violations.
- where: `titles/crash1/`, eventually `game/render/` only where SHARED-01 proves ownership
- gap: The BIOS auto-pad publication is closed — a held button moves the word at `0x80057054`, the captured frame, and the present counter, and the title reaches a real level. The camera's CENTRE PUBLICATION is closed too: `SetGeomOffset` 0x80042F8C is reached by two `jal`s only, the per-frame one at 0x80017F00 inside `GfxUpdateMatrices` 0x80017A14 which CoreLoop enters unconditionally at 0x800123BC, and a 200-frame 16:9 run exits 0 with 203 centre publications and a retail centre of (0, 0) — the level's centre is a camera-SHAKE word (issue 0023). What is open is the horizontal FIELD OF VIEW: `OFX + margin` translates this title's projection rather than widening it, so the widened canvas's added columns carry the guest's own fill and not more world. The extra columns need the pre-GTE horizontal frustum the title's view matrix carries, which is a native producer over camera/projection state. Representative gameplay remains the gate.
- notes: Resolve from game state before GTE submission, never from GTE/OT/GP0 output. `GfxUpdateMatrices` is the camera snapshot point after `CamUpdate`; `GoolObjectTransform` is the object producer seam because it still owns object identity, animation type/frame, asset, and world transform before the Svtx/Cvtx GTE paths. The call-site recovery for the projection owner is `$r31 - 8`: a `jal` links to its own address plus its delay slot plus four, and `$r31 - 4` names the delay slot (issue 0025).

### CRASH1-06 — owned widescreen and interpolation paths
- status: re-partial
- deps: CRASH1-05
- evidence: The horizontal culls are read (`docs/notes/screen-culls.md`): the world renderers have none (the SLST list is disc data), and the objects' is `Gfx_CalcObjectMatrices` 0x800180CC's box and centre tests. The port's head of that function widens its columns to the canvas, byte-identical to the retail routine at 4:3 in every scene the warp reaches but 0x04, and draws objects only the canvas keeps off the guest clock with logic state unchanged (psx-crash issue 0030).
- where: psx-crash `titles/crash1/render/object_cull.*`
- gap: The world's margins hold only what the 4:3 sort list names, so they stay partly empty; sprites, fragments and text are not read for a screen test; the intro's 4:3-wide overlay rectangle is untraced. Interpolation still lacks an authoritative simulation tick plus owned previous/current camera and object-transform producers, and no native renderer submission owner exists to consume either result.
- notes: Enhancements must remain off during faithful/oracle verification.

## Runtime execution migration

### CRASH1-JIT-01 — reproduce the current menu and PadRead frontier through Lightrec
- status: re-verified
- deps: CRASH1-02, CRASH1-03, CRASH1-FRAME
- evidence: The authenticated native/Lightrec product maps `SCUS_949.00` through psxport's per-`Core` executor, reaches the 3D menu, and now runs a disc-backed level to completion: 400 frames, 3,461,249 executed blocks, 46,438,388 executed instructions, 0 fallback blocks. A held button moves the BIOS `PadRead` word at `0x80057054` (active-low bit 11), changes the captured frame, and advances the present counter, and Start plus a menu tap walks the UIS copyright screen, the menu, and a real level ("N. Sanity Beach") — so the earlier "host input never reaches the title" reading was a fact about a run that presented nothing.
- where: psxport's target per-`Core` executor; `titles/crash1/entry/runtime.*`, `titles/crash1/frame/frame_driver.*`, `titles/crash1/input/bios_pad_input.*`
- gap: The level's camera publishes no centre through `SetGeomOffset` in a live unpaused level (issue 0023), so the product reaches a rendered level but not interactive gameplay. Keep the linked product boundary free of a full-game/player-selectable interpreter and generated guest code.
- notes: The static translator, corpus, dispatch adapters, seeds, and static-only tests were deleted
  before this integration; do not restore a compatibility path.

### CRASH1-SCENE-01 — the scene words a presented frame cuts on
- status: re-partial
- deps: CRASH1-JIT-01
- evidence: Ghidra over `SCUS_949.00`. `FUN_80011FC4` (CoreLoop) tests the request word `0x80056714` at `0x800121C0..0x80012300`: `-1` is no request; a scene id unloads the scene (`FUN_80016420` clears the loaded flag `0x8005C528`) and loads it through `FUN_80015B58`, which resets the request to `-1` (`0x80015BA0`) and stores the new id to `0x80056710` and `0x8005C52C`; it reads the 12-byte row at `0x8005E03C + id * 12` (header sector, byte size masked `0x1FFFF`, end sector), filled at boot, with no bound on the id. Live (2026-11-04): 44 of ids 0..0x3C have a row (0x03..0x39, first gap 0x0B); 0x00 and 0x3C are empty and fault at `0x80025A60` when requested; the warp lists only ids with a row (`FrameProgram::sceneTable`). `-2` reloads the scene saved at `0x800579A4` and then restores the checkpoint snapshot at `0x80057974` (`FUN_80026650`; the same call stores `-2` through `gp+0x318` at `0x80026698` when the snapshot belongs to another scene). Death or exit states `0x200/0x300/0x400` in `0x800618D0` request scene `0x19` (`0x80012214`). Script op 9 of the object interpreter `FUN_800201DC` stores `operand >> 8` to the request word (`0x800228A4`). Live: the title, menu and map screens all run in scene `0x19` with the request at `-1` (`rw 80056710`).
- where: `game/frame/frame_cut.*` samples the id and the request once per logic frame; `titles/crash1/executable.json` `runtime.native_frame.scene`
- gap: A checkpoint respawn inside the loaded scene (`FUN_80026650` with a matching snapshot scene) restores objects in place and raises neither word, so it is not a cut yet. Live, `warp 3` from boot cuts once, on the first record the level draws; a scene change from a portal or death is not yet observed live.
- notes: A cut is the frame after a pending request, or a changed id; the first frame of a session is a cut.

### CRASH1-JIT-02 — prove representative interactive gameplay
- status: todo
- deps: CRASH1-JIT-01
- evidence:
- where: Crash 1 product plus separately built independent-oracle harness
- gap: Enter and sustain a representative level with the PadRead owner and all native services active; compare timing, interrupts, CPU/memory and relevant device state; exercise an override, original call, and executable-memory invalidation in positive and controlled-negative cases; qualify released hosts.
- notes: This is the fidelity gate for the replacement product. No compatibility mode exists.
