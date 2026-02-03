# badchars

## Write the string "flag.txt" to memory, but certain "badchars" get mangled by the input buffer

### Objective

Similar to the last challenge, this binary has `print_file()` but no "flag.txt" string.
However, this time certain characters mess up your input.

We need to:

1. Determine which characters are "bad"
2. Come up with a way to avoid the badchars and still write "flag.txt" somewhere in memory
3. Call `print_file(address_of_our_string)`

### Solution Approach

1. Find writable memory location (`.bss` section)
2. Use gadgets to write an xor'd "flag.txt" to that location, bypassing the badchars check
3. Use gadgets to xor that string again to get our original "flag.txt" into memory
4. Call `print_file()` with pointer to our written string

### Useful Tools

```bash
# Find writable sections
rabin2 -S badchars

# Find gadgets, avoiding badchars
ROPgadget --binary badchars --badbytes "16|87|76|E2"
```

```python
# Useful code for xor'ng bytes properly
target = b"flag.txt"
mask = 2
xored_string = bytes([b ^ mask for b in target])
```

### Key Concepts Learned

- Bad characters or "Badchars" are specific byte values that the target application processes in a way that can crash the payload
- By sending test payloads and inspecting memory you can identify which bytes are "badchars"
- By using a reversible operation like XOR, you can craft a payload that successfully passes through an input buffer
- Once written in the programs memory, performing the xor on those bytes again restores the original string

### Mistakes Made

1. **Initial attempt wrote to the .data section**
   - Caused decoding step to break, unusure why as .data is empty throughout the whole runtime
   - Used the .bss for the arbitrary write instead, ensuring it was empty
