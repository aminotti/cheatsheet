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
