# write4

## Write the string "flag.txt" to memory, then call `print_file()` with a pointer to it

### Objective

The binary has `print_file()` but no "flag.txt" string.

We need to:

1. Write "flag.txt" somewhere in memory
2. Call `print_file(address_of_our_string)`

### Solution Approach

1. Find writable memory location (`.data` section)
2. Use gadgets to write "flag.txt" to that location
3. Call `print_file()` with pointer to our written string

### Useful Tools

```bash
# Find writable sections
readelf -S write4

# Find gadgets
ROPgadget --binary write4 | grep "pop r14"
ROPgadget --binary write4 | grep "pop rdi"

# Verify string encoding
python3 -c "from pwn import *; print(p64(0x7478742e67616c66))"
```

### Key Concepts Learned

**Memory sections and permissions:**

- `.text` / `.rodata` - Read-only (code and constants)
- `.data` / `.bss` - Writable (global variables)

Attempting to write to `.text` causes segfault.

**Writing arbitrary data to memory with gadgets:**

```assembly
mov [r14], r15  ; Write value in r15 to address in r14
```

This is how we get our string into memory during exploitation.

### Mistakes Made

1. **Initial attempt wrote to read-only memory** (0x4006B4 in `.text`)
   - Caused segfault when `mov [r14], r15` tried to write
   - Lesson: Always check section permissions

2. **Forgetting endianness** (initially)
   - Strings are stored little-endian in memory
   - "flag.txt" becomes `0x7478742e67616c66` when packed
