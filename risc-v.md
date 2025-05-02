# RISC-V

## Setup

```bash
sudo apt update
sudo apt install -y gcc-riscv64-unknown-elf qemu-system-riscv64
```

## Compilation & Edition de lien

```bash
cd <project>
riscv64-unknown-elf-as -o hello.o hello.S 
riscv64-unknown-elf-ld -T hello.ld -nostdlib -static -o hello.elf hello.o
riscv64-unknown-elf-objcopy -O binary hello.elf hello.bin
qemu-system-riscv64 -machine virt -nographic -serial mon:stdio -bios hello.bin
```

*Pour quitter Qemu : ``Ctrl+A``, puis ``C``, puis ``quit``.*

## Code

 Linker script ``hello.ld`` :

```bash
SECTIONS {
    . = 0x80000000;
    .text : {
        *(.text)   /* Place all .text (code) sections here */
    }
    /* On s'assur d'etre dans une autre page memoire pour evité que data et text soit mixte et donc text wirtable during runtime */
    . = ALIGN (CONSTANT (COMMONPAGESIZE));    
    .data : {
        *(.data)   /* Place all .data sections here */
    }
    .bss : {
        *(.bss)    /* Place uninitialized data here */
    }
}
```

Code source ``hello.S`` :

```asm
.equ UART_BASE, 0x10000000

.section .text
  la a0, message
  li a1, UART_BASE
  call puts

loop: j loop

puts:
1:
  lb t0, 0(a0)
  beq zero, t0, 2f
  sb t0, (a1)
  addi a0, a0, 1
  j 1b
2:
  ret

.section .data
message: .string "Hello, Bare Metal RISC-V!\n"
```
## RISC-V Extensions

### Base Integer Instruction Set

- **RV32I**: Base integer instruction set for 32-bit processors.
- **RV64I**: Base integer instruction set for 64-bit processors.

### Standard Extensions

| **Extension** | **Description** |
|---------------|-----------------|
| **M**         | Integer multiplication and division (RV32M, RV64M) |
| **A**         | Atomic memory operations (RV32A, RV64A) |
| **F**         | Single-precision floating-point (RV32F, RV64F) |
| **D**         | Double-precision floating-point (RV32D, RV64D) |
| **C**         | Compressed instructions (RV32C, RV64C) |
| **B**         | Bit manipulation (RV32B, RV64B) |
| **J**         | Dynamically translatable instructions (RV32J, RV64J) |
| **T**         | Vector instructions (RV32T, RV64T) |
| **V**         | Vector extension (RV32V, RV64V) |

***G** is an alias for **MAFD***

### Optional Extensions

- **P**: Packed SIMD instructions (for parallel processing)
- **Z**: Debugging and tracing support

### Privileged Extensions

| **Extension** | **Description** |
|---------------|-----------------|
| **S**         | Supervisor mode (for OS and privilege management) |
| **U**         | User mode (basic user-level instruction support) |
| **H**         | Hypervisor support (for virtualization) |
| **X**         | Extensions for custom instructions and features |

### Other Specialized Extensions

| **Extension** | **Description** |
|---------------|-----------------|
| **N**         | Standard for the custom RISC-V extensions (user-defined) |
| **L**         | Low-power optimization extension (optimizations for energy consumption) |

## Registers

