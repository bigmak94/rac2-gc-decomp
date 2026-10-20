# Near misses

C that compiles close to a function but not to its bytes: a starting point, never linked
and never counted. Move a file into `src/` once `tools/build.py` places it.

Recorded candidate sizes, profiles and differences describe the retained experiments, not
verified behavior. Declaration repairs and symbol renaming are not new match measurements.
Drafts marked G8 need a `-g8.c` source unit when checked again.

Extracted drafts may still need the original source unit's types and declarations. Their
recorded checks describe the original compilation unit, not standalone syntax or verified behavior.

| Function | Size | Bytes that differ |
| --- | ---: | ---: |
| [`func_00116300`](core/func_00116300.c) | 96 | Candidate is 88 bytes; retail gives the empty arm its own `b` over the block, this cc1 lays the block out as the fall-through |
| [`func_00116E10`](core/func_00116E10.c) | 756 | newlib `fREe`; candidate now reaches retail's exact size once the `next->fd` load is kept live across the islr branch (spelt as a separate load in the else arm only it falls back to 764, measured), the whole islr region then matching. Two words left, both compiler-side: this cc1 keeps the loaded FD in `$a2` and uses it at the join where retail loads into `$v1` and copies (`daddu $a2,$v1,$zero`), and it inserts a load-to-branch `nop` before the frontlink large-bin loop's rotated `beql` where retail has none |
| [`func_00118548`](core/func_00118548.c) | 156 | Candidate is 160 bytes; unsigned divide matches, but this cc1 copies the quotient into a third register and hoists the loop `divu` out of the delay slot |
| [`func_00115E90`](core/func_00115E90.c) | 152 | Call shape, order and the noreturn tail are exact; this cc1 swaps the counter and the entry pointer between `$s0` and `$s1` |
| [`func_001185E8`](core/func_001185E8.c) | 1496 | Candidate is 1508 bytes; the SDK vfprintf.obj float-to-string cvt, reconstructed from the assembly - its six-way Inf/-Inf/NaN/Ind dispatch matches instruction for instruction, and the twelve bytes left are register allocation (masked value in `$v1` against retail's `$s2`) plus the exponent tail |
| [`func_00118BC0`](core/func_00118BC0.c) | 264 | Candidate is 276 bytes; this cc1 materialises both global addresses and the buffer base into registers where retail keeps one base and offsets |
| [`func_001330D0`](core/func_001330D0.c) | 56 | Candidate is 60 bytes; the `$gp` base needs a SIZED `sda` declaration (an incomplete `int *tbl[]` never takes the gp form - with `int *tbl[2]` it does). The residue is allocation: `$v0`/`$v1` swapped and one extra `daddu` for the call argument |
| [`func_00133688`](core/func_00133688.c) | 52 | Candidate is 44 or 48 bytes; one unit needs both a `$gp` and a folded absolute access, which no single profile gives |
| [`func_0012F948`](core/func_0012F948.c) | 80 | Candidate is 76 bytes; retail's shift sits before the prologue with a nop after it, and the store order differs |
| [`func_001322B8`](core/func_001322B8.c) | 92 | Sequence and calls exact; this cc1 gives `$s0` to the shift and `$s1` to the object, retail the other way round |
| [`func_00118460`](core/func_00118460.c) | 112 | Candidate is five instructions long; retail peels the loop and orders the tests differently |
| [`func_00122630`](core/func_00122630.c) | 300 | libgcc `__pack_d`; candidate is 304 bytes, instruction-identical but for the `dli` expansion of 0x800FFFFFFFFFFFFF (gas patch 0052's top-down rule, 7 instructions, against retail's 6 the unpatched generic path gives) |
| [`func_00123B40`](core/func_00123B40.c) | 228 | libgcc `find_fde`; candidate is 216 bytes, the loop, `frame_init` call and binary search all match, but both profiles delete the redundant `if (ob == 0) return 0;` retail keeps |
| [`func_00115340`](core/func_00115340.c) | 128 | SDK `memcmp` (symbol_addrs's own SDK-library match): 128-bit `lq` and MMI compare ops no C reaches; ASM_FUNC candidate |
| [`func_0011B458`](core/func_0011B458.c) | 164 | SDK `_sceIDC` (symbol_addrs's own SDK-library match): `sync` / `cache` / `mfc0 TagLo` body; ASM_FUNC candidate |
| [`func_001291A0`](core/func_001291A0.c) | 116 | VU0 macro mode: 19 of 29 instructions are COP2 macro ops reading a constant pool at 0x137EA0; called from the ASM_FUNC vector helpers next door |
| [`func_00128F48`](core/func_00128F48.c) | 136 | Row is a stripped function's delay slot plus a 0xCDCDCDCD linker gap; the 128-byte body after them builds constant masks with no-op ANDs no compiler emits |
| [`func_0012FBF0`](core/func_0012FBF0.c) | 368 | libmpeg decode-or-skip driver; 91 of 92 instructions identical, only `*(int *)inner = 1`'s constant lands in `$v0` here and `$v1` in retail |
| [`func_0011FB8C`](core/func_0011FB8C.c) | 188 | Row starts with a `0xCDCDCDCD` linker gap and a dead-stripped function's delay slot; the real 176-byte body runs from 0x11FB98 |
| [`func_00132498`](core/func_00132498.c) | 328 | SDK `snd_BankLoadAsync`; five `$gp` globals, so it needs the `-g8` sibling unit route before a body can be checked (symbol map recorded) |
| [`func_00121AB8`](core/func_00121AB8.c) | 96 | __muldi3; the verbatim libgcc2.c source now: same size and sequence, one allocation rotation (`$v0` against `$a2` for the high extraction, `mflo`/`mfhi` `$a3`/`$a0` against `$a0`/`$v0`) and the 0xFFFF mask before the `multu` where retail keeps it after the `or`. The old "both compilers turn a*b into a jal" was the game compiler |
| [`func_00132220`](core/func_00132220.c) | 148 | 6 of 37 words differ, all in the first block: retail branches `beqz` to the shared epilogue with the return value in the delay slot and leaves the `jal` delay slot a `nop`, this cc1 branches the other way and fills both slots with the argument setup |
| [`func_00127218`](core/func_00127218.c) | 112 | Candidate is 104 bytes; the row is eight bytes too long - the leading `sw $a0,0x0($v1); nop` is the linker remnant rac1-decomp calls func_001235C0, and the USA v2.00 symbols still give func_00127218 size 0x8. The C reproduces the 104 bytes at 0x00127220 exactly; a `tools/levels.py` split unblocks it |
| [`func_001244B8`](core/func_001244B8.c) | 136 | WaitGsPathsIdle: reads VU0 control register 29 with `cfc2`, a COP2 instruction plain C cannot emit |
| [`func_0011B140`](core/func_0011B140.c) | 164 | Two `sync` barriers around the 0x1000F000 handshake; this cc1 emits no `sync` from C |
| [`func_00115250`](core/func_00115250.c) | 240 | SDK hand-written byte scan: 0x8080 mask build-up and multimedia ops the disassembler prints as `.word`; no C spelling |
| [`func_001153D4`](core/func_001153D4.c) | 176 | memcpy, SDK hand-written r5900 assembly: 128-bit lq/sq plus an ldl/ldr/sdl/sdr alignment prologue; no C spelling |
| [`func_001157AC`](core/func_001157AC.c) | 328 | strlen, SDK hand-written r5900 assembly (symbol_addrs), in one of the 16-byte-slot units |
| [`func_001334B8`](core/func_001334B8.c) | 196 | Exact size, one instruction differs at +0x48: retail fills the `beq` delay slot with the return-zero constant and branches to +168, this cc1 hoists the first argument's `lui` into the slot instead. Wave 4: the shared-exit `if/else` puts the whole body in retail's order and still misses that same word (204 bytes); `-fno-schedule-insns2` breaks the prologue without changing the body order |
| [`func_00118CC8`](core/func_00118CC8.c) | 204 | `1 of 1 places match` in tools/check.py, but tools/build.py refuses to place it in the boot link (`needs .rodata at its place, which is not C`): the 35-case conversion switch compiles to a jump table and retail keeps it at 0x13A4B0 |
| [`func_00115544`](core/func_00115544.c) | 332 | strcmp: SDK hand-written r5900 assembly (symbol_addrs), lq/sq plus MMI; the vendored `libc/machine/r5900/strcmp.S` is in this repo's build tree |
| [`func_001158F4`](core/func_001158F4.c) | 460 | Row starts in the `0xCDCDCDCD` linker fill - the real entry is 0x001158F8 and the body is the SDK hand-written strncmp (its `.S` is in the build tree); needs a `tools/levels.py` split |
| [`func_00119AC0`](core/func_00119AC0.c) | 264 | Candidate is 276 bytes; the stream writer's structure, statement order and every instruction match, but this cc1 materialises both global addresses and keeps a second copy of the staging buffer, so it needs ten saved slots against retail's eight (the same wall as its twin func_00118BC0) |
| [`func_00119BF8`](core/func_00119BF8.c) | 3072 | No C attempt: the SDK's vfprintf.obj is a newlib port whose PRINT writes through memcpy/the 0x119AC0 writer instead of iovecs, so the tree's vfprintf.c is not its source; its conversion switch's jump table at 0x13A7B0 cannot be placed anyway |
| [`func_00118D98`](core/func_00118D98.c) | 3368 | No C attempt: same object and same deviations as func_00119BF8, its jump table at 0x13A6C0 cannot be placed, and its cvt (0x1185E8) returns a string where newlib's returns a digit pointer |
| [`func_0012CDB0`](core/func_0012CDB0.c) | 496 | Exact size; three of the four IPU_CTRL read-modify-writes differ in one instruction: retail sinks the store into the following call's delay slot, this cc1 emits it before the call. Wave 4: the cc1's own `-da` dump shows those three stores are single insns with an absolute address (`sw $6,268443664`) that the assembler expands to `lui $at` + `sw` - 8 bytes, which never fit a 4-byte slot; the first block reaches its address through `$a3` and is filled. `-mno-split-addresses` does not change the store and moves the frame to 0x10 against retail's 0x20 |
| [`func_00130428`](core/func_00130428.c) | 680 | Candidate reaches retail's exact size once the dead `var_2_53 = mpeg + 0x528` inside the inner `if` is dropped (wave 3 was 676); the last seven instructions are the remaining wall: retail recomputes the frame-buffer address in both branch delay slots in `$v0` - the register the height load then clobbers - and keeps `ld $ra` in the shared epilogue, this cc1 keeps the address once in `$a0` and sinks the `ld $ra` into the second delay slot |
| [`func_00132C48`](core/func_00132C48.c) | 420 | Candidate is 432 bytes; snd_SendCommandSync is understood end to end, but this cc1 CSEs every symbolic address into callee-saved registers where retail re-materialises them per use |
| [`func_001210E0`](core/func_001210E0.c) | 484 | No C attempt: a 0x700 frame with f20-f31 saved that calls the D_00135D38 factory and registers the 0x12118C callback through func_001240C8; the object layout is needed first |
| [`func_0011C000`](core/func_0011C000.c) | 144 | Saturating double/fixed conversion; no C attempt - the entry copies `$a0` into `$a1` before any 64-bit use, which no plain-C body produces |
| [`func_0012CC88`](core/func_0012CC88.c) | 296 | MPEG header dispatcher; no C attempt - an 11-word local pointer table is copied to the stack with ldl/ldr before the `jalr` |
| [`func_0012DC50`](core/func_0012DC50.c) | 272 | 264-byte candidate; the `sdk -O2 -g2 -gstabs` profile (same as func_0012D808's) reproduces the 16-byte save slots and the whole body, but this cc1 will not make the last `return func_0012DA98(s0);` a sibling call where retail has `j func_12DA98` |
| [`func_001307B0`](core/func_001307B0.c) | 224 | IPU setup; wave 4 wrote the whole body (228 against 224). A 32-byte payload local (`int buf[8]`) reproduces retail's 0x60 frame and its 16-byte save slots; the single remaining word is the 0x10002000 (IPU_CMD) clear, which retail puts in the second `func_12C3B0` call's delay slot and this cc1 keeps before the call |
| [`func_0011BEE0`](core/func_0011BEE0.c) | 56 | Byte-exact only with a register pin (`__asm__("" : "=r"(p) : "0"(0x1000F180))`), which AGENTS.md forbids |
| [`func_00125F20`](core/func_00125F20.c) | 484 | Byte-exact only with `__asm__ __volatile__("sync")`; inline assembly is forbidden, so the barrier belongs to the ASM_FUNC route |
| [`func_00129DA8`](core/func_00129DA8.c) | 1052 | libmpeg `_getRef0`: the body compiles to the exact retail size and aligns instruction for instruction, but the register file is assigned differently - retail keeps `ref` in `$fp` and `fieldpred` in the temp `$t5`, this cc1 keeps `ref` in `$s6` and `fieldpred` in `$t8` - so the first word already differs (`daddu $t9` against retail's `daddu $t8`) |
| [`func_00115FC0`](core/func_00115FC0.c) | 828 | newlib `_mbtowc_r` (SHIFT_JIS / EUC-JP / ISO-2022-JP paths): exact C, `tools/check.py` says `1 of 1 places match`, but `tools/build.py` refuses it with `needs .rodata at its place, which is not C` - the eight-case `switch (action)` becomes a jump table retail keeps at 0x13A3C0 |
| [`func_0011C1F8`](core/func_0011C1F8.c) | 1516 | libkernl `_printf` (the kprintf core), transcribed from Lombyte: candidate is 1512 bytes, carrying one extra `daddu $v0,$zero,$zero` and one instruction short elsewhere, with two blocks scheduled the other way round; walled anyway - its `switch ((char)(*fmt - '0'))` jump table lives at 0x13A9F0 |
| [`func_0011D9C0`](core/func_0011D9C0.c) | 900 | No C attempt: the SIF/kernel command-completion side (slot record 0x20000000 \| 0x13F600 + index*0x440, memcpy via func_1153D4, 25-case switch on command-2, ends in iSignalSema). Walled by the switch's jump table, and RAC1's twin at 0x11B868 is `INCLUDE_ASM` there too, so no source exists on either side |
| [`func_0012FB48`](boot/func_0012FB48.c) | 20 | 3 |
| [`func_00278E78`](game/func_00278E78.c) | 48 | C candidate emits 40 bytes; control flow and global address sequence differ |
| [func_00282C50](game/func_00282C50.c) | 8 | 0 of 28 exact; jr ra matches, but retail delay slot is raw 0x460D6029 and C emits abs.s f0,f13 |
| [`func_00288970`](game/func_00288970.c) | 64 | Candidate is 68 bytes; retail branch-likely delay-slot store is missing |
| [`func_0028B288`](boot/func_0028B288.c) | 44 | 14 |
| [`func_00295870`](game/func_00295870.c) | 68 | Candidate is 64 bytes; mask is not in the retail branch delay slot |
| [`func_0029B658`](game/func_0029B658.c) | 60 | 60-byte candidate swaps the retail global-pointer and saved-object registers |
| [`func_0029B810`](game/func_0029B810.c) | 52 | Candidate is 48 bytes; retail has an extra pointer copy before jr |
| [`func_0029DC58`](game/func_0029DC58.c) | 52 | 26 bytes differ; retail uses bnel with a zero delay-slot result then tests bit 27 |
| [`func_0029FAE0`](game/func_0029FAE0.c) | 60 | 4 bytes differ; retail uses literal lui high halves, not %hi relocations |
| [`func_002A0380`](game/func_002A0380.c) | 60 | 2 bytes differ; table load and branch use v1 instead of retail v0 |
| [`func_002A0488`](game/func_002A0488.c) | 48 | 4 bytes differ; store placement at +0x24 is outside jr delay slot |
| [`func_002C38A0`](game/func_002C38A0.c) | 64 | Candidate is 56 bytes; retail begins with stack adjustment and nop |
| [`func_002C5020`](game/func_002C5020.c) | 68 | Candidate is 72 bytes; retail GP-relative load needs file-level -G8 |
| [`func_002C9A60`](boot/func_002C9A60.c) | 20 | 9 |
| [`func_002C9C60`](game/func_002C9C60.c) | 44 | 8 |
| [`func_002CC528`](game/func_002CC528.c) | 40 | 4 |
| [`func_002D33C0`](game/func_002D33C0.c) | 60 | 2 instructions differ; global-address lui and stack adjustment order differs |
| [`func_002E5F40`](game/func_002E5F40.c) | 32 | 1 |
| [`func_002FCF90`](game/func_002FCF90.c) | 52 | 12 bytes differ; retail schedules zero stores in the return delay slot |
| [func_0031AAB0](game/func_0031AAB0.c) | 88 | check.py 3/3, but level 00 cannot place it: needs .text.func_0031AB08 at its place, which is not C |
| [`func_0031E528`](game/func_0031E528.c) | 52 | 19 bytes differ; loop pointer schedule and beql delay-slot store differ |
| [`func_00327D08`](game/func_00327D08.c) | 72 | Candidate is 76 bytes; retail threshold load is GP-relative |
| [func_00341E58](game/func_00341E58.c) | 52 | check.py 28/28, but level 00 cannot place it: needs .text.func_00341E90 at its place, which is not C |
| [func_0034E478](game/func_0034E478.c) | 64 | check.py 28/28, but level 00 cannot place it: needs .text.func_0034E4B8 at its place, which is not C |
| [func_00351A38](game/func_00351A38.c) | 72 | check.py 28/28, but level 00 cannot place it: needs .text.func_00351938 at its place, which is not C |

| [`func_L00_0037E670`](levels/func_L00_0037E670.c) | 40 | Candidate is 48 bytes; retail hoists the 40.0f constant above the branch and uses the conditional-execute shape `beql $a0,$zero,join` with the store in the annulled delay slot; this cc1 emits the plain `bnez Lelse; nop; <then>; b join; <else>` form for every source shape tried |
| [`func_L00_002D3FB8`](levels/func_L00_002D3FB8.c) | 136 | Candidate is 132 bytes with a 32-bit prototype for func_L00_002D3FB0 (the file's `unsigned long` costs two `dsll32`/`dsra32` words); every instruction matches but retail's `daddu $a1,$v0,$zero` copy in the `beqz` delay slot, which this cc1 saves by loading the field straight into the register it uses |
| [`func_L01_002E1670`](levels/func_L01_002E1670.c) | 12 | Catalogue defect: `addiu sp,sp,0xC0` / nop / `addiu sp,sp,0xF0` at 0x2E1670, between the epilogue ending at 0x2E166C and the next prologue at 0x2E1680; no C can be placed |
| [`func_L03_002B0958`](levels/func_L03_002B0958.c) | 20 | Catalogue defect: five `addiu sp,sp,imm` / nop words at 0x2B0958, the same shape as func_L00_00341798; no C can be placed |
| [`func_L03_003245B0`](levels/func_L03_003245B0.c) | 12 | Catalogue defect: `addiu sp,sp,0x30` / nop / `addiu sp,sp,0x60` at 0x3245B0, between the epilogue ending at 0x3245AC and the next prologue at 0x3245C0; no C can be placed |
| [`func_L06_003829D8`](levels/func_L06_003829D8.c) | 20 | Catalogue defect: five `addiu sp,sp,imm` / nop words at 0x3829D8, the same shape as func_L00_00341798; no C can be placed |
| [`func_L06_0047C1D0`](levels/func_L06_0047C1D0.c) | 24 | Catalogue defect: three `jr $ra; nop` stubs in a row at 0x47C1D0 (one 24-byte C function cannot emit three returns); no C can be placed |
| [`func_L07_00323350`](levels/func_L07_00323350.c) | 20 | Catalogue defect: five `addiu sp,sp,imm` / nop words at 0x323350, the same shape as func_L00_00341798; no C can be placed |
| [`func_L01_002F1F70`](levels/func_L01_002F1F70.c) | 96 | Candidate is the right size and 21 of 24 words match; +0x2C/+0x34 (retail finishes $a1 then puts `addiu $a0,$zero,0x47` in the jal delay slot, this cc1 sinks the `ori` instead) and +0x50 (`or $a1,$v0,$a1` against `or $a1,$a1,$v0`). The load must be written as the literal `*(int *)0x001A72E0` |
| [`func_L03_002EFB60`](levels/func_L03_002EFB60.c) | 64 | COP2: `ldc2`/`sdc2`/`qmfc2` transfers plus an `mfc1`, no C can be placed |
| [`func_L03_0032AB20`](levels/func_L03_0032AB20.c) | 80 | Candidate is 72 bytes; the first ten instructions match (including the annulled `beql` + load pair) once the data base is the file's symbol `D_L03_00189E20`; the gap is block layout, retail sends all four failures to one return-0 block and keeps `beq` + `addiu $v0,$zero,1` where this cc1 branches forward and folds the last test into a boolean |
| [`func_L00_002D3EA8`](levels/func_L00_002D3EA8.c) | 136 | Candidate is 132 bytes; every instruction matches but one, this cc1 fills the `beqz` delay slot with `daddu $a0,$sp,$zero` and sinks the `sq`, retail leaves a `nop` and keeps `lq`/`sq` adjacent |
| [`func_L00_002D3D98`](levels/func_L00_002D3D98.c) | 268 | Every instruction matches in shape; the body differs on the 2nd/3rd parameter allocation ($s2/$s1 against retail's $s1/$s2), on the scale/first-conversion FP pair ($f20/$f21 against $f21/$f20) and on the block order of the two symmetrical mode handlers. A switch form reaches 35 of 67 words, an if form 47 |
| [`func_L00_002D5098`](levels/func_L00_002D5098.c) | 188 | ONE word from 27 of 27: the whole body matches with the `mode(TI)` probe, the second `swc1 $f0,0x8($sp)` is the selector's float return written back, and only the closing `sq` differs - retail `sq $v0,0x0($s1)` against our folded `sq $v0,0x80($s0)`. Eight spellings of the 16-byte destination all fold |
| [`func_L07_00409300`](levels/func_L07_00409300.c) | 172 | Catalogue defect: the row opens with `addiu $sp,$sp,0x190` / `nop` (a previous frame teardown); the real body is 164 bytes at 0x00409308 |
| [`func_L08_003D3E00`](levels/func_L08_003D3E00.c) | 180 | Not C: `sq $zero, 0x0($sp)` (word 0x7FA00000, opcode 0x1F) at 0x003D3E5C; the TI128 destination fold is the known blocker |
| [`func_L01_003CE7D8`](levels/func_L01_003CE7D8.c) | 144 | Measured once: candidate is 152 bytes. This cc1 loads *a0 before *a1, uses $f3 for the first value of *a0 where retail keeps $f2, allocates $f20 (a 0x20 frame) to keep the blend across the `div.s` where retail needs no callee-saved FP register, and emits `bc1f` + an explicit `b` where retail inverts to `bc1tl` |
| [`func_L02_003EB098`](levels/func_L02_003EB098.c) | 160 | The 0x7FFF id-list walk transcribed from the matched L01_003CAFE0-g8 skeleton and measured: the object lands in $a3 not $a2, the branch-likely/null guard becomes `bnel` not `beqz`+`nop`, and this cc1 ROTATES the loop, hoisting the `*(s16 *)(p-1) >= 0` test to the top where retail keeps it at the bottom after a forward `b` to the guard block |
| [`func_L02_003DBF40`](levels/func_L02_003DBF40.c) | 152 | The switch chain matches; this cc1 copies the argument to $a1 (`daddu $a1,$a0,$zero` and back) because it materialises the 0x001A7AFA address in $a0, where retail keeps arg0 in $a0 and uses $v1 for both data addresses. Two instructions, 160 bytes against 152 |
| [`func_L07_003F90E8`](levels/func_L07_003F90E8.c) | 200 | TWO `nop`s from 2 of 2. Needs a `-g8` sibling profile AND `sda` on the declaration for the single `lw $v1,0x2058($gp)`; the four conversions must be explicit func_00283CE0 calls, not casts. Six expression shapes measured - none moves the two `nop`s retail has between the last `sub.s` and the `div.s` |
| [`func_L02_003D4990`](levels/func_L02_003D4990.c) | 184 | Measured once: 132 bytes. This cc1 rotates the `do { i++; state[0x31A] = i; } while (cond);` so the increment sits at the loop head, where retail peels the first iteration and keeps its store in the `bc1f` delay slot; the constants match, but retail also holds (float)state[0x24] in $f2 and state[0x20] in $f1, the other way round |
| [`func_L00_002D5000`](levels/func_L00_002D5000.c) | 152 | Candidate is 144 bytes; retail materialises both 16-byte-copy addresses in registers then `lq`/`sq` at offset 0, this cc1 folds `lq 0x30($sp)` / `sq 0x10($s1)` |
| [`func_L00_00378DE0`](levels/func_L00_00378DE0.c) | 176 | Candidate is 172 bytes; with the limit read before the call and `if (r >= limit)` the dispatch matches, but this cc1 hoists the inner call's `addiu $a0,$zero,0x1129` into the `bnez` delay slot and keeps the base in $a1 where retail keeps $a0 |
| [`func_L01_002F1FD0`](levels/func_L01_002F1FD0.c) | 188 | MMI: the body opens with 0x70001CA9 (opcode 0x1C packed operations), no C can be placed |
| [`func_L04_003C6FE8`](levels/func_L04_003C6FE8.c) | 224 | Candidate is 228 bytes with the `switch` dispatch matching exactly; this cc1 copies the record pointer to $a1 at entry, retail keeps it in $a0 until the call loads 0x3C |
| [`func_L22_003F54B8`](levels/func_L22_003F54B8.c) | 196 | Candidate is 200 bytes; retail uses the annulled conditional store (`beql $v0,$zero,L; swc1` with the negative pi/2 above the branch), this cc1 emits the plain branch and both arms |
| [`func_L22_003F85A0`](levels/func_L22_003F85A0.c) | 160 | Right size; retail keeps the source order of the three byte/half stores and puts the last `daddu $a0,$s0,$zero` in the jal delay slot, this cc1 swaps the `sh` ahead and hoists that move |
| [`func_L01_002F21F0`](levels/func_L01_002F21F0.c) | 276 | Recorded candidate is 244 bytes; size does not match retail. |
| [`func_L01_00378A80`](levels/func_L01_00378A80.c) | 156 | Recorded G8 candidate is 144 bytes; size does not match retail. |
| [`func_L01_00397870`](levels/func_L01_00397870.c) | 224 | Recorded G8 instruction mismatch at +0x88. |
| [`func_L01_00397EB0`](levels/func_L01_00397EB0.c) | 340 | Recorded G8 instruction mismatch at +0x18. |
| [`func_L01_003993C8`](levels/func_L01_003993C8.c) | 324 | Recorded G8 instruction mismatch at +0x4. |
| [`func_L01_003CDD00`](levels/func_L01_003CDD00.c) | 344 | Recorded candidate is 352 bytes; size does not match retail. |
| [`func_L01_003DBB88`](levels/func_L01_003DBB88.c) | 304 | Recorded candidate is 312 bytes; size does not match retail. |
| [`func_L01_003E9CF8`](levels/func_L01_003E9CF8.c) | 324 | Recorded G8 instruction mismatch at +0x0. |
| [`func_L01_0041C890`](levels/func_L01_0041C890.c) | 204 | Recorded G8 candidate is 196 bytes; size does not match retail. |
| [`func_L01_0042A568`](levels/func_L01_0042A568.c) | 380 | Recorded candidate is 372 bytes; size does not match retail. |
| [`func_L01_0042A6F0`](levels/func_L01_0042A6F0.c) | 132 | Build refusal recorded; the exact remaining discrepancy was not recorded. |
| [`func_L01_0042E0A8`](levels/func_L01_0042E0A8.c) | 248 | Recorded candidate is 244 bytes; size does not match retail. |
| [`func_L02_003FCBF0`](levels/func_L02_003FCBF0.c) | 172 | Recorded G8 candidate is 180 bytes; size does not match retail. |
| [`func_L02_004114A0`](levels/func_L02_004114A0.c) | 144 | Recorded link refusal: needs .rodata at its place, which is not C. |
| [`func_L03_002AB5C8`](levels/func_L03_002AB5C8.c) | 148 | Recorded candidate is 144 bytes; size does not match retail. |
| [`func_L03_0038B2E8`](levels/func_L03_0038B2E8.c) | 168 | Recorded instruction mismatch at +0x8. |
| [`func_L03_0038B4E8`](levels/func_L03_0038B4E8.c) | 192 | Recorded candidate is 184 bytes; size does not match retail. |
| [`func_L03_003CACA8`](levels/func_L03_003CACA8.c) | 204 | Recorded G8 candidate is 212 bytes; size does not match retail. |
| [`func_L03_003D18F8`](levels/func_L03_003D18F8.c) | 208 | Recorded G8 candidate is 212 bytes; size does not match retail. |
| [`func_L03_003F71E0`](levels/func_L03_003F71E0.c) | 108 | Semantics and store order match; only register allocation differs ($t0/$t1/$a2/$a3 against $a0/$a2/$a3/$v0). |
| [`func_L04_0046CD68`](levels/func_L04_0046CD68.c) | 192 | Recorded G8 instruction mismatch at +0x34. |
| [`func_L05_00379ED0`](levels/func_L05_00379ED0.c) | 68 | Recorded G8 instruction mismatch at +0x4. |
| [`func_L05_0037B440`](levels/func_L05_0037B440.c) | 248 | Recorded G8 candidate is 224 bytes; size does not match retail. |
| [`func_L05_00416EC0`](levels/func_L05_00416EC0.c) | 192 | Recorded candidate is 188 bytes; size does not match retail. |
| [`func_L05_0041D498`](levels/func_L05_0041D498.c) | 212 | Recorded G8 candidate is 196 bytes; size does not match retail. |
| [`func_L05_00425FE0`](levels/func_L05_00425FE0.c) | 212 | Recorded G8 candidate is 196 bytes; size does not match retail. |
| [`func_L05_00426B08`](levels/func_L05_00426B08.c) | 212 | Recorded G8 candidate is 196 bytes; size does not match retail. |
| [`func_L05_0042BCE0`](levels/func_L05_0042BCE0.c) | 200 | Recorded candidate is 188 bytes. [Alternative field order](levels/alternatives/func_L05_0042BCE0.c) rechecked on 2026-10-15: 188 bytes, 0/4 placements; saved-register set and frame differ. |
| [`func_L05_00432080`](levels/func_L05_00432080.c) | 208 | Recorded candidate is 200 bytes. [Indexed-loop alternative](levels/alternatives/func_L05_00432080.c) rechecked on 2026-10-15: 200 bytes, 0/4 placements; store scheduling and loop control differ. |
| [`func_L05_004424B8`](levels/func_L05_004424B8.c) | 168 | Recorded candidate is 180 bytes. [Four-argument call alternative](levels/alternatives/func_L05_004424B8.c) rechecked on 2026-10-15: 168 bytes, 0/4 placements; first instruction difference at +0x14. |
| [`func_L05_00443C18`](levels/func_L05_00443C18.c) | 240 | Recorded candidate is 232 bytes; size does not match retail. |
| [`func_L05_00445F90`](levels/func_L05_00445F90.c) | 160 | Recorded candidate is 164 bytes; size does not match retail. |
| [`func_L07_003EF1E0`](levels/func_L07_003EF1E0.c) | 112 | Recorded candidate is 104 bytes; size does not match retail. |
| [`func_L07_003F68F0`](levels/func_L07_003F68F0.c) | 108 | Recorded G8 candidate is 128 bytes; size does not match retail. |
| [`func_L07_003F6CF0`](levels/func_L07_003F6CF0.c) | 208 | Recorded G8 candidate is 220 bytes; size does not match retail. |
| [`func_L07_003F8780`](levels/func_L07_003F8780.c) | 164 | Recorded G8 instruction mismatch at +0x70. |
| [`func_L07_0040F6B8`](levels/func_L07_0040F6B8.c) | 164 | Recorded G8 instruction mismatch at +0x30. |
| [`func_L11_00340500`](levels/func_L11_00340500.c) | 72 | Recorded instruction mismatch at +0x8. |
| [`func_L11_003F2C28`](levels/func_L11_003F2C28.c) | 240 | Recorded G8 candidate is 252 bytes; size does not match retail. |
| [`func_L11_004395C8`](levels/func_L11_004395C8.c) | 116 | Recorded G8 instruction mismatch at +0x4. |
| [`func_L11_0044C180`](levels/func_L11_0044C180.c) | 160 | Recorded candidate is 156 bytes; size does not match retail. Volatile quadword-store semantics need review. |
| [`func_L11_0044C268`](levels/func_L11_0044C268.c) | 192 | Recorded candidate is 164 bytes; size does not match retail. |
| [`func_L12_00300138`](levels/func_L12_00300138.c) | 108 | Recorded candidate is 100 bytes; size does not match retail. |
| [`func_L12_00388038`](levels/func_L12_00388038.c) | 84 | Recorded G8 instruction mismatch at +0x4. |
| [`func_L13_00409840`](levels/func_L13_00409840.c) | 348 | Recorded candidate is 344 bytes; size does not match retail. |
| [`func_L13_0040A3B0`](levels/func_L13_0040A3B0.c) | 208 | Recorded instruction mismatch at +0x0. |
| [`func_L13_0040DE50`](levels/func_L13_0040DE50.c) | 228 | Recorded G8 instruction mismatch at +0xc. |
| [`func_L13_0041BA60`](levels/func_L13_0041BA60.c) | 128 | Recorded candidate is 124 bytes; size does not match retail. |
| [`func_L14_003DE310`](levels/func_L14_003DE310.c) | 80 | 8; right size, two words differ on branch sense: `bgez`/`bltz` direction and `c.ole.s $f0,$f1` against `c.olt.s $f1,$f0`. |
| [`func_L14_003DE908`](levels/func_L14_003DE908.c) | 136 | Recorded candidate is 132 bytes; size does not match retail. |
| [`func_L14_00423B30`](levels/func_L14_00423B30.c) | 164 | Recorded candidate is 140 bytes; size does not match retail. |
| [`func_L14_00426E60`](levels/func_L14_00426E60.c) | 196 | Recorded G8 instruction mismatch at +0x30. |
| [`func_L15_0042FD40`](levels/func_L15_0042FD40.c) | 148 | Recorded G8 instruction mismatch at +0xc. |
| [`func_L15_00432008`](levels/func_L15_00432008.c) | 164 | Recorded G8 candidate is 176 bytes; size does not match retail. |
| [`func_L18_0042F688`](levels/func_L18_0042F688.c) | 220 | Recorded candidate is 196 bytes; size does not match retail. |
| [`func_L18_0042FAA8`](levels/func_L18_0042FAA8.c) | 56 | Recorded G8 candidate is 60 bytes; size does not match retail. |
| [`func_L18_00432630`](levels/func_L18_00432630.c) | 100 | Recorded candidate is 92 bytes; size does not match retail. |
| [`func_L18_00433088`](levels/func_L18_00433088.c) | 220 | Recorded G8 candidate is 216 bytes; size does not match retail. |
| [`func_L18_00434FC0`](levels/func_L18_00434FC0.c) | 288 | Recorded instruction mismatch at +0xa8. |
| [`func_L18_00437AA0`](levels/func_L18_00437AA0.c) | 260 | Recorded G8 candidate is 248 bytes; size does not match retail. |
| [`func_L18_00439FC8`](levels/func_L18_00439FC8.c) | 332 | Recorded instruction mismatch at +0xec. |
| [`func_L18_0043F308`](levels/func_L18_0043F308.c) | 288 | Recorded candidate is 280 bytes; size does not match retail. |
| [`func_L20_002B7A48`](levels/func_L20_002B7A48.c) | 192 | Recorded candidate is 180 bytes; size does not match retail. |
| [`func_L20_0036E720`](levels/func_L20_0036E720.c) | 92 | Recorded candidate is 88 bytes; size does not match retail. |
| [`func_L20_0039D7B0`](levels/func_L20_0039D7B0.c) | 204 | Recorded candidate is 192 bytes; size does not match retail. |
| [`func_L20_003C0350`](levels/func_L20_003C0350.c) | 216 | Recorded instruction mismatch at +0x6c. |
| [`func_L20_00449AE8`](levels/func_L20_00449AE8.c) | 200 | Recorded G8 instruction mismatch at +0x4. |
| [`func_L20_00449FD0`](levels/func_L20_00449FD0.c) | 308 | Recorded G8 candidate is 300 bytes; size does not match retail. |
| [`func_L20_0044A838`](levels/func_L20_0044A838.c) | 180 | Recorded G8 candidate is 184 bytes; size does not match retail. |
| [`func_L20_0044AA88`](levels/func_L20_0044AA88.c) | 224 | Recorded G8 candidate is 236 bytes; size does not match retail. |
| [`func_L22_002A8B98`](levels/func_L22_002A8B98.c) | 172 | Recorded instruction mismatch at +0x4. |
| [`func_L22_002A8CE0`](levels/func_L22_002A8CE0.c) | 184 | Recorded candidate is 168 bytes; size does not match retail. |
| [`func_L22_003D0840`](levels/func_L22_003D0840.c) | 60 | 4; matches size and every other word, but the parameter copy is `daddu $v1,$a0,$zero` against retail's `$a3`. |
| [`func_L22_0040DD20`](levels/func_L22_0040DD20.c) | 32 | Recorded instruction mismatch at +0x8. |
| [`func_L26_003DC128`](levels/func_L26_003DC128.c) | 124 | Recorded G8 instruction mismatch at +0x0. |

## Level functions

| Function | Retail size | Remaining differences |
| --- | ---: | --- |
| [`func_L00_002B1F90`](levels/func_L00_002B1F90.c) | 400 | 360-byte draft; 0/27 places. Seven-slot callback/state loop remains approximate. |
| [`func_L00_002B2120`](levels/func_L00_002B2120.c) | 220 | 212-byte draft; 0/27 places. State/predicate paths remain 8 bytes short after four source forms. |
| [`func_L00_002B2200`](levels/func_L00_002B2200.c) | 280 | G8 candidate is 304 bytes; 0/27 places. Alternate switch forms grew the body; jump-table linking was not verified. |
| [`func_L00_002B2A40`](levels/func_L00_002B2A40.c) | 400 | 364-byte draft; 0/16 places. Global-state setup and mode-one counter paths remain approximate. |
| [`func_L00_002B30E8`](levels/func_L00_002B30E8.c) | 348 | 296-byte draft; 0/27 places. Counter-table and progress updates remain 52 bytes short. |
| [`func_L00_002B35A0`](levels/func_L00_002B35A0.c) | 320 | 244-byte draft; 0/27 places. Approximate animation update; substantial instructions remain unmatched. |
| [`func_L00_002B4208`](levels/func_L00_002B4208.c) | 208 | 216-byte draft; 0/27 places. Argument signature and helper order remain uncertain. |
| [`func_L00_002B4308`](levels/func_L00_002B4308.c) | 336 | 316-byte draft; 0/27 places. Threshold literal and vector/result flow remain uncertain. |
| [`func_L00_002B47A8`](levels/func_L00_002B47A8.c) | 344 | 260-byte draft; 0/27 places. Signature is provisional; expected quadword copies and the retail 0x60 frame are missing. |
| [`func_L00_002B75A0`](levels/func_L00_002B75A0.c) | 372 | 360-byte draft; 0/27 places. Float/vector update remains 12 bytes short. |
| [`func_L00_002EDC90`](levels/func_L00_002EDC90.c) | 40 | G8 candidate is 44 bytes; 0/27 places. Separate multiply/add does not reproduce the retail COP1 accumulation sequence. |
| [`func_L00_002EDCC8`](levels/func_L00_002EDCC8.c) | 40 | G8 candidate is 44 bytes; 0/27 places. Separate multiply/add does not reproduce the retail COP1 accumulation sequence. |
| [`func_L00_002EDD00`](levels/func_L00_002EDD00.c) | 40 | G8 candidate is 44 bytes; 0/27 places. Separate multiply/add does not reproduce the retail COP1 accumulation sequence. |
| [`func_L00_002FC7A8`](levels/func_L00_002FC7A8.c) | 468 | G8 candidate is 464 bytes; base lifetime, hoisted count and register allocation differ. |
| [`func_L00_002FCBE8`](levels/func_L00_002FCBE8.c) | 492 | 452-byte candidate; base lifetime and repeated loop references differ. |
| [`func_L00_0031B9F8`](levels/shared/func_L00_0031B9F8.c) | 164 | 160 bytes with 40 of 41 words byte-identical (wave 7, 2026-10-16), registers included; the one missing word is the load-delay `nop` of `lwc1 $f12,0x8($s0) ; div.s`, the wall recorded in docs/COMPILER-NOTES.md (cc1 emits the `#nop` marker, the object drops it). The body needs the second 0x00283CE0 call to take `(index*index + index) >> 1`, which the R5900 emits as the three-operand `mult $v0,$s2,$s2` (rd = low product). Six head spellings measured. |
| [`func_L00_0031D670`](levels/shared/func_L00_0031D670.c) | 152 | Exact size and every instruction present (wave 7, 2026-10-16) once the two 16-byte copies use `__attribute__((mode(TI)))` - that is what emits retail's `lq`/`sq` (a plain 16-byte struct copy of the same body emits ldl/ldr + sdl/sdr and comes out 188; the f32-array spell comes out 140). Left: the callee-saved pair is swapped - retail copies the destination into `$s0` at +0x28 and materialises the right operand's address in the first call's delay slot (`addiu $s1,$sp,0x10`), here those two land in each other's registers and every store shifts one call later. ~15 spellings measured. |
| [`func_L00_0031B9F8`](levels/shared/func_L00_0031B9F8.c) | 164 | 172-byte float/index calculation candidate; 8 bytes larger; compile profile not recorded. |
| [`func_L00_0031D670`](levels/shared/func_L00_0031D670.c) | 152 | 188-byte four-component interpolation attempt with local vector copies; 36 bytes larger. |
| [`func_L00_00338E90`](levels/func_L00_00338E90.c) | 292 | 296-byte candidate; quadword copies are present, but entry scheduling and saved-register allocation differ. |
| [`func_L00_00354DF8`](levels/func_L00_00354DF8.c) | 272 | G8 candidate is 284 bytes; pointer/base allocation differs at the corrected entry. |
| [`func_L00_0035F640`](levels/func_L00_0035F640.c) | 148 | Exact-size candidate; branch layout at +0x20 and store order differ. Overlay data names corrected; bindings still need rechecking. |
| [`func_L00_0035F7E8`](levels/func_L00_0035F7E8.c) | 112 | 116-byte table-search candidate; caller-saved register allocation and table-base shape differ. |
| [`func_L00_00368F58`](levels/func_L00_00368F58.c) | 176 | Earlier 180-byte candidate differed in float allocation/scheduling. Retained draft has corrected symbolic global stores; placement and bindings need rechecking. |
| [`func_L00_0036EF40`](levels/func_L00_0036EF40.c) | 40 | 36-byte polling attempt; retail uses the assembler temporary as a working register, extra nops and a live LUI in the return delay slot. No assembly classification claimed. |
| [`func_L00_0037FF98`](levels/func_L00_0037FF98.c) | 188 | 156-byte candidate; 0/27 places. Two-stage table scan and GP-load scheduling remain different. |
| [`func_L00_0037FA90`](levels/func_L00_0037FA90.c) | 336 | 336-byte candidate, 83 of 84 instructions: only the quadword copy at +0x88 swaps its register pair (retail `la $v1; lq $v0`, this cc1 `la $v0; lq $v1`). Prologue schedule, the in-place-decremented D_L00_001B8FF0 pointer and the whole tail match. |
| [`func_L00_00387BB0`](levels/func_L00_00387BB0.c) | 248 | Not attempted: MMI-family body (two lq/sq pairs and an opcode-0x1C word at +0x4C). Call inventory and prologue in the near-miss; the 0x1C-group instruction must be identified first. |
| [`func_L00_00384F28`](levels/func_L00_00384F28.c) | 276 | Not attempted: strided scan (stride 0x100) with two arms, virtual calls and four per-level data addresses read both absolutely and gp-relative. Full instruction map in the near-miss; shape and the sda/ref_ binding are named. |
| [`func_L00_003825C8`](levels/func_L00_003825C8.c) | 504 | Not attempted: lq/sq in the body (tour-2 exclusion). |
| [`func_L00_00382910`](levels/func_L00_00382910.c) | 472 | Not attempted: lq/sq x3 and MMI-group instructions (tour-2 exclusion). |
| [`func_L00_00382AE8`](levels/func_L00_00382AE8.c) | 260 | Not attempted: lq/sq pair at +0xBC (tour-2 exclusion). |
| [`func_L00_00384070`](levels/func_L00_00384070.c) | 472 | Not attempted: seven lq/sq copies (tour-2 exclusion). |
| [`func_L00_00385040`](levels/func_L00_00385040.c) | 352 | Not attempted: lq/sq x3 plus MMI-group (tour-2 exclusion). |
| [`func_L00_00384E08`](levels/func_L00_00384E08.c) | 284 | Exact size; live-range structure differs: the retail keeps the pair-table base gp-relative in $s4 and object->f68 in $s3 across both blocks (0x50 frame), this candidate is one register short (0x40, absolute table base). |
| [`func_L00_003827E0`](levels/func_L00_003827E0.c) | 304 | 524-byte candidate; switch/table-loop control flow, register allocation and state-2 stack quadword setup differ. |
| [`func_L00_00382BF0`](levels/func_L00_00382BF0.c) | 248 | Exact-size candidate; 0/3 places. First recorded mismatch +0x3C involves float spills/argument order; GP global uses mixed access forms. |
| [`func_L00_00383300`](levels/func_L00_00383300.c) | 1620 | G0 candidate is 1576 bytes; recorded first instruction mismatch +0x8. Behavior and linked-image equality remain unverified. |
| [`func_L00_00383958`](levels/func_L00_00383958.c) | 568 | 528-byte candidate; dead TI local copies disappear, shrinking the frame and changing registers. |
| [`func_L00_00384CA8`](levels/func_L00_00384CA8.c) | 152 | 148-byte candidate; 0/27 places. Saved-register allocation differs and one instruction is absent. |
| [`func_L00_00384D40`](levels/func_L00_00384D40.c) | 196 | G8 candidate is 192 bytes; 0/27 places. Object/child register allocation and instruction scheduling differ. |
| [`func_L00_003851A0`](levels/func_L00_003851A0.c) | 256 | 268-byte interpolation/packing candidate; 0/27 places. Extracted unused declarations were repaired; helper prototype restored; candidate not remeasured. |
| [`func_L00_003852A0`](levels/func_L00_003852A0.c) | 204 | Exact-size candidate; 0/27 places. Helper argument/global-load ordering differs; source and worker notes disagree on the exact offset. |
| [`func_L00_00387370`](levels/func_L00_00387370.c) | 148 | G8 candidate is 152 bytes; 0/27 places. Register-save ordering differs and one instruction is extra. |
| [`func_L00_00389E18`](levels/func_L00_00389E18.c) | 148 | G8 candidate has exact size; 0/27 places. First recorded mismatch +0xC is s1/ra save ordering. |
| [`func_L00_0038B6F8`](levels/func_L00_0038B6F8.c) | 252 | 240-byte candidate; 0/27 places. State-gating/transition draft remains 12 bytes short. |
| [`func_L00_0038D750`](levels/func_L00_0038D750.c) | 1044 | G8 candidate is 1036 bytes; recorded first instruction mismatch +0x4. Behavior and linked-image equality remain unverified. |
| [`func_L00_0038DB68`](levels/func_L00_0038DB68.c) | 776 | 740-byte candidate; TI zero materialization and collision-base lifetime differ. |
| [`func_L00_00393148`](levels/func_L00_00393148.c) | 5868 | G0 candidate is 5620 bytes; recorded first instruction mismatch +0x4. Behavior and linked-image equality remain unverified. |
| [`func_L00_003B9CD8`](levels/func_L00_003B9CD8.c) | 2444 | G0 candidate is 2388 bytes; recorded first instruction mismatch +0x0. Behavior and linked-image equality remain unverified. |
| [`func_L00_003C5B88`](levels/func_L00_003C5B88.c) | 1804 | G0 candidate is 1740 bytes; recorded first instruction mismatch +0x4. Behavior and linked-image equality remain unverified. |
| [`func_L00_003CD160`](levels/func_L00_003CD160.c) | 1268 | G0 candidate is 1188 bytes; recorded first instruction mismatch +0x4C. Behavior and linked-image equality remain unverified. |
| [`func_L00_003CD658`](levels/func_L00_003CD658.c) | 644 | 632-byte candidate; TI zero, register scheduling and two pre-division nops differ. |
| [`func_L00_003CD8E0`](levels/func_L00_003CD8E0.c) | 2456 | G8 candidate is 2444 bytes; recorded first instruction mismatch +0x0. Behavior and linked-image equality remain unverified. |
| [`func_L00_003CE278`](levels/func_L00_003CE278.c) | 2516 | G0 candidate is 2444 bytes; recorded first instruction mismatch +0x0. Behavior and linked-image equality remain unverified. |
| [`func_L00_003CF438`](levels/func_L00_003CF438.c) | 248 | 244-byte candidate; compiler removes a dead ADDIU retained in the retail BEQZ delay slot, shifting the body by one word. |
| [`func_L00_003CF530`](levels/func_L00_003CF530.c) | 220 | 216-byte candidate; compiler removes a dead ADDIU retained in the retail BEQZ delay slot, shifting the body by one word. |
| [`func_L00_003CF610`](levels/func_L00_003CF610.c) | 924 | 920-byte candidate; bank absolute/GP access and TI-copy reloads differ. |
| [`func_L00_003CFB00`](levels/func_L00_003CFB00.c) | 1288 | G0 candidate is 1260 bytes; recorded first instruction mismatch +0x0. Behavior and linked-image equality remain unverified. |
| [`func_L00_003D0008`](levels/func_L00_003D0008.c) | 2312 | G0 candidate is 2284 bytes; recorded first instruction mismatch +0x0. Behavior and linked-image equality remain unverified. |
| [`func_L00_003D0910`](levels/func_L00_003D0910.c) | 2376 | G0 candidate is 2348 bytes; recorded first instruction mismatch +0x4. Behavior and linked-image equality remain unverified. |
| [`func_L00_003D54B0`](levels/func_L00_003D54B0.c) | 4424 | G8 candidate is 4404 bytes; 0/2 places. TI offsets fold into LQ/SQ, changing pointer materialization and saved-register allocation. |
| [`func_L00_003DA548`](levels/func_L00_003DA548.c) | 636 | Exact-size candidate; 17 non-relocation instruction words differ (20 bytes). Position/color s1/s2 allocation and two 64-bit packet-store positions differ. |
| [`func_L00_003EACC0`](levels/func_L00_003EACC0.c) | 568 | G0 candidate is 560 bytes; TI copies fold +0x10 offsets instead of materializing zero-offset bases. Object/state allocation and FPU scheduling also differ. |
| [`func_L00_003EE288`](levels/func_L00_003EE288.c) | 564 | G0 candidate has exact size; TI copy addressing, quadword register and callee-saved/FPU allocation differ; first recorded mismatch +0x28. |
| [`func_L00_0040D648`](levels/func_L00_0040D648.c) | 1472 | 1460-byte candidate; nibble-path tail merging, object/state allocation and phase scheduling differ after typed-field variants. |
| [`func_L00_0040EAA0`](levels/func_L00_0040EAA0.c) | 1540 | Exact-size candidate; frame is 0x60 instead of 0x50. Retained bitset base or shift-one and a spilled flag change mask lowering and branch scheduling. |
| [`func_L00_00412E68`](levels/func_L00_00412E68.c) | 400 | G8 candidate is 384 bytes; TI zero folds to SQ zero instead of POR/SQ, and stack-copy offsets fold away two retained pointers. |
| [`func_L00_00412FF8`](levels/func_L00_00412FF8.c) | 300 | G8 candidate is 276 bytes; table-index setup remains unresolved. Overlay data names corrected; helper argument/layout assumptions remain provisional. |
| [`func_L00_00413128`](levels/func_L00_00413128.c) | 364 | 208-byte first attempt; incomplete table-driven paths and quadword handling. Treat semantics and helper prototypes as provisional. |
| [`func_L00_00413298`](levels/func_L00_00413298.c) | 188 | 140-byte initialization attempt; two array-loop variants produced the same differing instruction/register/store order. |
| [`func_L00_00413870`](levels/func_L00_00413870.c) | 164 | G8 candidate has exact size; first recorded mismatch +0x54 is helper-call argument placement in the delay slot. |
| [`func_L00_004143D0`](levels/func_L00_004143D0.c) | 368 | 360-byte candidate; saved-register ordering and arithmetic scheduling differ. |
| [`func_L00_00416F68`](levels/func_L00_00416F68.c) | 316 | 376-byte aggregate-vector candidate; retail quadword transfers and saved-register layout are not reproduced. |
| [`func_L00_004170A8`](levels/func_L00_004170A8.c) | 368 | 380-byte candidate; transform/stack quadword transfers and scheduling remain different. |
| [`func_L00_00417700`](levels/func_L00_00417700.c) | 360 | 368-byte candidate; 24-entry loop shape, register allocation and instruction scheduling differ. |
| [`func_L00_0041A490`](levels/func_L00_0041A490.c) | 108 | 96-byte candidate; modulo-16 arithmetic, register allocation and return scheduling remain different. |
| [`func_L00_0041A500`](levels/func_L00_0041A500.c) | 192 | G8 candidate has exact size; first recorded mismatch +0x4 is prologue/register allocation and placement of -1. |
| [`func_L00_004263B8`](levels/func_L00_004263B8.c) | 188 | G8 candidate has exact size; 0/27 places. First recorded mismatch +0x20 is table-base register allocation (a2 versus retail a1). |
| [`func_L04_0046D6A8`](levels/func_L04_0046D6A8.c) | 192 | Not attempted: third place the opcode-0x1C word `0x700014A9` appears in lot 6-11 (with func_L16_00413700 and func_L23_0042AD10), always inside a 16-byte lq/sq stack copy and never with a live general register - it cannot be writing `$v0`, which func_L16_00413700 still needs afterwards. Full body map in the near-miss. ASM_FUNC candidate. |
| [`func_L16_00413700`](levels/func_L16_00413700.c) | 136 | Not attempted: opcode-0x1C word `0x700014A9` at +0x24 (the same word as func_L23_0042AD10). The surrounding lq/sq copies, the {x,y,1.0f,w} stack vector and all four calls are mapped in the near-miss. ASM_FUNC candidate. |
| [`func_L19_002A88C8`](levels/func_L19_002A88C8.c) | 148 | Not attempted: `.word 0x00651818` at +0x68 is a SPECIAL-funct-0x18 multiply whose product the next delay slot overwrites (dead result, no C spelling); the rest is a table lookup, mapped word by word in the near-miss. ASM_FUNC candidate. |
| [`func_L23_0041E538`](levels/func_L23_0041E538.c) | 12 | Catalogue defect: `sltiu $v0,$v0,1` / nop / `sltu $v0,$zero,$v0` at 0x41E538 reads `$v0` (never `$a0`) and has no `jr $ra`; it sits between func_L23_0041E418's epilogue and func_L23_0041E548. No C can be placed. |
| [`func_L23_004277B0`](levels/func_L23_004277B0.c) | 52 | Catalogue defect: the entry is 8 bytes early - `addiu sp,sp,0x190` / nop at 0x4277B0 are the previous body's restore (its `jr $ra` is at 0x4277AC) and the 44-byte body starts at 0x4277B8. That body is the two-call wrapper in the near-miss, so a `LEVEL_ENTRY_OFFSETS{(23, 0x004277B0): 8}` entry unblocks it; no C can cover the leading two words. |
| [`func_L23_0042AD10`](levels/func_L23_0042AD10.c) | 128 | Not attempted: opcode-0x1C word `0x700014A9` at +0x2C, plus a 16-byte stack copy emitted as sq/lq around three swc1 stores. The arm/store/call sequence is mapped in the near-miss. ASM_FUNC candidate. |
| [`func_L02_003FC1B0`](levels/func_L02_003FC1B0.c) | 152 | Exact size candidate is 156 bytes; the retail tree tests the lowest case first (0xA44, pivot 0xA45) with arm order 0xA44, 0x9CA, 0xC3B, 0xB22, 0xC3E, where this cc1 opens on 0xB22 and lays the arms out of line. Eight switch/if-chain shapes measured, first difference already at +0x4. |
| [`func_L02_003FC9C0`](levels/func_L02_003FC9C0.c) | 188 | Candidate is 208 bytes; body read end to end (state-8 spawn through func_L00_002D70A0, else close through func_L00_002D7140, then one of three float triples at 0x001B1234..0x001B1254 written to the 0xC0 object). This cc1 lays the arms the other way (`bnel` where retail has `beqz`). |
| [`func_L12_004254A8`](levels/func_L12_004254A8.c) | 104 | Exact size, 23 of 26 words: this cc1 puts the guard's `-1` in `$a0` and the field in `$v1` where retail uses `$v1` and `$a1`. Twelve spellings of the guard measured (plain comparison, named local, indexed pointer, constant on the left, goto-shaped, inverted) - all give the same cluster. Both level addresses must be written as file symbols (`D_L12_00189EA0`, `D_L12_001395DB`), not literals. |
| [`func_L23_0041E5B0`](levels/func_L23_0041E5B0.c) | 168 | Not placed: needs retail's single 16-byte `lq`/`sq` argument copy. A plain struct gives ldl/ldr/sdl/sdr, an `aligned(16)` struct gives two ld/sd pairs. Everything else (six-argument shape, both handler calls, the `$gp` store at 0x001B13D4, the `(a1 & 4) \|\| a5` tail) is settled. |
| [`func_L23_002BBC50`](levels/func_L23_002BBC50.c) | 1296 | 1280 bytes: everything but two 16-byte stores to the vector at `0($sp)` is retail's. The copy's offset folds and its neighbours are scheduled above it (3 words short), and the zero store goes into a branch's delay slot (1 word short). Retail's copies behave like inline-asm instructions. |
| [`func_L23_0035CE90`](levels/func_L23_0035CE90.c) | 288 | Catalogue defect: the row starts 16 bytes early, in the padding after the previous function's `jr $ra` at 0x35CE84 (`sw $t3,0x40($v1); nop; addiu $sp,$sp,0x20; nop`); the body is 0x35CEA0-0x35CFAF, 272 bytes, and this C, for the `-g8` sibling, gives 264 (two `nop`s and some order) |
| [`func_L23_00394630`](levels/func_L23_00394630.c) | 776 | 744 bytes: the quadword-copy pointers are folded, which saves `$s5`/`$s3` (a 0xB0 frame against 0xC0); the level-23 twin of func_L00_0038DB68 |
| [`func_L23_003BFE90`](levels/func_L23_003BFE90.c) | 300 | 296 bytes: retail zeroes a quadword with `por $v0,$zero,$zero` before its `sq`, this cc1 stores `$zero`; the save order also differs |
| [`func_L23_003C0A60`](levels/func_L23_003C0A60.c) | 532 | Exact size, 73 words differ: register roles, and a register copy where retail has three fresh `addiu $a0,$sp,0x20` |
| [`func_L23_003C0C78`](levels/func_L23_003C0C78.c) | 380 | Exact size: the entry block differs (27 words, the quadword-copy wall as in func_L23_00418DC0), and `i` and the object pointer swap `$s2`/`$s3` (about 12 words, allocation only) |
| [`func_L23_003C1420`](levels/func_L23_003C1420.c) | 692 | Same size by coincidence, 55 words differ: one of retail's two `por` is missing, there is an extra `movz`, and the `$s2`/`$s3` pointers are swapped |
| [`func_L23_003C16D8`](levels/func_L23_003C16D8.c) | 644 | Same size by coincidence, 39 words differ: no `por` in block 2, an extra `$s2` save, and `obj` in `$s2` (retail `$s0`) |
| [`func_L23_003C1B58`](levels/func_L23_003C1B58.c) | 812 | 808 bytes: all eight cases and the tail are retail's but for the folded quadword copies; its switch also needs a jump table in `.rodata`, which the build cannot place |
| [`func_L23_003C1E88`](levels/func_L23_003C1E88.c) | 668 | Exact size, 21 words differ from the first quadword copy on; with that copy written as an inline-asm `lq`/`sq` macro, which `src/` does not allow, it is 1 of 1 |
| [`func_L23_00418160`](levels/func_L23_00418160.c) | 652 | 632 bytes: six quadword copies fold, and the callee-saved registers follow from that |
| [`func_L23_004183F0`](levels/func_L23_004183F0.c) | 608 | Exact size: only the quadword copy `D_L23_001B3800 = v1` differs: retail's `lq $v0,0($s0)`/`sq` pair sits right after the call, this cc1 emits `lq $v0,0x10($sp)` and splits the pair |
| [`func_L23_00418BC8`](levels/func_L23_00418BC8.c) | 504 | 496 bytes: the quadword-copy wall and register numbering |
| [`func_L23_00418DC0`](levels/func_L23_00418DC0.c) | 360 | Exact size, exact from +0x84 to the end; the entry block (the register saves, the quadword copy and the first call's setup, 28 words) comes in another order, the quadword-copy wall. `func_L23_00419088` matches with an `s32` parameter, so the call can be prototyped |
| [`func_L23_004190C8`](levels/func_L23_004190C8.c) | 1520 | 1448 bytes, 241 of 380 instructions aligned: the quadword-copy wall in the default case's second loop (retail keeps the pointers in registers), which leaves a 0x170 frame against 0x180; it belongs in the `-g8` sibling, since the `-G0` file defines its callee `func_L23_00419088` with an `s16` parameter |
| [`func_L23_0041AFE8`](levels/func_L23_0041AFE8.c) | 416 | 396 bytes: the quadword-copy wall (retail's `lq`/`sq` go through materialised addresses, with a second `lq` from the same source) and retail's annulled `bnel` on the handle test |
| [`func_L23_0041B6D8`](levels/func_L23_0041B6D8.c) | 540 | 536 bytes: one quadword copy is folded where retail materialises both pointers, and the argument moves of `func_00282F20(A, A, 1.0f)` come in another order |
| [`func_L23_0041B9F0`](levels/func_L23_0041B9F0.c) | 1148 | 1140 bytes, 22 of 287 words differ: the three copies to `D_L23_001B36E0` are scheduled apart where retail keeps `lui`/`addiu`/`lq`/`sq` together, and gcse puts `st+0x140` on a split critical edge where retail computes it in the first block (the 8 bytes). |
| [`func_L23_0041C0E8`](levels/func_L23_0041C0E8.c) | 1168 | 1148 bytes, 242 of 292 instructions aligned, for the `-g8` sibling: gcse (PRE) keeps `D_L23_001B1698` in a register across stores to other `$gp` words where retail reloads it after every join (a `volatile` read gives retail's blocks, which `src/` does not allow; cc1 never treats two `$gp` symbols as aliasing) |
| [`func_L23_0041C578`](levels/func_L23_0041C578.c) | 312 | 308 bytes, needs `-G8`: retail zeroes the quadword with `por $v0,$zero,$zero` (MMI `0x700014A9`) before the `sq`, this cc1 stores `$zero`; with the `por`, four words of the then-branch are still reordered |
| [`func_L23_0041C6B0`](levels/func_L23_0041C6B0.c) | 292 | Exact size, two words: the if-body is `lq; mov.s` where retail has `mov.s; lq` (the quadword copy does not move in retail) |
| [`func_L23_0041CA58`](levels/func_L23_0041CA58.c) | 564 | Exact size: the destination address `addiu $t2,$sp,0xE0` stays in the loop's bottom block instead of the preheader, retail's `daddu $t3,$s5,$zero` is missing, and the bottom block's order and the registers differ (the loop shape that reaches this is in the file's header) |
| [`func_L23_0041D0A0`](levels/func_L23_0041D0A0.c) | 336 | 332 bytes: the entry copy's `+0x10` folds into the `lq` (the constant-offset wall), and the callee-saved registers are permuted |
| [`func_L23_00422540`](levels/func_L23_00422540.c) | 292 | 284 bytes: retail keeps the second local's address in `$s2` and reads it with `lq 0($s2)`; this cc1 folds it to `0x10($sp)` (the quadword-copy wall), and the allocation follows |
| [`func_L23_00422668`](levels/func_L23_00422668.c) | 300 | 296 bytes: the frame, the locals and every call are retail's; in the else branch retail copies with `lq $v0,0($s1)` after `beqz; nop`, and this cc1 folds that load into a `beql` delay slot, which shifts the saved registers by one |
| [`func_L23_00422798`](levels/func_L23_00422798.c) | 532 | 528 bytes, 3 words differ: retail zeroes the local {0,0,0,1} with `por $v0,$zero,$zero` and `sq`, which this cc1's zero-TI fold turns into `sq $zero`; the rest is exact |
| [`func_L23_00422E08`](levels/func_L23_00422E08.c) | 1380 | Retail's size, 63 of 345 words differ: retail does its four 16-byte copies through registers at offset 0, this cc1 folds every offset, and the registers that follow differ (`obj` and the stack vector swap `$s5`/`$s4`). Needs the `-G8` sibling. |
| [`func_L23_00425568`](levels/func_L23_00425568.c) | 392 | Exact size, 5 words differ, all in the `j == 4` arm: `$v0`/`$v1` swapped, and retail's `sq; b; nop` with the reload shared at the join (retail's quadword copy is one unsplittable unit, like an inline-asm copy) |
| [`func_L23_004256F0`](levels/func_L23_004256F0.c) | 340 | 332 bytes with retail's single return (the earlier 340-byte body reached the size with two wrong words): the 16-byte copy's store folds into the first call's delay slot, sched1 moves the four stack-argument stores first, and `$s3`/`$s4` swap. Exact in a scratch copy with an inline-asm 16-byte copy macro, which `src/` does not allow. |
| [`func_L23_00425848`](levels/func_L23_00425848.c) | 408 | 388 bytes: retail keeps `obj + 0x10` in `$s0` and `&loc[2]` in `$v1`, with zero offsets, for its three quadword accesses and reloads the quadword after storing it; this cc1 folds the offsets and forwards the stored register |
| [`func_L23_00425AF8`](levels/func_L23_00425AF8.c) | 1516 | 1464 bytes, for a unit of its own built with `-O2 -G8 -ffunction-sections -g`: its four 16-byte copies come out frame-relative and scheduled, where retail keeps pointer operands and schedules nothing into them, and the register allocation that follows from them differs. |
| [`func_L23_00426248`](levels/func_L23_00426248.c) | 900 | 880 bytes: five quadword pairs folded, retail spills `$a2`/`$t2`/`$t3` where this spills `$a3`, and one store order |
| [`func_L23_004265D0`](levels/func_L23_004265D0.c) | 456 | Exact size, 25 words differ: the registers and order of the first block's temporaries (a local-alloc priority); about 350 spellings measured |
| [`func_L23_004280D8`](levels/func_L23_004280D8.c) | 580 | 584 bytes: the first quadword store folds to `sq $v0,0x10($s4)` (retail `sq $v0,0($s2)`), the `obj + 0xD0`/`+ 0xC0` adds are hoisted (one more saved register), and `$f20`/`$f21` are swapped |
| [`func_L23_00428490`](levels/func_L23_00428490.c) | 2152 | Exact size, 519 of 538 instructions aligned, for the `-g8` sibling: one quadword copy comes out in another order (8 words), and the object position after the second loop is spilled to `0xFC($sp)` instead of staying in `$s0`. With the copy written as an inline-asm `lq`/`sq`, which `src/` does not allow, it is 1 of 1 |
| [`func_L23_00429098`](levels/func_L23_00429098.c) | 552 | Exact size (with `a0`/`a2` reused as the +0xC0/+0xD0 pointers), 91 words differ: the quadword wall on the +0x10 store, and a symbol address kept across a call where retail rebuilds it |
| [`func_L23_00429430`](levels/func_L23_00429430.c) | 2192 | Exact size, 540 of 548 instructions aligned, for the `-g8` sibling: only the quadword copy differs (the next call's argument moves are scheduled between its stores, 8 words); with the copy as inline asm it is 1 of 1. The twin of func_L23_00428490, reading globals where that one reads the object's fields |
| [`func_L23_00429DC8`](levels/func_L23_00429DC8.c) | 560 | 552 bytes: the quadword-copy wall and the `por` the zero-TI fold removes; the three loops are retail's |
| [`func_L23_0042ADB0`](levels/func_L23_0042ADB0.c) | 880 | 824 bytes: eight quadword pairs without their pointer `addiu`s, the `por` folded, the `div`/`break`/`nop` order, and a 0x170 frame against 0x190 |

## USA v2.00 trials

`v2.00/`: small functions written against the USA v2.00 release (`SCUS_972.68_v2.00.iso`, see
docs/REGIONS.md), most of them comparing equal there apart from link-dependent fields. Nothing
builds v2.00 yet; many of these functions also exist in v1.01 at other addresses, so they are
starting points for both. `FloatToInt` and the functions like it (an in-place `cvt.w.s $f12,$f12`)
are probably handwritten: no C spelling reproduces them.

## Moved from src/ (2026-10-11)

C carried over from the old layout that compiles but does not link at its places: usually a
level address written as a number, which only fits one level.

- [`func_0011EA38`](core/func_0011EA38.c)
- [`func_00121B18`](core/func_00121B18.c)
- [`func_0012C680`](core/func_0012C680.c)
- [`func_0012C788`](core/func_0012C788.c)
- [`func_0012C878`](core/func_0012C878.c)
- [`func_00130A60`](core/func_00130A60.c)
- [`func_00130A70`](core/func_00130A70.c)
- [`func_00130A80`](core/func_00130A80.c)
- [`func_001317E8`](core/func_001317E8.c)
- [`func_0027F110`](game/func_0027F110.c)
- [`func_00287E98`](game/func_00287E98.c)
- [`func_00288BE8`](game/func_00288BE8.c) â€” recovered in [maintained C](../src/game/00288BB8-g8.c); the earlier draft is retained.
- [`func_00288D70`](game/func_00288D70.c)
- [`func_00290670`](game/func_00290670.c)
- [`func_002985F0`](game/func_002985F0.c) â€” recovered in [maintained C](../src/game/002985F0-g8.c); the earlier draft is retained.
- [`func_00298780`](game/func_00298780.c) â€” recovered in [maintained C](../src/game/00298780-g8.c); the earlier draft is retained.
- [`func_L00_00309AE8`](levels/shared/func_L00_00309AE8.c) â€” recovered in [maintained C](../src/levels/shared/L00_00309AE8-g8.c); the earlier draft is retained.
- [`func_L00_00309B10`](levels/shared/func_L00_00309B10.c) â€” recovered in [maintained C](../src/levels/shared/L00_00309AE8-g8.c); the earlier draft is retained.
- [`func_L00_00309B58`](levels/shared/func_L00_00309B58.c) â€” recovered in [maintained C](../src/levels/shared/L00_00309AE8-g8.c); the earlier draft is retained.
- [`func_L00_00309B80`](levels/shared/func_L00_00309B80.c) â€” recovered in [maintained C](../src/levels/shared/L00_00309AE8-g8.c); the earlier draft is retained.
- [`func_L03_0043ACD8`](levels/shared/func_L03_0043ACD8.c) â€” recovered in [maintained C](../src/levels/shared/L03_003C5B28-g8.c); the earlier draft is retained.
- [`func_002985F0`](game/func_002985F0.c)
- [`func_00298780`](game/func_00298780.c)
- [`func_L00_00309AE8`](levels/shared/func_L00_00309AE8.c)
- [`func_L00_00309B10`](levels/shared/func_L00_00309B10.c)
- [`func_L00_00309B58`](levels/shared/func_L00_00309B58.c)
- [`func_L00_00309B80`](levels/shared/func_L00_00309B80.c)
- [`func_L03_0043ACD8`](levels/shared/func_L03_0043ACD8.c)

## Small level functions, first Haiku trial (2026-10-12)

Left by 16 Haiku workers after a bounded effort each (docs/AGENT_WORKFLOW.md); the note is the
worker's own. Several are not C problems: rows of the catalogue that are not functions, and
handwritten routines.

| Function | Size | What the worker found |
| [`func_L00_00309DB0`](levels/func_L00_00309DB0.c) | 28 | 7 instructions in retail, 12 in ours; no jr ra in the retail bytes (addiu sp,+0x20 / daddu / addiu sp,+0x40), so no C of this shape can equal them. Catalogue size or split is suspect. Stub below is a guess only. |
| [`func_L00_0031BE88`](levels/func_L00_0031BE88.c) | 36 | 28 bytes against retail's 36: retail is a beql on the null test with the 0 result in its delay slot, then a bnez with addiu 1 / daddu 0 tail; the compiler gives beqz or bnel plus sltu, so the branch-likely shape is missing. |
| [`func_L00_0032A088`](levels/func_L00_0032A088.c) | 48 | 1 of 12 instructions differs (27 places, all at +0x10): retail adds the element address as base + index (addu a2,a1); the compiler emits index + base (addu a1,a2). Reordering the operands, a cast, or a base local did not change it. |
| [`func_L00_00341798`](levels/func_L00_00341798.c) | 28 | No attempt: all 7 instructions would differ. The retail bytes at 0x341798 are not code: seven words 0x27bd0030, 0, 0x27bd0050, 0, 0x27bd0080, 0, 0x27bd0020 (addiu sp,sp,imm with nops between), directly after the epilogue of the function end… |
| [`func_L00_0037F790`](levels/func_L00_0037F790.c) | 84 | 84 bytes, 1 instruction differs: retail loads $a0 = 0 again before $a1/$a2 for the second call (the first call's delay slot holds one $a0 = 0 too), so retail has 21 instructions and this has 20; GCC puts that move in the delay slot of the s… |
| [`func_L00_00380058`](levels/func_L00_00380058.c) | 116 | 116 bytes, 3 instructions differ: the loop table pointer and the counter swap registers (retail: pointer in $s0 loaded with lui/addiu, counter in $s1; this gives counter $s0, pointer $s1 via a copy from $v0). Same result with for, do-while … |
| [`func_L00_00384C20`](levels/func_L00_00384C20.c) | 132 | 132 bytes, 4 instructions differ (128 bytes here): the two do-while loops have the right shape, but GCC loads each entry into $a0 and tests it there, while retail loads it into $v0, tests it, then copies it to $a0 in the branch delay slot, … |
| [`func_L00_0042A4E8`](levels/func_L00_0042A4E8.c) | 24 | 0 of 6 instructions match: retail is three `jr $ra; nop` pairs (24 bytes) with no prologue. A single C function cannot emit three bare returns; the catalogue row likely merged three empty stubs. |
| `func_L00_00428EE0` | 32 | no attempt: the bytes start mid-function, probably the tail of its neighbour |
| [`func_L00_0042B6A8`](levels/func_L00_0042B6A8.c) | 32 | 8 instructions, 40 bytes emitted against 32: retail computes the data address gp-relative (`addiu $v0,$gp,-0x1388`) and stores it in the call delay slot; -G0 gives lui/addiu and a frame that saves a0 in $v1. |
| [`func_L00_0042B6C8`](levels/func_L00_0042B6C8.c) | 32 | 8 instructions, 40 bytes emitted against 32: same shape as func_L00_0042B6A8 (`addiu $v0,$gp,-0x13A8`), same gp-relative address problem under -G0. |
| [`func_L00_002EE058`](levels/func_L00_002EE058.c) | 24 | from src/levels/shared/L00_002E5918.c: 6 retail instructions, C compiles to 5 (ld/sd pairs). Missing: retail's two ldc2 quad loads, a COP2 VU0 macro-mode op (.word 0x4BE2086B) and sdc2 quad store, which plain C cannot emit. Not matchable in… |
| [`func_L00_002EE078`](levels/func_L00_002EE078.c) | 24 | from src/levels/shared/L00_002E5918.c: 6 retail instructions, C compiles to 5 (ld/sd pairs). Missing: retail's two ldc2 quad loads, a COP2 VU0 macro-mode op (.word 0x4BE2086F) and sdc2 quad store, which plain C cannot emit. Not matchable in… |
| [`func_L00_002EE290`](levels/func_L00_002EE290.c) | 32 | from src/levels/shared/L00_002E5918.c: no C attempt; 8 retail instructions (ldc2, four COP2 macro-mode ops, mtc1, jr), a plain C body compiles to 2 and cannot emit the VU0 ops. Placeholder only. |
| [`func_L00_003B9290`](levels/func_L00_003B9290.c) | 64 | 72 bytes vs retail 64 (18 instructions vs 16): the loop body matches retail instruction for instruction; the difference is the first load. Retail reads the count straight off $gp (lw a0,0xD8C($gp)); cc1 emits lui+lw of the symbol address. S… |
| [`func_L00_003B60E8`](levels/func_L00_003B60E8.c) | 108 | 108 bytes: prologue, field loads and register use match retail; about 7 loop instructions differ (retail: beql on *w with the s+=0x150 in its delay slot, bgezl with the next *w load in its delay slot). Missing: the source shape that makes G… |
| [`func_L00_003B8158`](levels/func_L00_003B8158.c) | 116 | 88 bytes vs retail 116 (22 instructions vs 29): the control flow and the first call match; missing the else arm, which retail builds as an 8-byte local copied from 0x1AFBC8 with ldl/ldr/sdl/sdr (unaligned doubleword pairs, the struct's type… |
| [`func_L00_003B7908`](levels/func_L00_003B7908.c) | 148 | 156 bytes vs retail 148 (39 instructions vs 37): the loop, the calls and the frame match retail instruction for instruction; the difference is that retail takes the addresses of two $gp-resident globals with one addiu each (addiu s1,$gp,0xB… |
| [`func_L00_0035F9F0`](levels/func_L00_0035F9F0.c) | 24 | near miss: 4 of 6 retail instructions (retail 24 bytes, this 16). Missing the dsll32/dsra32 sign extension of the int parameter before bnel. |
| [`func_L00_0036BE28`](levels/func_L00_0036BE28.c) | 40 | near miss: 9 of 10 instructions (ours 36 bytes, retail 40). Retail loads through $at with hand-placed nops and ends in jr ra + lui ra, 0x7000 (looks like hand asm); plain C gives $v0 and fewer nops. |
| [`func_L00_0036C9AC`](levels/func_L00_0036C9AC.c) | 40 | near miss: 9 of 10 instructions (ours 36 bytes, retail 40). Same wait-loop family as func_L00_0036BE28: retail loads through $at, hand-placed nops, jr ra then lui ra, 0x7000. |
| [`func_L00_0036C9D4`](levels/func_L00_0036C9D4.c) | 40 | near miss: 9 of 10 instructions (ours 36 bytes, retail 40). Same wait-loop family as func_L00_0036BE28: retail loads through $at, hand-placed nops, jr ra then lui ra, 0x7000. |
| [`func_L00_0036D274`](levels/func_L00_0036D274.c) | 40 | near miss: 9 of 10 instructions (ours 36 bytes, retail 40). Same wait-loop family as func_L00_0036BE28: retail loads through $at, hand-placed nops, jr ra then lui ra, 0x7000. |
| [`func_L00_0036D29C`](levels/func_L00_0036D29C.c) | 40 | near miss: 9 of 10 instructions (ours 36 bytes, retail 40). Same wait-loop family as func_L00_0036BE28: retail loads through $at, hand-placed nops, jr ra then lui ra, 0x7000. |
| [`func_L00_003F34D8`](levels/func_L00_003F34D8.c) | 64 | func_L00_003F34D8: 8 instructions differ (register choice: retail keeps the count in $t1 and the quad temp in $v0; ours uses $v1/$a0). Quad copy is lq/sq via TImode. Missing: the allocator order. |
| [`func_L00_003F8DC8`](levels/func_L00_003F8DC8.c) | 64 | func_L00_003F8DC8: 72 bytes against 64 (retail uses $gp for the global at 0x1B0A20 via "sw ...,0x1A30($gp)"; this file builds -G0, so the sda global becomes lui/sw). Needs the file-level -G8 that the file does not have; not matched. |
| [`func_L00_003F0130`](levels/func_L00_003F0130.c) | 104 | func_L00_003F0130: 104 bytes, 27 places; the loop, prologue and call match, but the final read of the base global (0x1B2BB4) is "lw $v0,0x3BC4($gp)" in retail, which -G0 (this file) cannot emit: needs -G8 for L00_003EE100.c. Missing: the fi… |
| [`func_L00_003EEE20`](levels/func_L00_003EEE20.c) | 108 | func_L00_003EEE20: 100 bytes against 108; the whole body matches except that retail has two nops between cvt.s.w and div.s (the int divisor converted to float), which this compiler does not emit for any form tried (variable divisor, (float)… |
| [`func_L00_003F09A0`](levels/func_L00_003F09A0.c) | 124 | func_L00_003F09A0: 124 bytes, 1st differing instruction at +0x2c: retail schedules the counter increment before the $a3 constant and puts the element address in $v1 (ours in $v0); the loop, the call to func_L00_003F0B10 and the gp reload ma… |
| [`func_L00_002B5590`](levels/func_L00_002B5590.c) | 92 | 19 instructions (76 bytes) against retail's 23 (92 bytes): the frame is 0x30 not 0x40 and s1 is never saved; retail keeps &a in s1 across the first call (hoisted before it), GCC rematerializes sp+0 after it. Copy/zero order and the calls ma… |
| [`func_L00_00424A50`](levels/func_L00_00424A50.c) | 52 | 3 instructions differ, all store order (52 bytes): the field stores match retail (float b at +84, sh 1 at +62, float a at +76 of the block at +352) but GCC emits a, b, sh where retail has b, sh, a; missing: the source shape that makes the s… |
| [`func_L00_004028C0`](levels/func_L00_004028C0.c) | 96 | 96 bytes: under a -G8 probe the compile is 80 bytes; the prefix through the jal and the sb agree, the tail differs (retail stores -1 through a beq and keeps a dead li 10 before a b in the else path); also needs config/cflags.txt -O2 -G8 |
| [`func_L00_00403CB8`](levels/func_L00_00403CB8.c) | 160 | 152 of 160 bytes: the found-slot loop and the call sequence match in shape; retail keeps arg in s1 and the slot value in s0 across the calls, ours assigns the registers the other way round (and the else path ends with the value stored back) |
| [`func_L00_002D40A0`](levels/func_L00_002D40A0.c) | 76 | 72 bytes vs 76 (19 instructions): near miss. Retail keeps the 16-byte lq/sq temp in $v0 and folds the copy destination (0x18B6C0) into $v1, then rebases $v1 by -0x18A0 for the other stores; ours keeps the base in $v0 or $a2, so the temp reg… |
| [`func_L00_002D5E30`](levels/func_L00_002D5E30.c) | 132 | 172 bytes vs 132 (not matched): the loop is misread. Retail stores the running sum to *a0 on each pass, adds *a2 from the flag (M>>5 < sum) and walks halfwords from 0x262262; the early exits and the sum update order are not right here. Star… |
| [`func_L01_003246D0`](levels/func_L01_003246D0.c) | 36 | 4 of 11 instructions differ (36 bytes). Retail passes the 64-bit return of func_3246F8 straight into func_3246A0 (daddu, no conversion). Any C prototype narrower than unsigned long makes GCC sign-extend it (dsll32/dsra32); with unsigned lon… |
| [`func_L01_00304270`](levels/func_L01_00304270.c) | 52 | Does not match: the attempt compiles to 36 bytes, retail is 52 (13 instructions). Retail loads its pointer from $v1, a register that is not an argument, and tests with a2; the self-jump (j func_304270) on a 0x100 test suggests a caller-set … |
| [`func_L00_003E6710`](levels/func_L00_003E6710.c) | 104 | 0 of 27 exact: about 10 of 26 instructions differ. Loop shape and calls match (func_L00_003E6F48, the back-edge reload of D_L00_001B2B50 via sda); we keep the base load before the saves and swap the counter/offset registers (retail: counter… |
| [`func_L00_003E4B80`](levels/func_L00_003E4B80.c) | 108 | 0 of 27 exact: 100 bytes, retail 108. Missing two nops between cvt.s.w and div.s (retail: mtc1, nop, cvt.s.w, nop, nop, div.s); the early return and the join through a0 match. |
| [`func_L00_003E6F48`](levels/func_L00_003E6F48.c) | 124 | 0 of 27 exact: the loop, the call and its arguments match; the base-pointer load is hoisted above the saves (retail: after s1=a0) and the back-edge reload uses v1 where retail uses v0 (lw v0,0x3B64(gp) in the bgez-like delay slot). |
| [`func_L00_003E80A8`](levels/func_L00_003E80A8.c) | 164 | 0 of 27 exact: 144 bytes, retail 164 (5 instructions short: retail keeps base, offset and record pointer live across the calls and saves s0-s4). The calls and the 96.0f scale match; the compiler folds base+off and loses the saved registers. |
| [`func_L00_003E9490`](levels/func_L00_003E9490.c) | 172 | 0 of 27 exact: 164 bytes, retail 172. The 128-bit copies (lq/sq) and the calls match; the copy store lands in the branch delay slot (retail: lq, sq, b, ld in the delay slot), so the two copy paths are 2 instructions off. |
| [func_L01_003115E8](shared/func_L01_003115E8.c) | 88 | Candidate is 84 bytes; table pointer register selection and small global load order differ |
| [`func_L05_0030C930`](shared/func_L05_0030C930.c) | 24 | 0 of 4 places: store order and global-base relocation differ; candidate stores fields at +0x40/+0x44 in reverse order and the declared base symbol did not resolve. |
| [`func_L05_00324DB8`](shared/func_L05_00324DB8.c) | 48 | 0 of 4 places: two C forms emitted 64 and 60 bytes; both missed the call/branch and return shape. |
| [func_L00_002EDCB8](levels/func_L00_002EDCB8.c) | 16 | 12 bytes vs retail 16: candidate is one instruction short; object comparison cannot validate the relocation until the size matches. |
| [func_L00_002EE0C0](levels/func_L00_002EE0C0.c) | 16 | 12 bytes vs retail 16: the stores compile in the wrong order or without the retail nop; no plain C form found that preserves the exact delay-slot layout. |
| [func_L08_003CCD28](levels/func_L08_003CCD28.c) | 48 | 44-byte candidate; if-conversion changes the final branch and register allocation. |
| [func_L08_003D0098](levels/func_L08_003D0098.c) | 48 | 44-byte G8 candidate; branch and pointer calculation differ. |
| [func_L08_003D0508](levels/func_L08_003D0508.c) | 192 | 152-byte candidate; vector copy, frame and register scheduling differ. |
| [func_L08_003D3D80](levels/func_L08_003D3D80.c) | 128 | 120-byte candidate; retail materializes vector pointers before zero-offset quadword copies. Aggregate and memcpy variants also failed. |
| [func_L08_003D8290](levels/func_L08_003D8290.c) | 96 | 96-byte candidate; local quadword copy order and registers differ. |
| [func_L08_003D82F0](levels/func_L08_003D82F0.c) | 84 | 84-byte candidate; vector pointer formation and copy/FP scheduling differ. |

## Level functions from the six agent waves (2026-10-12)

Left by the six waves of workers that landed 521 functions in `src/` (PRs #149, #151,
#153, #155, #156 and #159). Bodies that did not match at every place, kept as starting
points; the note in each file is the worker's own, and this column says how far it got and
what was tried. None of these links, and none is counted. Move a file into `src/` once
`tools/build.py` places it.

| [`func_L06_00444068`](levels/func_L06_00444068.c) | 256 | ABANDON: best 268 bytes vs 256; three calls, float clamps to 0x2C0/0x2C8 and constants 0x3F49FDC0/0x3F060A93 recovered, but cc1 keeps float v in $f20 (frame 0x40) where retail uses $f0, and mis-rounds 0.7890167f to 0x3F49FD00. |
| [`func_L12_004163E8`](levels/func_L12_004163E8.c) | 1044 | kept as the worker's whole-file draft; no note was left |
| [`func_L12_0041A5F8`](levels/func_L12_0041A5F8.c) | 160 | Best attempt, not matching: 156 bytes vs 160 in 12_todano; node pointer in $v1 where retail has $a3, and the 128-bit copy folds offsets where retail pre-computes both bases (addiu $v1,$a3,0x210; lq $v0,0($v1)); one instruction short. |
| [`func_L12_0041BDF8`](levels/func_L12_0041BDF8.c) | 104 | Best attempt: 8 bytes short; retail keeps a separate $a1 copy of $a0 made at entry (daddu $a1,$a0,$zero), gcc folds the local onto $a0; twin BE60 also wants daddu $a0,$a1,$zero before func_41BED8; all spellings coalesce. |
| [`func_L00_003C6A88`](levels/func_L00_003C6A88.c) | 268 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003CA688`](levels/func_L00_003CA688.c) | 164 | kept as the worker's whole-file draft; no note was left |
| [`func_L06_003660F0`](levels/func_L06_003660F0.c) | 112 | Draft, best attempt: retail loads the flag as lui $v0,0x1A; lbu $v0,0x7A47($v0) and tests $v0; this source gives lbu $v1,%lo($v0) after the prologue saves; store order 0x20/0x24/0x28 matches; 0x001A7A47 is BOOT data, no sda. |
| [`func_L06_0037E6B0`](levels/func_L06_0037E6B0.c) | 280 | Draft, one instruction short (276 vs 280); retail materialises m+0x10 in $v1 and stores sq $v0,0($v1), this source folds the offset into sq $v0,0x10($s1); TI128 pointer shape tried; next idea a BLKmode struct assignment. |
| [`func_00308600`](game/func_00308600.c) | 360 | kept as the worker's whole-file draft; no note was left |
| [`func_0030E8A8`](game/func_0030E8A8.c) | 1052 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_004096B0`](levels/func_L00_004096B0.c) | 1944 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_002B3D10`](levels/func_L00_002B3D10.c) | 172 | NEAR MISS: 168 bytes/42 instructions vs 172; only the first branch is wrong: retail's bc1fl takes its delay slot from the branch target (lw $v1,0x2294($a0)), ours keeps bc1f with the fall-through lwc1 $f2,0($gp); sda and multiply order tried. |
| [`func_L00_002B5A88`](levels/func_L00_002B5A88.c) | 212 | NEAR MISS: 200 bytes/50 instructions vs 212/53; gcc rotates the loop and computes the D_00189E20 base inline instead of keeping lui $s3,0x19 / addiu $a1,$s3,-0x61E0; the (x + 31) % 32 part matches; no shape yet stops the rotation. |
| [`func_L05_0037CE08`](levels/func_L05_0037CE08.c) | 176 | Best attempt, not a match: 180 bytes vs 176; retail reloads lui+lw of D_L05_001B251C before each of the two loop calls, cc1 hoists the lui into $s3 (plus s4 and a bigger frame); same wall as func_L05_003D02C8. |
| [`func_L05_0037D6E8`](levels/func_L05_0037D6E8.c) | 128 | Best attempt: retail bases the walk on &elem.f14 (lw -0x8($v1) / sw 0($v1)) and keeps %hi of D_L05_002A1800 in $s0; this source materialises the full address, so cc1 emits daddu $s0,$v0 (124/128 with the struct form). |
| [`func_L05_003CFC48`](levels/func_L05_003CFC48.c) | 168 | Structure and size exact (168 bytes); residual is register allocation: retail keeps the D_L05_00221D10 base in $v1 and the value in $v0, this source the reverse, with the +0x10/+0x14 loads in opposite order; D[i]++ changes nothing. |
| [`func_L05_003D02C8`](levels/func_L05_003D02C8.c) | 88 | Near miss: 84 of 88 bytes; retail loads D_L05_001B251C with lui+lw inside the loop, cc1 hoists the lui into $a2, so the loop loses one instruction and the 8-byte alignment nop before the loop label never appears. |
| [`func_L05_00418440`](levels/func_L05_00418440.c) | 156 | Best of 6 compiles: exact 156 bytes, 38 of 39 instructions in retail order; only the daddu $a2,$s0,$zero third-argument copy sits between the halves of the lui/addiu address pair rather than after it; belongs in the -g8 sibling. |
| [`func_L05_0041C118`](levels/func_L05_0041C118.c) | 212 | Best of 2 compiles: 204 vs 212 bytes; the linked-list block matches, but the 16-byte copy at +0x10 folds into lq/sq where retail materialises both addresses, and a $gp load vs swc1 schedule flip remains; needs the -g8 sibling. |
| [`func_L05_0041C7E8`](levels/func_L05_0041C7E8.c) | 116 | Attempts 1-4: 100 vs 116 bytes; retail keeps a signed counter 0,1,2 with slti $v0,$a1,0x3 + bnez, while every source shape becomes a countdown (addiu $a2,$zero,0x2 ... bgez $a2); unsigned int emits sltiu instead. |
| [`func_L05_00421860`](levels/func_L05_00421860.c) | 168 | Best of 8 compiles: exact 168 bytes, all instructions in retail order; only the counter blocks use base=$v0/value=$v1 where retail has base=$v1/value=$v0; shared increments give 164 bytes, a char[] base 172. |
| [`func_L09_00326E90`](levels/func_L09_00326E90.c) | 108 | Kept for the next worker: two instructions over; gcc substitutes the incoming parameter for the loaded control word (daddu $v0,$a0,$0) instead of returning the word already in $v0. |
| [`func_L09_0033E658`](levels/func_L09_0033E658.c) | 56 | Kept for the next worker: layout is right, but gcc puts the -1 constant in the annulled delay slot (beql) where retail keeps a plain beqz + nop, and swaps lui/sll in the first delay slot. |
| [`func_L09_003EA2D0`](levels/func_L09_003EA2D0.c) | 140 | Kept for the next worker: the descriptor now lives in $s3 across the calls; something in the call registers or the epilogue still differs around +0x34. |
| [`func_L09_003F7C50`](levels/func_L09_003F7C50.c) | 140 | Kept for the next worker: one instruction out of place; retail loads the descriptor before the $s0 comparisons, gcc sinks that lw into the bne delay slot. |
| [`func_L09_003F87D8`](levels/func_L09_003F87D8.c) | 124 | Kept for the next worker: retail re-materialises the D_L09_001B285C address inside the loop, gcc hoists the lui out of it; the loop tail also differs. |
| [`func_L07_003F8C38`](levels/func_L07_003F8C38.c) | 152 | Wave 3 w05: size and register allocation identical (s0=a0, s2=0x68, s1=result); only schedule transpositions: reversed addiu $a0,0x5 / daddu $a1,0 for func_002E5CF8(5,0,a0), base in $a1 not $v0, daddu $a0,$s1 after the table load. |
| [`func_L07_003F9028`](levels/func_L07_003F9028.c) | 92 | Wave 3 w05: 92 bytes, 3 of 23 instructions off; the byte at 0x43($a0) is read into $a1 instead of $v1 (retail reuses the flag register, dead after the bnez); u8 c, s32 c and earlier declaration all still give $a1. |
| [`func_L07_003F9088`](levels/func_L07_003F9088.c) | 92 | Wave 3 w05: twin of 003F9028 with constants 3/9 instead of 2/8; same residual, the lbu lands in $a1 instead of $v1. |
| [`func_L07_003FF618`](levels/func_L07_003FF618.c) | 56 | Wave 3 w05: size OK (56 bytes); same transposition as 0040FC50: retail addiu $sp,$sp,-0x10 ; lui $v0,0x1A ; lbu $v0,0x7BD0($v0) versus candidate lui $v1,0x1A first; polarity fixed, != 0 gives ori 2, else the call. |
| [`func_L07_0040FC50`](levels/func_L07_0040FC50.c) | 52 | Wave 3 w05: size OK (52 bytes); only the first two instructions are transposed (retail addiu $v1,$zero,0xF before lui $v0,0x1B); declaration initialiser, comma expression and do/while all still put the lui first. |
| [`func_L11_00434AA8`](levels/func_L11_00434AA8.c) | 80 | kept as the worker's whole-file draft; no note was left |
| [`func_L11_00436850`](levels/func_L11_00436850.c) | 192 | kept as the worker's whole-file draft; no note was left |
| [`func_L06_0041FD60`](levels/func_L06_0041FD60.c) | 44 | ABANDON, best of 3 shapes: same size and sequence; retail addiu $v1,$zero,0xFF ; lbu $v0,0x21($a0) ; beq $v0,$v1 with daddu $a0,$v0 in the delay slot, ours coalesces the load into $a0; unsigned char, int and named-constant forms identical. |
| [`func_L06_0044B250`](levels/func_L06_0044B250.c) | 136 | ABANDON: only three words differ (first at +0x20); retail lw $v0,0x68($s0) ; addiu $v1,$zero,-1 ; lw $a1,0($v0) ; beq $a1,$v1, ours puts -1 in $a0; direct, named and reversed-operand shapes give the same allocation. |
| [`func_L06_0044B2D8`](levels/func_L06_0044B2D8.c) | 116 | ABANDON, 3 words off (see func_L06_0044B250.c); best shape tried was reversed operands -1 == **(int **)(a0 + 0x68). |
| [`func_L06_0044B350`](levels/func_L06_0044B350.c) | 116 | ABANDON, 3 words off (see func_L06_0044B250.c); best shape tried was a named int v assigned from *(int *)*(char **)(a0+0x68). |
| [`func_L06_004504C8`](levels/func_L06_004504C8.c) | 120 | ABANDON, 3 words off (see func_L06_0044B250.c); everything after the -1 test matches, including the branch-likely beql $v0,$zero for the second guard and the shared epilogue; best shape was char *p plus *(int *)p. |
| [`func_L06_00457920`](levels/func_L06_00457920.c) | 168 | ABANDON (172 bytes, LICM wall): retail materialises D_L06_001A8F20 inside the loop and hoists only the function pointer into $s3; gcc hoists the address too (pointer in $s4) and fills the back-edge slot with a hoisted lh $a2,0xAA($s1) bnel. |
| [`func_L06_00457AD0`](levels/func_L06_00457AD0.c) | 300 | ABANDON (304 bytes): every call, constant and address matches, including the three-way switch, the 0xA8 = 0xFF tail and the -0.033333335f/3.0f/6.0f arguments; one extra instruction and a callee-saved swap ($s0/$s1) remain. |
| [`func_L00_00426680`](levels/func_L00_00426680.c) | 100 | Best attempt: %hi at +0x38 differs; the seven stores are exact (source order must be 0,2,1,3,4,5,6 because cc1 rotates them); only the %hi/%lo pair of D_L00_001B1844 remains. |
| [`func_L00_0042A500`](levels/func_L00_0042A500.c) | 32 | Best attempt: only the base register allocation remains; retail lui $v0,%hi / lw $v0,%lo($v0) with one register, cc1 pins lui $v1,%hi / lw $v0,%lo($v1); same gap at -G0 and -G8, char* or int, with or without a temporary. |
| [`func_L00_00429048`](levels/func_L00_00429048.c) | 120 | Best attempt (-g8 sibling): 116 bytes vs 120; the whole structure (bne, bnel, bc1tl, xori/sltiu) is in place; the %hi/%lo pair of D_L00_001B8E10 differs (cc1 uses one register) and one store is missing; gp = 0x1AEFF0. |
| [`func_L00_0042A460`](levels/func_L00_0042A460.c) | 132 | Best attempt: instruction at +0x8 differs; everything is exact except the %hi/%lo pair of D_L00_001B1844, which retail holds in one register; the two stores are already in retail order. |
| [`func_L00_0042BAD0`](levels/func_L00_0042BAD0.c) | 52 | Family of four identical bodies (0042BAD0/_BCF8/_BFF8/_C290): cc1 hoists ld $ra,0x8($sp) before the store and interposes daddu $v0,$s0,$zero between lui and addiu, where retail keeps lui / addiu / daddu / sw / ld $s0 / ld $ra. |
| [`func_L16_004056A8`](levels/func_L16_004056A8.c) | 228 | kept as the worker's whole-file draft; no note was left |
| [`func_L16_004050D8`](levels/func_L16_004050D8.c) | 208 | Creates the handle's 0x274 record on demand, or copies the source windows into it and follows the flag's polarity on the record's byte. |
| [`func_L16_00412BA8`](levels/func_L16_00412BA8.c) | 176 | the worker's note describes the function only: selects one of two measured value tables by index and arms the resident channel. |
| [`func_L16_00412C58`](levels/func_L16_00412C58.c) | 240 | Walks a rotating candidate index over the measured byte table until a free slot is found, arms it, and clears the whole window when none is. |
| [`func_L11_0044C220`](levels/func_L11_0044C220.c) | 68 | Best attempt; instruction at +0x14 differs: ordering of the 8 integer arguments and the 0x68 base (retail $v0, ours $a3); the argument-to-register map is right; retail ABI is n64/n32 (a0-a3 + t0-t3, floats in f12-f19). |
| [`func_L05_00432158`](levels/func_L05_00432158.c) | 68 | Abandoned: 60 bytes vs 68; every version came out strength-reduced (addiu $v1,$zero,0x2 / daddiu $v1,$v1,-0x1 / bgez) and the padding nop landed inside the loop instead of before it; pointer, indexed, int and long counter forms tried. |
| [`func_L05_00441EA0`](levels/func_L05_00441EA0.c) | 140 | Recorded candidate is 128 bytes. [Stack-vector alternative](levels/alternatives/func_L05_00441EA0.c) rechecked on 2026-10-15: 140 bytes, 0/4 placements; first instruction difference at +0x4, parameter copy versus table load. |
| [`func_L05_00442720`](levels/func_L05_00442720.c) | 136 | Same body rechecked on 2026-10-15: 128 bytes, 0/4 placements. Address materialization and the return delay slot differ; the identical supplied draft is retained here. |
| [`func_L05_004427A8`](levels/func_L05_004427A8.c) | 128 | Exact size (128 bytes), 32 instructions in retail order; only the GPRs differ: retail $v0,$v1,$a0 for the byte fields and $a1 for the word, ours $a2,$a3,$v0,$v1; sda yields $at in ordinary flow, and one attribute cannot serve both mirrors. |
| [`func_L05_00442828`](levels/func_L05_00442828.c) | 52 | G8 candidate is 48 bytes. [Reversed-store alternative](levels/alternatives/func_L05_00442828-g8.c) rechecked on 2026-10-15: 48 bytes, 0/3 placements; address scheduling and the filled return delay slot differ. Neither variant has behavioral validation. |
| [`func_L05_00445A98`](levels/func_L05_00445A98.c) | 192 | Previously private draft rechecked on 2026-10-15: 176 bytes, 0/3 placements; tail reuses the table and index instead of reloading them. |
| [`func_L14_00423E50`](levels/func_L14_00423E50.c) | 144 | NEAR MISS: 136 bytes vs 144; the compiler folds idx*4 to 0x3FC in the idx == 0xFF arm and materialises the base without keeping the %hi, where retail recomputes sll and the base at L2; the loop and the 0xF91 test match. |
| [`func_L14_00425C08`](levels/func_L14_00425C08.c) | 164 | NEAR MISS: 168 bytes vs 164; retail materialises the constant 1 once (addiu $t0,$zero,0x1, used for sb $t0,0x31($v1) and as argument 5), this compiler rematerialises it for the field write; literal and shared int one = 1 tried. |
| [`func_L14_00426DE8`](levels/func_L14_00426DE8.c) | 116 | NEAR MISS: 112 bytes vs 116; the missing word is retail's write-back copy (sll $s0,$s0,24 in the jal delay slot then daddu $t0,$s0,$zero), which gcc forwards into the argument register (sll $t0,$s0,24); both shift spellings forward. |
| [`func_L14_00427210`](levels/func_L14_00427210.c) | 60 | NEAR MISS: 68 bytes vs 60; retail keeps the result in $a1 with one shared return block and addiu $a1,$zero,1 in the beql delay slot, but the trailing else-if is if-converted to sltu $v0,$zero,$v0; the if-conversion wall is documented. |
| [`func_L14_004286F8`](levels/func_L14_004286F8.c) | 124 | NEAR MISS: 120 bytes vs 124, belongs in the -g8 sibling (lw $a2,0x35AC($gp)); the missing word is retail's daddu $v1,$v0,$zero before the base clobbers $v0; loop constants land in $a3/$a2 and p/q are swapped; the loop shape matches. |
| [`func_L00_0034C090`](levels/func_L00_0034C090.c) | 132 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003588A8`](levels/func_L00_003588A8.c) | 80 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_00358D50`](levels/func_L00_00358D50.c) | 68 | kept as the worker's whole-file draft; no note was left |
| [`func_L08_003D8A28`](levels/func_L08_003D8A28.c) | 132 | Wave 6 w04: 128 of 132 bytes match, differing at +0x68 in the order of sb $v0,0xBC($s1) against the epilogue ld $s0,0x0($sp); the twin func_L08_00422AD8 fails identically; the if (v0 == -1) v0 = 1; else { call; v0 = 1; } form reproduces retail best. |
| [`func_L08_003D9208`](levels/func_L08_003D9208.c) | 208 | Wave 6 w04: cause is callee-saved allocation (retail $s1 = parameter copy, $s0 = moby pointer, ours reversed), first diff at +0x4; declaration order changes nothing; func_L08_004237F0 also needed the saturation constant 0x7FFD, not 0xFFFD. |
| [`func_L08_004238A0`](levels/func_L08_004238A0.c) | 204 | Wave 6 w04: 202 of 204 bytes match (+0x38); only the temp register differs (lw $v1,0x24($s1) vs retail lw $v0,0x24($s1)); lessons: the <= form gives c.ole.s + bc1f, and 0.8377582f is needed for 0x3F567752. |
| [`func_L00_002A7958`](levels/func_L00_002A7958.c) | 180 | 0 of 27 places match (+0x44 differs); 43 of 45 instructions: retail loads the fade value into $f1 via $at and issues addiu $a0,$a0,0x18E0 before the $gp load, here it is $f0 and the addiu follows the load. |
| [`func_L00_002A7DE8`](levels/func_L00_002A7DE8.c) | 120 | 0 of 27 places match (+0x30 differs); 33 of 36 instructions; three addu put the scaled index first where retail puts the base first (addu $a2,$s0,$a2 vs addu $a2,$a2,$s0); the 128-bit copy with qcopy_nc is exact. |
| [`func_L00_002AE070`](levels/func_L00_002AE070.c) | 88 | 0 of 27 places match (+0x4c differs); 21 of 22 instructions; the tail pointer addu has its operands reversed (retail addu $a2,$a2,$v1, here addu $v1,$v1,$a2); flat, [idx], int* and index-first spellings all give the same order. |
| [`func_L00_002A53B8`](levels/func_L00_002A53B8.c) | 196 | Wave 5 w11: 0 of 27 places match; 176 of 177 instructions - this cc1 hoists the second call's `daddu $a1,$s0` above the `beqz $v0` and leaves the `jal` delay slot a nop. The prologue shape (address kept in $v0 for the three sub-pointer adds, separate `daddu $s0,$v0`, s2 = +0x60 from the declaration order q(0x40), s(0x90), r(0x60), limit from $s0) is exact; argument casts, assignment inside the condition and local copies of the argument keep the hoist. |
| [`func_L00_002A5480`](levels/func_L00_002A5480.c) | 148 | Wave 5 w11: 0 of 27 places match (+0x74 differs); 35 of 37 instructions; only the two closing ADDU are swapped: ((tick + second) + (first + 0x14)) here against retail's ((tick + (first + 0x14)) + second). Needs a -G8 unit for the sda attribute to bite (a plain extern int stays absolute under -G8, which is what the two stamped reads want); the reversed comparison `D_001B19C8 > field` is what puts the tick load into the bnez delay slot. |
| [`func_L00_00324120`](levels/func_L00_00324120.c) | 88 | Wave 5 w11: 0 of 12 places match; 20 of 22 instructions; this cc1 if-converts the shared `||` body (beql + duplicated load + xor/sltiu) where retail keeps a plain beq, a bnel and a branchy body. Conditions, the 0x1B constant in the beq delay slot and the branch-likely into the shared block all match. |
| [`func_L00_003252C8`](levels/func_L00_003252C8.c) | 56 | Wave 5 w11: 0 of 27 places match (+0x4 differs); right size, both calls and argument moves exact; only the flag's register differs (retail keeps address and value in $v0 with %lo in the load, this cc1 uses $v1) plus the frame setup schedule. Its callers take one argument and it reads a1, so the filer needs the `__asm__` alias on the declaration (measured: func_L00_00325300 still 27/27). |
| [`func_L00_002E2048`](levels/func_L00_002E2048.c) | 44 | Wave 5 w11: 0 of 27 places match; 8 of 11 instructions; retail keeps the block base in $at (lui+addiu, +0x1C as the load's literal), the mask 0x60 in $v1 via ADDI and a nop in the first jr delay slot, where this cc1 loads through the destination register, folds the mask into ANDI and fills the delay slot with the shift. |
| [`func_L00_002D7D58`](levels/func_L00_002D7D58.c) | 68 | Wave 5 w11: 0 of 27 places match (+0x20 differs); 17 of 17 instructions, one addu's source operands swapped (retail addu $v0,$v0,$v1, here addu $v0,$v1,$v0). The dead `mult $v1,$a0` at +0x1c is retail too: this compiler keeps the scaled index in the multiplier register. [idx], byte-offset and in-place `e += i` spellings give the same order. |
| [`func_L00_002D8208`](levels/func_L00_002D8208.c) | 68 | Wave 5 w11: twin of func_L00_002D7D58 (same file, field +0x10); draft is the twin candidate, not separately re-measured. One addu operand order. |
| [`func_L00_00326080`](levels/func_L00_00326080.c) | 12 | Wave 5 w11: no C can be placed. The extent is `addiu $sp,$sp,+0x30; nop; addiu $sp,$sp,+0x30` between two real functions (previous ends jr $ra / addiu -N, next starts a real prologue): catalogue defect over padding, no body. |
| [`func_L00_00322F48`](levels/func_L00_00322F48.c) | 20 | Wave 5 w11: no C can be placed - five words of `addiu $sp,$sp,+N; nop` padding between two real functions (catalogue defect). |
| [`func_L01_003200C8`](levels/func_L01_003200C8.c) | 12 | Wave 5 w11: no C can be placed - `addiu $sp,$sp,+0x150; nop; addiu $sp,$sp,+0x10` padding between two real functions (catalogue defect). |
| [`func_L01_00322358`](levels/func_L01_00322358.c) | 20 | Wave 5 w11: no C can be placed - `addiu $sp,$sp,+N; nop` padding between two real functions (catalogue defect). |
| [`func_L01_0031EF98`](levels/func_L01_0031EF98.c) | 60 | Wave 5 w11: no C can be placed - ten `addiu $sp,$sp,+N; nop` pairs (60 bytes) between two real functions (catalogue defect, no body). |
| [`func_L00_00324E48`](levels/func_L00_00324E48.c) | 196 | Wave 5 w11 tour 2: 0 of 27 places match (+0x1c differs); right size, three calls, the 0x10-byte stack buffer and the five-float repack all identified; this cc1 swaps the two int parameters and hoists the second call's a1 into the first call's delay slot. |
| [`func_L18_002F1098`](levels/func_L18_002F1098.c) | 48 | Wave 5 w11 tour 2: 0 of 12 places match; right size and flow (|| with a result variable), only the address register differs (lui $v1/lw $v0 vs retail's lui $v0/lw $v0,off($v0)). |
| [`func_L00_002D75F0`](levels/func_L00_002D75F0.c) | 164 | Wave 5 w11 tour 2: 0 of 27 places match (+0x3c differs); right size, every constant exact; two independent stores swapped (retail: swc1 0x3D8, sb 0x3E9, call with the 0x3D4 store in the delay slot). Three statement orders all reorder. |
| [`func_L01_00323310`](levels/func_L01_00323310.c) | 112 | Wave 5 w11 tour 2: 0 of 18 places match; 104 of 112 bytes; state test, two movn masks and three shorts understood, but retail keeps the key in $a2 and materialises the block late ($t0 + addiu $v0). |
| [`func_L12_0041A6D0`](levels/func_L12_0041A6D0.c) | 92 | Wave 5 w11 tour 2: 0 of 2 places match; 88 of 92 bytes; three guards, two stores and the +0x7D0 stamp understood; retail keeps the parameter in $a3 and loads the table base out of it, this cc1 uses the parameter register directly. |
| [`func_L12_004081F8`](levels/func_L12_004081F8.c) | 228 | Wave 5 w11 tour 2: 0 of 7 places match; 236 bytes (two over); both $gp blocks and calls understood, but this cc1 spends an extra callee-saved register on the parameter copy (s0 = a0) where retail keeps s1 = a0 and gives s0 to the call result. Needs the -g8 unit. |
| [`func_L00_00322428`](levels/func_L00_00322428.c) | 288 | Wave 5 w11 tour 2: 0 of 27 places match; 268 bytes; all constants (1/60, 1/3600, 1/216000, 1.2, 1.44, 5/6, 0.0004, 0.02 and the 1-ulp pair) and globals identified, but this cc1 lays the a0 == 0 arm out as the branch target and materialises the constants up front. |
| [`func_L18_003044B8`](levels/func_L18_003044B8.c) | 192 | Wave 5 w11 tour 2: 0 of 3 places match; 152 bytes; int/float conversion plus two calls understood, but this cc1 keeps far fewer callee-saved slots than retail's six (s0..s3, f21, f22) and orders the saves differently. |
| [`func_L15_0036DAB0`](levels/func_L15_0036DAB0.c) | 288 | Wave 5 w11 tour 2: no C can be placed over the extent - its first 16 bytes are a tail (`sw $t3,0x40($v1)`, a nop, `addiu $sp,$sp,0x20`, a nop) whose registers are never set here; the real body starts at 0x0036DAC0 (catalogue split, 0x10 early). |
| [`func_L18_003F74B8`](levels/func_L18_003F74B8.c) | 212 | Wave 7 w07 tour 2: 0 of 3 places match; exactly retail's size with every call, both flag stores, the counter test and the arm layout matching - the arms were inverted until the guard was written `t != -1 || *(s16 *)(s1 + 0x296) == 0xE`. Left: this cc1 puts the first call's `addiu $a1,zero,0x13` in the jal delay slot (retail has it before the jal, the `sw $zero,0x20($s1)` in the slot) and sign-extends the counter into $v0 where retail uses $v1; a `s32 mode = 0x13;` spelling changes nothing. |
| [`func_L18_003F7AB8`](levels/func_L18_003F7AB8.c) | 196 | Wave 7 w07 tour 2: 0 of 3 places match; needs the -G8 unit (the resident count word is lw 0x3848($gp)); 236 bytes for both guard spellings (while condition, for(;;) with breaks) - this cc1 keeps several copies of the 0xFF and table-slot guards in separate blocks where retail folds them into one chain ending in the two annulled continue branches (bgezl/beql) into the walk step. |
| [`func_L18_003F9EF0`](levels/func_L18_003F9EF0.c) | 188 | Wave 7 w07 tour 2: 0 of 3 places match; needs the -G8 unit (four small-data tables); 200 bytes with every call, table read, -1 store and reloaded counter increment right, but keeping the index in a C local across the two tail calls costs a second callee-saved register (this cc1 saves s1; retail keeps the single s0 and reloads `lh $a0,0xAE($s0)`); two inline-reload spellings give 204. |
| [`func_L18_003FCD70`](levels/func_L18_003FCD70.c) | 180 | Wave 7 w07 tour 2: no C can be placed - the row opens on the real MMI word 0x700014A9 (`$zero,$zero,0x14A9`), the family COMPILER-NOTES keeps out of reach, around lq/sq pairs and a 0x90-strided stack-quad loop. ASM_FUNC candidate. |
| [`func_L00_003B8420`](levels/func_L00_003B8420.c) | 204 | Wave 7 w07 tour 2: 0 of 4 places match; every call, argument and flag store identified and the shared tail reproduced; the early-return spelling collapses retail's two-step exit (beqz to the shared `beql $v0,$zero` epilogue test) into one branch at 196 bytes, and the nested single-return spelling keeps the structure but swaps the two callee-saved pointers (this cc1 gives s1 to the 0x00190000 constant, retail gives it to the parameter). |
| [`func_L18_003F9DA0`](levels/func_L18_003F9DA0.c) | 176 | Wave 7 w07 tour 2: 0 of 3 places match; needs the -G8 unit (lwc1 off $gp, gp+0x1440..0x1460); 172 bytes against 176 with the switch spelling, which reproduces retail's compare chain (beq a1,1 / beq a1,2 / default) and the first store inside each case; the one word left is block layout (retail keeps the default case's store after the other two). Plain if/else-if/else inverts the chain (bne) at 164 bytes; a struct copy is 232. |
| [`func_L00_003B5210`](levels/func_L00_003B5210.c) | 180 | Wave 7 w07 tour 2: 0 of 27 places match; needs the -G8 unit; 176 bytes against 180, instruction-exact including the s-register mapping (s1 short array, s2 record, s3 second array) once the three pointers are declared r, p, q - declaration order selects the callee-saved registers here. The missing word is the annulled slot: retail's beql duplicates p += 0x50 into its delay slot, this cc1 emits a plain beqz and hoists q++ there instead (three loop spellings measured). |
| [`func_L01_003BD560`](levels/func_L01_003BD560.c) | 136 | Wave 7 w07: 0 of 1 places match; 152 bytes; the two quad copies (TImode lq/sq), the argument shuffles into the stack pair, the delay-slot store of f12 and the failure return are all exact, but retail clamps with `min.s` and returns `max.s` (COP1 funct 0x29/0x28), which no C reaches here - the ternary and if spellings both give c.olt.s + bc1f/bc1tl with mov.s. Same instruction class as func_L08_00425328. |
| [`func_L01_003BC490`](levels/func_L01_003BC490.c) | 176 | Wave 7 w07: 0 of 1 places match; 172 bytes; the allocation, the four field copies, the rand(-pi, pi) seed and the epilogue are exact; only the quad store differs - retail materialises the destination (`addiu $a1,$s0,0x10` then `sq $v0,0($a1)`) where this cc1 folds the constant offset into the store. Five further spellings measured here (local TI128 pointer, the prepared struct typedef, volatile, staged local, byte-cast), all fold: the TI-destination wall. |
| [`func_L18_003F50D8`](levels/func_L18_003F50D8.c) | 188 | Wave 7 w07: 0 of 3 places match; 192 bytes; both resident-word blocks, the two class edits and the whole 0/1/0x13 dispatch chain are exact; this cc1 speculates the +0x20 byte into the delay slot of a `bnel` and reloads it at the join where retail emits a plain `bne` (annulled-slot wall). Four spellings measured, all 192. |
| [`func_L00_00346DB0`](levels/func_L00_00346DB0.c) | 204 | Wave 7 w07: 0 of 27 places match (+0x18 differs); needs the -G8 unit (the resident word is `lw -0x5FA4($gp)`); the whole 204 bytes are instruction-exact except the three-way -1/0 select that closes the 0x10 arm - retail branches (beqz, `addiu $v1,zero,-1` in the delay slot, a shared `daddu $v1,zero,zero`), this cc1 always emits movn. Nine spellings measured (including a 64-bit result and a 64-bit select variable), every one 204 bytes and differing only there. |
| [`func_L00_0034C720`](levels/func_L00_0034C720.c) | 204 | Wave 7 w07: twin of func_L00_00346DB0 (same body, callee func_L00_0030D9F8 and callback base 0x0033EDB8); draft is the twin candidate, not separately re-measured beyond the -G8 measurement that produced the same +0x18 residue. |
| [`func_L00_0034EC00`](levels/func_L00_0034EC00.c) | 204 | Wave 7 w07: twin of func_L00_00346DB0 (same body, callee func_L00_0030DB48 and callback base 0x0033E1D8); draft is the twin candidate, not separately re-measured beyond the -G8 measurement that produced the same +0x18 residue. |
| [`func_L08_003D3928`](levels/func_L08_003D3928.c) | 196 | Wave 7 w07: 0 of 3 places match; profile lever found - the default profile writes every bank word as `lui $r,%hi; sw $x,%lo($r)` and CSEs the 0x001B34D4 address across the branch (188 bytes), while `-mno-split-addresses` restores retail's `lui $at,%hi; sw $x,%lo($at)` form and gives 192 bytes against 196, leaving only the prologue save order (this cc1 saves s0 before s1) and the bc1f delay slot (filled with `addiu $v1,zero,1` where retail has nop and sets it after the sq). Needs a unit of its own with that flag, or a save-order lever. |
| [`func_L00_003D3DE0`](levels/func_L00_003D3DE0.c) | 456 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003D3FB0`](levels/func_L00_003D3FB0.c) | 292 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003D4958`](levels/func_L00_003D4958.c) | 200 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003D5320`](levels/func_L00_003D5320.c) | 52 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003D5358`](levels/func_L00_003D5358.c) | 336 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003D7250`](levels/func_L00_003D7250.c) | 636 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DB0A8`](levels/func_L00_003DB0A8.c) | 108 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DBF50`](levels/func_L00_003DBF50.c) | 624 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DC1C0`](levels/func_L00_003DC1C0.c) | 268 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DC2D0`](levels/func_L00_003DC2D0.c) | 128 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DC350`](levels/func_L00_003DC350.c) | 140 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DC3E0`](levels/func_L00_003DC3E0.c) | 604 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DC640`](levels/func_L00_003DC640.c) | 476 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DC910`](levels/func_L00_003DC910.c) | 436 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DD2B8`](levels/func_L00_003DD2B8.c) | 964 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DDEC8`](levels/func_L00_003DDEC8.c) | 444 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DE088`](levels/func_L00_003DE088.c) | 252 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DE490`](levels/func_L00_003DE490.c) | 376 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DF030`](levels/func_L00_003DF030.c) | 212 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DF338`](levels/func_L00_003DF338.c) | 884 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DF6B0`](levels/func_L00_003DF6B0.c) | 96 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003DFDC0`](levels/func_L00_003DFDC0.c) | 468 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003E0088`](levels/func_L00_003E0088.c) | 436 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003E0240`](levels/func_L00_003E0240.c) | 960 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003E0F58`](levels/func_L00_003E0F58.c) | 436 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003E2180`](levels/func_L00_003E2180.c) | 912 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003E2BD0`](levels/func_L00_003E2BD0.c) | 684 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003E2E80`](levels/func_L00_003E2E80.c) | 984 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_003B4E88`](levels/func_L00_003B4E88.c) | 112 | Near miss: exact 112 bytes, only allocation differs (+0x20); retail counter $v0 (=3, bgez) with pointers $v1/$a0/$a1 and constants $a2=2, $a3=1, this source rotates temps as $a0/$v1/$v0; four statement orders tried; needs -g8 plus nosda on 0x28D0/0x28D4. |
| [`func_L00_003B69D8`](levels/func_L00_003B69D8.c) | 180 | Near miss: 172 bytes vs 180; structure right but p->$s1, q->$s2 where retail has p->$s0, q->$s1 (both declaration orders tried), one instruction short; the single-base D_L00_001B8FF0 form is required, two D_ symbols give 184 bytes. |
| [`func_L05_004157B0`](levels/func_L05_004157B0.c) | 256 | Draft, twin of func_L05_0041CA70: exact size with matching frame and registers; retail stores swc1 $f1,0x180($s1) before the 0x270/0x26C/0x268 stores and has 20.0f in $f1, 0.02f in $f0 (ours reversed); the 0.02f store must be written first. |
| [`func_L05_0041CA70`](levels/func_L05_0041CA70.c) | 244 | Draft: 244 bytes, 3 words differ; frame 0x40 and callee-saved allocation now match, but the func_37AEC0(obj,1) argument setups are reversed (retail addiu $a1,$zero,1 before daddu $a0,$s0,$zero) and addu $s1,$s1,$v0 is one slot late; scheduling-only. |
| [`func_L05_0041FC60`](levels/func_L05_0041FC60.c) | 228 | kept as the worker's whole-file draft; no note was left |
| [`func_L01_00397950`](levels/func_L01_00397950.c) | 212 | Worker w13: body identical instruction by instruction except the callee-saved pair (retail $s1 = $68 pointer, $s2 = loop counter, ours reversed); no source lever found; the source store order 50,54,5C,58 emits retail 5C,58,50,54. |
| [`func_L01_003990E8`](levels/func_L01_003990E8.c) | 132 | Worker w13: only one instruction differs, the preheader constants order (retail addiu $t1,$0,1 ; lhu $v0,0x34 ; addiu $t0,$0,2, ours reverses the two addiu); stores 0x98/0x34/0x31 and the loop match; store orders and an explicit i = 2 tried. |
| [`func_L01_003EE8E0`](levels/func_L01_003EE8E0.c) | 112 | Worker w13: 116 bytes vs 112; only one instruction too many, gcc rematerialises 0xFF for the return (addiu $v0,$0,0xFF again after sb $v0,0xA8($s0)) instead of reusing $v0; return 0xFF and r = 0xFF; return r tried. |
| [`func_L11_00456378`](levels/func_L11_00456378.c) | 192 | Draft w17: 184 bytes vs 192; allocation conforms (p->$s1, q->$s0) and the body matches except the branch (ours beq $v0,$s2 + nop, retail bnel $v0,$s2) and the then-block side; one-call and two-call variants give 160 or 200 bytes. |
| [`func_L11_00456438`](levels/func_L11_00456438.c) | 264 | Draft w17: 268 bytes vs 264, one instruction too many; the constant 1 is materialised twice ($t0 for the sb, $a3 for the argument) where retail reuses $a3. |
| [`func_L11_00456540`](levels/func_L11_00456540.c) | 148 | Draft w17: same size (148 bytes) but instruction at +0x8 differs; only the two callee-saved registers are swapped (retail p->$s0, q->$s1, ours p->$s1, q->$s0). |
| [`func_L11_00456938`](levels/func_L11_00456938.c) | 268 | Draft w17: reconnaissance stub only; uses $gp (0x2DB0/0x2DB4/0x2DB8) and 128-bit copies, so a -g8 sibling file is required. |
| [`func_L11_00456EA0`](levels/func_L11_00456EA0.c) | 220 | Draft w17: reconnaissance stub only; would use $gp (0x2DC4/0x2DC8/0x2DCC), so a -g8 sibling file is required. |
| [`func_L11_00458A98`](levels/func_L11_00458A98.c) | 232 | Draft w17: reconnaissance stub only; would use $gp (lhu 0x2F94($gp)) and lq/sq. |
| [`func_L11_00458C38`](levels/func_L11_00458C38.c) | 200 | Draft w17: reconnaissance stub only; would use $gp (0x2FCC/0x2FD0/0x2FD8/0x2FDC), so a -g8 sibling file is required. |
| [`func_L11_00459D48`](levels/func_L11_00459D48.c) | 252 | Draft w17: reconnaissance stub only; would use $gp (lwc1 0x300C($gp)). |
| [`func_L11_0045B5C8`](levels/func_L11_0045B5C8.c) | 256 | Draft w17: 252 bytes vs 256, one instruction missing; retail keeps $a0/$a1 for the two globals and puts the q->f0 store in the beq delay slot. |
| [`func_L07_003E5AC8`](levels/func_L07_003E5AC8.c) | 168 | Chain-walk family: shape A (goto rotation plus sda) reaches 168 bytes with body and test block byte-identical, but the +0x10..+0x20 preheader swaps $v0/$v1 and the dead nop at +0x2C is missing; shape B (local id) is 164 bytes. |
| [`func_L07_003E6F40`](levels/func_L07_003E6F40.c) | 168 | Chain-walk family: shape A (goto rotation plus sda) reaches 168 bytes with body and test block byte-identical, but the +0x10..+0x20 preheader swaps $v0/$v1 and the dead nop at +0x2C is missing; shape B (local id) is 164 bytes. |
| [`func_L07_003EB790`](levels/func_L07_003EB790.c) | 168 | Chain-walk family: shape A (goto rotation plus sda) reaches 168 bytes with body and test block byte-identical, but the +0x10..+0x20 preheader swaps $v0/$v1 and the dead nop at +0x2C is missing; shape B (local id) is 164 bytes. |
| [`func_L07_003EE640`](levels/func_L07_003EE640.c) | 200 | Exact 200 bytes; only the five-instruction preheader at +0x54..+0x64 differs (ours orders p = o + 8 and i = 7 before -1 and D_00189EA0); a countdown o[15 - i] form and hoisted locals both drop to 184 bytes. |
| [`func_L06_00458E10`](levels/func_L06_00458E10.c) | 288 | kept as the worker's whole-file draft; no note was left |
| [`func_L06_00458F30`](levels/func_L06_00458F30.c) | 236 | kept as the worker's whole-file draft; no note was left |
| [`func_L06_00459B18`](levels/func_L06_00459B18.c) | 144 | kept as the worker's whole-file draft; no note was left |
| [`func_L06_0045A490`](levels/func_L06_0045A490.c) | 112 | kept as the worker's whole-file draft; no note was left |
| [`func_L06_00464900`](levels/func_L06_00464900.c) | 236 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_0039F300`](levels/func_L00_0039F300.c) | 116 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_00390E10`](levels/func_L00_00390E10.c) | 276 | Restores the third-level object's transform and flags from the level's own globals, marks the level as initialised, publishes the default geometry constants and calls the boot helper with the three default words. |
| [`func_L00_00390F28`](levels/func_L00_00390F28.c) | 208 | Publishes the third-level object's transform and flags into the level's own globals, then, in the second mode, publishes five more constants and calls the boot helper with three words of the 0x1BD2C0 array. |
| [`func_L00_003FC108`](levels/func_L00_003FC108.c) | 284 | Wave 6 w28 best attempt: 272 bytes vs 284; the callee-saved mapping swaps $s2/$s3 (o vs a1) and the entry block order of the five stores differs; file src/levels/shared/L00_003F8E08.c. |
| [`func_L00_003ACC88`](levels/func_L00_003ACC88.c) | 112 | ABANDON, compiler-profile wall: retail materialises the 128-bit zero with pcpyld $v0,$zero,$zero and stores sq $v0,0x10($sp), but rac2_fold_zero_ti_store folds it to sq $zero whenever the register is dead afterwards, which it is; +0x1c differs. |
| [`func_L00_003ACCF8`](levels/func_L00_003ACCF8.c) | 104 | Nearest: exact 104 bytes, 25 of 26 instructions identical; only the position of mtc1 $zero,$f12 against lw $a2,0x7B8($gp) differs (retail emits the float constant first); a local float does not move it; needs the -g8 sibling. |
| [`func_L00_003B0A28`](levels/func_L00_003B0A28.c) | 136 | ABANDON: 116 bytes vs 136; retail walks the table with the cursor in $a1 and invariant copies in $a3/$t1, storing the cursor twice (bnel delay slot and after the wrap-around exit); the duplicate store is reproduced, the register copies are not. |
| [`func_L00_003B0AB0`](levels/func_L00_003B0AB0.c) | 108 | Nearest: exact 108 bytes, 26 of 27 instructions identical; index in $a3 and tag in $a2 (retail index $a2, tag $v0) flips bnel $v0,$a2 to bnel $a2,$a3; writing c == s->f17 fixes the registers but flips the branch back. |
| [`func_L00_003B0CE8`](levels/func_L00_003B0CE8.c) | 184 | ABANDON: 168 bytes vs 184; the shape is reproduced but not the two distinct 0x1C materialisations ($t1/$t2) nor the $t4 = base+0x14 register; retail clears slot[idx], rewrites bytes equal to idx and calls func_L00_003B0A28 on wrap-around. |
| [`func_L01_003D9830`](levels/func_L01_003D9830.c) | 172 | Best attempt: 176 bytes vs 172 in 01_oozla (+0x4 differs); retail keeps the func_312B68 result in $v0 with no daddu $v1,$v0,$zero, and its second test is beql with the 0xFF materialisation in the delay slot. |
| [`func_L01_003DA300`](levels/func_L01_003DA300.c) | 236 | Best attempt: exact 236 bytes (+0x4 differs); only the $s3/$s4 assignment of the $a0/$a1 copies is mirrored, pure register allocation (retail a0->$s3, a1->$s4). |
| [`func_L06_003E3ED0`](levels/func_L06_003E3ED0.c) | 88 | 8 bytes short (80 vs 88): the return 0/1 pair folds to lw $v0,0x588 ; xor $v0,$v0,$a0 ; jr $ra ; sltiu $v0,$v0,1 where retail keeps an explicit bne to the shared epilogue; both polarities and cross-jump blocks tried; if-conversion wall. |
| [`func_L06_003E4F08`](levels/func_L06_003E4F08.c) | 152 | 4 bytes short (148 vs 152): CSE keeps the 0.000290888245f constant in $f20 across call 1 (swc1/lwc1 $f20, frame -0x30, 37 words) where retail re-materialises lui/ori/mtc1 per call (frame -0x20, 38 words); sda and argument order tried. |
| [`func_L00_00401D50`](levels/func_L00_00401D50.c) | 212 | 204 vs 212 bytes; the missing pair is retail's redundant beql $s0,$zero to the loop test with lw $s0,0x28($s0) in the delay slot, before node->obj; while, for forms, && chains and continues tried; belongs in the -g8 sibling. |
| [`func_L00_00403BF8`](levels/func_L00_00403BF8.c) | 52 | Exact size and body; only the preamble order differs (retail addiu $v1,$zero,0xF before lui $v0,0x1B); int i = 15 first, while/do-while, comma init and array form tried; the same symptom appears on 00403C30 and 00403CB8. |
| [`func_L00_00403C30`](levels/func_L00_00403C30.c) | 56 | Same order wall as 00403BF8: the counter addiu $a0,$zero,$zero must precede the address, and cc1 builds it in $v0 then copies to $v1 where retail writes lui $v1,0x1B ; addiu $v1,$v1,0x2C30; array and scalar forms identical. |
| [`func_L00_00409480`](levels/func_L00_00409480.c) | 212 | 216 vs 212 bytes; only c is born in $v1 then copied to $a2 for the call (one daddu $a2,$v1,$zero too many) where retail makes it born in $a2 (addiu $a2,$zero,0x80 ; subu $a2,$a2,$v0); moving the call earlier gives 224 bytes. |
| [`func_L04_00441100`](levels/func_L04_00441100.c) | 116 | the worker's note describes the function only: wave 6 worker w38, file src/levels/04_barlow/L04_00422A00.c: best attempts for the four functions left as INCLUDE_ASM; each entry names the last check.py line observed. |
| [`func_L01_003BDB28`](levels/func_L01_003BDB28.c) | 132 | The note holds only the function name; no difference or attempt is recorded. |
| [`func_L01_003BE4D0`](levels/func_L01_003BE4D0.c) | 340 | The note holds only the function name; no difference or attempt is recorded. |
| [`func_L01_003C08A0`](levels/func_L01_003C08A0.c) | 288 | The note holds only the function name; no difference or attempt is recorded. |
| [`func_L19_0041C328`](levels/func_L19_0041C328.c) | 176 | Draft, one attempt: 160 bytes vs 176 in 19_grelbin; code and flow reconstructed, but retail places the body at 0x41C358 before the test block at 0x41C380 while ours puts the tests first; a loop form for that layout remains. |
| [`func_L19_00423CC0`](levels/func_L19_00423CC0.c) | 508 | Draft, two attempts: 460 bytes vs 508 in 19_grelbin; the body is rebuilt but the switch dispatch tree (order and form of the 4 head branches) does not reproduce with a 2-case switch or permuted cases. |
| [`func_L00_0041AD40`](levels/func_L00_0041AD40.c) | 228 | kept as the worker's whole-file draft; no note was left |
| [`func_L00_00424978`](levels/func_L00_00424978.c) | 144 | NEAR MISS, 3 instructions: everything from 0x4249C0 matches; only the 0x4249B0 pair differs (ours bne $v0,$a2, retail bnel $v0,$a2 with lh $v0,0x84($v0) in the slot); pointer, array and key-local variants give the same pair. |
| [`func_L00_00426248`](levels/func_L00_00426248.c) | 248 | NEAR MISS, 8 bytes over (256 vs 248, LICM wall): both loop bodies match, but the 0x1B1880 address is hoisted into $s7 (one extra sd/ld pair) where retail re-materialises lui $v1,0x1B ; lw $v1,0x1880($v1) in the inner loop; a local base costs 268. |
| [`func_L13_0040A2C8`](levels/func_L13_0040A2C8.c) | 232 | Best attempt, not marked: 228 vs 232 bytes, and a (t, v, q) local variant drops to 220; the 128-bit zero store folds to sq $zero,0x20($sp) where retail keeps por $v0,$zero,$zero ; sq $v0,0x20($sp); declaration order (t, v, q) still to try. |
| [`func_L08_003D4600`](levels/func_L08_003D4600.c) | 144 | Draft, not matching (36 words, +0x2c): the beqz $s0 delay slot holds the constant 1 where retail fills it with addiu $a2,$s0,0x10, and the stores come out 0x31/0x30/0x20/0x32 against retail sb 0x30 / sb 0x31 / sh 0x32 / sb 0x20. |
| [`func_L08_003F6568`](levels/func_L08_003F6568.c) | 168 | Draft, not matching: 152 vs 168 bytes; the 4-instruction gap is the accumulator sel, kept as -1 in $s2 across both func_002A8600 calls in retail (sd $s2,0x20($sp), sra $s2,$v0,8) but rematerialised by GCC in the bnez slot. |
| [`func_L08_00435600`](levels/func_L08_00435600.c) | 160 | Draft, not matching, belongs in the -g8 sibling (lwc1 $f1,0x2FA4($gp)); at +0x3c one store pair is swapped (retail sb $v1,0x30($s0) then sh $a0,0x32($s0)); three source orders tried, one folds the 0xFF and gives 156. |
| [`func_L00_003EA7D0`](levels/func_L00_003EA7D0.c) | 140 | Draft w49: exact 140 bytes, one instruction out of place (+0x34); 0x1B2BA0 must be a symbol (a literal emits ori), but symbolic forms grow the frame to 144/148/152 and the literal schedules lui $a1,0x1B one step early in the first call. |
| [`func_L00_003ECB00`](levels/func_L00_003ECB00.c) | 52 | Draft w49: exact 52 bytes, only the first two instructions permuted (retail addiu $v1,$zero,0x3 before lui $v0,0x1B, ours emits ori $v0,$v0,0x7A4); the matching twin func_L00_003FF7B8 starts from a register, so cc1 always hoists this pair. |
| [`func_L00_003ECC90`](levels/func_L00_003ECC90.c) | 236 | Draft w49: exact 236 bytes; everything matches except a $s1/$s2 permutation (retail $s1 = i, $s2 = pa, ours reversed); declaration order, parameter copies and do/while do not change it; the addresses must stay symbols (addiu, not ori). |
| [`func_L00_002EA668`](levels/func_L00_002EA668.c) | 160 | Wave 5 w04: slot allocator over D_L00_001BCC90. Best of 7 compiles: exact 160 bytes, 32 of 40 words identical; the array-form loop and `unsigned short` params keep retail's four `andi 0xFFFF`, but cc1 gives D's %hi to $a2 and the field pointer to $a0 (retail keeps $a0 live across the loop and $a1 for the pointer), so the a2/a3 shorts land in $t5/$t6 and the second %hi is re-materialised where retail reuses $a0 and its G store takes $at. |
| [`func_L00_002EC230`](levels/func_L00_002EC230.c) | 20 | Wave 5 w04: not a function; linker padding between two bodies (three `addiu $sp,$sp,N; nop` fragments, N = 0x30/0x80/0xC0, no `jr $ra`). The body before it ends at 0x002EC22C, the next function starts at 0x002EC248. `tools/levels.py` extent fix, no C reaches it. |
| [`func_L00_002ED7F8`](levels/func_L00_002ED7F8.c) | 12 | Wave 5 w04: not a function; `addiu $sp,$sp,0x150; nop; addiu $sp,$sp,0x30`, the padding after the body ending at 0x002ED7F8, cut mid-pair; the next function starts at 0x002ED808. `tools/levels.py` extent fix. |
| [`func_L00_002EDCF0`](levels/func_L00_002EDCF0.c) | 16 | Wave 5 w04: G8 draft; retail `lwc1 $f0,-0x76DC($gp); nop; jr $ra; mul.s $f0,$f0,$f12`. With `extern float D_001A7914 __attribute__((sda));` in the -g8 sibling the load and the `mul.s` operand order match, but the pinned cc1 emits the compact three-instruction form (12 bytes): retail's `nop` before `jr $ra` is the wall recorded in docs/COMPILER-NOTES.md. |
| [`func_L00_002EDD28`](levels/func_L00_002EDD28.c) | 16 | Wave 5 w04: G8 draft, twin of func_L00_002EDCF0 at gp-0x76D8 (0x001A7918); same `nop`-before-`jr` wall. |
| [`func_L00_002EDD38`](levels/func_L00_002EDD38.c) | 16 | Wave 5 w04: G8 draft, twin of func_L00_002EDCF0 at gp-0x76D4 (0x001A791C); same `nop`-before-`jr` wall. |
| [`func_L00_002EF688`](levels/func_L00_002EF688.c) | 20 | Wave 5 w04: not a function; four `addiu $sp,$sp,0x10; nop` padding pairs after the body ending at 0x002EF684; the next function starts at 0x002EF6A0. `tools/levels.py` extent fix. |
| [`func_L00_002EE340`](levels/func_L00_002EE340.c) | 72 | Wave 5 w04: no C attempt; VU0 macro mode (ldc2 x2 + seven COP2 macro-mode words + qmfc2 family, returns 0/1 by `bgtz`). Plain C cannot emit COP2 ops: ASM_FUNC candidate, same class as the 002EE058/002EE078/002EE290 stubs next door. |
| [`func_L00_002EE4D8`](levels/func_L00_002EE4D8.c) | 104 | Wave 5 w04: no C attempt; VU0 macro mode (ldc2, mfc1, twelve COP2 macro words, sdc2, two return paths 1/0). No C spelling of the COP2 stream: ASM_FUNC candidate. |
| [`func_L00_002EE660`](levels/func_L00_002EE660.c) | 72 | Wave 5 w04: no C attempt; VU0 macro mode plus MMI (ldc2, ori 0x7FFF, mfc1, SPECIAL2 words 0x70421488/0x70631C88/0x702208E8/0x702308C8, COP2 0x48A11000/0x4BE20858/0x4BE1097C/0x48210800). ASM_FUNC candidate. |
| [`func_L00_002EBE88`](levels/func_L00_002EBE88.c) | 776 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_002ECFE8`](levels/func_L00_002ECFE8.c) | 552 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1f). |
| [`func_L00_002F74F0`](levels/func_L00_002F74F0.c) | 120 | Wave 5 w04 tour 2: no C attempt; starts with addiu $sp,$sp,+0x10 (catalogue entry early). |
| [`func_L00_003A0DA0`](levels/func_L00_003A0DA0.c) | 396 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1c). |
| [`func_L00_003EED10`](levels/func_L00_003EED10.c) | 268 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003EF3E0`](levels/func_L00_003EF3E0.c) | 356 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003EFF58`](levels/func_L00_003EFF58.c) | 328 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F26C8`](levels/func_L00_003F26C8.c) | 468 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F3518`](levels/func_L00_003F3518.c) | 644 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F37E8`](levels/func_L00_003F37E8.c) | 668 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F3AD0`](levels/func_L00_003F3AD0.c) | 668 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F4B68`](levels/func_L00_003F4B68.c) | 256 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F4C68`](levels/func_L00_003F4C68.c) | 608 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F4EC8`](levels/func_L00_003F4EC8.c) | 724 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F5238`](levels/func_L00_003F5238.c) | 340 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F5390`](levels/func_L00_003F5390.c) | 708 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F5750`](levels/func_L00_003F5750.c) | 288 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_003F5870`](levels/func_L00_003F5870.c) | 384 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1c). |
| [`func_L00_003F5F90`](levels/func_L00_003F5F90.c) | 468 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1c). |
| [`func_L00_003F6168`](levels/func_L00_003F6168.c) | 416 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L02_0032EF10`](levels/func_L02_0032EF10.c) | 248 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L02_0032F008`](levels/func_L02_0032F008.c) | 12 | Wave 5 w04 tour 2: no C attempt; starts with addiu $sp,$sp,+0xc0 (catalogue entry early). |
| [`func_L02_00332A80`](levels/func_L02_00332A80.c) | 676 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1f). |
| [`func_L02_00332D28`](levels/func_L02_00332D28.c) | 456 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L02_00332EF0`](levels/func_L02_00332EF0.c) | 608 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L02_00333218`](levels/func_L02_00333218.c) | 232 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1c). |
| [`func_L02_00333480`](levels/func_L02_00333480.c) | 452 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L04_004323F8`](levels/func_L04_004323F8.c) | 312 | Wave 5 w04 tour 2: no C attempt; jr $t (jump table) @+184. |
| [`func_L04_00432530`](levels/func_L04_00432530.c) | 224 | Wave 5 w04 tour 2: no C attempt; jr $t (jump table) @+128. |
| [`func_L04_0043BC58`](levels/func_L04_0043BC58.c) | 488 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L04_00456860`](levels/func_L04_00456860.c) | 396 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1c). |
| [`func_L04_0046CE68`](levels/func_L04_0046CE68.c) | 336 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_00427090`](levels/func_L05_00427090.c) | 420 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_004276E8`](levels/func_L05_004276E8.c) | 640 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_00427968`](levels/func_L05_00427968.c) | 28 | Wave 5 w04 tour 2: no C attempt; starts with addiu $sp,$sp,+0x40 (catalogue entry early). |
| [`func_L05_00427988`](levels/func_L05_00427988.c) | 404 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_00427B20`](levels/func_L05_00427B20.c) | 752 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_00427E10`](levels/func_L05_00427E10.c) | 404 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_00427FA8`](levels/func_L05_00427FA8.c) | 28 | Wave 5 w04 tour 2: no C attempt; starts with addiu $sp,$sp,+0x40 (catalogue entry early). |
| [`func_L05_00445B58`](levels/func_L05_00445B58.c) | 224 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_00445C38`](levels/func_L05_00445C38.c) | 292 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L05_00445D60`](levels/func_L05_00445D60.c) | 560 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L08_003B2DB0`](levels/func_L08_003B2DB0.c) | 444 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L08_003C66F0`](levels/func_L08_003C66F0.c) | 200 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L08_003C67B8`](levels/func_L08_003C67B8.c) | 756 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L08_003CD648`](levels/func_L08_003CD648.c) | 128 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L08_003CD6C8`](levels/func_L08_003CD6C8.c) | 60 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L11_002C5E80`](levels/func_L11_002C5E80.c) | 512 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L11_00397C08`](levels/func_L11_00397C08.c) | 472 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L11_003F4198`](levels/func_L11_003F4198.c) | 472 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L11_00414660`](levels/func_L11_00414660.c) | 288 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L11_0046B9F0`](levels/func_L11_0046B9F0.c) | 536 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L11_0046BC08`](levels/func_L11_0046BC08.c) | 268 | Wave 5 w04 tour 2: no C attempt; MMI/LQ/SQ (op 0x1e). |
| [`func_L00_002F46C8`](levels/func_L00_002F46C8.c) | 88 | Wave 5 w04 tour 2: best of 4 compiles, 92 vs 88 bytes; retail's two guards are `bnel` with `ld s0,0(sp)` in the annulled slot, this cc1 emits a plain `bnez` and fills the slot from the fall-through. `-mbranch-likely` is not an option of the pinned cc1; the sdk profile gives 88 bytes but a 0x20 frame against retail's 0x10. |
| [`func_L00_002F6B90`](levels/func_L00_002F6B90.c) | 80 | Wave 5 w04 tour 2: exact 80 bytes, 3 words off - `lw $a0,0x10($a0); beqz; jalr $a0` where retail loads into `$v0` (the pointer dies at the load; four source shapes all tie it to $a0). |
| [`func_L11_0045FEF8`](levels/func_L11_0045FEF8.c) | 180 | Wave 5 w04 tour 2: exact 180 bytes, every instruction and delay slot matching; only the `$s0`/`$s1` pair is swapped (retail: pointer in `$s1`, int in `$s0`), which flips the two prologue `sd`. A local `char *p` is required (inline `a0+0x10` costs 184). |
| [`func_L00_002A4D18`](levels/func_L00_002A4D18.c) | 152 | Wave 5 w12: exact 152 bytes, 30 of 38 words. Everything through the `sb` matches, then the `sw $zero,0x2294($v0)` store is scheduled three slots early and the jal delay slot gets a nop (39 vs 38). `volatile` on D_001A73A0 is what emits retail's `lui $at,0x1A ; sw $zero,0x73A0($at)` (plain = `sw $zero,0($gp)`, 148 bytes; `nosda` = a hoisted `lui $a0`; a literal address = 156); the file must stay -G8 (at -G0 the D_001A79F0 load is `lui $a0 ; lw $v1`). The source order of the two 0.8f/0.7f pairs decides the emitted order: with 0x250/0x254 written first, cc1 emits retail's 264, C24, 258, 25C, sb. Eight source orders and four placements of the two zero stores tried. |
| [`func_L00_002A4DB0`](levels/func_L00_002A4DB0.c) | 152 | Wave 5 w12: exact 152 bytes, 8 words differ, 3 of them relocations. The sum tree and every instruction agree; only the two byte temporaries' registers are swapped ($a0 with $v0) - a pure allocation tie (retail keeps the 0x10 byte in $a0, the accumulator and result register). Typed array indexing (`rows[a1]`, `words[a2]`) is what puts the base first in the two addu; flat char-pointer arithmetic leaves the scaled index first. Ten spellings tried, all 8 words; the struct and literal variants are worse. |
| [`func_L00_002A4E48`](levels/func_L00_002A4E48.c) | 164 | Wave 5 w12: 156 vs 164. Control flow and every branch target line up; the gap is codegen habits: retail materialises the resident base twice (`addiu $a1,$v0,-0x61E0` for the 0x22B4/0x22BA/0x24EC probes, `addiu $a0,$a2,-0x61E0` from the same lui for the 0x24CC/0x1492 tail), fills the three slots with `addiu $v1,$zero,1`/nop and keeps plain `beqz/beqz/bnez`, and loads the table field as `lw $v0,0x28($v1)` after `addiu $a0,$a0,0x2570`; cc1 here forms `beql/bnel`, duplicates `lw $v1,0x24CC` into all three slots and folds the 0x28 into the symbol addiu. D_00139568 is resident (same 0x139568 in level 02), D_L00_00262570 per level (0x264A70 there); `kind * 0xE0` does give retail's single `mult $v1,$a1`. |
| [`func_L00_002C3A40`](levels/func_L00_002C3A40.c) | 56 | Wave 5 w12: if-conversion wall. The function is `0xC` before the call, `if (state == 2)` zeroes it, return it - six shapes tried (init-then-if, if/else, ternary, early return, switch, state latched into a local) all compile to 40-44 bytes with `xori`+`movz` where retail has `addiu $s0,$zero,0xC` in the jal delay slot and the conditional assignment in a `beql` slot. Retail keeps the constant in a callee-saved register across the call. |
| [`func_L01_00337B80`](levels/func_L01_00337B80.c) | 12 | Catalogue defect: opens on `addiu $sp,$sp,0x80 ; nop`, the previous body's epilogue runs to 0x337B7C and real code resumes at 0x337B90; no `jr $ra` in the extent. |
| [`func_L01_0034EA48`](levels/func_L01_0034EA48.c) | 12 | Catalogue defect: two `addiu $sp,$sp,0x10 ; nop` pairs between a body ending at 0x34EA44 and code resuming at 0x34EA58; no `jr $ra`. |
| [`func_L02_00457068`](levels/func_L02_00457068.c) | 12 | Catalogue defect: `addiu $sp,$sp,0x20 ; nop ; addiu $sp,$sp,0x20`; no `jr $ra`. |
| [`func_L22_0032F2C0`](levels/func_L22_0032F2C0.c) | 12 | Catalogue defect: `addiu $sp,$sp,0x50 ; nop ; addiu $sp,$sp,0x40`; no `jr $ra`. |
| [`func_L22_002F3B38`](levels/func_L22_002F3B38.c) | 24 | COP2/VU0: `ldc2`/`sdc2` around a COP2 macro word; handwritten vector routine, ASM_FUNC territory, no C. |
| [`func_L18_003F7320`](levels/func_L18_003F7320.c) | 52 | Wave 5 w12: accumulator wall. Retail keeps one `$a2` accumulator (`daddu $a2,$zero,$zero` before the test, one shared `addiu $a2,$zero,0x1`, `daddu $v0,$a2,$zero` at the return); six shapes tried - the plain if/else if-converts to 44 bytes with `xori`, and the branch-keeping ones return in `$v0` with the pointer in `$v1` instead of `$a1`. Its sibling func_L18_003F7470 matched with the inverted-early-return lesson: writing `if (gate == 0) { work; return work; } return 0;` is what makes cc1 emit the plain `bnez` where it otherwise forms `bnel`. |
| [`func_L13_00439A70`](levels/func_L13_00439A70.c) | 112 | Wave 5 w12, tour 2: everything from the record load to the epilogue matches (`lw $v1,0x1C($v1)` in the bne slot, the two `addiu $v0,$zero,3`, `sh $v0,0x7E($a2)`). Residual: the resident-base order against the symbol-load form. `extern int D_L13_001B22C4 __attribute__((nosda));` in a -g8 sibling gives retail's order but `lui $a1,$hi ; lw $v1,%lo($a1)`; adding the alias pair `extern int D_L13_001B22C4_v __asm__("D_L13_001B22C4");` and using it gives retail's `lui $v1 ; lw $v1` and the right delay slot, but hoists the load three slots above the index load. The alias lever trades the load form for the schedule. |
| [`func_L02_00416800`](levels/func_L02_00416800.c) | 68 | Wave 5 w12, tour 2: 68 bytes, 9 words, all register names; the instruction sequence is retail's exactly (including the `bc1tl` with the 1 in the slot). Retail keeps the state byte in `$v0`, the constant in `$v1`, the pointer in `$a1` and the accumulator in `$a2`; ours is the same four roles one register lower. Five spellings tried, all identical (a pure allocation tie). |
| [`func_L02_00411530`](levels/func_L02_00411530.c) | 80 | Wave 5 w12, tour 2: 68 vs 80; the twelve bytes are the re-materialisation of 1.0f before the subtraction, and they follow from the FPR split: retail holds 1.0f in `$f0` and the clamped value in `$f1`, so the second clamp loads 0.0f into `$f0` and destroys it; ours is the mirror image and keeps the constant live. Nine shapes tried (constant on either side of both clamps, ternary, nested ternary, bounds as locals, negation tests), all the same split. |
| [`func_L22_0032F228`](levels/func_L22_0032F228.c) | 52 | Catalogue defect: six `addiu $sp,$sp,N ; nop` pairs (0x30, 0x50, 0x70, 0x10, 0x40, 0x70, 0x40), no `jr $ra`. An extent fix in tools/levels.py. |
| [`func_L09_003F7370`](levels/func_L09_003F7370.c) | 120 | MMI (LQ/SQ macro words 0x78620000/0x7C820000) between the stack setup and an eight-argument call to func_3F4120; ASM_FUNC territory. |
| [`func_L05_003CEFA0`](levels/func_L05_003CEFA0.c) | 160 | MMI: three LQ/SQ words (a 128-bit copy of the s2+0x38 doubleword into s1+0x38) plus a `$gp+0xB78` float table indexed by the 0xAA halfword; the rest of the body is ordinary. ASM_FUNC or a later pass with the TI128 levers. |
| [`func_L05_003CFB90`](levels/func_L05_003CFB90.c) | 148 | Wave 5 w12, tour 2: everything matches except a rotation of three constant stores in the block setup. Retail emits `sb $a1,0x19` (a1 = 2), `sw $v0,0x14` (v0 = 0x10000), `sw $v1,0x20` (v1 = 1); ours emits the same three stores rotated (`sw $a1,0x20` with a1 = 1, `sb $v0,0x19`, `sw $v1,0x14`). Two source orders give the identical rotation. The 0x45AFDF66 literal round-trips as 5627.9248046875f. |
| [`func_L18_003F7988`](levels/func_L18_003F7988.c) | 124 | MMI: four LQ/SQ words (the s0+0x10 block into the record's +0x210, the record's +0x220 into s0+0xF0) around a halfword-to-float store (`lh 0x24` / `cvt.s.w` / `swc1 $f0,0x20`), then two calls. ASM_FUNC or the TI128 levers. |
| [`func_L22_003300E8`](levels/func_L22_003300E8.c) | 124 | MMI: four LQ/SQ words (the caller's doubleword into the sub object at s0+0xA0, s1+0x10 onto the stack), then `jal func_32F7A8(s1, s0, sp, sp+0x10, 0)` and the `lw 0x94($s0)` / constant-8 answer. ASM_FUNC or the TI128 levers. |


## Matched, but not landable under the Plain C rule (2026-10-13)

Bodies the build placed, removed from `src/` because they call `qcopy`/`qzero` from
`include/common.h`, which are inline assembly - AGENTS.md's "Plain C" rule (no inline
assembly in a function) rules that out as a matching route, and docs/COMPILER-NOTES.md says
so. They are kept as starting points: the shape is right, and only the 128-bit copies need a
plain-C spelling. Their markers are back in `src/`.

| Function | Size | Why it is here |
| [`func_L07_003F8BB0`](levels/func_L07_003F8BB0.c) | 160 | plain C, and it matched until the four `qcopy` bodies above it in the same file were taken out: one compilation unit, so a body's register allocation depends on its neighbours |
| [`func_L00_003B7410`](levels/func_L00_003B7410.c) | 152 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L00_003E5650`](levels/func_L00_003E5650.c) | 188 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L00_003EFA00`](levels/func_L00_003EFA00.c) | 152 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L00_00403D58`](levels/func_L00_00403D58.c) | 240 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L00_00404DD8`](levels/func_L00_00404DD8.c) | 256 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L03_003C7AA0`](levels/func_L03_003C7AA0.c) | 28 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L03_003C8660`](levels/func_L03_003C8660.c) | 56 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L05_00416E08`](levels/func_L05_00416E08.c) | 180 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L06_0045A0D0`](levels/func_L06_0045A0D0.c) | 224 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L07_003F4C18`](levels/func_L07_003F4C18.c) | 168 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L07_003F8010`](levels/func_L07_003F8010.c) | 168 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L07_003F8828`](levels/func_L07_003F8828.c) | 108 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L07_003F8898`](levels/func_L07_003F8898.c) | 112 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L11_004565D8`](levels/func_L11_004565D8.c) | 228 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L16_00411ED0`](levels/func_L16_00411ED0.c) | 172 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |
| [`func_L19_00422178`](levels/func_L19_00422178.c) | 192 | matched byte for byte, but the body needs `qcopy`/`qzero` (inline assembly): not a route under the Plain C rule |


### Found on the second pass (2026-10-13)

The first sweep used a pattern that missed definitions whose opening brace sits on the same line as
the signature, and these bodies call the helpers through a local `__asm__` copy. Same rule, same
treatment.

| [`func_L01_003DCD40`](levels/func_L01_003DCD40.c) | 148 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L01_003DCDD8`](levels/func_L01_003DCDD8.c) | 152 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L02_003CADA8`](levels/func_L02_003CADA8.c) | 72 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L02_003D01F0`](levels/func_L02_003D01F0.c) | 72 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L02_003F1608`](levels/func_L02_003F1608.c) | 160 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L02_0040BAB8`](levels/func_L02_0040BAB8.c) | 228 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L08_003CD600`](levels/func_L08_003CD600.c) | 68 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L08_003CDB38`](levels/func_L08_003CDB38.c) | 60 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L14_00328E70`](levels/func_L14_00328E70.c) | 32 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L14_00390DF8`](levels/func_L14_00390DF8.c) | 196 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L18_003FB5E8`](levels/func_L18_003FB5E8.c) | 40 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L20_0039C448`](levels/func_L20_0039C448.c) | 248 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L20_0039C540`](levels/func_L20_0039C540.c) | 140 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L20_0039E158`](levels/func_L20_0039E158.c) | 140 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L20_0039F210`](levels/func_L20_0039F210.c) | 32 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |
| [`func_L20_0043D470`](levels/func_L20_0043D470.c) | 100 | matched byte for byte, but the body needs `qcopy`/`qzero` or a local `__asm__` helper: not a route under the Plain C rule |

## Linked at some of their places only (2026-10-12)

C that was in `src/` and links in some programs but not in all: a level address written into
it, a jump table it needs, or two different functions the catalogue holds as one. A function
counts when it links at every place it has.

| Function | Places it links at | What the build says elsewhere |
| --- | --- | --- |
| [`func_002DCAB8`](game/func_002DCAB8.c) | 26 of 28 | instruction at +0x38 differs |
| [`func_L00_002B1308`](levels/func_L00_002B1308.c) | 0 of 27 | needs .rodata at its place, which is not C |
| [`func_L00_002D4A38`](levels/func_L00_002D4A38.c) | recovered: 27 of 27 | Current C in `src/levels/shared/L00_002D3798.c` uses an opaque `sda` reference under G0; the old partial draft is retained here for reference. |
| [`func_L00_00424BB8`](levels/func_L00_00424BB8.c) | 23 of 27 | instruction at +0x28 differs: `16_snivelak`, `22_dobbo_orbit`, `23_damosel_orbit` and `26_jamming_array` hold other constants |
| [`func_L02_003F9AF0`](levels/func_L02_003F9AF0.c) | 0 of 1 | needs .rodata at its place, which is not C |
| [`func_L02_003F9B70`](levels/func_L02_003F9B70.c) | 0 of 1 | needs .rodata at its place, which is not C |
| [`func_L02_003FE1B0`](levels/func_L02_003FE1B0.c) | 0 of 1 | needs .rodata at its place, which is not C |
| [`func_L11_0043E090`](levels/func_L11_0043E090.c) | 0 of 1 | needs .rodata at its place, which is not C |
| [`func_L11_0043E108`](levels/func_L11_0043E108.c) | 0 of 1 | needs .rodata at its place, which is not C |
| [`func_L11_004433D8`](levels/func_L11_004433D8.c) | 0 of 1 | needs .rodata at its place, which is not C |
| [`func_00120A30`](core/func_00120A30.c) | 132 | Loop, table and byte-kind test exact; this cc1 leaves the index in `$a0` where retail copies it to `$a2` at entry. Three loop wordings give the same 132 bytes |
| [`func_00122760`](core/func_00122760.c) | 152 | Every instruction matches; 4 short because retail has a standalone `nop` before the `bnel`, and the normal path's three stores take different slots |
| [`func_0011B0B0`](core/func_0011B0B0.c) | 144 | C except the two `sync` barriers, which no construct in this toolchain emits (`__sync_synchronize()` becomes a call). May belong as ASM_FUNC |
| [`func_0012CAB0`](core/func_0012CAB0.c) | 268 | Same size, `instruction at +0xc differs`: retail gives the constant 5 `$s6` and 0x1B3 `$s5`, this candidate the other way; the tail follows them |
| [`func_001306D0`](core/func_001306D0.c) | 216 | Same frame and callee-saved set; 8 short because retail rematerialises the stride three times and reloads the masks from the stack |
| [`func_001290BC`](core/func_001290BC.c) | - | VU0 macro code (`sqc2`/`lqc2` plus COP2 words) and the extent starts on the 0xCDCDCDCD linker fill |
| [`func_00125D94`](core/func_00125D94.c) | - | Cannot match as it stands: the 192-byte extent begins on a 0xCDCDCDCD fill word; the function is the 188 bytes from +4 |
| [`func_00128FD0`](core/func_00128FD0.c) | - | Reads `$s4` at +0x10 before anything writes it; the result keeps bits 32..63 of that register |
| [`func_00115690`](core/func_00115690.c) | - | R5900 MMI instructions (opcode 0x1C) in a word-at-a-time memcpy; rabbitizer prints them as `.word # INVALID` |
| [`func_0012DF18`](core/func_0012DF18.c) | - | Contains a `sync` at +0xEC, same barrier problem as func_0011B0B0; the rest looks like ordinary C |
| [`func_001208E8`](core/func_001208E8.c) | - | Not attempted in this run; ordinary-C shape, an entry walk with indirect calls |
| [`func_0012DAC0`](core/func_0012DAC0.c) | - | Not attempted in this run; four early exits, three calls and a packed word from six shifted fields |
| [`func_00124414`](core/func_00124414.c) | 164 | not a C target: hand-written COP2/VU0 code (`sync`, `cfc2`/`ctc2` $28/$29/$404, four `qmfc2`/`qmtc2` words); the first word also reads $t6, so the row starts mid-instruction |
| [`func_001184D0`](core/func_001184D0.c) | 120 | 120 bytes, matching retail instruction for instruction including the `bnel` and its delay-slot increment; only the base pointer's register differs (retail copies the parameter to $t1, this cc1 to $t0, counters in $t0/$t1 respectively). Needs the game compiler, not the unit's `sdk` line. Wave 4 (32 scratch bodies): the tie follows one knob - whether the counter is live in the entry block - and that knob also decides the `beqz` delay slot, so the two fixes are mutually exclusive; `register` and every declaration-order permutation are inert, closest bodies 8 words apart either way |
| [`func_00124780`](core/func_00124780.c) | 148 | 148 bytes; control flow, calls and relocations agree, the order of two adjacent store pairs does not (retail writes buf[1] then buf[2], and $s1 before $s0) |
| [`func_00124E08`](core/func_00124E08.c) | 736 | sceCdInit; 736 bytes, the control flow, both spin loops, the two scePrintf strings, the init_mode switch and every relocation agree - the callee-saved allocation is a rotation of retail's over the same eight values, and the counter base is copied from a temporary into a callee-saved register where retail materialises it there |
| [`func_0012EAA0`](core/func_0012EAA0.c) | 136 | Candidate is 96 (SDK) or 104 (game); retail selects the case with a value tree (`beq`/`sltu`+`bnel`/`beq`) where both cc1s emit two equality tests, and retail's unused 16-byte frame is not allocatable from this body |
| [`func_00131620`](core/func_00131620.c) | 72 | The row starts 8 bytes early: retail 0x131620 is an unreachable `sdr`+`nop` and the body's 64 bytes are at 0x131628. The candidate matches that body except the byte global's address register ($v1 in retail) |
| [`func_00115AC0`](core/func_00115AC0.c) | - | SDK `strncpy`: 17 of 115 words are MMI (`lq`/`sq`/`pcpyld`/`pcpyud`/`psubb`/`pand`, printed as `.word # INVALID`); the source is newlib's hand-written `strncpy.S`. ASM_FUNC candidate, like func_00115690 and func_00115340 |
| [`func_00132DF0`](core/func_00132DF0.c) | 716 | 989snd `snd_QueueCommandToRing`; 716 bytes against 736 - the two fast paths, the alignment guard, the scan loop's `continue`/`break` shape, the 4-byte header, the byte copy and the 16-byte ring-entry stores agree, but this cc1 keeps the loop constants and the error-string base in `$s6`/`$fp`/`$s7` and spills cmd/data/arg3 where retail keeps the three arguments in callee-saved registers. Records the measured `sda` recipe for a gp-relative array base: the extern array needs a size the -G8 limit accepts |
| [`func_00133108`](core/func_00133108.c) | - | 989snd `snd_SendCurrentBatch` (rac1-decomp 989snd.c func_0012EB18, delta +0x45F0; rac1 keeps it as INCLUDE_ASM too). Not attempted: needs a `-g8.c` unit for the `$gp` tables at 0x001A74A0-0x001A74F8, and the sceSifCheckStatRpc poll's arms do not fall out of an obvious wording yet |
| [`func_0012D4B0`](core/func_0012D4B0.c) | - | rac1's func_00129690 (delta +0x3E20), which rac1 also keeps as INCLUDE_ASM; bytes are identical to the rac1 boot ELF's 0x00129550. Not attempted: 692 bytes of arm selection, no source on either side |
| [`func_00117650`](core/func_00117650.c) | - | Not attempted: raw double-bits helper in the libc object; exponent split at 20/52, `dpadd` against 1e+300 (0x0013A3E8), 0xFFFFFFFF via `dsrl32`. Identity not established - looks like a `dtoa` decimal helper, not libgcc. Needs its own `-mastra-quad-saves` unit |
| [`func_001182B8`](core/func_001182B8.c) | - | Not attempted: same object and `-mastra-quad-saves` prologue; `dpmul` by 2^54, exponent re-bias, returns the -inf/+inf pair at 0x0013A480/0x0013A488. Identity not established |
| [`func_001236C8`](core/func_001236C8.c) | - | Not attempted: record-chain validator (two `abort()` guards on `s[1]` before and after func_001242A0) plus a heap sift-down over `s[2]` keyed on each pointee's +8 |
| [`func_0011DE08`](core/func_0011DE08.c) | 516 | sceFsInit (Lombyte's NON_MATCHING body). All 516 bytes match - frame, six saves in the same 16-byte slots, relocations - except the prologue's first two saves: retail walks `s0, s1`, this sdk cc1 walks `s1, s0` (the walk is rotated one register). Compiler knob, not source shape |
| [`func_0012E608`](core/func_0012E608.c) | 656 | _csc_storeRefImage (Lombyte's NON_MATCHING body). Every instruction matches except TWO delay slots: the volatile store retail puts in `enable_dmac(4)`'s slot, and the `jal func_0011F628` retail puts in the `beqz`'s slot (this cc1 emits `nop` in both and keeps the insn before). Wave 4: SDK-plausible prototypes (void returns for enable_dmac/EnableInterrupts/disable_dmac/RemoveDmacHandler/sendIpuCommand/_doCSC2) shift the IPU_CTRL read from `$v1` to `$v0` and leave it at 656 |
| [`func_001286C8`](core/func_001286C8.c) | 228 | libdbc's send-side twin of sceDbcReceiveData. Field stores, byte loop and the 0x8000091B RPC all right, but this cc1 materialises the buffer base twice (retail builds `%hi`/`%lo` once and keeps it in $t0), so the base lands caller-saved and the function is 4 bytes long |
| [`func_00126284`](core/func_00126284.c) | - | MMI instruction (`0x700014A9`, opcode 0x1C) plus a `sync`, and its first words read `$t6` before anything writes it - hand-written assembly. The legible part is a fixed-point packer with a 0x3B-case jump table at 0x13B3F0 writing twelve tagged quadwords to the caller's structure; belongs as ASM_FUNC |
| [`func_00123A00`](core/func_00123A00.c) | - | Not attempted in this run; one of the 0x123xxx record-parser family whose neighbours are matched in src/core/001232F0.c. Ordinary C, game-format code, no upstream source |
| [`func_001240C8`](core/func_001240C8.c) | - | Not attempted in this run; same 0x123xxx record parser. Frame and the 0x2F8 scratch record's ld/sd copy loop are the first things to pin down; decodable from the disassembly, no upstream source |
| [`func_00123D30`](core/func_00123D30.c) | 920 | libgcc `frame.o` `execute_cfa_insn`; the body is byte-exact - `tools/check.py` says `1 of 1 places match` - but `tools/build.py boot` refuses it with `needs .rodata at its place, which is not C`: the `switch (insn)` jump table lands in the object's `.rodata` and retail's is at 0x13B070 inside the loaded image. A build-side placement or a `tools/levels.py` piece unblocks it; the source needs nothing further |
| [`func_00120528`](core/func_00120528.c) | 728 | libgcc `_eh.o`, three functions in one extent: `__sjthrow`, `__sjpopnthrow` (0x001206A0) and `__eh_rtime_match` (0x001207B8). Candidate is 720 bytes; the bodies, the 0x750 frame and `__sjpopnthrow`'s missing epilogue are right, but `cleanup` lands in `$a0` where retail uses `$a1`, cc1 sinks `__builtin_setjmp`'s `addiu $v0,$zero,1` block to the end instead of leaving it inline at +0xB0, and it folds `__builtin_longjmp`'s `jr $v0` into a direct branch - the 8 bytes. Section attributes pin the two later bodies into `.text.func_00120528`, so one 728-byte symbol appears as soon as the extent is split |
| [`func_00120F00`](core/func_00120F00.c) | 476 | libgcc `__throw` (`_eh.o`); candidate is 480 bytes, retail's own offsets throughout except `__builtin_eh_return`'s stub, which cc1 emits between the memcpy loop and its 16-byte leftover instead of before the epilogue - the `b`'s delay slot stays a `nop` where retail runs the eh-register load there. Everything else, the 0x2F0-byte `frame_state` block move included, is byte-identical. Wave 4: the stub's place is invariant in the source - 13 bodies (named temp, `(void)` and `(long)` casts, `return __builtin_eh_return(...)`, explicit `return;`, `__builtin_memcpy`, explicit `0x2F0`, struct assignment, swapped cfa/memcpy order, 3-arg vs 2-arg) either give the very same 480 bytes or, for the reordered one, worse; `-fno-schedule-insns2` changes the prologue (484 bytes), so retail's build has both schedulers on |
| [`func_00122800`](core/func_00122800.c) | 576 | libgcc `_fpadd_parts` (`dp-bit.o`); instruction-identical but for `IMPLICIT_1 - 2` = 0x0FFFFFFFFFFFFFFE, twice: gas patch 0052 expands it top-down in 16-bit fields (7 instructions) where the unpatched mask path below gives retail's `addiu -1` / `dsll 5` / `dsrl 4` (3) |
| [`func_00127340`](core/func_00127340.c) | 444 | Row opens with two words of the previous function (`andi $v0,$v0,0x1` and a `pref` on arbitrary data, both reading registers nothing writes); the real 436-byte body starts at +0x8. A `tools/levels.py` split, not a C body |
| [`func_00131D98`](core/func_00131D98.c) | 656 | Row holds two functions: the 80-byte `main` loop driver at 0x00131D98 and a separate 576-byte body from +0x50. A `tools/levels.py` split at 0x00131DE8 unblocks both |
| [`func_001326D8`](core/func_001326D8.c) | 316 | SDK `snd_BankLoadFromIOP`; candidate is 328 bytes. The `-g8` unit route works (sda globals at GP 0x001AEFF0, the sentinel unsigned as `lui`+`ori`, its address absolute where retail passes it and literal in the wait loop); the residue is the scheduling around the busy-flag block and the wait paths |
| [`func_00132028`](core/func_00132028.c) | 488 | SDK `snd_Pump`; not attempted - it needs the `-g8` sibling unit (now proven by func_001326D8, same unit) and 488 bytes of indirect calls; sixteen gp globals and three absolute objects are mapped in the file |
| [`func_00122B00`](core/func_00122B00.c) | 680 | libgcc dp-bit.o `dpmul`: retail's own 680 bytes, every instruction matching except one delay slot - retail's rounding test is `bnel` with `tmp->fraction = high` annulled into the slot, this cc1 emits `bne` with the CLASS_NUMBER constant, which moves the merge point's first instruction and the two that follow. Swapping the final stores is measured to change nothing |
| [`func_00133580`](core/func_00133580.c) | 180 | snd_CheckLoadInProgress; 180 of 188 with `sltiu` and the right calls. Retail rematerialises `lui/addiu` for D_001A7100 before each of the two func_0011B500 calls and keeps only 0x1A713F in a saved register; this cc1 hoists the %hi outside the loop and needs a third saved register |
| [`func_001253A8`](core/func_001253A8.c) | 480 | sceCdRead; complete C written 2026-10-15: control flow, the 0x918/0x924 `mult`, both Kprintf guards and the nine-argument RPC call reproduce, but the allocation rotates (frame 0x90 against 0x80, a seventh callee-saved `$s6` live -- `$s2`/`$s3` and the flag base trade places; same wall class as func_0011DE08). One shape measured |
| [`func_0011D620`](core/func_0011D620.c) | - | Not attempted in this run; Sony libsif sceSifCallRpc, no public source on the machine. The near-miss records the nine-argument convention (a0-a3, t0-t3, 0(sp)) and the call site in func_001253A8 that pins it |
| [`func_0012E378`](core/func_0012E378.c) | - | Not attempted in this run; chained IPU colour conversion of cod/022FA8 ("CSC handler error"). Ordinary C, no VU0/MMI/`sync`; the head divides the row argument by 1023 |
| [`func_00129450`](core/func_00129450.c) | - | Not attempted in this run; Sony libmpeg slice/frame setup over the IPU. Ordinary C (the `.word # INVALID` words are R5900 three-operand `mult`). Head decoded: `div` by 0x12C($s0), scale by 16, spin on 0x1000D400 bit 0, `mult` by 0x140 |
| [`func_00132318`](core/func_00132318.c) | - | Not attempted in this run; snd_BankLoadByLoc belongs in a `00131780-g8.c` sibling (four gp-relative globals) before any C can match. All six callees are named in the near-miss |
| [`func_0012646C`](core/func_0012646C.c) | - | Not a C row: the 324-byte extent opens on the 0xCDCDCDCD fill word and the real body starts at 0x126470 (already its own symbol), then runs hand-written MMI. Needs tools/levels.py, then ASM_FUNC |
| [`func_00117278`](core/func_00117278.c) | 884 | newlib `_strtol_r` (the `-O0` unit its `strtol` shim src/core/001175F0.c calls); candidate size exact and 219 of 221 instructions identical. Both differences are the `dli` form of 0x8000000000000000: retail `addiu $x,$zero,-1; dsll32 $x,$x,31`, this cc1 `ori $x,$zero,0x8000; dsll32 $x,$x,16`. A seven-spelling probe shows no source form reaches retail's pair - patch 0052's rule covers the runs of ones but not the single-bit-at-63 case |
| [`func_00117848`](core/func_00117848.c) | - | Not attempted: double bit-splitting helper in the libc/double pocket (with func_00117650, func_001182B8), `-mastra-quad-saves` prologue; calls dpmul (func_122B00) twice and func_122DA8 once. Identity not established |
| [`func_00117CA0`](core/func_00117CA0.c) | - | Not attempted: the pocket's arithmetic engine, called by func_001182B8; 27 dpmul, 12 dpadd, 12 sub, 3 litodp, 1 dpcmp, 1 func_122DA8. Identity not established - the call inventory is in the near-miss |
| [`func_00125A20`](core/func_00125A20.c) | 884 | Sony libgraph graph003.o display-env builder (the engine behind sceGsDefDispEnv, named by rac1-decomp for the object and by the caller `0027F128.c` for the signature): candidate compiles to 904 bytes, 182 of the ~221 instructions differ. The prologue, the ROMVER/offset gate, the 0x66 and 1/2/3 stores and the DISPFB qword already line up; the four mode arms (2, 3, 0x50, error printf) do not - each computed arm carries one dead multiply beside the live R5900 MULT1, and two of their tails are cross-jumped at +0x568 and +0x640, which is the shape a next attempt has to reproduce |
| [`func_0011FC68`](core/func_0011FC68.c) | - | libgcc `__divdi3`; the recovered libgcc2.c source (`L_divdi3`, with longlong.h's MIPS fallbacks `__udiv_qrnnd_c` and the `__clz_tab` `count_leading_zeros`) is exact for the first 394 of retail's 443 instructions, but the candidate is 1788 bytes against 1772: it places six nops where retail places nine, and schedules the closing `__negdi2` four instructions longer. Its 256-byte `__clz_tab` also needs `.rodata` at 0x13AC58, which `tools/build.py` refuses to place |
| [`func_00121450`](core/func_00121450.c) | - | libgcc `__moddi3`; same libgcc2.c unit with the `L_moddi3` tail, 1648 bytes against retail's 1640, nops 9 against 14, diverging at the same `umul_ppmm` and closing `__negdi2` sites as `__divdi3`. Its own `__clz_tab` copy sits at 0x13AD58 (`-0x52A8`), 0x100 after `__divdi3`'s - the two units are not sharing one, and neither can be placed |
| [`func_0011C090`](core/func_0011C090.c) | - | Not attempted in this run; double -> decimal-exponent formatter over the libgcc soft-float helpers already named in src/core/001213B8.c (dpcmp/dpmul/dpdiv/dpsub/`__fixunsdfdi`). Real body, two sibling `j func_11C7E8` tails, no `jr $ra`; its threshold doubles live in the 0x13A9D8..0x13A9E8 `.rodata` run, the same placement blocker as the libgcc pair |
| [`func_0011C8D8`](core/func_0011C8D8.c) | - | Not attempted in this run; console/stdio init that installs the two hooks at 0x134698 and 0x13469C and fills a 0x80-byte structure (uncached alias, EE pokes at 0x1000E010 / 0x1000C000 / 0x10011FF0). Real body, three sibling `j` tails, no COP2/MMI/`sync` |
| [`func_0012A1C8`](core/func_0012A1C8.c) | - | Not attempted in this run; libmpeg per-channel routine, stride 0x140, indirect `jalr` through structure pointers and a `.word` MULT1. Not a `.rodata` jump table - the two-minute triage does not apply. Three sibling `j` tails |
| [`func_001246D0`](core/func_001246D0.c) | 176 | Row starts 16 bytes late: the first four instructions are an orphan frame teardown (`sw $zero,0x30($s2)` / `addiu $sp,$sp,0x60`, `$s2` never set, nothing branches to them) and the real 160-byte body runs from 0x1246E0. The body is plain C - uncached alias 0x20000000, two counted byte runs into the request's destinations, tail call to func_00124630 with the base 0x1363D4 - and is transcribed; a `tools/levels.py` split unblocks it |
| [`func_L00_00304AE8`](levels/func_L00_00304AE8.c) | 84 | Catalogue row starts eight bytes early in all 27 places: `addiu $sp,$sp,0x10; nop` are the previous function's return delay slot and padding; the real body (76 bytes) starts at row+8 (00: 0x00304AF0, 01: 0x002FA520, 25: 0x00317218) and is complete. Needs a `tools/levels.py` split. |
| [`func_L00_002B8E48`](levels/func_L00_002B8E48.c) | 172 | Candidate is 172 bytes with 11 words differing (or 164 bytes without the pointer local). Body instruction-identical: 16-byte lq/sq copy, three transform helpers off g_pHeroMoby+0xF0, two angle computations. Left: the prologue order (retail sets $s1=vector address and $s2=output before saving $s0/$ra; this cc1 saves s0,s1,s2 then sets them). The quad store needs the volatile qualifier. |
| [`func_L00_002E54E0`](levels/func_L00_002E54E0.c) | 136 | Exact size, 30 of 34 words. Only the float store at 0x001BD364: retail hoists `lui $v1,0x1C` one slot before `sub.s` and stores through $v1; this cc1 fuses the address into `lui $at` at the store. Statement order, a pointer local and literal addresses all give the same four words (four variants measured). |
| [`func_L00_00305600`](levels/func_L00_00305600.c) | 168 | Candidate is 176 bytes: this cc1 peels the loop's first iteration (tests entry[1] via the base+0x4B54 displacement, materialises the walk pointer later) where retail emits `addiu $a0,$a0,0x4B40` right after the entry[0] test. The rest, including the `!= -1` sentinel test reading the small-data word the bne delay slot loads, is understood. Needs the -g8 unit. |
| [`func_L05_0037D608`](levels/func_L05_0037D608.c) | 224 | Best candidate 216 bytes (literal base) or 220 (symbol base). Body understood: two-byte record list from the object's +0x21 field, bits 0-14 index a slot at (value&0x7FFF)<<8 off the pointer at 0x001B251C, bit 15 ends the list. The wall is that pointer: retail re-materialises `lui $a0,0x1B; lw $a0,0x251C($a0)` per site, this cc1 hoists the address out of the loop into a saved register; volatile does not stop it and an alias makes it worse. |
| [`func_L00_0033E210`](levels/func_L00_0033E210.c) | 12 | Not a function: `addiu $sp,$sp,0x70; nop; addiu $sp,$sp,0x30`, no `jr $ra`, identical in every place - a stripped pair of epilogues. Needs a `tools/levels.py` split. |
| [`func_L00_0032F038`](levels/func_L00_0032F038.c) | 68 | Exact size, 15 of 17 words: only the order of `addiu $a3,$zero,0x1E` (the modulo divisor) and the base `lui` differs; five source shapes give the same pair. `if (value <= limit)` is what reproduces retail's `slt+movz` clamp. |
| [`func_L00_00338D28`](levels/func_L00_00338D28.c) | 204 | Candidate is 208 bytes: the first 16-byte copy's store address is materialised in the `beqz` delay slot in retail, folded into the sq displacement here (member array and `(char *)o + 0x10` both measured, volatile present). Rest of the record template transcribed. |
| [`func_L00_0033BA68`](levels/func_L00_0033BA68.c) | 216 | Candidate is 220 bytes: same record template and same copy wall as func_L00_00338D28 (0x46 class, b2 indexed by the caller's offset, b8 from `value * 40.743664f`). |
| [`func_L08_00318F90`](levels/func_L08_00318F90.c) | 76 | Candidate is 68 bytes with the literal base (which is required: a named symbol splits the address into a second register). Left: retail's argument copies `daddu $t0,$a0` / `daddu $t1,$a1` and its extra pointer copy, which nested-test shapes do not reach. |
| [`func_L00_0032A0B8`](levels/func_L00_0032A0B8.c) | 48 | 11 of 12 instructions exact, all 27 places: the level's fixed-stride table lookup differs only at +0x10, where retail adds the element address as base + index (`addu $v0,$a2,$a1`) and this cc1 always emits index + base (`addu $v0,$a1,$a2`). Operand reorder, s32/u8* casts, named base and entry locals, an all-integer spelling, the spellings of the sibling `func_L00_0032A088` (same table, +0x20 - that near-miss records the same wall), the -G8 profile and the SDK compiler all give the same single swap |
| [`func_L00_0035CC98`](levels/func_L00_0035CC98.c) | - | Not a C row: the 12-byte extent is three words of orphaned epilogue residue (`addiu $sp,$sp,0x70` / `nop` / `addiu $sp,$sp,0x50`) with no `jr`, `jal` or branch, and a whole-text scan finds nothing branching or jumping into any of its three word boundaries (levels 00 and 01). The last complete body ends at 0x0035CC90 and the next prologue is at 0x0035CCA8; the extent needs folding into a neighbour or carrying as data |
| [`func_L00_002E3A90`](levels/func_L00_002E3A90.c) | - | Not C in its present form: the accumulate block (+0x54..+0x94) is R5900/MMI-class - three-operand `mult` (0x00C31818, 0x00852818), `mtlo`, `mflo` and the MMI word 0x70820000 that rabbitizer prints `.word # INVALID`. The header tests and the ordinary nine-iteration record walk can be written; the block cannot. ASM_FUNC candidate |
| [`func_L00_003296C0`](levels/func_L00_003296C0.c) | 112 | 96-byte candidate with the same body, order and constants; the only difference is how often the per-index table is re-materialised - retail materialises the global twice and re-reads the block pointer once per store, this cc1 keeps one address register and also drops the argument copies. Unblocker: the nosda levier (two C names for the one address, one per group of stores); the value argument must be `s32`, not `u8`, or the byte store gains an `andi` |
| [`func_L00_00326620`](levels/func_L00_00326620.c) | - | Not attempted: quad-copy class (0x78A20000 / 0x78620000 LQ into 0x7CC20000 / 0x7C820000 SQ) inside an else-branch that also runs two FP divides; see func_L18_003FC9B8 |
| [`func_L00_0032A1A8`](levels/func_L00_0032A1A8.c) | - | Not attempted: quad-copy class (0x78620000 LQ into 0x7FA20000 SQ over a stack vector) around FP moves and three helper calls; same blocker as func_L18_003FC9B8 |
| [`func_L00_00329FF0`](levels/func_L00_00329FF0.c) | 108 | 100-byte candidate, every instruction exact but two FPU-latency nops: retail runs `mtc1; nop; cvt.s.w; nop; nop; div.s`, this cc1 emits the nop after `mtc1` but not the pair after `cvt.s.w`. The register-divisor form is required (a literal 16 folds to `andi` + `mul.s`, 64 bytes); -G2, -G8 and the SDK compiler all stop at 100 bytes. Needs an FPU hazard/padding control |
| [`func_L18_003FC9B8`](levels/func_L18_003FC9B8.c) | - | Not attempted: four 128-bit words copy two sixteen-byte blocks (0x78A20000 / 0x78820000 LQ into 0x7CE20000 / 0x7CA20000 SQ) after a flag and field update. The repository's own drafts (func_L00_00338E90, func_L00_003EE288) show quadword copies are C-reachable but stop on scheduling and quad-register allocation - this row needs a shared quad plan first |
| [`func_L09_00325C58`](levels/func_L09_00325C58.c) | - | Not a C row: 20-byte extent of three orphaned epilogue restores (frames 0x190, 0x150, 0x40) with no `jr`, `jal` or branch - the same catalogue defect as func_L00_0035CC98, longer |
| [`func_L17_0032B558`](levels/func_L17_0032B558.c) | - | Not a C row: same defect, 20 bytes of restores (frames 0x50, 0x10, 0xB0) with no return path |

## Level functions, wave 5 agent lvl-03 (2026-10-16)

| [`func_L00_002A6708`](levels/func_L00_002A6708.c) | 240 | Closest candidate is 228 bytes; the loop, both record tests and the -1 store shape are exact, but this cc1 rotates the three increments to the top of the body where retail leaves them at the end, gives `$s1`/`$s2` to the -1 constant and the trip counter the other way round, and emits `addu $v0,$s7,$s3` where retail emits `addu $v0,$s3,$s7` |
| [`func_L00_002A75D8`](levels/func_L00_002A75D8.c) | - | Compiler-profile wall, the same as `func_L00_003ACC88`: the retail 128-bit zero (`0x700014A9` = `pcpyld $v0,$zero,$zero`, then `sq $v0,0x0($sp)`) is folded to `sq $zero` by `rac2_fold_zero_ti_store`, because `$v0` is dead after the store. No C deposits |
| [`func_L00_002A9E30`](levels/func_L00_002A9E30.c) | 252 | Candidate is 240 bytes; the arithmetic sequence, the 4-float argument convention and the object walk are instruction-exact, but retail keeps the resident base as a completed address in `$v1` (and its high half in `$a1`) where this cc1 keeps the high half with displacements, the two FP branches come out with the likely-ness inverted, and two `c.eq.s` nops are missing |
| [`func_L00_002A74A8`](levels/func_L00_002A74A8.c) | 300 | Candidate is 272 bytes; both blocks' calls, the signed `% 4` (movn) sequence and the `(old & ~a3) \| a2` splice are exact, but retail keeps the resident high half in `$s1` across the whole body and completes the address twice, the `(A \|\| B) && C` gate gets `$a1` as its phi, and the `(0x90,0x90,0xF0)` stores are ordered differently |
| [`func_L00_002D3248`](levels/func_L00_002D3248.c) | 68 | Same size and every instruction matches; only the store order differs. Retail leaves the `+0x1430` float store for the `jr` delay slot and this cc1 schedules it first. Two source orders measured, same result |
| [`func_L00_00444D18`](levels/func_L00_00444D18.c) | 44 | Same size, three words; retail fuses the resident load into the high address register (`lui $v0,%hi` then `lw $v0,%lo($v0)` at +0x4) while this cc1 schedules the constant's `lui` between them and loads into `$v1`. Six spellings measured, all identical |
| [`func_L00_004450E8`](levels/func_L00_004450E8.c) | 44 | Same three words as `func_L00_00444D18` (same thunk shape, other callee) |
| [`func_L00_00447678`](levels/func_L00_00447678.c) | 48 | Same thunk shape as `func_L00_00444D18` with the constant 0xD9090, plus `return 1` |
| [`func_L00_004476A8`](levels/func_L00_004476A8.c) | 48 | Same thunk shape as `func_L00_00447678` with the other callee |
| [`func_L00_003CA650`](levels/func_L00_003CA650.c) | - | Catalogue defect: the extent opens on `addiu $sp,$sp,0x60; nop` (a stray epilogue). The C of the body from +8 (0x003CA658) is deposited; the boundary needs re-deriving |
| [`func_L19_003DD6D0`](levels/func_L19_003DD6D0.c) | - | Catalogue defect, `addiu $sp,$sp,+N; nop` family: the extent opens on the previous function's epilogue tail (`jr $ra; addiu $sp,$sp,0xA0`) and the body from +8 is not a coherent prologue either. No C deposited |
| [`func_L19_0043CFD8`](levels/func_L19_0043CFD8.c) | - | Same defect family as `func_L19_003DD6D0` (extent opens on `addiu $sp,$sp,0x20; nop`) |
| [`func_L04_0033D158`](levels/func_L04_0033D158.c) | - | Same defect family (extent opens on `addiu $sp,$sp,0x40; nop`) |
| [`func_L04_0034B9B0`](levels/func_L04_0034B9B0.c) | - | Same defect family (extent opens on `addiu $sp,$sp,0x10; nop`) |

## Level functions, wave 5 agent lvl-03, tour 2 (2026-10-16)

| [`func_L04_003524D0`](levels/func_L04_003524D0.c) | 68 | Candidate 64 bytes; clamp, `div` guard and table walk exact, only the table/window register PAIR is swapped (`$a2`/`$a3`). Three declaration orders measured. Reads the boot's own table at 0x138180 |
| [`func_L00_002B9BB0`](levels/func_L00_002B9BB0.c) | 168 | Candidate 160 bytes, structure exact; retail keeps the resident high half live in `$s3` and COMPLETES the base twice where this cc1 reuses the earlier register, plus an index/table register swap. Same wall as `func_L00_002A74A8` |
| [`func_L00_002D20D0`](levels/func_L00_002D20D0.c) | 152 | Candidate 160 bytes; every offset and store identical, but the resident block/object pair lands in `$t1`/`$t0` against retail's `$a0`/`$a3`, and this cc1 reloads the owner once more across the object store |
| [`func_L00_002CAF20`](levels/func_L00_002CAF20.c) | 276 | Candidate size exact and the sequence instruction-for-instruction; this cc1 gives the resident-state base `$s1` and the looked-up record `$s0`, retail the other way round. Eleven shapes (record read before/after the call, duplicated record expression, the `+0x28` load in the call's delay slot, a pointer variable per base) all give the same swap |
| [`func_L00_002CB218`](levels/func_L00_002CB218.c) | 188 | Candidate size exact; the `+0x24F8` word is `lwu` in all 27 places, which this cc1 only emits for a 64-bit variable holding the unsigned 32-bit load (measured), and retail's single base with two plain offsets for 0x00138180 comes out as per-site `%hi`/`%lo` pairs (or as a base materialised above the first test) here |
| [`func_L00_002CB2D8`](levels/func_L00_002CB2D8.c) | 204 | Candidate is 208 bytes; this cc1 loads every `movz`/`movn` operand absolutely, where retail reads 0x001A7398 `lw $v1,-0x7C58($gp)` into the merge. Six source shapes and a `-G8` unit all give `lui`+`lw`; outside a merge the same `sda` declaration does give `$gp` with a GPREL16 relocation |
| [`func_L00_002CB038`](levels/func_L00_002CB038.c) | - | Not attempted: the front-end screen loader (symbol_addrs's `MenuScreenLoad`). Body read in full and recorded; needs the same `$gp` counter block as func_L00_002CB2D8 and two `func_2AE888` calls with four float arguments each |
| [`func_L00_002CAD90`](levels/func_L00_002CAD90.c) | - | Not attempted: float work around two 16-byte `lq`/`sq` block copies. Body read in full and recorded; needs a 16-byte aggregate whose copy this cc1 lowers to `lq`/`sq` |
| [`func_L00_002D1B70`](levels/func_L00_002D1B70.c) | - | Not attempted: `func_2ADEA0(3)` selects between three `$gp` tables at 0x001A7D60 / 0x001A7D48 / 0x001A7D78, then a 4-byte-stride scan for a byte id; the selector's `movn` is the same merge shape as func_L00_002CB2D8's blocker |
| `func_L00_002D1DA0` | - | Not attempted: same three `$gp` tables as func_L00_002D1B70 plus a stack array of matching slots and a `func_31A768` call; the three tables are the shared blocker |
| `func_L00_002D1C40` | - | Not attempted: not disassembled in this run (352 bytes, 27 places, same unit as func_L00_002D1B70) |
| [`func_L00_00311080`](levels/func_L00_00311080.c) | 100 | Candidate is 92 bytes with the same instruction set; retail fills the load-to-store window with two `nop's and keeps the `+0x22` store first, this cc1 puts `div.s` right after the load and hoists the `+0x23` store. Five source shapes give the identical object |
| [`func_L00_00311080`](levels/func_L00_00311080.c) | 100 | Candidate is 92 bytes with the same instruction set; retail fills the load-to-store window with two `nop`s and keeps the `+0x22` store first, this cc1 puts `div.s` right after the load and hoists the `+0x23` store. Five source shapes give the identical object |
| [`func_L04_0040A5C0`](levels/func_L04_0040A5C0.c) | 100 | Candidate is 116 bytes; the body is right but retail decrements the counter at the top of the block with one induction pointer, this cc1 emits a two-pointer loop with a second exit test. Three source shapes give 116 |
| [`func_L00_0031AFF0`](levels/func_L00_0031AFF0.c) | - | Not a C row: catalogue defect. The 12-byte extent is two function epilogue tails (`addiu $sp,$sp,0x20` / `nop` / `addiu $sp,$sp,0x20`), no prologue and no `jr $ra` |
| [`func_L00_0031A710`](levels/func_L00_0031A710.c) | - | Not a C row: catalogue defect. The 20-byte extent is three bare `mul.s` on `$f13` with no prologue, no store and no `jr $ra` - a neighbour's fragment |
| [`func_L04_0030CC70`](levels/func_L04_0030CC70.c) | - | Not a C row: catalogue defect. `addiu $sp,$sp,0x30` / `nop` / `addiu $sp,$sp,0x80`, no prologue and no `jr $ra` (same shape as func_L00_0031AFF0) |
| [`func_L04_0030EDE0`](levels/func_L04_0030EDE0.c) | - | Not a C row: COP2/VU0 (`ldc2`, a COP2 op, `sdc2`). ASM_FUNC candidate |
| [`func_L04_0030FA30`](levels/func_L04_0030FA30.c) | - | Not a C row: COP2/VU0 (eight `ldc2` from two vectors, four COP2 matrix ops, four `sdc2`). ASM_FUNC candidate |
| [`func_L04_002CC748`](levels/func_L04_002CC748.c) | 144 | Kept for the next worker: guard, record lookup, `movz` tail and block order all match; retail keeps the `$hi` of D_00189E20 in `$a1` from the entry block into the shared tail and re-issues only the `lo_sum` per site, this cc1 gives one register the full `lui`/`addiu` and re-materialises a fresh `lui` in the tail (152) |
| [`func_L04_00365300`](levels/func_L04_00365300.c) | 184 | Division-floor class: 176-byte candidate, every instruction exact but retail's `mul.s ; nop ; nop ; div.s`; the assembler pads once a code label precedes the division (measured: 176 -> 184) but no source shape puts one there (38 slots after the last label in four shapes). The prologue also rotates `$s1`/`$s2` |
| [`func_L09_003E99E0`](levels/func_L09_003E99E0.c) | 188 | TI/MMI class: 176-byte candidate with the whole body right; retail materialises both quadword pointers (`addiu $a0,$s1,0x10` / `addiu $v1,$s0,0x330`) before `lq`/`sq` at offset zero, this cc1 folds the constant offsets into `lq $v0,0x10($s1)` / `sq $v0,0x330($s0)`. Four spellings measured (inline, two pointer locals, a TI value local, copy moved after the calls) |
| [`func_L04_0039FC28`](levels/func_L04_0039FC28.c) | 176 | 176 bytes, 34 of 44 words exact: the single-precision `sda` float and both absolute constants land correctly (the `-g8` sibling + the unit's `-mno-split-addresses` line + literals for the absolutes), but retail issues `lui $at,0x1B ; lwc1 $f0,-0x27B8($at)` before the four prologue stores where this cc1 issues it after them; six shapes and `-fno-schedule-insns2` all agree |
| [`func_L05_0037AA88`](levels/func_L05_0037AA88.c) | 64 | Candidate is 68 bytes; the chain walk matches, retail branches `bltz` to a `return -1` placed after the loop while this cc1 inverts the branch (68) or duplicates the loop test (84-92). Tried: while, `for (;;)` with an early return, both early-return forms, pointer instead of byte offset |
| [`func_L05_0037AC00`](levels/func_L05_0037AC00.c) | 64 | Candidate is 68 bytes; retail fills the `bltz` delay slot with `daddu $v0,$zero,$zero` and branches to the shared epilogue, this cc1 gives the zero arm its own block plus a `b` over it (76 when the zero is a variable initialiser) |
| [`func_L05_0037AC40`](levels/func_L05_0037AC40.c) | 64 | Twin of func_L05_0037AC00 (record field 0x10 against 0x14); identical exit/delay-slot difference |
| [`func_L05_003D1E20`](levels/func_L05_003D1E20.c) | 60 | Loop exact (five iterations of two stores and two increments, counter 4 down to -1); retail puts the counter in `$v0` and the blocks in `$a0`/`$v1`, this cc1 gives the counter `$a0`, and it materialises both block bases before the `$gp` store. Needs the `-g8` unit |
| [`func_L00_00410840`](levels/func_L00_00410840.c) | 72 | Candidate is 64 bytes; retail materialises 60 and 30 in `$v0` and ends `slt; addiu $v1,$zero,2; movn $a0,$v1,$v0`, this cc1 folds the compares to `slti` and uses `movz` |
| [`func_L00_0040B330`](levels/func_L00_0040B330.c) | 104 | Candidate size exact, one instruction out of place at +0x3c: this cc1 issues the `$gp` handle load before retail's first `mtc1`. Needs the `-g8` unit and `__attribute__((sda))` on the handle (without it the body grows to 120) |
| [`func_L02_002DCEA0`](levels/func_L02_002DCEA0.c) | 72 | Candidate is 64 bytes, instruction-identical but for the two pipeline `nop's retail keeps between the mode store and `div.s`; needs one base plus offsets (three extern floats give three base materialisations) |
| [`func_L02_002DCEA0`](levels/func_L02_002DCEA0.c) | 72 | Candidate is 64 bytes, instruction-identical but for the two pipeline `nop`s retail keeps between the mode store and `div.s`; needs one base plus offsets (three extern floats give three base materialisations) |
| [`func_L00_0037DD18`](levels/func_L00_0037DD18.c) | 100 | Candidate is 112 bytes; retail keeps 0x230000 in `$t2` and 0x231340 in `$v0` and scales an index (`sll $a3,$t0,4`), this cc1 walks a pointer and keeps a second copy of the address |
| [`func_L00_00358670`](levels/func_L00_00358670.c) | - | Not a C row: the extent opens on `addiu $sp,$sp,0x10; nop; addiu $sp,$sp,0x10`; no function starts with a stack restore, the entry after the gap is func_002DF1D0. Catalogue split |
| [`func_L00_00430A90`](levels/func_L00_00430A90.c) | - | Not a C row: `addiu $sp,$sp,0x30; nop; addiu $sp,$sp,0x30`, the previous entry ends `jr $ra; addiu $sp,$sp,0x30` and the next row is func_0033B170. Catalogue split |
| [`func_L05_0037B6C0`](levels/func_L05_0037B6C0.c) | - | Not a C row: `lw $v0,0xC($v1)` with `$v1` never set, then restores; dead bytes between func_L05_0037B6D0 and the entry before it |
| [`func_L00_004263A8`](levels/func_L00_004263A8.c) | - | Not a C row: the 16 bytes are the previous entry's stack restore plus a complete `return 0` body at +8; re-cut to 0x4263B0 |
| [`func_L05_0033E600`](levels/func_L05_0033E600.c) | - | Not a C row: three consecutive `addiu $sp,$sp,+N` restores with their padding |
| [`func_L05_0033F4A8`](levels/func_L05_0033F4A8.c) | - | Not a C row: 156 bytes of restores and padding, `addiu $sp,$sp,0x30/0x50/0x70` openers |
| [`func_L00_00428EE0`](levels/func_L00_00428EE0.c) | - | Not a C row: dead `sw` tail with unset base registers, a restore, and a complete `return 0` at 0x428EF8; re-cut to 0x428EF8 |
| [`func_L02_002F1FB8`](levels/func_L02_002F1FB8.c) | 24 | VU0 macro mode: `ldc2`/`sdc2` plus a COP2 macro op (0x4BE2086C); no C spelling, ASM_FUNC candidate |
| [`func_L02_002F21C0`](levels/func_L02_002F21C0.c) | 32 | VU0 macro mode: two `ldc2` and four COP2 macro ops; no C spelling, ASM_FUNC candidate |
| [`func_L02_0031E918`](levels/func_L02_0031E918.c) | 88 | Candidate size exact, one instruction differs at +0x1c: a cyclic permutation of `$f0`/`$f1`/`$f2` (retail reads the value into `$f0`, 1/4096 into `$f1`, 1.0f into `$f2`). Tried the one-expression and three-local forms |
| [`func_L02_00321C30`](levels/func_L02_00321C30.c) | 164 | Candidate is 152 bytes; retail keeps the travel in `$f22` across `func_2F2270` and saves three floats (frame 0x50), this cc1 uses `$f21` and saves two (frame 0x40); the branch layout is mirrored too |
| [`func_L00_002ADFE8`](levels/func_L00_002ADFE8.c) | 136 | 76-entry tail-call dispatch table at 0x0029FF70: `lw $a0,...; jr $a0` at +0x24. A C switch emits its own `.rodata` table and `jalr`; ASM_FUNC candidate |
| [`func_L00_00421A98`](levels/func_L00_00421A98.c) | 184 | Jump table: `lw $a0,...; jr $a0` at +0x4c. ASM_FUNC candidate |
| [`func_L05_003CFCF0`](levels/func_L05_003CFCF0.c) | 668 | Jump table: `lw $a0,...; jr $a0` at +0x60. ASM_FUNC candidate |
| [`func_L02_002EF968`](levels/func_L02_002EF968.c) | 148 | 128-bit block moves: raw `lq`/`sq` words at +0x54 (opcodes 0x1E/0x1F) inside a 4-iteration float and block copy; no C spelling, ASM_FUNC candidate |
| [`func_L00_004284B0`](levels/func_L00_004284B0.c) | 176 | 128-bit block moves: five raw `lq`/`sq` words from +0x28 around float work and three calls; no C spelling, ASM_FUNC candidate |
| [`func_L00_003A8E28`](levels/func_L00_003A8E28.c) | 468 | SPECIAL2/MMI word 0x700014A9 at +0x78 (opcode 0x1C, printed INVALID); one family with the next two rows; ASM_FUNC candidate |
| [`func_L00_00428000`](levels/func_L00_00428000.c) | 752 | SPECIAL2/MMI word 0x700014A9 at +0x5c; ASM_FUNC candidate |
| [`func_L05_003D0538`](levels/func_L05_003D0538.c) | 496 | SPECIAL2/MMI word 0x700014A9 at +0x44; ASM_FUNC candidate |
| [`func_L03_0038B5A8`](levels/func_L03_0038B5A8.c) | 44 | Not a C row: both placements are linker fill (`addiu $sp,$sp,N; nop` chains, no `jr $ra`, no prologue); the next real body is func_L03_0038B5D8 at +0x30. Needs a tools/levels.py split |
| [`func_L04_003A11F8`](levels/func_L04_003A11F8.c) | 68 | Not a C row: all five placements are linker fill; the next real body is func_L04_003A1240 at +0x48. Needs a tools/levels.py split |
| [`func_L04_003A1578`](levels/func_L04_003A1578.c) | 68 | Complete C, instruction-exact but for one register: this cc1 fetches the table word into $v0, the register already holding the computed base, where retail fetches into $v1 and keeps the base in $v0. Two source shapes measured, both land on $v0 |
| [`func_L18_00426E00`](levels/func_L18_00426E00.c) | 72 | Complete C (logic measured: 0 for the bytes 0xFD/0xFE and for a null object, 1 for the halfwords 0xEC0/0xED4/0xEE1). This cc1 folds the byte pair into a 0xFD..0xFE range test, returns 0 by an early `jr $ra` and keeps the result in $v1 where retail branches to a shared return with it in $a1. A switch spelling is worse (100 bytes, `slti` chains) |
| [`func_L03_00419C58`](levels/func_L03_00419C58.c) | 72 | Complete C: only the state register differs ($v1 here against $a1 in retail, which reuses the incoming pointer's register after moving it to $a0). Naming the pointer in a local first did not move it |
| [`func_L03_00419C00`](levels/func_L03_00419C00.c) | 84 | Complete C: 9-argument call, frame addresses in $t1/$t2, both float constants exact; only the placement of the two -1 sentinel stores differs (retail at +0x38 and in the `jal` slot, this cc1 both right after the state load). The 9-argument prototype is the one measured in L02_002ED7B0.c; the canonical unit's 7-integer form cannot express this call, so the body is kept as its own file |
| [`func_L00_002FD480`](levels/func_L00_002FD480.c) | 88 | Complete C: four `lq`/`sq` block copies and a $gp store of the advanced source pointer. The $gp store needs its own -G8 unit (measured: `-O2 -G8 -ffunction-sections`, destinations `nosda`); with it only the register assignment differs (retail: destination in $v1, block in $v0, $v1 reused for all four blocks) |
| [`func_L22_003FBFD0`](levels/func_L22_003FBFD0.c) | 116 | Not attempted in this run; decoded: an eight-iteration loop over two per-level calls with $gp float scales, both pointer bases `nosda` at 0x255820/0x2558A0 (level 22) and the second call without an a2 set-up. Needs its own -G8 unit for the two one-instruction `lwc1 ...($gp)` loads; the level-22/23 callee addresses are in the near-miss |
| [`func_L03_00419850`](levels/func_L03_00419850.c) | 92 | Complete body (16-byte `lq`/`sq` frame copy, float nudge at +8, 8-argument call with two $gp values); 88 bytes against 92 because this cc1 folds the `object + 0x10` pointer into the load and issues the $gp accesses early. Needs a -G8 unit |
| [`func_L04_003A19E8`](levels/func_L04_003A19E8.c) | 92 | Exact loop (guard, per-entry call, 0x10 and 0x1190 pointer steps) but the two pointers take each other's saved register ($s2/$s1 here against retail's $s1/$s2) and the count copy is hoisted above the guard. Two shapes measured |
| [`func_L04_003A14B8`](levels/func_L04_003A14B8.c) | 132 | Complete (both signed power-of-two divisions with their `slt`/`movn` pairs, the masked tag loop); 124 bytes against 132: the tag stays in $a1 against retail's $t1, the state pointer goes to $a2 against $v1, and the retail loop's extra copy plus padding `nop` are not emitted |
| [`func_L00_002FE268`](levels/func_L00_002FE268.c) | 108 | Complete (both 64-bit constants reach retail's exact dli sequences); 112 bytes against 108: this cc1 computes the block address into a spare register and copies it into $a3, where retail materialises it straight into $a3. Needs a -G8 unit |
| [`func_L00_00303388`](levels/func_L00_00303388.c) | 144 | Complete (descending table walk, one-based return); 136 bytes against 144: the register assignment differs (retail shares $v1 between the data and the +0x20 base with the count in $v0), which folds the base into the load offset and drops the loop padding |
| [`func_L02_003CE9A8`](levels/func_L02_003CE9A8.c) | 124 | Second attempt; same wall as the note kept in `src/levels/shared/L02_003C43F0.c` (wave7 draft, 116 bytes): the branch-likely pair and the duplicated `lh` delay-slot loads are retail's, the register copies differ. This candidate is 128 bytes with the kind byte in $v1 against $v0. Needs a -G8 unit |

## Wave 5 (2026-10-16)

| [`func_L00_002A4EF8`](levels/func_L00_002A4EF8.c) | 240 | Same 60 words at the right size and every retail instruction present; this cc1 schedules the two index-clamp ops (`movz`/`slt`) after the 20-byte literal copy where retail keeps them beside the `slti`, and the remaining 12 words follow from that placement |
| [`func_L00_0037E640`](levels/func_L00_0037E640.c) | 36 | One instruction short: this cc1 saves `$ra` before the argument `la` and puts the `la`'s second half in the call's delay slot; retail saves after the `la` and leaves a `nop`. Eleven call spellings and six flag sets all give the same 8 words |
| [`func_L22_003A2DF8`](levels/func_L22_003A2DF8.c) | 20 | Bit extract from a float's raw pattern: `mfc1`, the `daddu` copy, `srlv` and `andi` are all exact, but this cc1 inserts the FPR hazard `nop` after `mfc1` where retail has none. Six shapes give the same extra word - probably what stops the other `F32Bits` readers in that unit too |
| [`func_L22_003A1EC8`](levels/func_L22_003A1EC8.c) | 12 | Not a function: `addiu $sp,$sp,0x70; nop; addiu $sp,$sp,0x30`, no `jr $ra`; the twelve bytes before it are the previous function's epilogue. A split-off remnant - fix in `tools/levels.py` |
| [`func_L05_0037BB78`](levels/func_L05_0037BB78.c) | 20 | Not a function: no `jr $ra` and `$v1` is read before anything sets it (`addiu $sp,$sp,0x30; nop; lw $v0,0x8($v1); nop; addiu $sp,$sp,0x20`), the neighbour's tail |
| [`func_L00_0042BC30`](levels/func_L00_0042BC30.c) | 12 | Not a function: `addiu $sp,$sp,0x20; nop; addiu $sp,$sp,0x20`, no `jr $ra` and no register traffic |
| [`func_L02_003F0788`](levels/func_L02_003F0788.c) | 16 | Not a function: `addiu $sp,$sp,0x50; nop; jr $ra; nop` - it deallocates a frame it never allocated, so the row is the tail of the function before it |
| [`func_L00_0037DE30`](levels/func_L00_0037DE30.c) | 196 | One instruction short with the `for (; a2 > 0; a2--)`-plus-`continue's form (48 of 49 words); what remains is the `blezl $s2` / `bnel`-delay-slot pair that only executes when the branch is taken |
| [`func_L00_0037E5F0`](levels/func_L00_0037E5F0.c) | 76 | Right size and every retail instruction present; three words stay permuted - the gate load's destination register, the `mtc1 $zero,$f1` position and the `neg.s` against the absolute store's `lui`. Four spellings give the same three. Its -G8 home with the `nosda`/`_1` double names is src/levels/shared/L00_0037DD80-g8.c |
| [`func_L04_0039FEA8`](levels/func_L04_0039FEA8.c) | 68 | Body exact, 60 of retail's 68 bytes: this cc1 drops the two `nop's retail keeps between the divisor `lwc1` and the `div.s`. Three spellings give the same 60 |
| [`func_L00_0037DE30`](levels/func_L00_0037DE30.c) | 196 | One instruction short with the `for (; a2 > 0; a2--)`-plus-`continue`s form (48 of 49 words); what remains is the `blezl $s2` / `bnel`-delay-slot pair that only executes when the branch is taken |
| [`func_L04_0039FEA8`](levels/func_L04_0039FEA8.c) | 68 | Body exact, 60 of retail's 68 bytes: this cc1 drops the two `nop`s retail keeps between the divisor `lwc1` and the `div.s`. Three spellings give the same 60 |
| [`func_L12_00427C58`](levels/func_L12_00427C58.c) | 84 | Wall, one compile: this cc1 turns the `if (a2 != 0)` guard into `beql` with the load in the annulled slot, retail keeps `bnez` and a separate load |
| [`func_L05_0037BB28`](levels/func_L05_0037BB28.c) | 80 | Wall, one compile: same annulled-slot idiom, and here the slot holds the frame restore `ld $s0,0x0($sp)` |
| [`func_L16_0040C7E8`](levels/func_L16_0040C7E8.c) | 88 | Not C: `.word 0x78620000`/`0x7CA20000`, MMI (lq/sq family) |
| [`func_L22_003A2988`](levels/func_L22_003A2988.c) | 124 | Not C: `.word 0x7BA20000`/`0x7E220000`, MMI |
| [`func_L16_003960F8`](levels/func_L16_003960F8.c) | 152 | Not C: `.word 0x78620000`/`0x7FA20000`, MMI (plus `beql` loads) |

## Level functions, wave 6 agent lvl-04 (2026-10-16)

| [`func_L01_002ABF90`](levels/func_L01_002ABF90.c) | - | Not a C row: catalogue defect, linker fill. `addiu $sp,$sp,0x40; nop; addiu $sp,$sp,0x70` with no prologue and no `jr $ra`, wedged between func_L00_002AA200's epilogue and func_L00_002AA938 (at row+0x10 in both places). Needs a `tools/levels.py` split |
| [`func_L01_002E1150`](levels/func_L01_002E1150.c) | - | Not a C row: same fill family. `addiu $sp,$sp,0xA00; nop; addiu $sp,$sp,0x100`, then func_L00_002EBE88 at row+0x10 in all 8 places. Not a duplicate name for it (no address is carried twice in the catalogue) |
| [`func_L01_0042C450`](levels/func_L01_0042C450.c) | - | Not a C row: recycled `sb $zero,0x38($v0)` / `sltiu $v0,$v0,0x1` fragment, `$v0` live-in and never defined, no `jr $ra`; next real body func_L00_004290C0 at row+0x10 |
| [`func_L01_002B46A0`](levels/func_L01_002B46A0.c) | 100 | Right size, every instruction present: this cc1 materialises the FIELD address (base+0x1B0) and reaches 0x2308 off it, or copies the base into `$v1` (`lui $v0; addiu $v1,$v0,lo`), where retail keeps the BASE in `$v0` and uses both 0x1B0 and 0x24B8 off it. First differing word +0x24 |
| [`func_L01_00367F78`](levels/func_L01_00367F78.c) | 96 | Loop body matches word for word; the one extra word is the gp table base, which comes out absolute (`lui %hi; addiu %lo`) where retail has `addiu $a1,$gp,-0x1AB0`. Measured: `sda`, `section(".sdata")` and a -G8 unit all still absolute here |
| [`func_L01_002F14E8`](levels/func_L01_002F14E8.c) | 176 | Body shapes all measured; 20 bytes over because the four gp slots at 0x001A90C4-0x001A90D0 come out as absolute lui/lw pairs where retail uses `-0x5F28($gp)` etc. Same GPREL blocker, three spellings tried |
| [`func_L01_002A7248`](levels/func_L01_002A7248.c) | 124 | One word short: the `movn` tail is right, but this cc1 folds away one of the two `addiu $v0,$zero,0x51` materialisations retail duplicates (bnel delay slot and the second entry) |
| [`func_L10_0037D200`](levels/func_L10_0037D200.c) | 140 | Two words short: retail keeps the halfword in a second register and tests it with `sll $v0,$a1,16; bgez`, re-materialising the shift in the annulled slot; this cc1 reloads with `lh` and emits `bgezl`. A named halfword variable changed nothing |
| [`func_L10_004190F0`](levels/func_L10_004190F0.c) | 152 | Right size and instruction set; retail stores `sw $v1,0x148($v0)` before the second call's `a1 = 1` set-up and jal (delay slot `addiu $a1,$zero,0x1`), this cc1 hoists the set-up and sinks the store into the jal slot (+0x24) |
| [`func_L03_0038B408`](levels/func_L03_0038B408.c) | 204 | Frame and loop rotation: retail keeps the 0x001FE700 table base in `$s5` with a 0x50 frame saving s0-s5, both candidates reach it through `$s4` in a 0x40 frame; first differing word +0x0C. The table walk and both callees are measured |
| [`func_L00_00410430`](levels/func_L00_00410430.c) | 228 | Three words over on the eight-argument call (`a0-a3` plus `f12-f15`, first differing word +0x14); both branches, the two recomputed `a2 - 1` stores and the `0x001B2D58` word are measured |
| [`func_L17_0032E898`](levels/func_L17_0032E898.c) | 12 | Not a function: `addiu $sp,$sp,0x30; nop; addiu $sp,$sp,0x50`, no `jr $ra` and no register traffic; the twelve bytes before it are the previous function's epilogue (`jr $ra; addiu $sp,$sp,0x50`). A split-off remnant - fix in `tools/levels.py` |
| [`func_L18_0033D768`](levels/func_L18_0033D768.c) | 12 | Not a function: `addiu $sp,$sp,0x30; nop; addiu $sp,$sp,0xD0`, same class - the previous function's `jr $ra; addiu $sp,$sp,0x50` sits right before the row |
| [`func_L06_003E1040`](levels/func_L06_003E1040.c) | 104 | The row starts eight bytes early (`addiu $sp,$sp,0x20; nop` is the previous function's return delay slot and padding); the real body is row+8, 96 bytes, and is a message-id switch (0x18 -> fixed id 0x1243, else a 0x2A-based table at 0x003D3C0C indexed by state-1). Needs the split in `tools/levels.py` before any C can match |
| [`func_L12_00404778`](levels/func_L12_00404778.c) | 68 | 64 bytes: this cc1 merges retail's adjacent `== 6` / `== 5` compares into one range test (`addiu $v0,$v1,-0x5; sltiu $v0,$v0,2`), which costs the missing word and moves the result out of `$a1`. Separate ifs and a switch give 84 bytes; its sibling `func_L12_003DE4B0` (order 6 / <0 / 8) matches |
| [`func_L12_00413EA8`](levels/func_L12_00413EA8.c) | 124 | 120 bytes with the table base rematerialised inside the loop (retail's shape, the byte store is why it cannot hoist); every instruction present, one scheduling word short of retail's 124, and the two hoisted constants scheduled differently. Assigning `base` before the loop gives 128 |
| [`func_L12_003A6EF8`](levels/func_L12_003A6EF8.c) | 80 | Same 80 bytes with two registers swapped: retail opens with `daddu $a1,$a0,$zero` (object in `$a1`, shared word in `$v1`) and reuses `$a0` for the call constant; this cc1 keeps the object in `$a0`. Two spellings give the same - nothing in the body keeps the object live over the `lui $a0` |
| [`func_L17_003C5E20`](levels/func_L17_003C5E20.c) | 88 | 80 bytes: retail builds the answer through an if/else tail (`bne` + `daddu $v0,$zero,$zero` in the delay slot, `addiu $v0,$zero,0x1` in the true paths); this cc1 if-converts the `\|\|` chain into `beql` + `xor`/`sltiu`. A result variable with a merged else gives 92 |
| [`func_L17_003BCBC8`](levels/func_L17_003BCBC8.c) | 100 | 104 bytes: this cc1 inverts retail's `slti $v0,$v0,0xF` into `addiu $v0,$zero,0xE; slt $v0,$v0,$rem; movz` (retail selects with `movn` and no extra constant). `rem <= 14` and a single ternary are worse (104/108) |
| [`func_L20_003C02D0`](levels/func_L20_003C02D0.c) | 128 | 132 bytes, one scheduling word over: retail hoists the 20.0f constant (`lui $at,0x41A0; mtc1 $at,$f1`), the destination base and the record base out of the loop and reloads the record's float after the destination store. Reordering the flag/float stores or the per-iteration base changes other words |
| [`func_L30_00413528`](levels/func_L30_00413528.c) | 120 | Diverges at `+0x24`: this cc1 emits the four `lui's before their `addiu's and swaps the loop counter and the 0x260F30 pointer (`$t2`/`$t3`) against retail's order (`t3` = 0x260F30, `v1` = 0x260EB0, `a0` = 0x260E30, `t2` = 0, `t5` = 0x1B3D30). Array form, pointer form and a re-ordered pointer form all reach the same word |
## Level functions, wave 6 agent lvl6-07 (2026-10-16)

| [`func_L30_00413528`](levels/func_L30_00413528.c) | 120 | Diverges at `+0x24`: this cc1 emits the four `lui`s before their `addiu`s and swaps the loop counter and the 0x260F30 pointer (`$t2`/`$t3`) against retail's order (`t3` = 0x260F30, `v1` = 0x260EB0, `a0` = 0x260E30, `t2` = 0, `t5` = 0x1B3D30). Array form, pointer form and a re-ordered pointer form all reach the same word |




| [`func_L26_002EC840`](levels/func_L26_002EC840.c) | - | Catalogue defect, the `addiu $sp,$sp,+N; nop` family: the whole 20-byte extent is `addiu $sp,$sp,0x80/0xC0/0xF0` with a pad after each, no prologue, no `jr $ra`, no register ever set. The entries on both sides are complete (`func_L02_002EF968` ends `jr $ra; addiu $sp,$sp,0x30` at 0x002EC838, `func_L00_002EC608` opens `addiu $sp,$sp,-0x130` at 0x002EC858), and the same `addiu $sp,$sp,0xF0; nop` pair sits before `func_L00_002EC608` in level 0 too, where the catalogue gives it to the previous entry. Re-cut in `tools/levels.py`; no C can be placed |
| [`func_L02_003DAF90`](levels/func_L02_003DAF90.c) | 156 | Candidate 152 bytes, ONE instruction short: the 16-byte `lq`/`sq` copy's destination stays folded on `$sp` where retail materialises `addiu $v1,$sp,0x10` first. Five spellings of that copy all fold, so it is the allocator. Needs the -G8 unit of `L02_003D26B0.c`; the `$gp` addresses are worked out in the near-miss ($gp is pinned at 0x001AEFF0 in every program) |
| [`func_L02_003DB158`](levels/func_L02_003DB158.c) | 164 | Candidate 160 bytes, one instruction short on the same folded `sq` destination as its twin above; the frame, the four scratch blocks and all five calls are exact |
| [`func_L08_004237F0`](levels/func_L08_004237F0.c) | 172 | Every instruction is retail's, in order, with retail's delay-slot fills; the only difference is at +0x7C, the first value computed after the final `jal`, which lands in `$v0` here and `$v1` in retail. Four spellings give the same `$v0`. Same shape as func_L18_003F9E50 below, so if the October build allocates that register differently both are matches |
| [`func_L18_003F9E50`](levels/func_L18_003F9E50.c) | 160 | Candidate is instruction-for-instruction identical to retail - five `$gp` table bases, the reload of the state index at every call, every delay slot - except one register: the last store's constant lands in `$v0` against retail's `$v1`. Four spellings give the same `$v0`. Needs a -G8 `L18_002C4000-g8.c`; the five addresses are in the near-miss |
| [`func_L02_003DAF90`](levels/func_L02_003DAF90.c) | - | Not attempted: three arguments are `addiu $a1,$gp,0x1450`, `lwc1 $f12,0x1448($gp)`, `lwc1 $f13,0x1444($gp)`, so it needs the -G8 unit for `L02_003D26B0.c` with that unit's small-data block reproduced. The rest of the body is decoded in the near-miss (four calls, a 0x90 frame and one `lq`/`sq` 16-byte copy reachable through `mode(TI)`) |
| [`func_L08_004237F0`](levels/func_L08_004237F0.c) | 172 | Every instruction is retail's, in order, with retail's delay-slot fills; the only difference is at +0x7C, the first value computed after the final `jal`, which lands in `$v0` here and `$v1` in retail. Four spellings give the same `$v0`. Same shape as func_L18_003F9E50 below. **Refuted at the container 2026-10-16** (`check.py` gives `+0x7c`), so the split is the allocator, not the build |
| [`func_L18_003F9E50`](levels/func_L18_003F9E50.c) | 160 | Candidate is instruction-for-instruction identical to retail - five `$gp` table bases, the reload of the state index at every call, every delay slot - except one register: the last store's constant lands in `$v0` against retail's `$v1`. Four spellings give the same `$v0`. **Refuted at the container 2026-10-16** (`check.py` gives `+0x88` at all 3). Needs a -G8 `L18_002C4000-g8.c`; the five addresses are in the near-miss |
| [`func_L16_002D2D10`](levels/func_L16_002D2D10.c) | 88 | Candidate 72 bytes (six spellings give the identical object). Retail has two 2-`nop` pairs and divides by `$f13` first (into `$f1`, stored at +0x278) where this cc1 always divides by `$f12` first and stores +0x274 first, whatever the source order. The resident block is 0x001B99C0, not the 0x001BFF99C0 the stale declarations in the unit named |
| [`func_L09_00406BF8`](levels/func_L09_00406BF8.c) | 108 | Candidate 112 bytes, one saved register too many: retail keeps the outgoing kind in `$a1` and uses `beqz` + `b`, this cc1 spills both locals and uses `beql` with `addiu $s1,$zero,0x2` in the annulled slot (same idiom as func_L12_00427C58) |
| [`func_L09_00403700`](levels/func_L09_00403700.c) | 152 | Candidate 160 bytes; the guard comes out `bnel` with the epilogue `ld $s0,0x0($sp)` in the annulled slot where retail keeps `bne` and hoists `lw $s2,0x68($s1)` into it, the trip test lands in `$v0` against `$v1`, and one `nop` after the `lbu` is extra. Everything else - 0x40000001, 0.17f, the `cvt.s.w`, the `bnel` back edge - is exact |
| [`func_L18_00427B48`](levels/func_L18_00427B48.c) | 148 | Candidate 144 bytes; the chain pointer and the stored 1 take each other's register (`$a0`/`$a1`), and one bare `nop` after the four `addiu` constant set-ups is missing |
| [`func_L00_00427BE8`](levels/func_L00_00427BE8.c) | 144 | Candidate 128 bytes (five spellings identical). The two quadword copies do reach `lq`/`sq` through `mode(TI)`, but this cc1 folds both destinations into the store offsets on `$s0` where retail materialises each in a register and re-copies the blend weight with `mov.s $f12,$f0` before the first call |
| [`func_L08_003F79E0`](levels/func_L08_003F79E0.c) | 104 | Candidate 124 bytes; the four bounds constants (7000000/5500000/9000000/1.0f) land in different FPRs, the frame grows to 0x20 with `$ra`/`$s0` saved, and retail's leading 128-bit zero (`pcpyld $a0,$zero,$zero; sq $a0,0x0($sp)`) is the fold already recorded for func_L00_002A75D8. Note `D_L08_001B9E04` is a word store, not `sb` |

| [`func_L09_003F02D0`](levels/func_L09_003F02D0.c) | 92 | Wave 6 w08: 96 bytes, one word over. Retail shares a single `a3 = 3` between the byte store at 0x28 and the call's fourth argument; this cc1 materialises the 3 twice. Shared locals and swapped last stores (four shapes) all stay at 96 bytes. |
| [`func_L23_00418650`](levels/func_L23_00418650.c) | 124 | Exact size, two pairs of words swapped: the scheduler orders the parameter copy and the address's second half by a tie-break no spelling moved (+0x14), and puts `ld $s0` between the `lq` and the `sq` where retail keeps `lq; sq` before the restores (+0x60; 64 functions end that way in retail, none with a restore between) |
| [`func_L00_0032EF10`](levels/func_L00_0032EF10.c) | 160 | Wave 7 w09: 152 bytes, two copies short. Ring scan; the `% 30` needs a VARIABLE divisor (a literal folds to a multiply and drops the divide guard), `if (n >= arg1)` gives retail's `movz` where `if (n < arg1)` gives `movn`, and `base` + a hoisted `tbl = base + 0x1E0` local are both needed. Retail's preheader holds two copies this cc1 will not make (`$t1 = $t0` base, `$t2 = $v0` bound) with the register assignment that follows from them (retail i = `$a3`, base = `$t0`, bound = `$v0`->`$t2`; here i = `$t0`, base = `$t2`). ~30 forms over five runs, all 148 or 152. |
| [`func_L16_00412E70`](levels/func_L16_00412E70.c) | 212 | Wave 7 w09 (tour 2): **size right (212)**, first difference at +0x1c. Level teardown; the prologue, every call's argument setup and the three guarded `if (x != 0) func_0029F338(x)` (next handle loaded in the branch delay slot) all match. The store's value must come from a local (`u8 two = 2; D_0013963B = two;`; a literal puts the first difference at +0x4), and what remains is the slot where the `2` is materialised: retail `addiu $v0,$zero,0x2` at 0x412E7C, this cc1 one slot later into `$a0`. Both address spellings are relocated, so the register is free. |
| [`func_L15_00381CF0`](levels/func_L15_00381CF0.c) | 208 | Wave 7 w09 (tour 2): 216 bytes, two over - the whole residue is the frame. Id-list walk with three tests per entry; every instruction matches except that retail re-materialises the base's address inside the loop (`lui $a0,0x1B ; lw $a0,0x261C($a0)`, frame 0x30, its three saved registers all taken) where this cc1 hoists that `lui` into `$s3`, paying the `sd`/`ld $s3` pair and a 0x40 frame. Slot inline or in a local, and either spelling of the float test, all give 216. |
| [`func_L00_002BA138`](levels/func_L00_002BA138.c) | - | Not a C row: MMI (two 128-bit pairs, `lq`/`sq`). ASM_FUNC candidate, same batch as func_L16_00411600.c and func_L22_003A2988.c. |
| [`func_L16_00416EC8`](levels/func_L16_00416EC8.c) | - | Not a C row: MMI (2 pairs). Screened in wave 7 w09 tour 2. |
| [`func_L02_00413730`](levels/func_L02_00413730.c) | - | Not a C row: MMI (4 pairs). Screened in wave 7 w09 tour 2. |
| [`func_L15_004320B0`](levels/func_L15_004320B0.c) | - | Not a C row: MMI (6 pairs). Screened in wave 7 w09 tour 2. |
| [`func_L08_00389B68`](levels/func_L08_00389B68.c) | - | Not a C row: MMI (2 pairs). Screened in wave 7 w09 tour 2. |
| [`func_L16_00404E40`](levels/func_L16_00404E40.c) | - | Not a C row: MMI (2 pairs). Screened in wave 7 w09 tour 2. |
| [`func_L16_00411600`](levels/func_L16_00411600.c) | - | Not a C row: MMI (`lq $s0,0($v0)` 0x7A020000, `sq $v1,0($v0)` 0x7C620000) inside a two-iteration handle loop. ASM_FUNC candidate. |
| [`func_L23_003C01F8`](levels/func_L23_003C01F8.c) | 204 | Retail's size, 12 words differ, all in the three 16-byte copies: one store folds to `sq $v0,0x10($sp)`, the `-2.0f` argument is scheduled before the copies, and the last copy swaps its address and data registers. Not an MMI row: an older decoder read `sq $v0,0($sp)` backwards. Exact in a scratch copy with an inline-asm 16-byte copy macro, which `src/` does not allow. |
| [`func_L16_0040D188`](levels/func_L16_0040D188.c) | 208 | Wave 7 w09 (tour 2, measured): **size right (208), first word differs**. Id-list walk matching the already-matched src/levels/01_oozla/L01_003CAFE0-g8.c skeleton; three body-first shapes land on exactly 208 out of 14 measured, every other shape costs 4-24 bytes more. The unit needs `-G8` plus a sized `sda` bound (retail's bound is `lw $v0,0x3B48($gp)`). Residual: retail opens with `daddu $a2,$a0,$zero` (the parameter moved out of `$a0`, which then holds the class value) and materialises 0xF35/0xEA3/0xFF/1 at their uses in `$v0`; this cc1 keeps the parameter in `$a0` and hoists the four constants into `$t0`-`$t3`. |
| [`func_L08_002F5CB8`](levels/func_L08_002F5CB8.c) | 192 | Wave 7 w09: 188 bytes, one short. Spawn call plumbing; the ABI (5 int args, so `$t0`, then `$f12`-`$f14`) and both call sites match word for word. Residues: retail saves `$f22 = $f12` and converts into `$f21`, this cc1 swaps the two, and retail opens with `daddu $v0,$a1,$zero` before `mtc1 $v0,$f21` where this cc1 reads `$a1` directly; spelling the copy, saving the float in a local and inlining the conversion all fold away. |
| [`func_L00_002B9CA8`](levels/func_L00_002B9CA8.c) | 184 | Wave 7 w09: 180 bytes, one short. Eight-entry walk with calls. Retail keeps the base in `$s3` and derives `$s0`, `$s2` (walker) and `$v1 = $s3 - 0x23F0` (the D_00189E20 read, same high half) from it; this cc1 walks the base register itself, copy-propagates the walker away and materialises D_00189E20 with its own `lui`. Spelling the walker explicitly, routing the u8 read through the base, a struct field and declaration orders all still give 180; an `s16` for func_00282978's return adds an `sll`/`sra` pair (188) and the index form is 168. |
| [`func_L22_003A3CA0`](levels/func_L22_003A3CA0.c) | 176 | Wave 7 w09: 180 bytes, one over. Twin of func_L23_0041CCC0.c (same 3x0xF0 clear loop) and the same residue: the outer counter gets a second pseudo (`addiu $v0,$a1,0x1` ... `daddu $a1,$v0,$zero` against one in-place `addiu $a0,$a0,0x1`). The tail must read each counter once and store it to both targets; grouping by target or interleaving the loads costs three more instructions (188). Two functions of the family now show the same +1, from different tails. |
| [`func_L16_00424B48`](levels/func_L16_00424B48.c) | - | Not a C row: `addiu $sp,$sp,0x190; nop; addiu $sp,$sp,-0x10`; the level-16 entry starts 8 bytes early in the previous function's epilogue. Real body: 160 bytes at 0x424B50, ending at the next entry (0x424BF0). |
| [`func_L05_00334358`](levels/func_L05_00334358.c) | - | Not a C row: `addiu $sp,$sp,0x60; nop; addiu $sp,$sp,0xA0`; no function starts with a stack restore, the entry after the gap is func_002A8E20. Catalogue split |
| [`func_L05_0033C5E8`](levels/func_L05_0033C5E8.c) | - | Not a C row: `addiu $sp,$sp,0x60; nop; addiu $sp,$sp,0x10`; the entry after the gap is func_002B0360. Catalogue split |
| [`func_L05_0033E228`](levels/func_L05_0033E228.c) | - | Not a C row: `addiu $sp,$sp,0x40; nop; addiu $sp,$sp,0x30`; the entry after the gap is func_L00_00329E90. Catalogue split |
| [`func_L05_00339EE8`](levels/func_L05_00339EE8.c) | - | Not a C row: three stack restores `0x50/0x30/0xD0` with their padding; the entry after the gap is func_L00_00322A18. Catalogue split |
| [`func_L05_0033C650`](levels/func_L05_0033C650.c) | - | Not a C row: three stack restores `0x30/0x180/0x20` with their padding; the entry after the gap is func_002B0520. Catalogue split |
| [`func_L05_00304B40`](levels/func_L05_00304B40.c) | - | Not a C row: four stack restores `0x30/0x80/0xC0/0xF0`; the entry after the gap is func_L00_002EC608. Catalogue split |
| [`func_L05_0033C2E0`](levels/func_L05_0033C2E0.c) | - | Not a C row: four stack restores `0x50/0x30/0x30/0x30`; the entry after the gap is func_002AFEB8. Catalogue split |
| [`func_L05_0033CD40`](levels/func_L05_0033CD40.c) | - | Not a C row: restores and padding around a stray `sh $zero,0x1C($a0)`; the entry after the gap is func_002B0A18. Catalogue split |
| [`func_L05_0033DEE8`](levels/func_L05_0033DEE8.c) | - | Not a C row: thirteen consecutive `addiu $sp,$sp,+N` restores with their padding; the entry after the gap is func_002B37E8. Catalogue split |
| [`func_L07_00321D68`](levels/func_L07_00321D68.c) | 132 | Raw 128-bit MMI words (`lq`/`sq`, opcodes 0x1E/0x1F) around the `jal func_322878` stem call and the func_3221C8 tail; no C spelling, ASM_FUNC candidate |
| [`func_L10_00417440`](levels/func_L10_00417440.c) | 136 | Candidate is 140 bytes; the 128-iteration clear head is byte-exact, but retail materialises the arena base in three words (symbol %lo then `addiu 0x2FE0`) where this cc1 folds the offset into the reloc, and retail rebuilds the D_001B251C address inside the list-walk loop where this cc1 hoists its `lui` into the `beqz` delay slot and drops retail's `daddu $a0,$v0,$zero` |
| [`func_L10_00419188`](levels/func_L10_00419188.c) | 96 | Row is eight bytes too long (real body 0x419188..0x4191E0 = 88 bytes, the trailing `jr $ra; nop` at 0x4191E0 is unreachable and the next row starts at 0x4191E8); re-cut to 0x58. The candidate is 84 bytes with the same hoisted-address trait as func_L10_00417440 |
| [`func_L11_00445D28`](levels/func_L11_00445D28.c) | 136 | Candidate is 112 bytes; the semantics reproduce but retail keeps three separate exits (early `jr $ra; daddu $v0,$zero,$zero`, a forward one for both later `return 0`s, and the `jr $ra; sh $a0,0x2A0($v1)` arm) where this cc1 merges the two `return 0` epilogues, and it lays the 0x14 split out differently |
## Wave 6, agent lvl6-01 (2026-10-16, src/levels)

Matched this tour (7, all re-checked with the Docker-free ruler, `N of N places match`):
`func_L08_00424ED0` (1/1), `func_L08_00425F28` (1/1), `func_L25_00377608` (1/1),
`func_L30_00382F10` (1/1), `func_L30_00384768` (1/1), `func_L04_003EDFF0` (2/2),
`func_L00_003F5660` (27/27).

| [`func_L30_0032AAF0`](levels/func_L30_0032AAF0.c) | 20 | Not a function: `addiu $sp,$sp,0x30` / nop / `addiu $sp,$sp,0x30` / nop / `sh $zero,0x1C($a0)` at 0x32AAF0, the epilogue-fragment shape of func_L00_00341798; no prologue and no `jr $ra` in the extent, needs the real start from the maintainers |
| [`func_L19_0041C3D8`](levels/func_L19_0041C3D8.c) | 148 | Catalogue defect, entry eight bytes early: the row opens `addiu $sp,$sp,0x10` / nop and the real body (`addiu $sp,$sp,-0x20` ... `jr $ra`) is 140 bytes at 0x0041C3E0. The reconstructed body is in the file (state gate on byte 0x20, then func_1234F0 / func_131430 / func_122B00 / func_122DA8 / func_123298 with two `ori`+`dsll32` 64-bit operands) |
| [`func_L08_004254D0`](levels/func_L08_004254D0.c) | 72 | Register wall: the sibling func_L08_00425518's body minus its live-state test; retail puts the table in `$v1` and the shifted index in `$v0`, this cc1 the other way round, and hoists `a0 = 0` above the `addu` where retail sinks it into the `beqz` slot. Four spellings (two locals, swapped statements, one `char *p` holding the sum, early return) all give the same |
| [`func_L08_00429408`](levels/func_L08_00429408.c) | 152 | Body, calls and order exact; the three bases (0x1B39E0 / 0x1B39A0 / 0x1B3A30) interleave differently in the prologue from retail's `lui/addiu` grouping. Array-index and pointer-local spellings give the same 176-byte shape |
| [`func_L00_003BCDC0`](levels/func_L00_003BCDC0.c) | 100 | 108 bytes against 100: the walk uses an end pointer so this cc1 emits `sltu` where retail has the signed `slt` (the original compared integers), and it materialises `0x23F060+0xB00` first and derives the base by `addiu $s0,$s2,-0xB00` where retail materialises the base itself |
| [`func_L02_003DBEC8`](levels/func_L02_003DBEC8.c) | 120 | Register wall, second wave to hit it: every load, store, the table lookup, the `lq`/`sq` copy and the trailing call are in the retail sequence; the parameter copy and the context land in `$t1`/`$t0` where retail keeps `$a2`/`$a3`, and this cc1 sinks the epilogue's `ld $ra` into the `bnez` slot (112 bytes against 120) |
| [`func_L12_0041A578`](levels/func_L12_0041A578.c) | 108 | 112 bytes against 108: the index lands in `$a2` where retail increments the parameter in place (`addiu $a1,$a1,0x1`), and this cc1 folds the loop-back into the float test (`bc1fl`) where retail keeps `bc1t` + a separate `b` |
| [`func_L00_0037E000`](levels/func_L00_0037E000.c) | 192 | 176 bytes against 192: this cc1 turns `for (i = 0; i < 32; i++)` into a descending `i--` countdown where retail counts up, plus the record pointer stays in `$s0` where retail copies it to `$a0` for the store |
| [`func_L20_00443CF0`](levels/func_L20_00443CF0.c) | 100 | 0x24: retail emits `daddu $a1,$sp,$zero` before the prologue's `sd $s0,0x30($sp)`, this cc1 the other way round; every other word matches |
| [`func_L20_00444018`](levels/func_L20_00444018.c) | 108 | Retail hoists the 0.78539824f materialisation above `addiu $sp,$sp,-0x50` and gives `$s1` to the state pointer, `$s2` to `&v1`; this cc1 sinks the constant below the frame adjust and swaps the two callee-saved registers |
| [`func_L20_00443D58`](levels/func_L20_00443D58.c) | 152 | Reaches retail's exact 0x60 frame and its `swc1 $f20,0x50($sp)` once the helper output is re-read into a second float local after the 0x443B60 call. 0x2c: retail sets `$a3 = $sp+0x20` before `$a2 = $s2`, this cc1 the other way round; every other word matches |
| [`func_L20_00443328`](levels/func_L20_00443328.c) | 136 | Same size; this cc1 loads `D_L20_001B2F9C` through `lui $v1` + `lw $a1` and takes the state pointer in `$a0` where retail keeps them in `$a0`/`$v1`, and the `if (… == 0) return 0;` arm lays out as a `beqz` fall-through where retail gives the empty arm its own `b` over the block (the func_00116300 wall) |
| [`func_L20_00443508`](levels/func_L20_00443508.c) | 148 | Candidate is 136 bytes; the head load and the 0x1FF/0x200/0x1233 guard chain are the ones the disassembly shows, but the three early `return 0` arms collapse to three words fewer than retail's, which materialises the return-zero constant in each branch's delay slot |
| [`func_L13_0043E8C0`](levels/func_L13_0043E8C0.c) | 24 | Catalogue defect: `jr $ra` / nop three times over at 0x43E8C0, three merged empty bodies; a tools/levels.py split unblocks it |
| [`func_L03_00329ED0`](levels/func_L03_00329ED0.c) | 28 | Catalogue defect: starts `addiu sp,sp,+0x10` / nop, real body at 0x329EE8, row carries no `jr $ra`; a tools/levels.py split unblocks it |
| [`func_L13_0042BE60`](levels/func_L13_0042BE60.c) | 44 | Catalogue defect: starts `addiu sp,sp,+0x10` / nop, a clean 32-byte body at 0x42BE68; a tools/levels.py split unblocks it |
| [`func_L13_003383B8`](levels/func_L13_003383B8.c) | 52 | Catalogue defect: starts `addiu sp,sp,+0x10` / nop, real body at 0x3383E0, row carries no `jr $ra`; a tools/levels.py split unblocks it |
| [`func_L03_0038AA20`](levels/func_L03_0038AA20.c) | 268 | Catalogue defect: starts `addiu sp,sp,+0x18` / nop, real body at 0x38AA38; a tools/levels.py split unblocks it |
| [`func_L00_002F7298`](levels/func_L00_002F7298.c) | 136 | Slot picker over the 0x0027DD00 block (0x90-byte records, `i` 0..12, then a `slt`/`movz` max into 0x7C). Diverges at `+0xc`: retail peels the first comparison (`lw $v1,0x64($v0)`), tests `i < 13` again on the found path, and carries a dead `mult $a1,$v1` (0x00A32018) that no spelling here reproduces |
| [`func_L00_002F59A8`](levels/func_L00_002F59A8.c) | 140 | 144 bytes: two shared counters stepped around the indexed `func_2F4490` call. Retail saves only `$ra` (frame 0x10, nothing live across the call) and reads the 0x1B1BA7 byte that it increments out of `$gp` (`lbu $v1,0x2BB7($gp)`) while the 0x1B1BA6 byte and the index read stay absolute - the gp copy needs its own name. Keeping the index or the next value in a local grows the frame to 0x20 with two saved registers (144-148) |
| [`func_L30_004185E0`](levels/func_L30_004185E0.c) | 140 | 144 bytes: the owner's 0x50 object refreshed through two vector helpers and its 0x64 method. Retail keeps 0x004005BC in `$s0` across both calls and derives the second constant as `addiu $a2,$s0,-0x230` (one symbol, negative index); the one-base and two-local forms both give 144 with a 0x30 frame, the two-separate-addresses form gives 136 with a 0x20 frame |
| [`func_L20_00435178`](levels/func_L20_00435178.c) | 156 | Distance test against a per-object threshold; three spellings reach 152/160 ("instruction at +0xc differs"). Retail ends on the FP-compare hazard `c.olt.s $f0,$f1; nop; bc1tl` - the extra/missing word is that `nop` and the `-1` materialisation. Hazard-`nop` class already recorded for func_L04_0039FEA8 and func_L22_003A2DF8 |
| [`func_L17_003C71B0`](levels/func_L17_003C71B0.c) | 160 | Every instruction right at 160 bytes except one: this cc1 materialises the eighth argument of the 8-argument setup (`D_L17_001A8F20`) FIRST, retail keeps its `lui/addiu` pair last, after the 4 and 6 constants. Two more spellings give 156 and 164 |
| [`func_L03_003CC478`](levels/func_L03_003CC478.c) | 152 | Not C: `.word 0x7A620000`/`0x7C820000`, MMI (lq/sq family) |
| [`func_L30_00413490`](levels/func_L30_00413490.c) | 152 | Not C: `.word 0x78620000`/`0x7C820000`, MMI (lq/sq family) |
| [`func_L03_0042B868`](levels/func_L03_0042B868.c) | 156 | Not C: `.word 0x7A620000`/`0x7E020000`/`0x7A820000`/`0x7C620000`/`0x7A220000`, MMI (lq/sq family) |
| [`func_L03_003E3750`](levels/func_L03_003E3750.c) | 172 | 164 bytes (flat-pointer spelling 156): the keyed 16-byte record lookup at 0x002D2AB0 with the kind dispatch (<=0 -> 0, 1-2 -> word at (0x64+1)*4, 3 -> words +4 and +8). Retail keeps the record byte offset in `$a0` and rematerialises `a3 + a0` twice at the end, which both spellings fold away |
| [`func_L00_003FE8D0`](levels/func_L00_003FE8D0.c) | 76 | 46 spellings, best is exact except two swapped loads: this cc1 issues the `$gp` count load before the base-pointer load and fills the `bnez` delay slot with it, retail issues the base load first and fills the slot with that; the `sh` then sinks behind the `blez`. Register roles, the three filler `nop's and the tail all match. -G8 unit, `sda` member |
| [`func_L00_003FE8D0`](levels/func_L00_003FE8D0.c) | 76 | 46 spellings, best is exact except two swapped loads: this cc1 issues the `$gp` count load before the base-pointer load and fills the `bnez` delay slot with it, retail issues the base load first and fills the slot with that; the `sh` then sinks behind the `blez`. Register roles, the three filler `nop`s and the tail all match. -G8 unit, `sda` member |
| [`func_L02_003CB090`](levels/func_L02_003CB090.c) | 96 | 92 bytes: retail takes the branch-likely form and brings the epilogue restore into its delay slot (one extra instruction), this cc1 fills from the fall-through; the absolute member is materialised into a scratch base instead of the argument register |
| [`func_L02_00332900`](levels/func_L02_00332900.c) | 112 | 120 bytes: retail rebuilds the absolute `lui+lw` at both sites, this cc1 keeps the address in a callee-saved register (the `_1` double name took 128 to 120, not to 112) |
| [`func_L18_004283F0`](levels/func_L18_004283F0.c) | 56 | Two-case value select: this cc1 if-converts every spelling (48 or 60 bytes, `xori`+`movz`), retail keeps the plain `beq` chain with its own `jr $ra` per arm |
| [`func_L25_00373EE0`](levels/func_L25_00373EE0.c) | 20 | Not code: no `jr $ra`, both stores go through `$v0 = 0`, words interleaved with `nop`s, and it clobbers the previous entry delay-slot load. Catalogue entry, not C |
| [`func_L07_003791E8`](levels/func_L07_003791E8.c) | 36 | Catalogue entry 8 bytes early: it opens on an orphan `addiu $sp,$sp,+8` / `nop` epilogue fragment; the real body is 28 bytes at 0x003791F0 |
| [`func_L03_0041F048`](levels/func_L03_0041F048.c) | 72 | Not C (measured): four spellings of the 16-byte copy all give ld/sd word moves, this cc1 never emits the retail `lq`/`sq` |
| [`func_L02_003A8770`](levels/func_L02_003A8770.c) | 148 | Not C: `.word 0x7A420000`/`0x7C820000`, LQ/SQ pair in the middle of an otherwise ordinary body |
| [`func_L07_002E2090`](levels/func_L07_002E2090.c) | 112 | Not C: two `sq` (opcode 0x1F) into the stack temporaries; the rest is plain COP1 float work with 0.4f |
## Level functions, wave 6 agent lvl6-12 (2026-10-16)

| [`func_L06_0035B768`](levels/func_L06_0035B768.c) | - | Not a C row: catalogue defect. The 20-byte extent is three stack restores with their padding (`addiu $sp,$sp,0x50` / `nop` / `0x40` / `nop` / `0x80`), no prologue and no `jr $ra`; the bytes before it are the epilogue tail of func_L04_0033CB08 and level 5 has a real function (func_002CFDA8, 0x41C) over the same address. Same shape as func_L00_0031AFF0 / func_L00_00358670 |
| [`func_L22_003E87B8`](levels/func_L22_003E87B8.c) | 120 | Candidate size exact; only the argument setup order differs at +0x2c - retail materialises the fourth argument's `addiu $a3,$zero,0xE0B` before the call's `addiu $a0,$v0,0xC`, this cc1 the other way round. Tried: the constant in a named local, the font pointer in a local (identical) |
| [`func_L17_003E4860`](levels/func_L17_003E4860.c) | 124 | Candidate is 120 bytes; the byte/index pair lands in `$v1`/`$v0` swapped and the walk pointer in `$a0` against retail's `$a1`/`$a2`, and this cc1 emits plain `bltz`/`bne` where retail annuls them (`bltzl`/`bnel`, each with the next `lh` in its delay slot). Hoisting the record base into a named local does give retail's `$gp` load in the branch delay slot |
| [`func_L03_003C85F8`](levels/func_L03_003C85F8.c) | 100 | Candidate is 96 bytes; retail carries one extra instruction, the second argument copied `$a1 -> $v0 -> $a0` (the copy waits in `$v0` while `$a0` is still the first argument), and gives the first argument `$s0` where this cc1 gives it `$s1`. Tried: the mode in a local, both pointers in locals at entry (identical) |
| [`func_L04_00412E38`](levels/func_L04_00412E38.c) | 132 | Candidate is 120 bytes; retail keeps the second pointer in `$s0`/the first in `$s1` (opposite here), preserves the third float in `$f21` with the product in `$f20` where this cc1 keeps only one float live (one saved pair less), and rematerialises a second `mul.s` for the 0xF4 field where this cc1 reuses one |
| [`func_L04_00415E28`](levels/func_L04_00415E28.c) | 124 | Candidate is 116 bytes with the walk over the 140 records exact (a `do {} while` is required - retail has no pre-loop guard). Retail annuls both tests and duplicates the record advance into the delay slots; its loop test is the SIGNED `slt` where this cc1 emits `sltu` for a pointer compare, and retail carries a dead `lw $t0,0x140($s0)` this cc1 does not emit |
| [`func_L04_00415EA8`](levels/func_L04_00415EA8.c) | 60 | Candidate is 56 bytes; sequence exact but retail annuls both tests (`beql` with the store in the delay slot, `bnel`) and decrements the counter twice (delay slot plus shared loop bottom) where this cc1 hoists the single decrement into the `beqz` delay slot. Tried: `do {} while (--n >= 0)`, `for` with the decrement in the increment, byte pointer |
| [`func_L00_002D23C0`](levels/func_L00_002D23C0.c) | 176 | Candidate is 180 bytes; retail keeps the caller's index in `$a2` with the scale folded into `sll $v1,$a2,2` and compares the clamped byte with the signed `slt`, this cc1 materialises the index in `$t0`, gives one of the two value registers the other name and emits `sltu` + `andi 0xFF`. Writing both index stores before the helper call moves the index to a caller-saved register and drops the `sd`/`ld` pair, but the size and the register names stay off |
| [`func_L00_003032F0`](levels/func_L00_003032F0.c) | 148 | Size exact (148/148) and instruction-identical but for one register: this cc1 materialises the state base straight into the register the slow arm keeps live (`addiu $a3,$v0,%lo` / `lw $v1,0x1C($a3)`) where retail materialises into `$v0` for the guard and copies later (`daddu $a3,$v0,$zero`). Needs `$gp` pointers, so the `-G8` sibling plus `extern Rec *D_x[1] __attribute__((sda))`. 27 places |
| [`func_L11_00443CE0`](levels/func_L11_00443CE0.c) | 148 | Not a C row: `jr $a0` at +0x1c (raw word 0x00800008), a register jump. ASM_FUNC candidate |
| [`func_L02_003DB880`](levels/func_L02_003DB880.c) | 184 | Candidate is 204 bytes (196 with an `s32` counter); the float gate reproduces (the 0x20 float against `(float)0x24 * (3.0f - (float)(s16)counter) * 0.33333334f`) but retail peels the walk - one `sh 0x282` before the empty counter loop and one after - where this cc1 keeps a store per iteration |
| [`func_L09_003FB860`](levels/func_L09_003FB860.c) | 164 | Wave 6 w08: exact size (164) with the pair stores written as full expressions in retail's order (pair[0], table, pair[1]); one ordering difference left at +0x8 -- retail materialises D_001B36E0 as an adjacent lui/addiu pair before the first save, this cc1 splits the D_00255E90 pair around `sd $s0,0x0($sp)`. Four shapes with a named pair pointer stay one word short (160). |
| [`func_L02_00389F70`](levels/func_L02_00389F70.c) | 152 | 148 bytes out of 152: this cc1 copies the frame argument into `$a1` (`daddu $a1,$a0,$zero`) and drops one `nop` retail keeps in the `bne` delay slot; eight spellings give the same 148 (144 with gp-relative members) |
| [`func_L02_00348420`](levels/func_L02_00348420.c) | 180 | Not C: `.word 0x78820000`/`0x7FA20000`, LQ/SQ pair moving the loop scratch record; the rest is a nearest-index float loop (9999999.0f, bc1f/mov.s) |
| [`func_L08_00425328`](levels/func_L08_00425328.c) | 108 | Not C with this cc1: the success arm is the single word `0x4600A028` (COP1 fmt=S funct=0x28, `fd=$f0 fs=$f0 ft=$f20`), i.e. max.s - the ternary gives `c.olt.s`+`bc1f`+2 `mov.s` and `r = D; if (r < f14) r = f14;` gives `c.olt.s`+`bc1tl`, both 116 bytes against 108. Everything else (the two `lq`/`sq` copies with f13/f12 in their third float, the `func_002761F8(high, low, 2, 0, 0)` call) is understood |
| [`func_L08_00425398`](levels/func_L08_00425398.c) | 176 | Not C with this cc1, same wall: the word at 0x00425434 is `0x46010028`, the same COP1 funct 0x28. Body and frame layout are in the file for whoever gets a max.s-capable cc1 |
| [`func_L02_003D7B88`](levels/func_L02_003D7B88.c) | 148 | 140 bytes against 148 with the guard first (136 with the context loaded first): the sibling func_L02_003DBEC8's body plus the two level lookups (pointer table at 0x1FEAC0 indexed by +4, base pointer 0x1B359C scaled by 0x100 indexed by +0) is exact; the parameter copy lands in `$t2`/`$t1` against retail's `$t1`/`$t0` and this cc1 sinks the epilogue's `ld $ra` into the bnez slot where retail loads the context |
| [`func_L18_00428048`](levels/func_L18_00428048.c) | 180 | Every instruction matches except 3 words: retail gives the first counter `$v1` and reuses it for the second, this cc1 gives it `$v0`. Six spellings give the same 3 words |
| [`func_L02_003C0890`](levels/func_L02_003C0890.c) | 180 | Not C: `.word 0x7A030010`/`0x7C430010`, LQ/SQ 16-byte copy inside an object initialiser |
| [`func_L18_00428338`](levels/func_L18_00428338.c) | 180 | 176 bytes: everything present (the `lbu 0x42 != 10` early return that never writes `$v0`, the `mtc1`+`nop`+`cvt.s.w`, the `xori`/`movz` 0x40000001-vs-0x40100000 select, the 9-argument call); this cc1 hoists the `lbu 0xBC` and `ori $a3` above the `mtc1` where retail keeps them after the conversion, and the select group then costs one instruction less. Four spellings give 176 |
| [`func_L02_003D0238`](levels/func_L02_003D0238.c) | 184 | Not C: three LQ/SQ pairs (`.word 0x78A20000`/`0x7C820000`, `0x7C620000`, `0x7E000000`) in a spawn/initialise body |
| [`func_L00_003A1F68`](levels/func_L00_003A1F68.c) | 172 | Blend of two 16-byte elements by t, 164 of 172: a 16-byte copy reaches `lq`/`sq` only through `__attribute__((mode(TI)))` (a plain 16-byte struct assignment emits four `ldl`/`ldr` + `sdl`/`sdr` pairs and comes out 212), and every instruction, both float compares and the three calls then agree. The 8 bytes are the first copy's branch delay slot: retail emits `lq`, `sq` then the branch and lets the filler duplicate the epilogue's `ld $s0`; this cc1 postpones the `sq` into the slot. Same `lq`/`sq` family as func_L16_0040C7E8 and func_L22_003A2988, but the C spelling does exist |
| [`func_L11_002FE4C0`](levels/func_L11_002FE4C0.c) | 12 | Not a function: `addiu $sp,$sp,0x80; nop; addiu $sp,$sp,0xC0`, both adjustments positive and no `jr $ra`; the rows either side fit exactly around it (func_L02_002EF968 ends at 0x002FE4BC, func_L00_002EC248 starts at 0x002FE4D0), so the row is padding cut mid-pair. Same class as func_L00_002ED7F8 and func_L05_0037BB78 - fix in `tools/levels.py` |
| [`func_L04_00422D40`](levels/func_L04_00422D40.c) | 104 | Not C: `.word 0x700024A9` at +0x14, opcode 0x1C with an MMI funct field, the same word the func_L00_003A8E28 near miss documents (0x700014A9); the body builds four float constants into a 16-byte stack slot and feeds the packed op. ASM_FUNC candidate |
| [`func_L04_003E5298`](levels/func_L04_003E5298.c) | 124 | 116 of 124: structure, both branch-likely delay slots, the `bgez` terminator loop and the `$gp` word (the -G8 sibling with `extern int D_L04_001B24DC __attribute__((sda))` gives `lw $a2,0x34EC($gp)`) are exact. Retail keeps two `daddu` copies this cc1 coalesces away - the tag loaded into `$v0` and copied to `$v1`, the table entry copied to `$a0`. Register allocation |
| [`func_L04_00407F80`](levels/func_L04_00407F80.c) | 164 | One register apart: every instruction agrees once the +0x68 field is read unconditionally (that is what makes it `bnez` with the load in the slot, not `bnel`) and the +0x30 constant is `unsigned char` (a signed `char` folds 0xC4 to `addiu $v1,$zero,-0x3C`). The delayed load lands in `$v1` here against retail's `$a1`. Register allocation |

## Level functions, wave 6 agent lvl6-12, tour 2 (2026-10-16)

| [`func_L04_00404068`](levels/func_L04_00404068.c) | - | Not a C row as split: the 152-byte extent opens with eight bytes of the PREVIOUS function's epilogue; the real entry is 0x00404070, size 144. Master's LEVEL_ENTRY_OFFSETS only carries three boot cases, so the ruler still compares from 0x00404068. Body written out for the re-split |
| [`func_L14_003F0E98`](levels/func_L14_003F0E98.c) | 160 | Size EXACT and the first 19 stores byte-identical - a quad corner block with the 64-bit pair 0x0000FF9000000260 at 0x80. Only the ORDER of the store groups differs; measured with three source orders (the emitted sequence is neither the source order, nor its reverse, nor a rotation) |
| [`func_L22_00415A48`](levels/func_L22_00415A48.c) | 160 | Candidate is 176 bytes; the record pointer's `nosda` second name is right, the four extra instructions come from three guards whose retail form folds the frame restore into the annulled branch delay slot (`beql`/`bnel` + `ld $s0,0x0($sp)`) |
| [`func_L04_0040ACC8`](levels/func_L04_0040ACC8.c) | 92 | Candidate is 104 bytes: all four globals come out ABSOLUTE here where retail has the bare `$gp` form. Measured with the W4 recipe (`__asm__` name first, then `__attribute__((section(".sdata")))`), under -O2 -G0 and under -O2 -G8, without change. The matched bodies in L04_00402D90-g8.c only reach `$gp` for stores that land in a delay slot (`.set nomacro`) |
## Level functions, wave 5 agent lvl6-03 (2026-10-16)
## Level functions, wave 6 agent lvl6-03 (2026-10-16)

| function | size | verdict |
| [`func_L14_004321C0`](levels/func_L14_004321C0.c) | 16 | Not a function: two complete eight-byte `jr $ra` + `nop` stubs (0x004321C0 and 0x004321C8) that the catalogue merged into a single row; 0x004321BC is alignment padding and the 636-byte body starts at 0x004321D0. Fix in `tools/levels.py` |
| [`func_L01_003BD150`](levels/func_L01_003BD150.c) | 28 | Not code: 28 is not a multiple of eight and cannot be a body - the row is a small table of `addiu $sp,$sp,0x20` / `addiu $sp,$sp,0x10` + `nop` pairs. The `jr $ra` before it is the previous function's epilogue and the next body starts at 0x003BD178 |
| [`func_L08_0036EFC8`](levels/func_L08_0036EFC8.c) | 100 | Both blocks have retail's exact shape (guard, `lhu`/`ori`/`sh`, the reload, `sw $zero,0x98`; then the array walk) and 20 of 25 words match. The fault is the 0x221AE0 list: retail keeps the %hi in $a1 and pays the %lo twice (`lw $v0,0x1AE0($a1)`, `addiu $a1,$a1,0x1AE0`) while this cc1 materialises the address whole into $v0 and loads `0($v0)`. Twenty spellings measured (for/while/do-while walks, index form, guard written on the symbol, two `__asm__`-free names, literal addresses - the last 108 bytes) all give the same form |
| [`func_L08_0036F030`](levels/func_L08_0036F030.c) | 116 | Same wall as its neighbour func_L08_0036EFC8, with the `&= 0xFFFC` body and the `*(*(p+0x24)+0x10)` chain: block 1 matches retail word for word (22 of 29), and only the walk's address materialisation differs at +0x2C/+0x34 |
| [`func_L08_0037E6D8`](levels/func_L08_0037E6D8.c) | 116 | 26 of 29 words. Retail copies the block base into $a3 for the loop bound (`daddu $a3,$s1,$zero` at +0x2C, `lh $v0,0x44($a3)` at +0x50) where this cc1 reuses $s1 and fills the freed word with the assembler `nop`. Holding the base in a local is what keeps the block address materialised (the folded per-field form is 8 diffs); a second name for the base in four placements stays at 3 |
| [`func_L04_0033CB08`](levels/func_L04_0033CB08.c) | 124 | Every retail instruction present; only the second call's delay slot differs - retail leaves the third integer move `daddu $a2,$s2` for it, this cc1 puts `mov.s $f14,$f21` there and the integer move beside the others. The scale is a `mul.s` by 0.0009765625f (`lui $at,0x3A80` + `mtc1`), not a division by 1024. Four argument spellings give the same 124 bytes |
| [`func_L04_0033C420`](levels/func_L04_0033C420.c) | 160 | Decoded chain: two 16-byte buffers copied out of a0 with `lq`/`sq`, the +8 float of each nudged by f12/f13 (the second clamped at 0), `func_002761F8(v1,v2,0x22,a1,0)`, then two flags zeroing the result through `slt`+`movz` and `movn`. Frame wall: retail is 0x30 with the buffers at 0x00/0x10, every spelling here lands on 0x40 (TI128 copies 37 diffs, struct assignment and `memcpy(v,a0,16)` 212 bytes, s32 buffers the same) |
| [`func_L04_0033D0C0`](levels/func_L04_0033D0C0.c) | 148 | Decoded (16-byte copy of a0+0x10, two `*100.0f` calls on the same object float, then `func_L04_0033CC00(a0,a1,v,v+0x10,1.0f)`). With the source pointer kept as a value the body is 144 bytes - cc1 folds the address into `lq $v0,0x10($s0)` where retail keeps `addiu $v1,$s0,0x10` - and the frame is 0x40 against retail's 0x50 (f20 placed right after $ra), which shifts every stack offset: 32 diffs |
| [`func_L04_0032F340`](levels/func_L04_0032F340.c) | 128 | Not C: COP2/VU0 - eight `ldc2` of two 0x40-byte blocks into $vf2-$vf9, a run of macro-mode words (`0x4BE111BF`, `0x4BE13088`, `0x4BE119BF`, `0x4BE138C8`, ...), four `sdc2`, and a 23-iteration `addi $t2,$t2,-1` / `bnez` counter |
| [`func_L04_003A0C40`](levels/func_L04_003A0C40.c) | 164 | Not C under the Plain C rule: every store goes through the VU0 window (`lui $v1,0x7000` + `ori $v1,$v1,0x2000`), with a `mult $v0,$a3` stride of 0x60 and four-float table reads out of the 0x1B0000 block |
| [`func_L00_002D6418`](levels/func_L00_002D6418.c) | 160 | 27 places, decoded in full: a 0/1 decision chain (a guard on 0x001A7908, the `$gp` word at -0x60F0 against 2 and 6, a pair of guards on 0x001B8E10/0x00190086, then four fields of the 0x00189E20 block and a four-value compare on +0x2294). Three spellings (an `sda` name with an absolute `_1` alias for the block, the block inline, a base local) give 168/168/172 bytes against 160 - the branch layout differs first. Note the `beql` at +0x28 carries the body's `lui $v0,0x19` base setup in its delay slot |
| [`func_L11_003973F8`](levels/func_L11_003973F8.c) | 176 | 160 of 176, short by exactly four `addiu`: `mode(TI)` does reach `lq`/`sq` here, but this cc1 folds the +0x10/+0xF0 into the `lq`/`sq` offsets where retail materialises `$a0=s0+0x10`, `$v1=s1+0x10`, `$a1=s0+0xF0`, `$v1=s1+0xF0` and uses offset 0. The float registers also come out swapped (`$f21`=f13 against retail's `$f21`=f12) |
| [`func_L11_0030ED58`](levels/func_L11_0030ED58.c) | 156 | Guard and band structure recovered - state word 11 at 0x001A79F0, +0x30 == 2 in the block at 0x001BE5C0, then `id` classified into [0x1B9,0x26B]→0x46, [0x321,0x3B5]→0x47, `<= 0x44B`→0x48, else -1, then `func_2FF678($gp string, which, 2, 0x80808080, 0)` - but the C is a transcription of three literal bands with no derivation in the bytes |
| [`func_L11_00397A30`](levels/func_L11_00397A30.c) | 184 | Not C: `.word 0x700014A9`, the MMI word the func_L00_003A8E28 near miss documents, and it is the first instruction after the prologue. ASM_FUNC candidate |
| [`func_L00_002FC058`](levels/func_L00_002FC058.c) | 184 | 27 shared places, decoded in full. Exact retail size and retail's instruction sequence, blocked on the dispatch's block layout: retail branches forward to the case-1 block at +0x34 and falls through the tree, this cc1 keeps case 1 as the fall-through with `bne` inverted (+0xc is the only difference reported, all 27 places). Three reusable measurements: the three `$gp` globals need SIZED `sda` declarations (a plain `extern int` gives `lui`/%lo per site), both conditions branch on `!= 0` (`func_L00_002FC018() != 0` selects the two-call arm, `ref_.._02 != 0` the early-clear arm), and `-O2 -G8 -ffunction-sections` is what yields `sw $v0,-0x5C80($gp)`. |
| [`func_L08_00424F40`](levels/func_L08_00424F40.c) | 184 | Exact retail size, 45 of 46 instructions byte-identical - the 0x21 guard, both stack-buffer calls, the `while` shape with its `beql`/`bnel` annulled branches and the `lw $a0,0x68($s0)` reload in the loop-back delay slot. One word left: the loop test's load is `lw $v1,0x0($sp)` in retail (the same `$v1` both times round) and `lw $v0` here. Seven loop spellings measured give the same word. |
| [`func_L08_00425568`](levels/func_L08_00425568.c) | 192 | Plain `if (byte31 != 0) store 0; else counter++` gives retail's instruction set but a `beqz`+`b` where retail has `bnel` with the store in the delay slot, so retail's source must hold BOTH statements under `if (byte31 == 0)` and the `= 0` store really is dead (`counter = 0` then `+ 1`). This cc1 forwards the store into the load and folds the pair (184 bytes, form 1); a distinct pointer, `r = s1`, or a local copy do not block it (cc1 CSEs them), and marking only the LOAD `volatile` blocks the fold and reaches 192 bytes but costs the annulled branch (form 2). With the fold blocked the residue is allocation: the 0x68 load's hoist into the guard's delay slot, the 0x20 byte in `$v0` against retail's `$v1`, and 2.0f in `$f0` against `$f2`. |
| [`func_L08_00429350`](levels/func_L08_00429350.c) | 184 | Exact retail size; the three-slot walk, the `bnel` delay-slot counter, the four pointer bumps at the loop bottom and the found-slot release arm all match. Blocked on the prologue: retail materialises 0x1B39A0/0x1B39E0/0x1B39F0/0x1B3A30 in that order into `$a2`/`$s1`/`$s2`/`$v1`, each `lui`/`addiu` interleaved with one `sd`, and keeps the walk base in `$v1`; this cc1 groups them differently. Three spellings measured, same interleave - the wall the sibling func_L08_00429408 near miss records for the same data family. |
| [`func_L08_004294A0`](levels/func_L08_004294A0.c) | 192 | 188 bytes; the first 8 words and the whole body are retail's - three-slot walk, `bnel` delay-slot counter, 1/4 -> 2/3 state rewrite, `jal` with 0x40, and a 16-byte `lq`/`sq` copy at offset 0 on both sides (`mode(TI)` reaches it). Allocation cascades: retail puts the slot counter in `$a3` and the 0x1B3A30 base in `$a2`, this cc1 the other way (`i` in `$a2`, walk base in `$a1`), which shifts the four state constants down one register each and re-interleaves the base materialisations. Six spellings measured; `-mno-split-addresses` moves it further away (+0x4). A bare 0x002603E0 pointer cast emits `lui`/`ori` - the symbol spelling is what gives `addiu`. |
| [`func_L12_0041A460`](levels/func_L12_0041A460.c) | 204 | 168 bytes; the 0x110 table index, the 0x224 offset scaled by 16, the two position differences, both calls and the `& 0x8F` / 0x224-vs-0x220 tail are all decoded and reproduced. Blocked on the 16-byte copy the frame carries: retail reserves 0x30 (0x20 here) and runs `addiu $v1,$v1,0x10 ; lq $at,0x0($v1) ; sq $v0,0x0($sp)` with nothing reading `$sp+0` afterwards; an unused `TI128` local is dropped by this cc1. 0x001FEB40 must be spelled as a SYMBOL - a literal emits `ori` where retail has `lui 0x20 ; addiu -0x1540`. |
| [`func_L20_0044AB68`](levels/func_L20_0044AB68.c) | 204 | Not C with this cc1: the third instruction is `.word 0x700014A9` (opcode 0x1C, the MMI window), the same word the func_L11_00397A30 and func_L00_003A8E28 near misses document, and the body carries a quadword `sq $v0,0x20($sp)` beside the `addiu $a0,$sp,0x20` feeding the first call. ASM_FUNC candidate; the rest (0.3f at $sp+0x28, the three calls, the 0x1B8 release) is decoded in the entry. |
| [`func_L05_00397F48`](levels/func_L05_00397F48.c) | 208 | Exact retail size and retail's words through +0x40, including the three expected blockers now SOLVED: the four `$gp` floats come out bare (`lwc1 $f0,0x0($gp)` + GPREL16) under `-O2 -G8 -ffunction-sections -mno-split-addresses` (needs its own file - the unit is the -G0 default), the 16-byte copy IS `lq $v0,0x0($s0)`+`sq $v0,0x0($sp)` via `mode(TI)` at offset 0 both sides, and 0x45AFDF66 encodes correctly from the decimal 5627.9248046875f. Left: this cc1 hoists the `lq` above the f1C/0x20 stores where retail keeps that order with `sq` in the `jal` slot. Three placements measured, all +0x40. The constant is 0x810000, not 0x81. |

## Level functions, wave 7 agent lvl7-02 (2026-10-16)

| [`func_L20_004499E8`](levels/func_L20_004499E8.c) | 140 | 132 bytes: every instruction is present in retail's order and the eight missing bytes are exactly the two `addiu` retail pays for the TI128 destinations, which this cc1 folds into `sq $zero,0x50($t0)` and `sq $v0,0x10($a3)` (the func_L11_003973F8 wall - a named `TI128 *`, `volatile` destinations and `__builtin_memcpy(...,16)` all measured, the last 200 bytes). Allocation also differs (`arg0` copied into `$a3`, the state in `$t0`). Carries a new measured habit: an `s32` field stored from `(s32)(float)` keeps the value in the FP register and comes out `cvt.w.s` + `swc1`, where a `float` field with a `(float)(int)` cast round-trips through `mfc1`/`mtc1` (148 bytes) |
| [`func_L20_00444088`](levels/func_L20_00444088.c) | 164 | Retail's exact size and every word, with one pair swapped at +0x2c: retail sets `$a3 = $sp+0x20` before `$a2 = $s0`, this cc1 the other way round - the same wall recorded on func_L20_00443D58 next door. A named `float *pf = &f` for the fourth argument costs a frame slot and an `$s3` (176 bytes) |
| [`func_L20_0043F460`](levels/func_L20_0043F460.c) | 176 | 184 bytes; the shape is recovered (a 210000.0f-scaled two-float helper, a `== 0xB5A` arm choosing between the `$gp` word `D_L20_001B1FC0` and three words retail materialises with one `lui`+`lw` each, so `nosda`, then a six-argument call whose fifth argument rides in `$t0`). A `s32[3]` spelling reaches the absolutes through one shared base and costs two more words (204); the residue is the selector's delay slot (retail carries the `$gp` load there) and the `pair[2]` stores retail keeps on the stack |
| [`func_L20_004424A0`](levels/func_L20_004424A0.c) | 176 | 168 bytes; calls and both constant blocks are in retail's order. Three residues: `$s0`/`$s1` hold the object and the state the other way round, the `*(f32*)(state+0x7C)` term is CSE'd with the store just made (retail reloads it for `add.s $f12,$f12,$f12`), and `*(TI128 *)(state + 0xF0) = 0` folds instead of materialising `addiu $v0,$s0,0xF0` (the func_L11_003973F8 wall again). Taking the 0.000277777785f product before the second call moves it into `$f20` and costs a frame slot |
| [`func_L20_00444B48`](levels/func_L20_00444B48.c) | 208 | 204 bytes, every instruction retail's: the guards convert the two integer parameters inline (`D_L20_001B20A0 <= (f32)a0` -> f12, `(f32)a1 <= D_L20_001B20A0` -> f13) while the blend calls the int-to-float helper `func_00283CE0` three times (twice on a0, once on a1, in the order the inline denominator evaluates). Two residues: retail's `nop` between the third `sub.s` and the `div.s`, and one set-up swapped at +0x8C (`$s1 = $a1` in the bc1f delay slot against `mov.s $f23,$f13` four words earlier). Locals for the quotient's operands give 200 bytes; `(f32)` casts instead of the three calls let this cc1 fold them (104 bytes) |

## Level functions, wave 7 agent lvl7-02, tour 2 (2026-10-16)

| [`func_L20_00444138`](levels/func_L20_00444138.c) | 156 | 152 bytes; the 0x100-stride object walk is recovered from the matched `L01_003CAFE0-g8.c` / `L04_00422A00-g8.c` recipe and body and tail match word for word (the `bnel` carries the 0xAA halfword in its delay slot, the id arm is the `(u16)(id - 0x1233) < 2 \|\| id == 0x1231 \|\| id == 0x1232` chain). The missing word is retail's fresh `lui $v0,0x1B; lw $v0,0x2FA0($v0)` re-materialisation of the re-read bound on the storing path, where this cc1 keeps the %hi in a caller-saved register; retail also keeps a `nop` after the loop head's `lhu`, and its two bound reloads carry two addressing forms (`$gp` in the bne delay slot, absolute on the storing path) - a second `sda` name reproduces the `$gp` form but turns the `bne` into a `bnel`, and declaring the bound `sda` outright gives 144 bytes with the arm inlined before the tail |

| [`func_L18_00432D60`](levels/func_L18_00432D60.c) | 196 | 244 bytes; same walk family as func_L20_00444138 (pre-loop `a0[0x21] == 0xFF` guard, `p = table[a0[0x21]]`, a test that re-reads the key each pass with the nested `p == table[a0[0x21]]` continue, body `base + ((*p & 0x7FFF) << 8)` then `func_L18_00432AD0`). Wall is allocation, not shape: retail keeps only the table's %hi in `$s2` and re-materialises `addiu $v1,$s2,-0x4CE0` per site in a 0x20 frame, this cc1 keeps the whole base live in `$s4` (plus `$s3`) and lands on 0x30, shifting every word. The `break`-in-body spelling and a `$gp` bound are identical (244) |
## Level functions, wave 7 agent lvl7-03 (2026-10-16)

| [`func_L05_004222D8`](levels/func_L05_004222D8.c) | 164 | Candidate is 172 bytes; the clamp-and-publish body is decoded in full and needs the `-G8` sibling (level 5's `$gp` base is 0x1AEFF0, so `lw $v0,0x2360($gp)` is `D_L05_001B1350` with the `sda` attribute while 0x1B1354/58/5C stay absolute). Retail materialises `lui $v0,0x14; addiu $v0,$v0,-0x6A48` last, this cc1 hoists the same pair into `$a2` at the top and then rematerialises a dead `lw $v0,0x0($gp)` into two clamp delay slots - the two extra words. Nine shapes give 172/176 |

## Level functions, wave 7 agent lvl7-10 (2026-10-16)

| [`func_L00_004195C8`](levels/func_L00_004195C8.c) | - | Not a C row: the 8-byte opener `addiu $sp,$sp,0x190; nop` is a stack restore, the same in all six places (00, 03, 06, 09, 14, 18), and the real 148-byte body starts at 0x004195D0. Body recovered in the file |
| [`func_L22_003FBF00`](levels/func_L22_003FBF00.c) | 204 | 208 bytes: one instruction over, at the join where retail reloads the fallback float with the bare `lwc1 $f12,0x1C60($gp)` and this cc1 rematerialises it as `lui $at` + `lwc1`. Everything else is in retail's order, including the v/w/x declaration order for the sp+0x00/0x10/0x20 scratch vectors and the two-step slot base (`q = handle + a2*4` then `*(int *)(q + 0x60)` - folded it grows to 216 and shifts the base). `volatile`, a named `f32` local and two names for the resident float all give the same 208 |
| [`func_L22_0040DD40`](levels/func_L22_0040DD40.c) | 192 | Exactly 192 bytes and every instruction present; only the 16-byte copy's schedule differs - this cc1 folds the +0x10 into the `lq` (`lq $v0,0x10($s0)`) and sinks the `sq` into the `jal` delay slot where retail materialises `addiu $a0,$s0,0x10` first and keeps the pair adjacent. The TI128 destination wall the lead reconfirmed; seven spellings measured (named source pointer, `+ 0`, two names, TI-typed local, volatile source, volatile destination, copy as a comma expression inside the argument list) all land on it. The three-label switch is required - two cases make cc1 build a comparison chain instead of the tree |

## Level functions, wave 7 agent lvl7-11 (2026-10-16)

| [`func_L00_0040B2C0`](levels/func_L00_0040B2C0.c) | 112 | 27 places. ABANDON, the `func_L00_003ACC88` wall: the body is that twin instruction for instruction (only the `$gp` offset of the float constant differs). Retail materialises the 128-bit zero with `pcpyld $v0,$zero,$zero` and stores the register form (`sq $v0,0x10($sp)`), then copies the built quadword with `lq $v0,0x10($sp)` / `sq $v0,0x0($sp)`; `rac2_fold_zero_ti_store` folds the zero store to `sq $zero` because the register is dead afterwards, so no source reaches the register form under the pinned chain |
| [`func_L04_00455418`](levels/func_L04_00455418.c) | 220 | 8 places. Nearest: 208 bytes. Wraps the float argument into the `[a0+0x10, a0+0x14)` band with the `a0+0xE4` period, then walks three thresholds (`func_L04_004554F8((v-d)/d)`, `1.0f`, `1.0f - func_L04_004554F8((v-e)/*(a0+0x1C))`). Two measured gaps: the two FPR hazard `nop`s retail carries between the second `lwc1` and the `div.s`, and the annulled `bc1fl` slot that holds `sub.s` in retail where the `*(a0+0x18)` load is hoisted here. Four spellings give 208 |
| [`func_L10_004184F0`](levels/func_L10_004184F0.c) | 140 | Exact size, 6 words off (first at +0x40). Retail materialises the two byte constants as `$a1 = 1` then `$a0 = 0xFF` and stores 0x20/0x30/0x32/0x34 in ascending order; this cc1 always gives `$a0 = 1` / `$a1 = 0xFF` and schedules the 0x34 read-modify-write before both byte stores. Four source orders identical |
| [`func_L22_00385F40`](levels/func_L22_00385F40.c) | 220 | ONE differing word at +0xac. Retail emits the second `func_362E98` argument's `daddu $a1,$zero,$zero` one slot before the `lui`/`sb` of the `D_L22_001A7A78` store; this cc1 emits it after. Three spellings (named zero, one named byte for store and argument, `found == 0 && ...`) give the same word; everything else, registers included, is retail's |
| [`func_L14_003C9658`](levels/func_L14_003C9658.c) | 136 | Nearest: 132 bytes. cc1 folds the +0x340 into the `sq` (`sq $v0,0x340($s1)`) where retail materialises `addiu $a0,$s1,0x340` and stores at offset 0 - the wave-6 TI128 wall; the `func_420C68` argument setup also lands in a different order (retail a0, a1, a2) |
| [`func_L07_003F6C40`](levels/func_L07_003F6C40.c) | 172 | Nearest: 168 bytes. Both TI128 stores fold their constant offsets (`sq $v0,0x10($s0)`, `sq $zero,0x50($s1)`) where retail materialises `$a0 = s0+0x10` and `$v1 = s1+0x50` and stores at offset 0 - same wall as `func_L11_003973F8`. Retail reaches 0x001B0FD8 through `$gp` and 0x001B0FDC absolutely, so the body belongs in the `-g8` sibling |
| [`func_L14_003E50B8`](levels/func_L14_003E50B8.c) | 212 | Nearest: 208 bytes. The s16 ring walk (mod 8 over `*(s16 *)(a1+0x16)` until the limit at `a1+0x14` is met, then `func_321F58(a0, 2, -1)` on an odd index) is exact; only the temporaries differ (this cc1: `$a2`/`$a3`/`$a0` and `$t0` for the parameter; retail: `$a1`/`$a2`/`$a0` and `$a3`). Five spellings identical |
| [`func_L22_003FD140`](levels/func_L22_003FD140.c) | 168 | Not a C row: catalogue defect. The first four words are `lwc1 $f0,0x44($v0)` (a `$v0` the body never sets) plus three `nop`s; the real prologue `addiu $sp,$sp,-0x70` opens at 0x003FD150 and the epilogue closes at 0x003FD1E7, so the entry should be 0x003FD150 with a size of 152. Decoded body noted for the maintainers |


| [`func_L00_00300378`](levels/func_L00_00300378.c) | 192 | Size EXACT, 27 places, decoded in full (three header words select three pointer blocks; each non-zero block's base is published - 0x001BF200, 0x001B1AC0, 0x001B1AC4 - and added to its 16-byte-strided pointer fields at +0x1C, `count` re-read every iteration). Needs the -G8 sibling with a `config/cflags.txt` line `L00_002FC7A0-g8.c -O2 -G8 -ffunction-sections -mno-split-addresses`: that flag is what turns the two blanket zeros into retail's `lui $at; sw %lo($at)` (the two matched neighbours still measure 27/27 under it). One register left - retail computes the block base into `$v1` and copies `daddu $a1,$v1`, this cc1 goes straight into `$a1`, so the `$gp` store cannot land in the `blez` delay slot |

## Level functions, wave 7 agent lvl7-06 (2026-10-16)









| [`func_L01_00324550`](levels/func_L01_00324550.c) | 132 | Variadic register-save prologue no C text asked for: `$a1-$a3`/`$t0-$t3` to 0x268..0x298 and `$f12`/`$f14`/`$f16`/`$f18` to 0x258..0x264 on a 0x2A0 frame, plus `$s0` held in a 128-bit slot (`sq $s0,0x210($sp)`, read back with `lq $s0,0x210($sp)`) although it is an ordinary 64-bit value. Body understood - `func_2E99D0(a0)` picks between the `func_324410`/`func_2ECB20` pair and `func_2A6CF0(0x001AA590,a0)`. No C measured; ASM_FUNC candidate |
| [`func_L03_002ADC00`](levels/func_L03_002ADC00.c) | 156 | Not C: `.word 0x700014A9` at +0x4C, immediately before the `sq $v0,0x0($sp)` - the same MMI word the func_L11_00397A30 near miss records. Rest decoded (`D_L03_00189E20`, a 2.0f vector built on the stack at sp+0x10) |
| [`func_L10_00425888`](levels/func_L10_00425888.c) | 164 | Clamp shape and the whole tail are exact (`lw`/`slti`/`sw` on four objects, `lbu` copies into the object at +0x66..+0x69). Residue: 3 words this cc1 puts a duplicate load of the NEXT clamp into the branch delay slots where retail fills them from the fall-through constant. Ruler gives 176 bytes, and 160 for the `u8` spelling kept as a form. Also settles the `$gp` recipe: the row needs a -G8 unit, and only the one object carrying `sda` goes `$gp` while plain externs stay absolute |
| [`func_L08_00422850`](levels/func_L08_00422850.c) | 172 | `max.s` / `min.s` (0x4600A028 fd=$f0 fs=$f20, 0x460C7829 fd=$f0 fs=$f15 ft=$f12), the word class the sixth wave measured as unemittable from C. ASM_FUNC candidate |
| [`func_L02_003CD5B0`](levels/func_L02_003CD5B0.c) | 184 | Id-list family (skeleton transcribed from the matched L01_003CAFE0-g8.c). First C attempt measured 152 bytes against 184: the loop also carries an `a2 == 0xFF` re-test inside the body and two always-taken dead guards (`bnez $a3` with $a3 pinned to 0, `beqz $a1` right after the cursor increment), so the original is a hand-rolled `for (;;)` with explicit breaks rather than the do-while |
| [`func_L02_003D18D8`](levels/func_L02_003D18D8.c) | 192 | Same id-list family and same base table; predicate chain decoded (reject +0x20 == 0xFE then 0xFD, require +0xAA == 0x9CA and +0x95 == 6, answer 1 on +0x2F6 == 1). First attempt measured 184 bytes: this cc1 reloads where retail rematerialises `sll $a1,16` into every delay slot and leans on the annulled `beql` leaving the `lbu` in `$v0` for the next compare |
| [`func_L10_0042E4D0`](levels/func_L10_0042E4D0.c) | 192 | Three-arm selector, each arm copying a 128-bit object (`lq $v0,0x0($v1)`/`sq $v0,0x0($sp)`, `lq $v0,0x0($sp)`/`sq $zero,0x0($s0)`) and calling func_308E10 with 0x3F4CCCCD / 0x3E888889 / 0x3D088889. Blocked by the TI128 destination-materialisation wall worker 02 reconfirmed (retail's `addiu $v1,$v1,0xC0` and `addiu $v1,$v1,-0x500` around the pairs) |
| [`func_L00_00427620`](levels/func_L00_00427620.c) | 200 | CATALOGUE DEFECT, not a C question: the row opens on `sb $a0,0x88($v1)` with `$v1` never set and a `nop`, and the real body starts 8 bytes later at 0x00427628. All 26 places need the same +8 shift |
| [`func_L08_00422900`](levels/func_L08_00422900.c) | 200 | One word from its neighbour's wall: 0x46010028 = `max.s $f0,$f0,$f1`, plus a 128-bit copy to the stack and a `c.olt.s` + `bc1tl` clamp of +0x1B0 against +0x1D4. ASM_FUNC candidate |
| [`func_L02_003D1ED0`](levels/func_L02_003D1ED0.c) | 208 | Nine-argument call (func_32B468) after a stack vector build through func_31EA58 with 0.1f / 0.1666667f; blocked by the same TI128 fold - retail's `sq $v0,0x10($sp)` has a constant 0x10 displacement this cc1 folds |
| [`func_L00_004282F0`](levels/func_L00_004282F0.c) | 216 | 27 places, everything reproduces retail except the `*(TI128 *)(p+0xB0) = *(TI128 *)(p+0x80)` pair: retail materialises both pointers (`addiu $a0,$s0,0xB0`, `addiu $v1,$s0,0x80`) and copies at offset 0, this cc1 folds both displacements into the lq/sq (measured 208 bytes; pointer locals did not stop it). Two fixes measured on the way: the three `$gp` arguments need SIZE on the objects (an incomplete `extern u8 D[]` costs a `lui` each) and 0x001B8F10 is the literal case |


| [`func_L00_00419A80`](levels/func_L00_00419A80.c) | - | Not a C row: the 16-byte opener is two stack restores (`addiu $sp,$sp,0x50; nop; addiu $sp,$sp,0x30; nop`), the same in both places (00, 14), and the real 196-byte body starts at 0x00419A90. Recovered body in the file, including the per-place `$gp` float (0x2454 / 0x2B1C) |
| [`func_L01_00416B30`](levels/func_L01_00416B30.c) | 212 | Exactly 212 bytes, every constant right (`-2.25f` is 0xC0100000, not -2.5f), but the prologue's saved pair is swapped: retail keeps the loop counter in `$s2` and the handle in `$s1`, this cc1 the other way, so `sd $s1/$s2` and the whole body shift from +0x8. Counter-first, handle-first and both for-header spellings do not move the pair, and all 24 permutations of the four tail stores (lead lever 1, one ruler call) still fail first at +0x8 - the pair masks the stores |
| [`func_L01_00416A50`](levels/func_L01_00416A50.c) | 224 | **The `$gp` float wall is broken: 232 bytes with the -g8 sibling plus `-mno-split-addresses`** (lead lever 2), 8 bytes from retail. 248 at -G0, 252 at -G8 alone; the extra flag gives all four `$gp` floats the bare one-instruction `lwc1 $f1,0x2A70($gp)` form, i.e. a `config/cflags.txt` line `-O2 -G8 -ffunction-sections -mno-split-addresses`. This contradicts the earlier "the sda spelling changes nothing for loads" measurement for this unit. The 8 bytes left: a 1-ULP decimal for 0x3998825C, and a `mov.s $f1,$f0` plus one redundant `swc1 $f0,0xF8` where retail stores `$f0` twice |



## Level functions, wave 7 agent lvl7-03, tour 2 (2026-10-16)

| [`func_L20_0039E0A0`](levels/func_L20_0039E0A0.c) | 184 | Candidate is 156 bytes; the list-walk family body is decoded in full (0x001FBB20 slot table, bound 0x001B2FB8, base 0x001B2F9C, counts the entries whose class byte `e[0x20]` is not 6, wrap-around tail `while (!((s16)p[-1] < 0 && p != list))`). All 7 missing words are THREE guards retail keeps and this cc1 proves dead away: `beq $a2,$t2` on the `(arg & 0xFF) == 0xFF` slot, `bnez $t0` on a register retail materialises as 0 and never writes, and `beqz $a1` on a pointer retail proved non-null. `e` must be `unsigned char *` for retail's `lbu` |
| [`func_L30_003848B8`](levels/func_L30_003848B8.c) | 196 | **Size EXACT (196)**, level 30, decoded in full (0x001A63A8 display state, 0x00295C60 class table, `$gp` bitmask 0x001AEC90 = 0x1AEFF0-0x360, needs the -G8 sibling). The size only lands with the negation spelled `(((D >> cls) & 1) ^ 1) != 0`: that `^ 1` is what makes cc1 emit retail's `srav; xori; andi; beqz` - `!(D & (1 << cls))` drops the `xori` and gives 192. Left: layout - retail sends all three guards to ONE return-0 block placed AFTER the body (`+0x24` branches) while this cc1 puts it right after the guard chain (`+0x7`), shifting everything from +0x10, and retail keeps only the HIGH half of 0x001A63A8 in `$t1`, rebuilding it at the tail. Nested-guard and early-return spellings give the same 196 |


## Level functions, wave 7 agent lvl7-06, tour 2 (2026-10-16)




| [`func_L00_004290C0`](levels/func_L00_004290C0.c) | 220 | The whole arm structure is exact - four `obj+0xC/0x10/8/0x14` fields in a nested `>= 0` test whose annulled `bltzl` slots carry the next field's load. Measured 208-212 bytes against 220 for four spellings: retail keeps the shared `r != 0` normalisation in its own block after the arms and every arm branches there with `b` + `nop`, while this cc1 sinks it into each arm's delay slot as `sltu` and branches straight to the epilogue. The two constants (0x00189EA0, the 0x001FDA80 table) must be SYMBOLS: as literals cc1 emits `lui 0x18`/`ori` because bit 15 of the low half is set |
| [`func_L10_00424448`](levels/func_L10_00424448.c) | 224 | Decoded, not attempted: one-shot init behind the `$gp` word at 0x001B13A4 (`sw $zero,0x00221CFC`, then func_342218/func_37CFD8/func_37BD00/func_37BCA0/func_37BCB0), a countdown loop calling func_37D048 on `+0xAA == 0xF8E` entries, then func_425798, func_425410(0x32,0xC), func_37BCB8(0xF8E), func_37D168(v0) and, only when that answers zero, func_423DC8(a0,2) |
| [`func_L02_003D25C8`](levels/func_L02_003D25C8.c) | 228 | Two INVALID words plus a max.s/min.s class word (`4600xxxx`, funct 0x28/0x29) - the word class the sixth wave measured as unemittable from C. ASM_FUNC candidate, same wall as func_L08_00422850 |
| [`func_L08_003F6D30`](levels/func_L08_003F6D30.c) | 228 | Four `lq`/`sq` pairs with the pointers materialised first (`addiu $a3,$a0,0xF0`, `addiu $a2,$s0,0xF0`, `addiu $v1,$a0,0x10`, `addiu $s0,$s0,0x10`) - the shape that in principle reaches `mode(TI)` - but func_L00_004282F0 measured that this cc1 folds the constant displacements anyway. Rest is plain: `0x131D` id walk, a `div.s` of `*(f32*)(s0+0x2C)`, func_31FC78, func_31D540 guarded by a -1 sentinel |
| [`func_L00_00428F00`](levels/func_L00_00428F00.c) | 232 | 27 places, same TI128 wall: four `lq`/`sq` pairs taken out of D_L00_001C8C80 (0x7A420000/0x7C620000, 0x7A220000/0x7C820000, 0x78620000/0x7CA20000 twice), each with a materialised pointer at offset 0. The `0x2294` state dispatch and the tail (func_428588, the 0x2D store, func_428EB0, func_2DA1E0(s1+0x30, 0.5f)) are readable; only the quad copies are blocked |
| [`func_L01_003209F8`](levels/func_L01_003209F8.c) | 240 | Decoded: `movn $s2,$a2,$v1` with `$v1 = nor $zero,$a2` conditional id replace, then the walk over 0x001BC080+0x18C comparing `+0xAA` against `$s2` and the byte at 0x001B1E61, calling func_307048(entry, s4, 0x001B1E60) and storing f20 to 0x001B1E80/84/88 with one `lui $at,0x1B` each. No known wall; not attempted. Shares its preamble shape with func_L01_00394148 |
| [`func_L02_003CD668`](levels/func_L02_003CD668.c) | 240 | Two INVALID words, five calls, no max.s/min.s. Same L02_002ED7B0.c unit as the two id-list rows deposited this wave - check first whether the INVALID words are `lq`/`sq` (then the L01_003CAFE0-g8.c recipe applies) or real MMI |
| [`func_L10_00425798`](levels/func_L10_00425798.c) | 240 | Size EXACT at 240 and the body byte-identical: walk the +0x28 chain from 0x001B2528, nine `+0xAA` ids (0x1315 inline, the other eight hoisted into $s1-$s7/$fp in exactly retail's order), func_32AF78 on a hit, the advance in the annulled `bnel` delay slot. Only the 12-word prologue is permuted - retail saves `$s0` first then loads the head into `$s0`, this cc1 saves the other seven first and loads through a scratch register. Five spellings measured, all the same permutation |
| [`func_L02_003D2390`](levels/func_L02_003D2390.c) | 244 | Two INVALID words, seven calls - the busiest L02 row of this tour. Same triage as func_L02_003CD668 |
| [`func_L01_00394148`](levels/func_L01_00394148.c) | 248 | 4 places; size EXACT at 248 for four spellings and the loop byte-identical, including the annulled `bnel` slots that carry the re-read count. Residue is the preamble: retail materialises the 0x001BD1C0 base into `$s2` only after the 0x001A8F40 load and builds the cursor with one `addiu $s1,$s2,0x18C`, this cc1 loads the state into `$a0` instead of `$v1` and builds the cursor through a second register. Same prologue-interleave wall as func_L10_00425798. The base MUST be a symbol - as a literal cc1 folds `base + 0x18C` into a constant and the cursor disappears |
| [`func_L03_003C4368`](levels/func_L03_003C4368.c) | 252 | Decoded; the row to try the `$gp`-float finding on. The two float arguments come straight from `$gp` (0xF18(gp) and 0xF14(gp)) - exactly the case worker 10 measured as needing `-g8` PLUS `-mno-split-addresses` on the unit's cflags line to come out bare instead of `lui $at` + `lwc1`. Rest: 0x20 state is 4 or 2, the `+0x198`/func_322158 guard with an early return on `0x3ADA740F < r`, func_2EEFE0(sp, a0+0x10, a1), then func_2EF298 twice-doubled float and func_2EEFB0/func_3217B8 on `s1+0x1B0` |
| [`func_L08_003F76C0`](levels/func_L08_003F76C0.c) | 252 | Five `$gp` accesses, four calls, no INVALID word and no max.s/min.s - so it belongs in the L08_003D8348-g8.c sibling with sized `sda` declarations (see func_L10_00425888 and func_L00_004282F0 for the measured rules). Nothing else looks like a wall; not attempted |
| [`func_L09_003F9970`](levels/func_L09_003F9970.c) | 256 | Four INVALID words, five calls, no max.s/min.s, no `$gp` - the biggest INVALID count of this tour. Triage as the L02 rows: `lq`/`sq` (reachable with `mode(TI)`) or the `0x7000_xxxx` MMI family (needs a compiler patch) |
| [`func_L03_003C4468`](levels/func_L03_003C4468.c) | 268 | Clean row: no INVALID word, no max.s/min.s, no `$gp`, six calls, in the same L03_002AB5C8.c unit as func_L03_003C4368. Most promising untouched row of the tour; not attempted |



| [`func_L00_003BFF68`](levels/func_L00_003BFF68.c) | 200 | Tour 2, the closest row: exact 200 bytes (a `char scratch[0x50]` reproduces retail's whole frame - the 16-byte copy at +0x00, the float at +8, the pair output at +0x10, the zeroed word at +0x40), every instruction present, first difference at +0x4 is the prologue save order. Retail emits the callee-saved saves in liveness order (s1, s2, f20, s0, ra) with the two register moves interleaved; this cc1 groups them in ascending register number (s0, s1, s2, ra, f20) then runs the moves, which also displaces the `a1 = s2+0xB4` and `swc1 $f0,0x8($sp)` delay-slot fills. `-mno-split-addresses` gives identical bytes. The 16-byte copies are offset-0 `lq`/`sq` (the case the TI128 destination wall does not block) and the last statement subtracts the TWO `func_00282C48` calls, not one value twice |
| [`func_L05_0041C708`](levels/func_L05_0041C708.c) | 216 | Tour 2: the prologue matches retail word for word through +0x2C (this row does not hit the save-order wall). Two measured causes from +0x30: the guard must be an enclosing `if (s0 != 0) { ... } return s0;` - written as an early return this cc1 inverts the branch and adds a copy because it can see the value is zero; and the three-iteration copy loop comes out REVERSED (`i = 2; i >= 0; i--`, `bgez`) against retail's upward `slti $v0,$a0,0x3; bnez`. Four source orders of the five flag stores all give the same emitted order 0x30, 0x32, 0x31, 0x34, 0x20, so the permutation is not the fault |




















## Additional source forms

Keep a primary attempt at `levels/<catalogue-name>.c`. Distinct alternatives live
at `levels/alternatives/<catalogue-name>-form-NN.c`, with the catalogue name kept
on the function definition. Keep harnesses, generated lists and synthetic probes
in ignored local work directories.

These alternatives preserve distinct authored experiments alongside the primary
near-miss entries. They are indexed by catalogue function, never linked or
counted as matches. Their extraction is not a new compile or matching result;
read the primary entry for context and verify each form separately.

- [`func_L00_002A53B8`](levels/func_L00_002A53B8.c): [form 1](levels/alternatives/func_L00_002A53B8-form-01.c), [form 2](levels/alternatives/func_L00_002A53B8-form-02.c), [form 3](levels/alternatives/func_L00_002A53B8-form-03.c), [form 4](levels/alternatives/func_L00_002A53B8-form-04.c), [form 5](levels/alternatives/func_L00_002A53B8-form-05.c), [form 6](levels/alternatives/func_L00_002A53B8-form-06.c), [form 7](levels/alternatives/func_L00_002A53B8-form-07.c), [form 8](levels/alternatives/func_L00_002A53B8-form-08.c), [form 9](levels/alternatives/func_L00_002A53B8-form-09.c), [form 10](levels/alternatives/func_L00_002A53B8-form-10.c), [form 11](levels/alternatives/func_L00_002A53B8-form-11.c), [form 12](levels/alternatives/func_L00_002A53B8-form-12.c), [form 13](levels/alternatives/func_L00_002A53B8-form-13.c), [form 14](levels/alternatives/func_L00_002A53B8-form-14.c).
- [`func_L00_002A5480`](levels/func_L00_002A5480.c): [form 1](levels/alternatives/func_L00_002A5480-form-01.c), [form 2](levels/alternatives/func_L00_002A5480-form-02.c), [form 3](levels/alternatives/func_L00_002A5480-form-03.c), [form 4](levels/alternatives/func_L00_002A5480-form-04.c), [form 5](levels/alternatives/func_L00_002A5480-form-05.c), [form 6](levels/alternatives/func_L00_002A5480-form-06.c), [form 7](levels/alternatives/func_L00_002A5480-form-07.c), [form 8](levels/alternatives/func_L00_002A5480-form-08.c), [form 9](levels/alternatives/func_L00_002A5480-form-09.c), [form 10](levels/alternatives/func_L00_002A5480-form-10.c).
- [`func_L00_002D23C0`](levels/func_L00_002D23C0.c): [form 1](levels/alternatives/func_L00_002D23C0-form-01.c), [form 2](levels/alternatives/func_L00_002D23C0-form-02.c), [form 3](levels/alternatives/func_L00_002D23C0-form-03.c).
- [`func_L00_002D3EA8`](levels/func_L00_002D3EA8.c): [form 1](levels/alternatives/func_L00_002D3EA8-form-01.c), [form 2](levels/alternatives/func_L00_002D3EA8-form-02.c), [form 3](levels/alternatives/func_L00_002D3EA8-form-03.c), [form 4](levels/alternatives/func_L00_002D3EA8-form-04.c).
- [`func_L00_002D3FB8`](levels/func_L00_002D3FB8.c): [form 1](levels/alternatives/func_L00_002D3FB8-form-01.c), [form 2](levels/alternatives/func_L00_002D3FB8-form-02.c), [form 3](levels/alternatives/func_L00_002D3FB8-form-03.c), [form 4](levels/alternatives/func_L00_002D3FB8-form-04.c), [form 5](levels/alternatives/func_L00_002D3FB8-form-05.c).
- [`func_L00_002D6418`](levels/func_L00_002D6418.c): [form 1](levels/alternatives/func_L00_002D6418-form-01.c), [form 2](levels/alternatives/func_L00_002D6418-form-02.c).
- [`func_L00_002D75F0`](levels/func_L00_002D75F0.c): [form 1](levels/alternatives/func_L00_002D75F0-form-01.c), [form 2](levels/alternatives/func_L00_002D75F0-form-02.c), [form 3](levels/alternatives/func_L00_002D75F0-form-03.c).
- [`func_L00_002D7D58`](levels/func_L00_002D7D58.c): [form 1](levels/alternatives/func_L00_002D7D58-form-01.c), [form 2](levels/alternatives/func_L00_002D7D58-form-02.c), [form 3](levels/alternatives/func_L00_002D7D58-form-03.c), [form 4](levels/alternatives/func_L00_002D7D58-form-04.c), [form 5](levels/alternatives/func_L00_002D7D58-form-05.c), [form 6](levels/alternatives/func_L00_002D7D58-form-06.c).
- [`func_L00_002E2048`](levels/func_L00_002E2048.c): [form 1](levels/alternatives/func_L00_002E2048-form-01.c), [form 2](levels/alternatives/func_L00_002E2048-form-02.c), [form 3](levels/alternatives/func_L00_002E2048-form-03.c), [form 4](levels/alternatives/func_L00_002E2048-form-04.c), [form 5](levels/alternatives/func_L00_002E2048-form-05.c), [form 6](levels/alternatives/func_L00_002E2048-form-06.c).
- [`func_L00_00322428`](levels/func_L00_00322428.c): [form 1](levels/alternatives/func_L00_00322428-form-01.c).
- [`func_L00_003252C8`](levels/func_L00_003252C8.c): [form 1](levels/alternatives/func_L00_003252C8-form-01.c), [form 2](levels/alternatives/func_L00_003252C8-form-02.c), [form 3](levels/alternatives/func_L00_003252C8-form-03.c).
- [`func_L00_003296C0`](levels/func_L00_003296C0.c): [form 1](levels/alternatives/func_L00_003296C0-form-01.c), [form 2](levels/alternatives/func_L00_003296C0-form-02.c).
- [`func_L00_00329FF0`](levels/func_L00_00329FF0.c): [form 1](levels/alternatives/func_L00_00329FF0-form-01.c).
- [`func_L00_0032A0B8`](levels/func_L00_0032A0B8.c): [form 1](levels/alternatives/func_L00_0032A0B8-form-01.c), [form 2](levels/alternatives/func_L00_0032A0B8-form-02.c), [form 3](levels/alternatives/func_L00_0032A0B8-form-03.c), [form 4](levels/alternatives/func_L00_0032A0B8-form-04.c), [form 5](levels/alternatives/func_L00_0032A0B8-form-05.c), [form 6](levels/alternatives/func_L00_0032A0B8-form-06.c), [form 7](levels/alternatives/func_L00_0032A0B8-form-07.c), [form 8](levels/alternatives/func_L00_0032A0B8-form-08.c), [form 9](levels/alternatives/func_L00_0032A0B8-form-09.c), [form 10](levels/alternatives/func_L00_0032A0B8-form-10.c), [form 11](levels/alternatives/func_L00_0032A0B8-form-11.c), [form 12](levels/alternatives/func_L00_0032A0B8-form-12.c), [form 13](levels/alternatives/func_L00_0032A0B8-form-13.c), [form 14](levels/alternatives/func_L00_0032A0B8-form-14.c), [form 15](levels/alternatives/func_L00_0032A0B8-form-15.c), [form 16](levels/alternatives/func_L00_0032A0B8-form-16.c).
- [`func_L00_00410430`](levels/func_L00_00410430.c): [form 1](levels/alternatives/func_L00_00410430-form-01.c), [form 2](levels/alternatives/func_L00_00410430-form-02.c).
- [`func_L01_002A7248`](levels/func_L01_002A7248.c): [form 1](levels/alternatives/func_L01_002A7248-form-01.c), [form 2](levels/alternatives/func_L01_002A7248-form-02.c), [form 3](levels/alternatives/func_L01_002A7248-form-03.c).
- [`func_L01_002B46A0`](levels/func_L01_002B46A0.c): [form 1](levels/alternatives/func_L01_002B46A0-form-01.c), [form 2](levels/alternatives/func_L01_002B46A0-form-02.c), [form 3](levels/alternatives/func_L01_002B46A0-form-03.c), [form 4](levels/alternatives/func_L01_002B46A0-form-04.c), [form 5](levels/alternatives/func_L01_002B46A0-form-05.c), [form 6](levels/alternatives/func_L01_002B46A0-form-06.c).
- [`func_L01_00323310`](levels/func_L01_00323310.c): [form 1](levels/alternatives/func_L01_00323310-form-01.c).
- [`func_L01_00367F78`](levels/func_L01_00367F78.c): [form 1](levels/alternatives/func_L01_00367F78-form-01-g8.c), [form 2](levels/alternatives/func_L01_00367F78-form-02.c), [form 3](levels/alternatives/func_L01_00367F78-form-03.c), [form 4](levels/alternatives/func_L01_00367F78-form-04.c).
- [`func_L02_003FC1B0`](levels/func_L02_003FC1B0.c): [form 1](levels/alternatives/func_L02_003FC1B0-form-01.c), [form 2](levels/alternatives/func_L02_003FC1B0-form-02.c), [form 3](levels/alternatives/func_L02_003FC1B0-form-03.c), [form 4](levels/alternatives/func_L02_003FC1B0-form-04.c), [form 5](levels/alternatives/func_L02_003FC1B0-form-05.c), [form 6](levels/alternatives/func_L02_003FC1B0-form-06.c), [form 7](levels/alternatives/func_L02_003FC1B0-form-07.c), [form 8](levels/alternatives/func_L02_003FC1B0-form-08.c).
- [`func_L02_003FC9C0`](levels/func_L02_003FC9C0.c): [form 1](levels/alternatives/func_L02_003FC9C0-form-01.c), [form 2](levels/alternatives/func_L02_003FC9C0-form-02.c), [form 3](levels/alternatives/func_L02_003FC9C0-form-03.c), [form 4](levels/alternatives/func_L02_003FC9C0-form-04.c).
- [`func_L03_0038B408`](levels/func_L03_0038B408.c): [form 1](levels/alternatives/func_L03_0038B408-form-01.c), [form 2](levels/alternatives/func_L03_0038B408-form-02.c).
- [`func_L03_003C85F8`](levels/func_L03_003C85F8.c): [form 1](levels/alternatives/func_L03_003C85F8-form-01.c), [form 2](levels/alternatives/func_L03_003C85F8-form-02.c), [form 3](levels/alternatives/func_L03_003C85F8-form-03.c).
- [`func_L04_002CC748`](levels/func_L04_002CC748.c): [form 1](levels/alternatives/func_L04_002CC748-form-01.c), [form 2](levels/alternatives/func_L04_002CC748-form-02.c).
- [`func_L04_0033C420`](levels/func_L04_0033C420.c): [form 1](levels/alternatives/func_L04_0033C420-form-01.c), [form 2](levels/alternatives/func_L04_0033C420-form-02.c), [form 3](levels/alternatives/func_L04_0033C420-form-03.c), [form 4](levels/alternatives/func_L04_0033C420-form-04.c), [form 5](levels/alternatives/func_L04_0033C420-form-05.c), [form 6](levels/alternatives/func_L04_0033C420-form-06.c), [form 7](levels/alternatives/func_L04_0033C420-form-07.c), [form 8](levels/alternatives/func_L04_0033C420-form-08.c), [form 9](levels/alternatives/func_L04_0033C420-form-09.c), [form 10](levels/alternatives/func_L04_0033C420-form-10.c), [form 11](levels/alternatives/func_L04_0033C420-form-11.c).
- [`func_L04_0033D0C0`](levels/func_L04_0033D0C0.c): [form 1](levels/alternatives/func_L04_0033D0C0-form-01.c), [form 2](levels/alternatives/func_L04_0033D0C0-form-02.c), [form 3](levels/alternatives/func_L04_0033D0C0-form-03.c).
- [`func_L04_0040ACC8`](levels/func_L04_0040ACC8.c): [form 1](levels/alternatives/func_L04_0040ACC8-form-01.c).
- [`func_L04_00415E28`](levels/func_L04_00415E28.c): [form 1](levels/alternatives/func_L04_00415E28-form-01.c), [form 2](levels/alternatives/func_L04_00415E28-form-02.c).
- [`func_L04_00415EA8`](levels/func_L04_00415EA8.c): [form 1](levels/alternatives/func_L04_00415EA8-form-01.c), [form 2](levels/alternatives/func_L04_00415EA8-form-02.c).
- [`func_L10_0037D200`](levels/func_L10_0037D200.c): [form 1](levels/alternatives/func_L10_0037D200-form-01.c), [form 2](levels/alternatives/func_L10_0037D200-form-02.c), [form 3](levels/alternatives/func_L10_0037D200-form-03.c), [form 4](levels/alternatives/func_L10_0037D200-form-04.c).
- [`func_L10_004190F0`](levels/func_L10_004190F0.c): [form 1](levels/alternatives/func_L10_004190F0-form-01.c), [form 2](levels/alternatives/func_L10_004190F0-form-02.c).
- [`func_L12_004081F8`](levels/func_L12_004081F8.c): [form 1](levels/alternatives/func_L12_004081F8-form-01.c).
- [`func_L12_0041A6D0`](levels/func_L12_0041A6D0.c): [form 1](levels/alternatives/func_L12_0041A6D0-form-01.c), [form 2](levels/alternatives/func_L12_0041A6D0-form-02.c), [form 3](levels/alternatives/func_L12_0041A6D0-form-03.c).
- [`func_L12_004254A8`](levels/func_L12_004254A8.c): [form 1](levels/alternatives/func_L12_004254A8-form-01.c), [form 2](levels/alternatives/func_L12_004254A8-form-02.c), [form 3](levels/alternatives/func_L12_004254A8-form-03.c), [form 4](levels/alternatives/func_L12_004254A8-form-04.c), [form 5](levels/alternatives/func_L12_004254A8-form-05.c), [form 6](levels/alternatives/func_L12_004254A8-form-06.c), [form 7](levels/alternatives/func_L12_004254A8-form-07.c), [form 8](levels/alternatives/func_L12_004254A8-form-08.c), [form 9](levels/alternatives/func_L12_004254A8-form-09.c), [form 10](levels/alternatives/func_L12_004254A8-form-10.c), [form 11](levels/alternatives/func_L12_004254A8-form-11.c), [form 12](levels/alternatives/func_L12_004254A8-form-12.c).
- [`func_L14_003F0E98`](levels/func_L14_003F0E98.c): [form 1](levels/alternatives/func_L14_003F0E98-form-01.c), [form 2](levels/alternatives/func_L14_003F0E98-form-02.c), [form 3](levels/alternatives/func_L14_003F0E98-form-03.c).
- [`func_L17_003E4860`](levels/func_L17_003E4860.c): [form 1](levels/alternatives/func_L17_003E4860-form-01.c).
- [`func_L18_002F1098`](levels/func_L18_002F1098.c): [form 1](levels/alternatives/func_L18_002F1098-form-01.c).
- [`func_L22_003E87B8`](levels/func_L22_003E87B8.c): [form 1](levels/alternatives/func_L22_003E87B8-form-01.c), [form 2](levels/alternatives/func_L22_003E87B8-form-02.c).
- [`func_L22_003F85A0`](levels/func_L22_003F85A0.c): [form 1](levels/alternatives/func_L22_003F85A0-form-01.c), [form 2](levels/alternatives/func_L22_003F85A0-form-02.c), [form 3](levels/alternatives/func_L22_003F85A0-form-03.c), [form 4](levels/alternatives/func_L22_003F85A0-form-04.c).
- [`func_L22_00415A48`](levels/func_L22_00415A48.c): [form 1](levels/alternatives/func_L22_00415A48-form-01.c).



















































- [`func_L10_00425888`](levels/func_L10_00425888.c): [form 1](levels/alternatives/func_L10_00425888-form-01.c).
- [`func_L00_002FC058`](levels/func_L00_002FC058.c): [form 1](levels/alternatives/func_L00_002FC058-form-01.c), [form 2](levels/alternatives/func_L00_002FC058-form-02.c).















































| [`func_L04_00423178`](levels/func_L04_00423178.c) | 224 | 7 places. ABANDON, the address-CSE wall: retail rematerialises every absolute address site by site (`lui $v1,0x1B` / `lw $v1,-0x7100($v1)`, `lui $v0,0x1A` / `lbu $v0,0x7A40($v0)`, `lui $v0,0x1B` / `lbu $v0,0x1DE1($v0)`, three `lui $at,0x1B` / `swc1 $f20,0x1E00/0x1E04/0x1E08($at)`), holding only the 0x001BD380 block in a register. This cc1 CSEs them and the frame grows 0x40 -> 0x60 with $s4-$s7/$fp. Four spellings (index walk, moving pointer, named count, `nosda` on every symbol) give 272/276/260/272 |



















































| [`func_L00_003EC078`](levels/func_L00_003EC078.c) | 200 | 27 places. Screened out, MMI class: `pcpyld $v0,$zero,$zero` at +0x1C plus `sq $v0,0x20($sp)`, the `rac2_fold_zero_ti_store` wall of func_L00_0040B2C0 / func_L00_003ACC88; the body also moves quadwords with `lq $v0,0x20($sp)` (+0x5C) / `sq $v0,0x10($sp)` (+0x64) |
| [`func_L07_003F8CD0`](levels/func_L07_003F8CD0.c) | 188 | Screened out, MMI class: `pcpyld $v0,$zero,$zero` at +0x30 plus `sq $v0,0x30($sp)`, same folded-zero wall; the rest is ordinary calls (func_0029FCC0, func_00282F20, func_L07_003F6528, func_L04_0030C4C0, func_L07_003F65E8) |
| [`func_L00_0040AFB8`](levels/func_L00_0040AFB8.c) | 224 | 27 places. Screened out, TI128 class: `lq $v1,0x10($s0)` at +0x88 and `sq $v1,0x10($sp)` at +0xB8 with two live `mult` (0x00861818, 0x00461018 - opcode 0x00 funct 0x18, alive despite rabbitizer's INVALID); the constant destination offset folds into the sq where retail materialises the pointer, so it waits for a compiler-side lever |



















































| [`func_L00_004117B8`](levels/func_L00_004117B8.c) | 236 | 27 places. ABANDON, address-CSE wall (260 bytes): walks the 30 entries of the 0x00256620 block and folds each set record into a 16-byte buffer (`func_2AFFD8(rec+0x10,buf,1)`, `func_282D30(buf,buf,*(rec+0xC) * 0.0008300250046886504f = 0x3A599609)`, `buf[0] += *(rec+0) * 0.0009764999849721789f = 0x3A7FFBCE`), then the same scale from 0x00250004/0x00250008 and `func_L00_00411F48(a0)`. Retail reuses the CSE'd `lui $v0,0x25` for the two out-of-guard loads across the calls; here the addresses land in $s3/$s4 and the frame grows 0x40 -> 0x50. `nosda` on the three symbols and the field spelling both give 260 |
| [`func_L04_00434AD0`](levels/func_L04_00434AD0.c) | 224 | 2 places. Not retried: byte-for-byte the twin of func_L04_00423178 (same block 0x001BD380, same D_001B1DE1 guard, same func_3325E0 call and 4.0f publishes), so it hits the same address-CSE wall (260 vs 224, frame 0x40 -> 0x60) |
| [`func_L19_0042A8B8`](levels/func_L19_0042A8B8.c) | 228 | Not retried: the same family at level 19 (block 0x001BD740, +0xAA compared with the immediate 0xE3E, scale f20 = 0x3FF33333 = 1.9f), same address-CSE wall measured on func_L04_00423178 |




## Level functions, wave 7 agent lvl7-01 (2026-10-16)





| [`func_L00_003B3090`](levels/func_L00_003B3090.c) | 196 | Exact size, 12 words left. Record builder: the 16-byte `lq`/`sq` copy through `mode(TI)`, the pointer at +0x10, 0x10000 at +0x14, 1 at +0x20, the float of A1+0x2C at +0x1C, then `func_00282F70(&rec,&rec,1.0f)` and the tail (1.0f, 0x45AFDF66, 3, 2, the halfword of A0+0xAA) for `func_00277F48(A2,1,D_0018C0B0,&rec,float at A1+0x30)`. Retail keeps the stores ahead of the `lq`/`sq` and materialises both small constants in `$v0`/`$v1`; this cc1 sinks the stores past the call set-up, keeps them in `$v1`/`$a3` and hoists the `lq` into `$v1`. Six store permutations and four spellings of the float measured |
| [`func_L02_003CD1E8`](levels/func_L02_003CD1E8.c) | 176 | Candidate 180 bytes, one word over and every other word retail's. This cc1 allocates the first parameter's pseudo to `$v0`, so the `lw $s1,0x68($a0)` retail keeps in the first call's delay slot becomes `daddu $v0,$a0,$zero` + `lw $s1,0x68($v0)`. Twelve source shapes (state pointer before/between/after the two calls, struct field, `unsigned`/`int`/`void *` loads, index expression, explicit copies of both parameters, `-O2` and `-O2 -G8`) all give the copy, so it is the allocator, not the shape |
| [`func_L02_003CEBB8`](levels/func_L02_003CEBB8.c) | 184 | Not written: the 0x7FFF list walk of the matched L01/L04 siblings (`q = table[a0]`, `base = *(u8 **)D_L02_001B359C`, `e = base + ((*q & 0x7FFF) << 8)`, class 0xD returns `e`), plus a continuation chain this cc1 duplicates (the 0xFF test again, a `bnez` on a register the prologue just cleared, `q == 0`, then `(s16)*(q-1) >= 0` OR `q == table[index]`). Structure and gates in the file |
| [`func_L02_003CD8C0`](levels/func_L02_003CD8C0.c) | 176 | Not written: the void twin of the row above (class 0x41/0xFFFF flag pair on `*(u32 *)(e+0x68) + 0x386/0x382`, the index byte 0x21 re-read every iteration because the body stores alias it). The duplicated continuation chain is the wall; one dead `nop` at 0x003CD8EC |
| [`func_L01_00398A90`](levels/func_L01_00398A90.c) | 192 | No C can place it: the case index at 0x001A79F0 feeds `sltiu` against 0x14 and then a 20-entry `jr` table at 0x002A4420, which the linker cannot place. The five arms and the converging `func_002B0C28(...,4,1)` are written out in the near-miss |
| [`func_L02_00401480`](levels/func_L02_00401480.c) | 176 | No C can place it: the word after the prologue is `0x700014A9` (opcode 0x1C) with `sq $v0,0x0($sp)` next, i.e. the 16-byte copy's non-`lq` (`0x1E`) load half - the MMI/LQ/SQ class of the neighbouring rows. The rest of the row is ordinary and is written out in the near-miss |

### Additional source forms

- [`func_L00_003B3090`](levels/func_L00_003B3090.c): [form 1](levels/alternatives/func_L00_003B3090-form-01.c).
- [`func_L02_003CD1E8`](levels/func_L02_003CD1E8.c): [form 1](levels/alternatives/func_L02_003CD1E8-form-01.c).



















































- [`func_L08_00425568`](levels/func_L08_00425568.c): [form 1](levels/alternatives/func_L08_00425568-form-01.c), [form 2](levels/alternatives/func_L08_00425568-form-02.c).








































































































































































































| [`func_L22_003FA1D0`](levels/func_L22_003FA1D0.c) | 212 | Exact size, ONE word at +0x34: retail materialises func_362DE0's constant fourth argument (`addiu $a3,$zero,0xE0B`) before the dependent first one (`addiu $a0,$v0,0xC`), this cc1 the other way round. Registers are retail's throughout; three spellings (inline, inner call in a named pointer, fourth argument named) give the same word |




## Level functions, wave 7 agent lvl7-12 (2026-10-16)

| [`func_L13_004267B0`](levels/func_L13_004267B0.c) | 112 | Size exact and the whole first block byte-identical once the 0x0019B2E8 table is a 16-byte-entry array indexed by the 0x001A79F0 counter; the second block swaps the delay slot and two registers (retail: object pointer in the beqz slot, index next, 0x001B291C base into `$v0`; here the base `lui` takes the slot and the pointer/base land in `$v1`/`$a1`). Fifteen source spellings give the same block. The 0x001B291C base needs its absolute second name - under `sda` the delay-slot load comes out one gp word (108 bytes) |
| [`func_L00_0031CDD0`](levels/shared/func_L00_0031CDD0.c) | 184 | 176 bytes, whole body and every register exact; the 8 missing bytes are the two `nop` retail carries between the second call's delay slot and its `div.s` (cc1's .s emits no marker there) - the division-padding hole of docs/COMPILER-NOTES.md item (4). Body: buf = a-b, t = |buf|/arg4, `out` = t, result = -((a[2]-b[2]) + arg5*(t*t)*0.5f)/t. Four spellings, all 176. |
| [`func_L00_0031BDC0`](levels/shared/func_L00_0031BDC0.c) | 196 | Body and frame fully recovered (buf1 = a2-a1, buf2 = a3-a1, t = dot(buf1,buf2)/dot(buf1,buf1), scale, dst = a1+t*buf1, return t); the 32 extra bytes are the clamp, which retail does with the two COP1 words `max.s`/`min.s` (funct 0x28/0x29) that this cc1 never emits from C - same wall as func_L08_00425328. Five clamp spellings measured (228/236). |
## Level functions, wave 7 agent lvl7-01, tour 2 (2026-10-16)

| [`func_L00_003959C0`](levels/func_L00_003959C0.c) | 200 | Exact size, 10 words left: retail gives `$s0` to the nine-times counter and `$s3` to the record pointer, this cc1 the other way round, and retail splits the record base pair `lui $v0,0x24` / `addiu $s3,$v0,-0x6110` around the register saves. **The row only reaches 200 bytes in a `-g8` sibling built with `-G8 -mno-split-addresses`** (worker 10's lever confirmed here): the same source at `-G0` is 216 bytes with the two `$gp` floats materialised through `lui`/`lwc1`. Sixteen declaration orders measured, best 10 words; fully indexed / strength-reduced spellings 14 |
| [`func_L00_0041AE28`](levels/func_L00_0041AE28.c) | 200 | Exact size, 14 words, all prologue interleaving (`lui $a1,0x5` before the saves in retail, after them here). The two `func_L00_0041AFD8` floats are **not** the same constant: 0.25f (0x3E800000) first, 0.5f (0x3F000000) second - writing 0.5f twice costs 208 bytes with an `$f20` save. Three corrected shapes all 200/14 |
































































































































































































































































| [`func_L02_003FB4D8`](levels/func_L02_003FB4D8.c) | 196 | Size exact, 48 of 49 words byte-identical (id guard, symbol table lookup - a literal 0x1FC060 compiles to `lui; ori` and misses -, the u16 `(*q & 0x7FFF) * 256` walk with a `$gp` base, the four element tests, the +0xB8 store and a TWO-argument tail call). Only the loop-bottom branch differs: retail `bgez` (plain, 0x0441FFEE) against this cc1's annulling `bgezl` (0x0443FFEE). Seven spellings measured. |
| [`func_L13_0041BDA8`](levels/func_L13_0041BDA8.c) | 220 | 224 bytes: the opening state test and the whole eight-id walk (0x00282F20 call, -1.0f, the 10.0f store at D_00189E20+0x2FC) are recovered, but retail rematerialises the 0x00189EA0 base per iteration (`lui` hoisted, `addiu` inside the loop) and keeps the counter's decrement in the body with the test at the bottom, where this cc1 hoists both address halves into $s4/$s5/$s6 and rotates the loop. Three loop spellings, all 224. |
| [`func_L00_00326110`](levels/shared/func_L00_00326110.c) | 188 | 180 bytes, whole body recovered (call to 0x00282E48 against 7.0f picking 3 or 1, the D_001A79F0 clamp via sltiu/movz against 25, the D_002A0D50 table load and the R5900 three-operand `mult $a1,$a1,$s2`, then the six-integer-argument tail call with -1/0). The 8 bytes are one saved pair: retail keeps the mode in $s1 and the argument in $s2, this cc1 uses $t0/$s1. Three spellings, all 180. |
| [`func_L01_003B5310`](levels/func_L01_003B5310.c) | 180 | Size exact and every instruction right except one swap: retail puts `sw $zero,0x350($s0)` in the `beqz` delay slot with the call's `addiu $a0,$zero,0x12C` after it, this cc1 emits the pair the other way round. Branch layout, the two annulled `beql` guards, the flags walk and both tail calls are exact. Five spellings. |
| [`func_L01_003A64F0`](levels/func_L01_003A64F0.c) | 188 | 176 bytes: the whole call chain is recovered and the lq/sq pairs need `mode(TI)` locals (v = *(a0+0x10), the 0x00282F20/0x00282CC0/0x00282CF0 trio on v, w = *(a0+0xF0), the `*(s16*)(p+0x86) == 5` split with the 0x00335370/0x003353C8 pair against 0x00335438 with four zeros). The 12 missing bytes are the materialised local addresses (`addiu $v1,$s0,0x10`, `addiu $s1,$sp,0x20`, `addiu $s1,$sp,0x10`) that this cc1 folds into the lq/sq offsets. Needs the -g8 sibling (a `$gp` float) plus `nosda` for the absolute D_001B9500. |
| [`func_L00_003266D8`](levels/shared/func_L00_003266D8.c) | 212 | 208 bytes, whole three-stage countdown/reset state machine and the seven-argument 0x0028B3D0 call byte-identical (four `$gp` globals via the -g8 sibling, three absolute 0x001A8Fxx via `nosda`). The 4 bytes are the clamp only: retail has `slti / bnez / sw(delay) / addiu / b / sw` (two stores), the ternary gives `slti / movn` (208) and the if/else an extra `beql`+`b` (216). Four spellings. |









































































































































































































































































































































































| function | size | verdict |
| --- | --- | --- |
