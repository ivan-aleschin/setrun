# Setun — Open Ternary Architecture

> A modern, open-source ternary CPU specification and browser-based emulator  
> inspired by the Soviet Setun-70 computer (Brusentsov, Moscow State University, 1970)

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status: In Development](https://img.shields.io/badge/Status-In%20Development-orange.svg)]()
[![Contributions Welcome](https://img.shields.io/badge/Contributions-Welcome-blue.svg)](CONTRIBUTING.md)

---

## Why ternary? Why now?

Binary computers are built around a hardware accident: transistors are switches, and switches have two states. That was a practical choice in the 1950s — not a mathematically optimal one.

The most information-efficient integer base is **e ≈ 2.718**. The closest integer is **3**. A single trit (ternary digit) carries log₂(3) ≈ **1.585 bits** of information — 58% more than a bit.

Balanced ternary, where each digit is −1, 0, or +1, has concrete engineering advantages that binary simply cannot replicate:

- **No two's complement.** Negation is a single operation: flip all signs.
- **No sign bit.** The sign is embedded in the number itself.
- **Native three-valued comparison.** A single `SIGN` instruction returns −1, 0, or +1 — replacing a branch chain.
- **Fewer carries in addition.** Balanced representation minimizes carry propagation.
- **Direct mapping to ternary neural network weights.** BitNet and similar architectures already quantize weights to {−1, 0, +1}. This is ternary arithmetic running on binary hardware — with all the overhead that implies.

This is not a historical curiosity. In 2025, Huawei filed a patent for a ternary logic gate. Between 2020 and 2024, over 100 papers on ternary logic circuits were published on IEEE Xplore. Carbon nanotube FETs (CNTFETs) have demonstrated 50% transistor count reduction for basic gates and 65% for adders compared to equivalent binary circuits.

The original Setun replaced by a binary machine in 1970 — which cost **2.5× more** and performed no better.

We are building the foundation so that when the hardware is ready, the software stack exists.

---

## What this project is

**A complete, open, formally specified ternary ISA** — the Setun-1 architecture — with:

- A browser-based emulator (no installation required)
- A human-readable instruction set specification (PDF + source)
- An assembler
- A C-to-ternary compiler backend (LLVM)
- Documented example programs
- A comparison benchmark suite against RISC-V

Everything is MIT-licensed. No patents. No restrictions. This is infrastructure, not a product.

---

## Project roadmap

### Milestone 1 — Working emulator `v0.1` (Month 1–2)

The goal is a single HTML file you can open in any browser and run ternary assembly programs.

**CPU core (`cpu.js`):**
- [ ] Balanced ternary integer representation (trits as `−1 | 0 | +1`)
- [ ] 9 general-purpose registers × 9 trits each
- [ ] 243-word addressable memory × 9 trits per word
- [ ] Fetch–decode–execute cycle
- [ ] 24 base instructions (see ISA section below)
- [ ] Flags: sign flag (−1 / 0 / +1), halt flag

**Interface (`index.html`):**
- [ ] Assembly code editor (left panel)
- [ ] Register state display — balanced ternary + decimal (right panel)
- [ ] Memory viewer (bottom panel)
- [ ] Step / Run / Reset controls
- [ ] Three built-in example programs (load with one click)

**Specification (`spec/isa-v0.1.pdf`):**
- [ ] Word format and memory model
- [ ] Full instruction table with encoding, description, pseudocode
- [ ] Comparison table: Setun-1 vs RISC-V for 10 canonical operations

**Milestone 1 definition of done:**  
A user opens the site, clicks "Load example: sort three numbers", clicks Run, and sees the correct output with register state visible at each step. No installation. No account.

---

### Milestone 2 — Documented examples and benchmark `v0.2` (Month 2–3)

**Example programs (fully commented assembly):**
- [ ] `examples/sign.s` — determine the sign of a number (1 instruction vs 3-branch binary chain)
- [ ] `examples/sort3.s` — sort three numbers using SIGN
- [ ] `examples/matmul_ternary.s` — matrix × vector multiply with {−1, 0, +1} weights
- [ ] `examples/fibonacci.s` — standard recursion test
- [ ] `examples/bubble_sort.s` — array sort benchmark

**Benchmark suite:**
- [ ] 10 operations measured: comparison, negation, sort, multiply, branch
- [ ] Output: instruction count comparison vs RISC-V pseudocode
- [ ] Rendered as a static page on the project site

**Project site (`ternary.dev`):**
- [ ] Live emulator embedded on the landing page
- [ ] One-sentence description above the fold
- [ ] Three clickable examples
- [ ] Link to specification PDF
- [ ] Link to this repository

---

### Milestone 3 — Assembler and academic output `v0.3` (Month 3–4)

**Assembler (`assembler/`):**
- [ ] Parse `.s` assembly files into binary (ternary-encoded) object files
- [ ] Labels, constants, basic macros
- [ ] Error messages with line numbers
- [ ] CLI: `node assemble.js program.s -o program.to`

**Academic paper (draft):**
- [ ] Title: *Setun-1: A balanced ternary ISA for efficient signed comparison and neural network inference*
- [ ] Sections: motivation, ISA design decisions, comparison with RISC-V, emulation results
- [ ] Target venue: IEEE Computer Architecture Letters or arXiv cs.AR
- [ ] Status: [ ] outline → [ ] draft → [ ] submitted

**Grant application (RF):**
- [ ] RSF (Russian Science Foundation) young researcher grant
- [ ] Application prepared in parallel with the paper
- [ ] Working emulator + paper draft attached as proof of progress

---

### Milestone 4 — Compiler backend `v0.4` (Month 5–8)

- [ ] LLVM backend for Setun-1 ISA
- [ ] Compile a subset of C: integers, arrays, loops, function calls
- [ ] Run the example programs through the compiler pipeline end-to-end
- [ ] Benchmark: compiler-generated code vs hand-written assembly

---

### Future (v1.0+)

- Formal ISA verification (Coq or Lean)
- Hardware description (Verilog/VHDL simulation)
- RISC-V compatibility layer (binary-to-ternary translation)
- WASM-compiled emulator for maximum browser performance
- Community ISA extension process (modeled on RISC-V)

---

## Instruction set (Setun-1 ISA, draft)

Each instruction word is 9 trits. The first 3 trits encode the opcode (27 possible instructions), the remaining 6 encode operands.

| Mnemonic | Operands | Description |
|---|---|---|
| `LOAD` | `Rd, addr` | Load word from memory into register |
| `STORE` | `Rs, addr` | Store register to memory |
| `MOV` | `Rd, Rs` | Copy register |
| `LIT` | `Rd, imm` | Load 6-trit immediate |
| `ADD` | `Rd, Ra, Rb` | Balanced ternary addition |
| `SUB` | `Rd, Ra, Rb` | Subtraction (invert Rb, add) |
| `MUL` | `Rd, Ra, Rb` | Multiplication |
| `NEG` | `Rd, Rs` | Negation (flip all trit signs) |
| `SIGN` | `Rd, Rs` | Sign: result is −1, 0, or +1 |
| `MIN` | `Rd, Ra, Rb` | Ternary MIN (analog of AND) |
| `MAX` | `Rd, Ra, Rb` | Ternary MAX (analog of OR) |
| `CONS` | `Rd, Ra, Rb` | Consensus: Ra if Ra=Rb, else 0 |
| `ANY` | `Rd, Ra, Rb` | Any: Ra if Ra≠0, else Rb |
| `SHL` | `Rd, Rs, n` | Shift left by n trits (× 3ⁿ) |
| `SHR` | `Rd, Rs, n` | Shift right by n trits (÷ 3ⁿ) |
| `JMP` | `addr` | Unconditional jump |
| `JNEG` | `Rs, addr` | Jump if SIGN(Rs) = −1 |
| `JZER` | `Rs, addr` | Jump if SIGN(Rs) = 0 |
| `JPOS` | `Rs, addr` | Jump if SIGN(Rs) = +1 |
| `CALL` | `addr` | Call subroutine (push PC) |
| `RET` | | Return from subroutine |
| `IN` | `Rd` | Read from input |
| `OUT` | `Rs` | Write to output |
| `HALT` | | Stop execution |

> This table is a living document. Open an issue to propose additions.

---

## Example: sign detection

The most illustrative difference between binary and ternary.

**Binary (x86-like pseudocode):**
```asm
; Determine sign of value in R1
; Result: R2 = -1, 0, or +1
CMP  R1, #0
BLT  negative       ; branch if less than zero
BEQ  zero           ; branch if equal to zero
MOV  R2, #1         ; positive
JMP  done
negative:
  MOV R2, #-1
  JMP done
zero:
  MOV R2, #0
done:
; 7 instructions, 3 branches
```

**Setun-1 ternary:**
```asm
; Determine sign of value in R1
SIGN R2, R1
; 1 instruction, 0 branches
```

---

## Example: sort three numbers

```asm
; Sort R1, R2, R3 in ascending order
; Uses SIGN to avoid branch chains

sort3:
  SUB  R4, R1, R2     ; R4 = R1 - R2
  SIGN R5, R4         ; R5 = sign(R1 - R2)
  JNEG R5, skip_swap1 ; R1 < R2, already ordered
  MOV  R6, R1
  MOV  R1, R2
  MOV  R2, R6         ; swap R1, R2
skip_swap1:
  SUB  R4, R2, R3
  SIGN R5, R4
  JNEG R5, skip_swap2
  MOV  R6, R2
  MOV  R2, R3
  MOV  R3, R6
skip_swap2:
  SUB  R4, R1, R2
  SIGN R5, R4
  JNEG R5, done
  MOV  R6, R1
  MOV  R1, R2
  MOV  R2, R6
done:
  HALT
```

---

## Repository structure

```
setun/
├── emulator/
│   ├── cpu.js          # CPU core — registers, memory, execute loop
│   ├── index.html      # Browser UI — editor, register view, memory
│   └── trit.js         # Balanced ternary arithmetic primitives
├── assembler/
│   ├── assembler.js    # .s → .to object file
│   └── parser.js       # Tokenizer and parser
├── examples/
│   ├── sign.s
│   ├── sort3.s
│   ├── matmul_ternary.s
│   ├── fibonacci.s
│   └── bubble_sort.s
├── spec/
│   ├── isa-v0.1.tex    # LaTeX source of the specification
│   ├── isa-v0.1.pdf    # Compiled specification
│   └── comparison/     # Setun-1 vs RISC-V benchmark tables
├── compiler/
│   └── llvm-backend/   # LLVM backend (Milestone 4)
├── paper/
│   └── setun1-arch.tex # Academic paper draft
├── site/
│   └── index.html      # Landing page (ternary.dev)
├── CONTRIBUTING.md
├── LICENSE             # MIT
└── README.md
```

---

## Getting started

**Run the emulator (no installation):**

Open `emulator/index.html` in any modern browser. Or visit [ternary.dev](https://ternary.dev).

**Run the assembler:**

```bash
git clone https://github.com/ternary-foundation/setun
cd setun
node assembler/assembler.js examples/sort3.s -o sort3.to
```

**Run a program in the emulator from CLI:**

```bash
node emulator/run.js sort3.to
```

---

## Contributing

This project is in active early development. The highest-value contributions right now are:

1. **CPU core correctness** — find edge cases in balanced ternary arithmetic
2. **ISA design feedback** — if you work in computer architecture, we want your opinion on the instruction set before v0.1 is frozen
3. **Example programs** — any algorithm that demonstrates ternary advantages
4. **Documentation** — especially translations (the history of Setun is mostly in Russian)

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

Open an issue before starting significant work — we want to coordinate to avoid duplication.

---

## Background: the Setun computers (1958–1970)

In 1958, Nikolay Brusentsov and Sergei Sobolev built the world's first electronic ternary computer at Moscow State University. Named after the Setun river near the university, it used balanced ternary throughout — not as an experiment, but as a practical engineering decision.

Fifty Setun machines were built between 1959 and 1965. They were used in universities, industrial plants, and served as the platform for the first automated computer-based learning system at the Zhukovsky Air Force Engineering Academy.

In 1970, Brusentsov designed the Setun-70, which independently anticipated several RISC architecture principles and implemented structured programming in hardware — before Dijkstra's ideas had become mainstream.

Production was halted not because ternary failed — the replacement binary machine cost 2.5× more and performed no better — but because the global semiconductor industry had standardized on binary transistor technology, and the Soviet computing establishment followed.

This project continues that line of work on open foundations.

---

## Related work and references

- Brusentsov, N.P. et al. *Development of ternary computers at Moscow State University*. computer-museum.ru
- Knuth, D. *The Art of Computer Programming*, Vol. 2 — calls balanced ternary "the prettiest number system of all"
- Hayes, B. *Third Base*. American Scientist, 89(6), 2001
- Huawei patent CN202510196 — ternary logic gate circuit, 2025
- Ma et al. *The Era of 1-bit LLMs: BitNet b1.58* — ternary weight quantization in neural networks, 2024
- Hunger, F. *SETUN: An Inquiry into the Soviet Ternary Computer*. Institut für Buchkunst Leipzig, 2008

---

## License

MIT — see [LICENSE](LICENSE).

Use it, fork it, build on it. If you build something interesting, open an issue and tell us.

---

## Authors

Developed by researchers at [MIET and MSUCE].  
Contact: [aleshin.ik@phystech.edu]  
Inspired by the work of Nikolay Petrovich Brusentsov (1925–2014).