| **Nom**  | **Alias** | **Description** |
|---------|---------|---------------|
| `x0`   | `zero` | Toujours **0** (écriture ignorée, lecture donne 0). |
| `x1`   | `ra`   | **Return Address** (adresse de retour des fonctions). |
| `x2`   | `sp`   | **Stack Pointer** (pointe vers le sommet de la pile). |
| `x3`   | `gp`   | **Global Pointer** (utilisé pour les variables globales). |
| `x4`   | `tp`   | **Thread Pointer** (utilisé pour les threads). |
| `x5`   | `t0`   | **Temporaire** (`t` = temporary), premier registre temporaire. |
| `x6`   | `t1`   | **Temporaire**. |
| `x7`   | `t2`   | **Temporaire**. |
| `x8`   | `s0` / `fp` | **Saved Register** ou **Frame Pointer** (registre sauvegardé, peut être utilisé comme base de frame). |
| `x9`   | `s1`   | **Saved Register** (registre sauvegardé). |
| `x10`  | `a0`   | **Argument 0 / Valeur de retour 0** (premier argument des fonctions, ou valeur retournée). |
| `x11`  | `a1`   | **Argument 1 / Valeur de retour 1**. |
| `x12`  | `a2`   | **Argument 2**. |
| `x13`  | `a3`   | **Argument 3**. |
| `x14`  | `a4`   | **Argument 4**. |
| `x15`  | `a5`   | **Argument 5**. |
| `x16`  | `a6`   | **Argument 6**. |
| `x17`  | `a7`   | **Argument 7**. |
| `x18`  | `s2`   | **Saved Register** (registre sauvegardé). |
| `x19`  | `s3`   | **Saved Register**. |
| `x20`  | `s4`   | **Saved Register**. |
| `x21`  | `s5`   | **Saved Register**. |
| `x22`  | `s6`   | **Saved Register**. |
| `x23`  | `s7`   | **Saved Register**. |
| `x24`  | `s8`   | **Saved Register**. |
| `x25`  | `s9`   | **Saved Register**. |
| `x26`  | `s10`  | **Saved Register**. |
| `x27`  | `s11`  | **Saved Register**. |
| `x28`  | `t3`   | **Temporaire**. |
| `x29`  | `t4`   | **Temporaire**. |
| `x30`  | `t5`   | **Temporaire**. |
| `x31`  | `t6`   | **Temporaire**. |

*Arithmetic & logic operations (e.g., add, sub, and, or) only work on registers*

### Registres temporaires (`t0 - t6`)

* Utilisés pour **les calculs intermédiaires**.
* **⚠️ Pas sauvegardés entre les appels de fonction** (ils peuvent être écrasés).

### Registres d'arguments (`a0 - a7`)

* Passent les **paramètres** aux fonctions.
* `a0` et `a1` contiennent la **valeur de retour** d'une fonction.

### Registres sauvegardés (`s0 - s11`)

* Conservent des valeurs importantes sur une longue durée.
* **✅ Sauvegardés à travers les appels de fonction** (les fonctions doivent préserver leur contenu).

### Registres spéciaux (`sp`, `ra`, `gp`, `tp`)

* `sp` (**Stack Pointer**) → Gère la **pile** pour stocker des variables locales.
* `ra` (**Return Address**) → Stocke l'**adresse de retour** après un `call`.
* `gp` (**Global Pointer**) → Référence les **variables globales**.
* `tp` (**Thread Pointer**) → Utilisé pour **les threads**.

## Instructions

### Formats

| \[31\]      | \[30–25\] | \[24–21\] | 20 | \[19–15\] | \[14–12\] | \[11–8\] | 7  | \[6–0\]  | Type   |
|------------|-----------|-----------|----|-----------|-----------|----------|----|----------|--------|
|            | funct7    | rs2       |    | rs1       | funct3    | rd       |    | opcode   | R-type |
|            | imm\[11:0\]         |           |    | rs1       | funct3    | rd       |    | opcode   | I-type |
|            | imm\[11:5\]         | rs2       |    | rs1       | funct3    | imm\[4:0\] |    | opcode   | S-type |
| imm\[12\]  | imm\[10:5\]         | rs2       |    | rs1       | funct3    | imm\[4:1\] | imm\[11\] | opcode | B-type |
| imm\[31:12\]                             |                           |                      |                     |                          |            | rd       |    | opcode   | U-type |
| imm\[20\]  | imm\[10:1\]         | imm\[11\]  |    | imm\[19:12\] |           | rd       |    | opcode   | J-type |

### Instructions RV32I 

