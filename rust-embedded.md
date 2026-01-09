# Rust Embedded

* [Cross-compilation](#cross-compilation)
* [Startup code](#startup-code)

## Startup code

* Vector table
* Reset handler
* Exceptions handlers

## Linker Script

* attribut rwx aide linkier à placer automatiquement si pas defini dans sections.
* Readonly data (``.rodata``) : constantes, global (==static) variable immutable et string litérale. Stoqué en ROM (flash)
* Data initialisées (``.data``) : global (==static) variable mutable ou variable local (stack). Stoqué dans ROM (flash) et copiér dans RAM (sram) par le startup code pour pouvoir être modifié.
* Data non initialisées (``.bss``) : variable statics mutables et non mutables. ROM stoque uniquement la taile de ``.bss`` et startup script initialise à 0 le ``.bss`` en RAM.

## Cross-compilation

Minimum ``main.rs`` :
```rust
#![no_std]
#![no_main]

#[unsafe(no_mangle)]
fn main() -> ! {
    loop {}
}

#[panic_handler]
fn panic(_info: &core::panic::PanicInfo) -> ! {
    loop {}
}
```

```bash
# installation des outils
cargo install cargo-binutils
rustup component add llvm-tools-preview

# List available target for cross-compilation
rustup target --list
# Ajout d'un target
rustup target add thumbv7em-none-eabihf

# Compilation
cargo build --taget thumbv7em-none-eabihf
# To run cargo build without --target :
cat .cargo/config.toml 
[build]
target = "thumbv7em-none-eabihf"
```

## STM32F407VET6

* Foundation, Arm Cortex-M4, 100 pins LQFP, 512Ko flash, -40°C et +85°C => d'après [naming convention](https://www.digikey.com/en/maker/tutorials/2020/understanding-stm32-naming-conventions)
* Arm Cortex-M4 with FPU => Armv7E-M => **thumbv7em-none-eabihf**
