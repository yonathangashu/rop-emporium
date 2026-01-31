# ROP Emporium Notes

## ret2win (Date: 2026-01-30)

### What I did:

- Got my environment setup in macOSX and ssh'd into my utm ubuntu server
- Found buffer size from challenge description (32 bytes)
- Calculated offset: 32 (buffer) + 8 (saved RBP) = 40 bytes
- Found win function address w/ gdb/objdump: 0x400756
- Built payload: padding + p64(win_addr)

### Key insight:

On x86_64, saved RBP sits between your buffer and the return address. Always buffer_size + 8 to reach the return address.

### Mistakes made:

- Initially used p32() instead of p64() (wrong architecture)
- Tried offset of 32 instead of 40 (forgot about saved RBP)

## Stack Alignment Issue

**Problem:** Jumped directly to ret2win() and got "Well done!" but no flag

**Cause:** Stack wasn't 16-byte aligned. Functions compiled with modern gcc
use SIMD instructions (movaps) that require alignment.

**Solution:** Add a `ret` gadget before the function address:

- ret gadget pops 8 bytes off stack
- Misaligns stack to match what `call` would do
- Function executes successfully

**Key insight:** When you `call`, return address is pushed (8 bytes).
When you ROP, there's no push, so add manual alignment.

Found ret gadget with: `ROPgadget --binary ret2win | grep ": ret$"`
Address: 0x40053e

--
