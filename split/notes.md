# split

## simple ROP chain to call `system("/bin/cat flag.txt")` by controlling RDI register

### Objective

Write a ROP chain to call `system()` with a pointer to "/bin/cat flag.txt" as the argument.

The binary contains:

- `usefulFunction` - calls `system()` but with "/bin/ls" as the arg
- `usefulString` - contains the argument we want: "/bin/cat flag.txt"

We need to call `system(usefulString)`.

### Solution Approach

**First real ROP chain!** Need to:

1. Pop the string address into RDI (first argument register)
2. Call system() from PLT

### Useful Tools

```bash
# finds something to get data into rdi
ROPgadget --binary split | grep "pop rdi"

# finds a simple ret instruction for alignment
ROPgadget --binary split | grep "ret$"

# gets a list of symbols
rabin2 -s split

# simple disassembly view
objdump -d split
```

### Key Concepts Learned

- **x86_64 calling convention (System V ABI):**
  - First argument goes in RDI
  - Second in RSI, third in RDX, etc.
- **ROP gadget chaining:**

```
  Stack layout:
  [gadget_address]  ← ret instr pops this into RIP, executes gadget
  [data]            ← gadget operates on this data
  [next_gadget]     ← gadget's ret pops this
  [more_data]
```

- **Why not call usefulFunction directly:**
  usefulFunction sets its own argument before calling system():

```assembly
  usefulFunction:
      mov edi, 0x400617  ;
      call system@plt
```

So, I decided we can just cut the middle man and call system() directly once RDI is loaded.

- **PLT (Procedure Linkage Table):**
  The PLT is used to call external library functions (like system).
  We can call system@plt directly instead of going through usefulFunction.

### Exploit Execution Flow

```
1. pwnme returns
    -> pops pops_rdi_ret gadget addr into RIP
    -> RSP points to useful_str_addr
2. pop_rdi_ret gadget
    -> pops useful_str_addr off stack and into RDI
    -> ret pops ret_gadget addr into RIP
    -> RSP points to system_plt addr
3. ret_gadget
    -> pops system@plt into RIP
    -> stack alignment (b/c of x86_64 ABI)
4. system_plt
    -> RDI points to "/bin/cat flag.txt"
    -> system(RDI) runs the command
    -> We get the flag!
```

### Mistakes Made

1. **Wrong payload order:** Initially put string address first, which loaded it into RIP instead of using it as data
