# Xv6 OS Extensions — Programs, Shell Commands & System Calls

> **Course Project | Operating Systems | University of South Florida**


## Team Members
| Name | UID |
|------|-----|
| Sriram Mullapudi | U69942711 |
| Yaswanth Bellamkonda | — |
| Sai Mukesh Reddy Gutha | — |

---

## Environment
| Tool | Version |
|------|---------|
| OS | Ubuntu |
| Xv6 Version | xv6-public |
| Compiler | GCC 9.3.0 |
| Emulator | QEMU (i386) |

---

## Overview

This project extends MIT's teaching operating system **Xv6** with new user programs, a modified shell command, and a custom kernel system call. All four parts were implemented in C, compiled with GCC, and tested inside a QEMU virtual machine.

---

## Part 1 — Hello World User Program

Created `hello.c`, a minimal Xv6 user program that prints `Hello Xv6!` to standard output.

- Used `echo.c` from the existing Xv6 codebase as a reference
- Added `hello` to the `UPROGS` section of the `Makefile` so it compiles alongside all other user programs
- Verified correct output by running the binary directly from the Xv6 shell inside QEMU

---

## Part 2 — Modified `ls` Command

Rewrote `ls.c` to improve usability:

- **Hidden files filtered by default** — entries beginning with `.` are no longer shown unless a flag is passed
- **Directory indicator** — a `/` is appended to directory names so they are visually distinct from regular files
- **`-a` flag** — restores the original behavior, revealing all hidden files and directories when needed
- Updated the directory iteration loop to handle filtering and formatting cleanly, with thorough edge-case testing

---

## Part 3 — `hello()` System Call

Added a brand-new system call named `hello()` that prints `Hello from the Kernel!` directly from kernel space.

**Files modified:**
| File | Change |
|------|--------|
| `syscall.h` | Added `SYS_hello` syscall number |
| `syscall.c` | Registered `sys_hello` in the dispatch table |
| `sysproc.c` | Implemented the `sys_hello` kernel function |
| `usys.S` | Added the user-space stub |
| `user.h` | Declared `hello()` prototype |
| `hello.c` | Called both `printf` and the new `hello()` syscall |

The implementation was modeled on the existing `getpid` syscall to understand the user-space → kernel-space transition correctly.

**Output when running `hello` in the Xv6 shell:**
```
Hello Xv6!
Hello from the Kernel!
```

---

## Part 4 — `sleep` Command

Implemented `sleep.c`, a user program that pauses execution for a user-specified number of timer ticks.

- Reads the tick count from `argv[1]` and converts it to an integer with `atoi()`
- Delegates to the existing kernel `sleep` syscall — no kernel modification needed
- Prints a clear usage message (`Usage: Sleep <ticks>`) when called with no arguments

**Example:**
```sh
$ sleep 300
```
The process blocks for 300 ticks and then returns to the shell prompt.

---

## Build & Run

```bash
# Clone xv6-public
git clone https://github.com/mit-pdos/xv6-public.git
cd xv6-public

# Apply project changes, then build and launch in QEMU
make clean
make qemu-nox
```

Inside the Xv6 shell:
```sh
$ hello          # Runs Part 1 & Part 3
$ ls             # Runs Part 2 (hidden files filtered)
$ ls -a          # Shows all files including hidden
$ sleep 300      # Runs Part 4
```

---

## Key Learnings

- How Xv6 boots and how the `Makefile` controls which user programs are compiled into the filesystem image
- The complete lifecycle of a system call: user stub → trap → syscall dispatch table → kernel handler
- How to safely pass arguments between user space and kernel space in a minimal OS
- Modifying core shell utilities (`ls`) while preserving backward compatibility via flags

---

## References

- [xv6-public source code](https://github.com/mit-pdos/xv6-public) — MIT PDOS
- Course-provided documentation and project instructions
- Stack Overflow — troubleshooting `sleep` argument handling
- YouTube tutorials on Xv6 system call implementation
