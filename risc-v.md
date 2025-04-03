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
* `offset` = Immediate value added to `rs1` (memory address offset)
* `rd` = Destination register (for loads)
* `rs2` = Source register (for stores)
