# Rust Embedded

* [Startup code](#startup-code)
* [Cross-compilation](#cross-compilation)
* [Linker Script](#linker-script)

## Startup code
* En tout debut de la FLASH on a le pointeur de stack (32bits), puis juste après la table de vecteurs (offset 0x0000 0004)
* Vector table : création auto avec ``cargo install svd-vector-gen && svd-vector-gen`` mais vérifier quand même avec le datasheet. Elle contient les adresses des fonctions à appeler lors d'exceptions, d'interuptions,...
* Reset handler
* Exceptions handlers

``extern "C"`` : force l'usage de la convention C ABI d'appel de fonctions, qui permet à la fonction appelante et la fonction appelée de se mettre d'accord sur le passage des paramètres, la valeur de retour, setup de la stack frame,... Nécessaire car notre MCU est un Cortex-M qui respoect l'ARM Embedded ABI qui est proche de la C ABI.

## Linker Script

Le compiler transforme le code en fichiers objects contenant les differentes sections et l'editeur de lien (linker) regroupe ces sections dans un unique fichier elf

1. Le linker va assigner les adresse des differentes sections dans le fichier elf en respectant le plan d'adressage défini dans le linker script
2. l'outil de programmation va se servire des adresses de sections définies dans le fichier elf pour les positionner dans le microcontroller

* attribut rwx aide linker à placer automatiquement si pas defini dans sections.
* Instruction section ``.text``.
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

# Utiliser les commandes suivantes pour vérifié les adresse de symnboles avec le contenu du vector table
# Check des adresse dans le elf
cargo objdump -- -h <executable>
# Dump d'une section
cargo readobj -- -x .data <executable>
# Pour voir toutes les infos
cargo readobj -- -all <executable>

# Flasher le firmware avec SWD
# Voir le MCU est supporté
probe-rs chip list|grep "STM32F4"
# Flash
cargo flash --chip STM32F407VE [--release]
# Debug
probe-rs attach --chip STM32F407VE --connect-under-reset target/thumbv7em-none-eabihf/debug/<my_binary> 
```

## STM32F407VET6

* Installer``STM32CubeCLT`` pour récupérer le fichier svd dans ``/opt/st/stm32cubeclt_1.20.0/STMicroelectronics_CMSIS_SVD/STM32F407.svd``
* Foundation, Arm Cortex-M4, 100 pins LQFP, 512Ko flash, -40°C et +85°C => d'après [naming convention](https://www.digikey.com/en/maker/tutorials/2020/understanding-stm32-naming-conventions)
* Arm Cortex-M4 with FPU => Armv7E-M => **thumbv7em-none-eabihf**
* Little Endian : en mémoire le premier octet est le dernier et le dernier octet est le premier (ex pour une valeur sur 64bit, le 1 octet est le dernier et le 8eme octet est le premier)