| Syntax                       | Name                    | Opcode   | funct3 | funct7   | Description                        | Type |
|------------------------------|-------------------------|----------|--------|----------|------------------------------------|-----|
| add rd, rs1, rs2             | ADD                     | 011_0011 | 000    | 000_0000 | rd = rs1 + rs2                     | R   |
| sub rd, rs1, rs2             | SUB                     | 011_0011 | 000    | 010_0000 | rd = rs1 - rs2                     | R   |
| xor rd, rs1, rs2             | XOR                     | 011_0011 | 100    | 000_0000 | rd = rs1 ^ rs2                     | R   |
| or rd, rs1, rs2              | OR                      | 011_0011 | 110    | 000_0000 | rd = rs1 \| rs2                    | R   |
| and rd, rs1, rs2             | AND                     | 011_0011 | 111    | 000_0000 | rd = rs1 & rs2                     | R   |
| sll rd, rs1, rs2             | Shift Left Logical      | 011_0011 | 001    | 000_0000 | rd = rs1 << rs2                    | R   |
| srl rd, rs1, rs2             | Shift Right Logical     | 011_0011 | 101    | 000_0000 | rd = rs1 >>u rs2                   | R   |
| sra rd, rs1, rs2             | Shift Right Arithmetic  | 011_0011 | 101    | 010_0000 | rd = rs1 >>s rs2                   | R   |
| slt rd, rs1, rs2             | Set Less Than           | 011_0011 | 010    | 000_0000 | rd = (rs1 s< rs2) ? 1 : 0          | R   |
| sltu rd, rs1, rs2            | Set Less Than Unsigned  | 011_0011 | 011    | 000_0000 | rd = (rs1 u< rs2) ? 1 : 0          | R   |
| addi rd, rs1, imm              | ADD Immediate            | 001_0011 | 000    |                        | rd = rs1 + IMMI                        | I   |
| xori rd, rs1, imm              | XOR Immediate            | 001_0011 | 100    |                        | rd = rs1 ^ IMMI                        | I   |
| ori rd, rs1, imm               | OR Immediate             | 001_0011 | 110    |                        | rd = rs1 \| IMMI                       | I   |
| andi rd, rs1, imm              | AND Immediate            | 001_0011 | 111    |                        | rd = rs1 & IMMI                        | I   |
| slli rd, rs1, shamt            | Shift Left Logical Imm   | 001_0011 | 001    | imm\[11:5\]=000_0000   | rd = rs1 << imm\[4:0\]                 | I   |
| srli rd, rs1, shamt            | Shift Right Logical Imm  | 001_0011 | 101    | imm\[11:5\]=000_0000   | rd = rs1 >>u imm\[4:0\]                | I   |
| srai rd, rs1, shamt            | Shift Right Arith Imm    | 001_0011 | 101    | imm\[11:5\]=010_0000   | rd = rs1 >>s imm\[4:0\]                | I   |
| slti rd, rs1, imm              | Set Less Than Imm        | 001_0011 | 010    |                        | rd = (rs1 s< IMMI) ? 1 : 0             | I   |
| sltiu rd, rs1, imm             | Set Less Than Imm (U)    | 001_0011 | 011    |                        | rd = (rs1 u< IMMI) ? 1 : 0             | I   |
| lb rd, imm(rs1)        | Load Byte          | 000_0011 | 000    | —      | rd = M\[rs1 + imm\] | I   |
| lh rd, imm(rs1)        | Load Half          | 000_0011 | 001    | —      | rd = M\[rs1 + imm\] | I   |
| lw rd, imm(rs1)        | Load Word          | 000_0011 | 010    | —      | rd = M\[rs1 + imm\] | I   |
| lbu rd, imm(rs1)       | Load Byte Unsigned | 000_0011 | 100    | —      | rd = M\[rs1 + imm\] | I   |
| lhu rd, imm(rs1)       | Load Half Unsigned | 000_0011 | 101    | —      | rd = M\[rs1 + imm\] | I   |
| sb rs2, imm(rs1)           | Store Byte           | 010_0011 | 000    | —      | M\[rs1 + imm\] = rs2 (8 bits)         | S   |
| sh rs2, imm(rs1)           | Store Halfword       | 010_0011 | 001    | —      | M\[rs1 + imm\] = rs2 (16 bits)        | S   |
| sw rs2, imm(rs1)           | Store Word           | 010_0011 | 010    | —      | M\[rs1 + imm\] = rs2 (32 bits)        | S   |
| beq rs1, rs2, imm          | Branch if ==         | 110_0011 | 000    | —      | if (rs1 == rs2) PC += imm            | B   |
| bne rs1, rs2, imm          | Branch if !=         | 110_0011 | 001    | —      | if (rs1 != rs2) PC += imm            | B   |
| blt rs1, rs2, imm          | Branch if <          | 110_0011 | 100    | —      | if (rs1 < rs2) PC += imm             | B   |
| bge rs1, rs2, imm          | Branch if ≥          | 110_0011 | 101    | —      | if (rs1 ≥ rs2) PC += imm             | B   |
| bltu rs1, rs2, imm         | Branch if < Unsigned | 110_0011 | 110    | —      | if (rs1 <u rs2) PC += imm            | B   |
| bgeu rs1, rs2, imm         | Branch if ≥ Unsigned | 110_0011 | 111    | —      | if (rs1 ≥u rs2) PC += imm            | B   |
| lui rd, imm                | Load Upper Immediate | 011_0111 | —      | —      | rd = imm << 12                      | U   |
| auipc rd, imm              | Add Upper Imm to PC  | 001_0111 | —      | —      | rd = PC + (imm << 12)               | U   |
| jal rd, imm                | Jump and Link        | 110_1111 | —      | —      | rd = PC + 4 ; PC += imm             | J   |
| jalr rd, rs1, imm   | Jump And Link Register | 110_0111 | 000    | —      | rd = PC+4; PC = (rs1 + imm) & ~1                 | I   |
| ecall               | Environment Call       | 111_0011 | 000    | —      | Appel au système d'exploitation ou superviseur   | I   |
| ebreak              | Environment Break      | 111_0011 | 000    | —      | Utilisé pour déclencher un débogueur             | I   |


