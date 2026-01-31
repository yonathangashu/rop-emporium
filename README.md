# Writeups for ROP Emporium Challenges

My solutions and notes while working through [ROP Emporium](https://ropemporium.com/) binaries

## Progress

- [x] ret2win - basic buffer overflow + return address overwrite
- [x] split - simple ROP chain, calling system()
- [ ] callme
- [ ] write4
- [ ] badchars
- [ ] fluff
- [ ] pivot
- [ ] ret2csu

## Structure

Each challenge has a directory with:

- the challenge binary
- `flag.txt`
- `exploit.py` - My working exploit
- my notes `notes.md`

## Environment

I'm working in macOS running Ubuntu 24.04 x86_64 in UTM

- pwntools
- pwndbg
- ROPgadget

## Running

In any given challenge directory, simply run:

```bash
python3 exploit.py
```

---

Once I finish all challenge binaries, I might flesh out the notes into detailed writeups/walkthroughs
