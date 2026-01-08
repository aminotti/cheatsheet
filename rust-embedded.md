# Rust Embedded

* [Cross-compilation](#cross-compilation)

## Cross-compilation

```bash
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
