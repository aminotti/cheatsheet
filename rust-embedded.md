# Rust Embedded

* [Cross-compilation](#cross-compilation)

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
```

## STM32F407VET6

* Foundation, Arm Cortex-M4, 100 pins LQFP, 512Ko flash, -40°C et +85°C => d'après [naming convention](https://www.digikey.com/en/maker/tutorials/2020/understanding-stm32-naming-conventions)
* Arm Cortex-M4 with FPU => Armv7E-M => **thumbv7em-none-eabihf**
