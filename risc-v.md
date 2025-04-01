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