### Unconditional Jump Instructions

| **Instruction**  | **Description** | **Saves Return Address?** |
|------------------|--------------------------------|----------------|
| `j label`       | Unconditional jump to `label` (alias for `jal x0, label`) | ❌ No |
| `jr register`   | Jump to address stored in `register` (alias for `jalr x0, register, 0`) | ❌ No |
| `jal label`     | Jump to `label` and store return address in `ra` | ✅ Yes (`ra = PC+4`) |
| `jalr register` | Jump to address in `register` and store return address in `ra` | ✅ Yes (`ra = PC+4`) |
| `call function` | Calls a function (alias for `jal function`) | ✅ Yes (`ra = PC+4`) |
| `ret`           | Returns from function (alias for `jalr x0, ra, 0`) | ❌ No |

### Conditional Branch Instructions

| **Instruction**         | **Description**                             |
|-------------------------|---------------------------------------------|
| `beq rs1, rs2, label`  | Branch to `label` if `rs1 == rs2`           |
| `bne rs1, rs2, label`  | Branch to `label` if `rs1 != rs2`           |
| `blt rs1, rs2, label`  | Branch to `label` if `rs1 < rs2` (signed)   |
| `bge rs1, rs2, label`  | Branch to `label` if `rs1 >= rs2` (signed)  |
| `bltu rs1, rs2, label` | Branch to `label` if `rs1 < rs2` (unsigned) |
| `bgeu rs1, rs2, label` | Branch to `label` if `rs1 >= rs2` (unsigned) |

### Load and Store Instructions

| **Instruction** | **Operation**                                      | **Description**                      |
|---------------|------------------------------------------------|----------------------------------|
| `lb rd, offset(rs1)`  | `rd = *(int8_t*)(rs1 + offset)`     | Load **byte** (8-bit) **signed** |
| `lbu rd, offset(rs1)` | `rd = *(uint8_t*)(rs1 + offset)`    | Load **byte** (8-bit) **unsigned** |
| `lh rd, offset(rs1)`  | `rd = *(int16_t*)(rs1 + offset)`    | Load **halfword** (16-bit) **signed** |
| `lhu rd, offset(rs1)` | `rd = *(uint16_t*)(rs1 + offset)`   | Load **halfword** (16-bit) **unsigned** |
| `lw rd, offset(rs1)`  | `rd = *(int32_t*)(rs1 + offset)`    | Load **word** (32-bit) |
| `ld rd, offset(rs1)`  | `rd = *(int64_t*)(rs1 + offset)`    | Load **doubleword** (64-bit) (RV64 only) |
| `sb rs2, offset(rs1)`  | `*(int8_t*)(rs1 + offset) = rs2`  | Store **byte** (8-bit) |
| `sh rs2, offset(rs1)`  | `*(int16_t*)(rs1 + offset) = rs2` | Store **halfword** (16-bit) |
| `sw rs2, offset(rs1)`  | `*(int32_t*)(rs1 + offset) = rs2` | Store **word** (32-bit) |
| `sd rs2, offset(rs1)`  | `*(int64_t*)(rs1 + offset) = rs2` | Store **doubleword** (64-bit) (RV64 only) |

