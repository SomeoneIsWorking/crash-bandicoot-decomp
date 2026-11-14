# Screen-edge culls — Crash Bandicoot (`SCUS_949.00`)

Read from the retail executable's disassembly. Crash 2 and 3 test every world polygon against a packed 512 by 217
bound (crash2-decomp and crash3-decomp `docs/notes/screen-culls.md`); Crash 1 holds no such bound. Its world is
not culled at all, and its objects are tested in object space before they are transformed.

## World polygons

`Gte_RTransformWorlds` (0x80035438) walks the sort list from its last entry to its first. For each entry it loads the
three corners, runs RTPT, stores SXY0..2 into the next primitive (GT3 when the texture word's bit 7 is set, F3/G3
otherwise), averages SZ1..SZ3 into an ordering-table slot (clamped to the lowest slot reached so far), and links the
primitive at that slot's head. No path between the RTPT and the link compares a screen coordinate: every polygon the
list names is drawn. The variants (`Gte_RTransformWorldsFog` 0x80035898, `...Ripple` 0x80035E10, `...Lightning`
0x80036340, `...Dark` 0x80036BF4 with its loop 0x80036D9C, `...Dark2` 0x800375EC) add colour and vertex work; their
only `slt` compares are a corner's depth against the fog start, never SX or SY.

The list is disc data. `Slst_Update` (0x80029B0C) either copies a whole SLST item into the buffer (`RMemcpy`
0x80033FBC, when the item's word 2 has bit 0 clear) or applies the item as a delta, forward (`Slst_RDecodeForward`
0x800334A0) or backward (`Slst_RDecodeBackward` 0x80033878) by the camera's direction along its path. Neither decoder
touches the GTE. So the world's margins at 16:9 hold exactly what the list for the 4:3 view names, and there is no
screen test to widen.

## Objects

`Gfx_CalcObjectMatrices` (0x800180CC, `src/main/render/transform_88CC.c`) decides whether an object is drawn before
it builds the object's matrices. Its only callers are `Gfx_TransformSvtx` (0x80018964, the `jal` at 0x800189AC) and
`Gfx_TransformCvtx` (0x80018A40, at 0x80018B00), both reached from `Gool_ObjectTransform` with the current draw
context's ordering table (context + 0x88). A return of 0 makes the caller return without drawing; a reject writes
nothing but the function's own frame, the scratchpad words below and the GTE.

The test, after `SetRotMatrix(0x80057844)`, `SetTransMatrix(0x8005726C)` and `RotTrans` of the object's position
less the camera eye (0x80057888, each axis >> 8) into the frame's view position (sp + 0x2C) and flag (sp + 0x88):

1. Level flag 0x10000 at 0x800618B0 set: no test, the matrices are built (0x800188AC).
2. Far clip: the camera zone's word 0x2E8 >> 8 (stored to sp + 0x88). Rejected beyond it, or beyond depth 0x2EE0 when
   it is 0.
3. Status bit 0x40000 (object + 0xCC): no screen test.
4. Rejected when the depth is not beyond H (0x800578D0).
5. Status bit 31 set, the box test: `Gte_RProjectBound` (0x800351C8) projects the object's bound box (object + 8)
   and leaves the extents in the scratchpad: min x 0x1F800048, min y 0x4C, max x 0x50, max y 0x54. They carry the
   GTE's screen offset, which `Gfx_UpdateMatrices` sets each frame (0x80017F00: x from 0x8006193C, y from
   0x80061894 >> 8 plus 0x80061940); the bounds are centred on it. Rejected when both x extents are below -0x100 or
   both above 0x100, or both y extents below -0x6C or both above 0x6C.
6. Otherwise the centre test: sx = x * H / z and sy = y * H / z (the C divide, truncated); rejected when |sx| >= 0x13D
   or sy + 0x8A >= 0x147 unsigned. 0x13D is the half width 0x100 plus 0x3D of slack for the object's size.

So the screen is 512 columns wide in both tests, centred: the box test's columns are -0x100 to 0x100, the centre
test's -0x13C to 0x13C. A wider canvas moves both edges out by its margin.

`Gfx_TransformSvtx` passes no result pointer; `Gfx_TransformCvtx` passes sp + 0x28, which the function zeroes on entry
and sets to a level of detail when it keeps the object, and which the CVTX drawer receives. A kept object also writes
its light matrix (object + 0x30) in light modes 2 and 4.

## Seen in the picture

At 16:9 (`warp scene 0x06`, 8 s after the warp, 60 frames on from a saved state), with the five compare immediates
raised by the 86-column margin through the debug server (0x80018288, 0x80018290, 0x8001829C, 0x800182A8 to ±0x156;
0x80018374 to 0x193), an object appears in the right margin (216 pixels) that the retail bounds drop. Scenes
0x03, 0x0A, 0x11, 0x16 and 0x1A and the intro's castle showed no change in the frame taken.

## In the port

psx-crash `crash1::ObjectCull` (`titles/crash1/render/object_cull.*`) runs the test above in host code, with each
column bound moved out by the record canvas's margin, and resumes the original at 0x8001838C or 0x800188AC. At 4:3
it is the retail routine: in every scene the warp reaches (all but 0x04, which faults with or without it, psx-crash
issue 0043) every sampled call compared equal. Over those scenes 2.5% of the calls take the box test (scenes 0x08,
0x09, 0x15, 0x17-0x19, 0x1C, 0x1D, 0x1F, 0x22, 0x23, 0x29), the rest the centre test or the 0x40000 bypass; the level
flag 0x10000 was never set.

## The draw context

`Gpu_GetCurPrimsTail` (0x80016E18) returns the current context (0x8005840C) + 0x78. A context is 0x2088 bytes: + 0x74
the primitive buffer, + 0x78 its tail, + 0x88 the 0x800-slot ordering table. `Gpu_SetupPrims` (0x80016C18) allocates
each context's buffer by the level at 0x80056710: 0x1E000 bytes for level 0x19, 0x1CC00 for 0x38, 0x16000 otherwise.
Nothing checks the tail against the buffer's end.
