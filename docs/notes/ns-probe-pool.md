# The size-class lookup 0x80015978 and the allocate path 0x80012F10 (`SCUS_949.00`)

Moved from psx-crash `titles/crash1/executable.json` (`runtime.block_pool`, `runtime.pool_node`); the port's owner is
`titles/crash1/render/block_pool.*` and the open question is its issue 0020. This tree names the lookup `Ns_Probe`,
its bounded sibling `Ns_ProbeSafe` and the allocate path `Ns_PageUpdateEntries` (`docs/our-names.md`).

## The lookup

Crash 1's size-class block pool. The product's guest PC when it stops is 0x800159A8, and that word is `lw $v0,0x4($v1)` - the second class-field read of the cell lookup at 0x80015978. The pool is a table of 8-byte cells: `+0x00` is the payload the allocate path stores (0x80013020 `sw a1,0(s3)`) and `+0x04` is the class the cell serves, which is the only field this module's callers compare. There are exactly six `jal 0x80015978` sites in the image and 818 distinct `jal` targets in 72,192 words, so a function-entry census saturates and cannot rank the six by likelihood; which of them ran is a RUNTIME question, answered by titles/crash1/render/block_pool.cpp.

What is not claimed: The unit of the caller's request word is NOT established. The lookup compares each cell's +0x04 against the request word WHOLE - `beq $2,$4` at 0x8001599C and `bne $2,$4` at 0x800159B0 both name $a0, and the srl at 0x80015978 only selects the bucket - and the six callers do not pass a uniform byte count: 0x80015034 and 0x80015118 pass a tagged handle read out of a struct at `lw a0,0(s0)`. So the recovered code names the field for what it is compared against and no more.

## Its globals

Every one of these is read with a `lui $reg,0x8006` + `lw` pair, so a literal-immediate scan cannot see them, and none of them has a `sw` with that displacement anywhere in the image - the pool is filled through a computed pointer, not by a direct store to the global.

## The allocate path

The pool's OWN ALLOCATE PATH, recovered from the image. It matters because it is where the CLASS WORD is defined: the word is loaded at 0x80012FBC, shifted at 0x80012FC8 to pick a bucket, and the SAME unshifted word is compared against a cell's class field at 0x80012FFC and stored into the cell at 0x80013140. The lookup at 0x80015978 was recovered against the SHIFTED form, could never match anything this function wrote, and its walk ran off the end of main RAM on every lookup. Corrected in titles/crash1/render/block_pool.{h,cpp}; this block is the allocate side of the same evidence and is gated by its own probe.