* `rs1` = Base register (holds the memory address)
* `offset` = Immediate value added to `rs1` (memory address offset). The offset is always in bytes, regardless of the data size being loaded or stored.
* `rd` = Destination register (for loads)
* `rs2` = Source register (for stores)

```
# Assume x1 = 0x1000 (word-aligned address)
lw x2, 4(x1)   # ✅ Loads a 32-bit word from 0x1004 (aligned)
lh x3, 2(x1)   # ✅ Loads a 16-bit halfword from 0x1002 (aligned)
lb x4, 3(x1)   # ✅ Loads a byte from 0x1003 (bytes are always aligned)
lh x3, 5(x1)   # ❌ Misaligned! 0x1005 is not a multiple of 2
ld x4, 6(x1)   # ❌ Misaligned! 0x1006 is not a multiple of 8 (on RV64)
```

### arithmetic & logical operations

#### Arithmetic Instructions

| **Instruction** | **Description** | **Type** |
|---------------|----------------|--------|
| `ADD rd, rs1, rs2`  | `rd = rs1 + rs2` (Addition) | R |
| `ADDI rd, rs1, imm` | `rd = rs1 + imm` (Addition with Immediate) | I |
| `SUB rd, rs1, rs2`  | `rd = rs1 - rs2` (Subtraction) | R |
| `LUI rd, imm` | Load Upper Immediate: `rd = imm << 12` | U |
| `AUIPC rd, imm` | Add Upper Immediate to PC: `rd = PC + (imm << 12)` | U |

---

#### Logical Instructions

| **Instruction** | **Description** | **Type** |
|---------------|----------------|--------|
| `AND rd, rs1, rs2`  | `rd = rs1 & rs2` (Bitwise AND) | R |
| `ANDI rd, rs1, imm` | `rd = rs1 & imm` (Bitwise AND with Immediate) | I |
| `OR rd, rs1, rs2`   | `rd = rs1 \| rs2` (Bitwise OR) | R |
| `ORI rd, rs1, imm`  | `rd = rs1 \| imm` (Bitwise OR with Immediate) | I |
| `XOR rd, rs1, rs2`  | `rd = rs1 ^ rs2` (Bitwise XOR) | R |
| `XORI rd, rs1, imm` | `rd = rs1 ^ imm` (Bitwise XOR with Immediate) | I |

---

#### Shift Instructions

| **Instruction** | **Description** | **Type** |
|---------------|----------------|--------|
| `SLL rd, rs1, rs2`  | `rd = rs1 << rs2` (Logical Left Shift) | R |
| `SLLI rd, rs1, imm` | `rd = rs1 << imm` (Logical Left Shift with Immediate) | I |
| `SRL rd, rs1, rs2`  | `rd = rs1 >> rs2` (Logical Right Shift) | R |
| `SRLI rd, rs1, imm` | `rd = rs1 >> imm` (Logical Right Shift with Immediate) | I |
| `SRA rd, rs1, rs2`  | `rd = rs1 >> rs2` (Arithmetic Right Shift) | R |
| `SRAI rd, rs1, imm` | `rd = rs1 >> imm` (Arithmetic Right Shift with Immediate) | I |

---

#### Set-on-Condition Instructions

