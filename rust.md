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
* [Librairie standard](#librairie-standard)

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
// Entier literal
98_222, 38_000_u16, 0xff, 0o77 (octal), 0b1111_0000, b'A' (byte)
// Floats
float32, float64
// Floats literal
0.01_f64, 1_000.000_1_f32
// Caractère UTF8 sur 4 bytes
char
// Boolean
bool

// Convertion d'un type
let v = 38_000_u16 as i64
// Taille maximum d'un type
u64::MAX
```

## Types composés

```rust
// Tuples
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup;
let x = tup.0;

// Array
let a: [i32; 5] = [1, 2, 3, 4, 5];
let a = [1, 2, 3, 4, 5];
let a = [3; 5];
let first = a[0];
```

* Slice ??
* Collections ??

## Déclarations variable et constantes

```rust
// Déclaration variable mutable
let mut ma_var: i32 = 6; 
// Déclaration de constante
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
// Déclaration variable static mutable
static mut MUTABLE_STATIC_VARIABLE: i32 = 5;
```

## Fonctions

```rust
fn my_function(ma_var: i32) -> i32 {
  let ret_var = {
    let x = 3;
    x + 1
  };
  ret_var + ma_var
}
```

## Control Flow

```rust
let my_number = if expression {
  1
} else if expression {
  2
} else {
  3
}


// loop
let result = loop {
  break 2 + 2;
};

'counting_up: loop {
  loop {
    if expression1 {
      break; // 2ème loop, pas de label
    }
    if expression2 {
      break 'counting_up; // 1er loop grâce au label
    }
  }
}


// While loop
while expression {
  break;
}


// For loop
let a = [10, 20, 30, 40, 50];

// equivaut à a.into_iter() : le contenu du tableau est move ou copy selon le type (ici copy)
for element in a {
  println!("the value is: {element}");
}

// Element du tableau passé par référence
for element in a.iter() {
  println!("the value is: {element}");
}

for (idx, element) in a.iter().enumerate() {
  println!("the value at index {idx} is: {element}");
}
```

## Structures

## Enum

## Iterators

## Librairie standard

  println!("5 in binary is {:08b}", 5); // Ecrit 5 en binaire sur 8bits : 00000101
  println!("12 in hexa is {:x}", 12); // Ecrit 12 en hexadecimal : C
  println!("Guess the number!");
