# Vertex frames — Crash Bandicoot (`SCUS_949.00`)

Read from the retail executable's disassembly of `Gte_RTransformSvtx` (0x8003460C) and `Gte_RTransformCvtx`
(0x80034B08). `Gool_ObjectTransform` hands `Gfx_TransformSvtx` (0x80018964) or, when the animation's entry is of kind
20, `Gfx_TransformCvtx` (0x80018A40) one frame: item `anim_frame >> 8` of the animation's entry, with the ordering table
and the object.

Both kinds share one layout:

| offset | what |
|---|---|
| 0x00 | vertex count |
| 0x04 | model (TGEO) id |
| 0x08 | origin x, y, z (three words) |
| 0x14 | box (six words) |
| 0x2C | collision point (three words) |
| 0x38 | vertices, 6 bytes each |

A vertex's first three bytes are its x, y and z: the renderer reads them as unsigned and puts `(byte + origin - 0x80) << 2`
in the GTE (x and y as one halfword at +0, z the low byte of the halfword at +2). The last three bytes are SVTX's signed
normal and CVTX's colour: `Gte_RTransformCvtx` packs bytes 3, 4 and 5 of a triangle's vertices as unsigned r, g, b
into RGBC and runs `DPCS` (0x80034D58..0x80034EC0). The model's polygons address the vertices by
byte offset from +0x38.