| **Instruction** | **Description** | **Type** |
|---------------|----------------|--------|
| `SLT rd, rs1, rs2`  | `rd = (rs1 < rs2) ? 1 : 0` (Set if Less Than) | R |
| `SLTI rd, rs1, imm` | `rd = (rs1 < imm) ? 1 : 0` (Set if Less Than Immediate) | I |
| `SLTU rd, rs1, rs2`  | `rd = (rs1 < rs2) ? 1 : 0` (Unsigned Comparison) | R |
| `SLTIU rd, rs1, imm` | `rd = (rs1 < imm) ? 1 : 0` (Unsigned Comparison with Immediate) | I |

---

#### Multiplication & Division (RV32M / RV64M Extension)

| **Instruction** | **Description** | **Type** |
|---------------|----------------|--------|
| `MUL rd, rs1, rs2`   | `rd = rs1 * rs2` (Multiply) | R |
| `MULH rd, rs1, rs2`  | `rd = (rs1 * rs2) >> XLEN` (Multiply High Signed) | R |
| `MULHU rd, rs1, rs2` | `rd = (rs1 * rs2) >> XLEN` (Multiply High Unsigned) | R |
| `MULHSU rd, rs1, rs2` | `rd = (rs1 * rs2) >> XLEN` (Signed * Unsigned) | R |
| `DIV rd, rs1, rs2`   | `rd = rs1 / rs2` (Signed Division) | R |
| `DIVU rd, rs1, rs2`  | `rd = rs1 / rs2` (Unsigned Division) | R |
| `REM rd, rs1, rs2`   | `rd = rs1 % rs2` (Signed Remainder) | R |
| `REMU rd, rs1, rs2`  | `rd = rs1 % rs2` (Unsigned Remainder) | R |

### System Instructions (Environment Call & Break)

| **Instruction** | **Description** |
|-----------------|-----------------|
| `ECALL`         | Environment call (used for system calls) |
| `EBREAK`        | Triggers a breakpoint (used for debugging) |

---

### Fence Instructions (Memory Ordering)

| **Instruction** | **Description** |
|-----------------|-----------------|
| `FENCE`         | Ensures memory operations complete in a specific order |
| `FENCE.I`       | Synchronizes instruction memory (flushes instruction cache) |

---

### Atomic Instructions (Atomic Memory Operations)

| **Instruction** | **Description** |
|-----------------|-----------------|
| `LR.W` / `LR.D` | Load-Reserved (read memory with intent to modify) |
| `SC.W` / `SC.D` | Store-Conditional (write memory only if no other core modified it) |
| `AMOSWAP.W` / `AMOSWAP.D` | Atomic swap |
| `AMOADD.W` / `AMOADD.D` | Atomic addition |
| `AMOXOR.W` / `AMOXOR.D` | Atomic XOR |
| `AMOAND.W` / `AMOAND.D` | Atomic AND |
| `AMOOR.W` / `AMOOR.D` | Atomic OR |
| `AMOMIN.W` / `AMOMIN.D` | Atomic minimum (signed) |
| `AMOMAX.W` / `AMOMAX.D` | Atomic maximum (signed) |
| `AMOMINU.W` / `AMOMINU.D` | Atomic minimum (unsigned) |
| `AMOMAXU.W` / `AMOMAXU.D` | Atomic maximum (unsigned) |

---

### Compressed Instructions (RISC-V C Extension)

| **Instruction** | **Description** |
|-----------------|-----------------|
| `C.ADDI`        | Compressed add immediate |
| `C.LI`          | Compressed load immediate |
| `C.LW`          | Compressed load word |
| `C.SW`          | Compressed store word |
| `C.J`           | Compressed jump |
| `C.BEQZ` / `C.BNEZ` | Compressed branch instructions |

---

### Floating-Point Instructions (RISC-V F & D Extensions)

| **Instruction** | **Description** |
|-----------------|-----------------|
| `FADD.S` / `FADD.D` | Floating-point addition |
| `FSUB.S` / `FSUB.D` | Floating-point subtraction |
| `FMUL.S` / `FMUL.D` | Floating-point multiplication |
| `FDIV.S` / `FDIV.D` | Floating-point division |
| `FSQRT.S` / `FSQRT.D` | Floating-point square root |
| `FSGNJ.S` / `FSGNJ.D` | Floating-point sign injection |
| `FCVT.W.S` / `FCVT.L.D` | Convert between integer and float |
