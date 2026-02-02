# callme

## Chain three function calls through the PLT with the same arguments

### Objective

Write a ROP chain to call `callme_one`, `callme_two`, and `callme_three` consecutively

### The Gadget

```assembly
pop rdi
pop rsi
pop rdx
ret
```

This one gadget loads all three argument registers at once

### Solution Approach

Each function's `ret` pops the next gadget address, which then loads the next set of arguments and calls the next function.
The chain repeats the pattern three times, using the gadget above to load the arguments repeatedly.

### Key Concepts Learned

It can be useful to use the same gadget sequence multiple times in one chain.
