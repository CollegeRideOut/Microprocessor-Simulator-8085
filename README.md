# Intel 8085 Microprocessor Simulator

A full Intel 8085 CPU emulator written in Go, compiled to WebAssembly for an interactive browser-based UI. Write assembly, run it cycle-by-cycle, and watch every register, memory address, and stack operation update in real time.

[![Go](https://img.shields.io/badge/Go-1.18+-00ADD8?logo=go)](https://golang.org)
[![WASM](https://img.shields.io/badge/Target-WebAssembly-654FF0?logo=webassembly)](https://webassembly.org)

---

## Features

- **Full Instruction Set** — arithmetic (`ADD`, `SUB`, `MUL`, `DIV`), logical (`AND`, `OR`, `XOR`, `NOT`), data transfer (`MOV`, `PUSH`, `POP`), branching (`JZ`, `JNZ`, `JC`, `JNC`, `CJNE`, `CJE`, `DJNZ`), and machine control (`CLR`, `STA`, `BEGIN`, `END`)
- **8 General-Purpose Registers** — AL, AH, B, C, D, E, L, H, plus IR, MAR, MBR, and 16-bit PC
- **ALU** — arithmetic and logic unit with comparison, carry flag, and temp registers
- **Memory** — 65,536 addressable bytes (256 × 256)
- **Stack** — push/pop operations with stack pointer
- **Clock** — cycle-accurate timing with configurable frequency
- **Custom Assembler** — converts human-readable assembly to 8-bit opcodes with label/variable support
- **Syntax Checker** — real-time error highlighting in the code editor
- **Web UI** — register display, memory viewer, stack viewer, and execution controls
- **WebSocket Hub** — real-time communication between the emulator and browser

---

## Architecture

```
┌──────────────────────────────────────────┐
│             MicroProcessor               │
│  ┌──────┐ ┌──────┐ ┌──────┐ ┌────────┐  │
│  │ ALU  │ │Regs  │ │Memory│ │ Stack  │  │
│  │Temp1 │ │AL AH │ │65K   │ │Push/Pop│  │
│  │Temp2 │ │B C D │ │words │ │SP      │  │
│  │Comp  │ │E L H │ │      │ │        │  │
│  │Carry │ │PC IR │ │      │ │        │  │
│  └──────┘ └──────┘ └──────┘ └────────┘  │
│  ┌──────────────────────────────────────┐│
│  │            Clock                     ││
│  │  Tick → Fetch → Decode → Execute    ││
│  └──────────────────────────────────────┘│
│  ┌──────────────────────────────────────┐│
│  │         Custom Assembler             ││
│  │  assembly → opcode map → memory     ││
│  └──────────────────────────────────────┘│
└──────────────────────────────────────────┘
         │ Go WebAssembly (lib.wasm)
         ▼
┌──────────────────────────────────────────┐
│              Web UI (Browser)            │
│  Register View · Memory View · Stack     │
│  Code Editor · Syntax Errors · Controls  │
└──────────────────────────────────────────┘
```

### Package Layout

| Package | Purpose |
|---------|---------|
| `microprocessor/` | CPU core — fetch-decode-execute loop, assembler, syntax checker |
| `alu/` | Arithmetic logic unit — add, sub, mul, div, and, or, xor, not, comparison |
| `clock/` | Clock cycle generator with configurable frequency |
| `memory/` | 65,536-byte addressable memory with read/write |
| `opcode/` | Opcode constants and operand-type lookup maps |
| `register/` | 8-bit register with load and get operations |
| `stack/` | Stack with push/pop and stack pointer management |
| `util/` | Binary/decimal conversion utilities |
| `hub/` | WebSocket hub for real-time browser communication |
| `main.go` | WASM entry point — JS function bindings |
| `server.go` | HTTP file server for development |

---

## Quick Start

### Prerequisites

- Go 1.18+
- WebAssembly support (included in modern browsers)

### Build & Run

```bash
# Build the WASM binary
GOOS=js GOARCH=wasm go build -o lib.wasm main.go

# Start the dev server
go run server.go

# Open in browser
open http://localhost:8080
```

### Usage

1. Write your 8085 assembly in the code editor
2. Click **Check Syntax** to validate
3. Click **Run** to load and execute
4. Watch registers, memory, and stack update in real time

### Example Program

```asm
BEGIN
INC C
cjne C 3 M0x1
MOV AL C
END
```

This program increments register C until it reaches 3, then moves the result to AL and halts.

---

## Instruction Set

| Category | Instructions |
|----------|-------------|
| Arithmetic | `ADD`, `SUB`, `MUL`, `DIV`, `INC`, `DEC` |
| Logical | `AND`, `ORL`, `XOR`, `NOT` |
| Data Transfer | `MOV`, `PUSH`, `POP`, `STA` |
| Branching | `JZ`, `JNZ`, `JC`, `JNC`, `CJNE`, `CJE`, `DJNZ` |
| Machine Control | `BEGIN`, `END`, `CLR` |

### Operand Reference

- **Registers**: AL, AH, B, C, D, E, L, H
- **Memory**: `M0X{hex_address}` (e.g., `M0X1A`)
- **Values**: immediate decimal integers
- **Stack**: `S` (top of stack)
- **Labels**: custom identifiers for jump targets

---

## Why This Matters

Building a microprocessor emulator requires:

- Understanding CPU architecture at the gate level
- Implementing a full instruction set with correct opcode encoding
- Managing memory addressing modes (immediate, register, direct)
- Handling flags, carry bits, and conditional branching
- Building an assembler from scratch (parser + symbol table)
- Concurrent execution with clock-cycle timing

This project demonstrates systems-level thinking that separates infrastructure engineers from application developers.

---

## License

MIT
