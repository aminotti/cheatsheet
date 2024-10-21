# Rust

* [Cargo](#cargo)
* [Types scalaires](#types-scalaires)
* [Types composés](#types-composés)
* [Déclarations variable et constantes](#déclarations-variable-et-constantes)
* [Fonctions](#fonctions)
* [Control Flow](#control-flow)
* [Structures](#structures)
* [Enum](#enum)
* [Iterators](#iterators)

## Cargo

```bash
# Update rust
rustup update
# Remove rust
rustup self uninstall

# Compile & run simple file
rustc main.rs

# Create project
cargo new [--lib] projectName
# Create project from existing directory
cargo init [--lib]
# Build
cargo build [--release]
# Compile and run Cargo project
cargo run
# Check compilation sans build (plus rapide au cours du dev)
cargo check
```

## Types scalaires

```rust
// Entiers
i8, i16, i32, i64, i128, isize
u8, u16, u32, u64, u128, usize
// Floats
float32, float64
// Caractère UTF8 sur 4 bytes
char
// Boolean
bool
```

## Types composés

* Tuples
* Array
* Slice ??
* Collections ??

## Déclarations variable et constantes

## Fonctions

```rust
fn my_function(ma_var: i32) -> i32 {
  ma_var
}
```

## Control Flow

## Structures

## Enum

## Iterators
