# Rust

* [Cargo](#cargo)
* [Types scalaires](#types-scalaires)
* [Types composés](#types-composés)
* [Déclarations variable et constantes](#déclarations-variable-et-constantes)
* [Opérateurs binaires](#opérateurs-binaires)
* [Fonctions](#fonctions)
* [Control Flow](#control-flow)
* [Structures](#structures)
* [Enum](#enum)
* [Iterators](#iterators)
* [Match](#match)
* [Librairie standard](#librairie-standard)
  * [Printing and formating](#printing-and-formating)

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
# Check compilation sans build (plus rapide)
cargo check
```

## Types scalaires

```rust
// Entiers
i8, i16, i32, i64, i128, isize
u8, u16, u32, u64, u128, usize
// Entier literal
98_222, 38_000_u16, 0xff, 0o77 (octal)
0b1111_0000, b'A' (u8)
// Floats
float32, float64
// Floats literal
0.01_f64, 1_000.000_1_f32
// Caractère UTF8 sur 4 bytes
char
// char literal
'e', '\n', '\u{1f600}'
// Boolean
bool

// type casting
let v = 38_000_u16 as i64;
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
* Collections (String, Vector, HashMap) ??

## Déclarations variable et constantes

```rust
// Déclarations variable
let mut ma_var = String::from("hello");
let ma_ref = &mut ma_var;
let ref ma_ref_bis = ma_var;
assert_eq!(*ma_ref, *rma_ref_bis);

// Déclaration de constante
const THREE_HOURS_IN_MINUTES: u32 = 3 * 60;

// Déclaration variable static mutable
static mut MUTABLE_STATIC_VARIABLE: i32 = 5;
```
## Opérateurs binaires

```rust
let (g, h) = (0x1, 0x2);

let bitwise_and = g & h;  // => 0
let bitwise_or = g | h;   // => 3
let bitwise_xor = g ^ h;  // => 3
let right_shift = g >> 2; // => 0
let left_shift = h << 4;  // => 32
```

## Fonctions

```rust
fn my_func(ma_var:  &mut String) -> i32 {
  let ret_var = {
    let x = 3;
    x + 1
  };
  ret_var
}

fn ma_func(ref d: char) {
  // d est une ref,
  // mais l'appel à ma_func() se fait sans &
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
      break 'counting_up; // 1er loop label
    }
  }
}


// While loop
while expression {
  break;
}


// For loop
let a = [10, 20, 30, 40, 50];

// equivaut à a.into_iter()
// item sont move ou copy selon le type
for element in a {
  println!("the value is: {element}");
}

// Element du tableau passé par référence
for element in a.iter() {
  println!("the value is: {element}");
}

for (idx, element) in a.iter().enumerate() {
  println!("{element} at index {idx}.");
}
```

## Structures

```rust
// Unit-Like Structs
struct AlwaysEqual;
let subject = AlwaysEqual;

// Tuple struct
struct Color(i32, i32, i32);
let (a, b, c) = Color(0, 0, 0)

#[derive(Debug)]
struct User {
  active: bool,
  username: String,
  age: u8,
  sign_in_count: u64,
}

impl User {
  fn new_user(username: String) -> Self {
    active: true;

    Self {
      active,
      username,
      sign_in_count: 1,
    }

  }

  fn majeur(&self) -> bool {
    self.age => 18
  }
}

let user1 = User::new_user(String::from("j.martin"));

let user2 = User {
  email: String::from("martin@domain.tld"),
  ..user1  // user1 plus utilisable : username moved
};
```

## Enum

```rust
enum Message {
  Quit, // no data associated with it at al
  Move { x: i32, y: i32 },  // named fields, like a struct
  Write(String),  // includes a single String
  ChangeColor(i32, i32, i32),  // includes three i32 values
}

impl Message {
  fn call(&self) {
    // method body would be defined here
  }
}

let m = Message::Write(String::from("hello"));
m.call();
```

## Iterators

## Match

## Librairie standard

### Printing and formating

```rust
println!("{country}", country = "France");
println!("{} {}", 1, 3);
println!("{0} {1} {0}", "Rust", "is");
println!("binary {:08b}, octal {:o}, hexa {:x}",5, 5, 5);
println!("{:?}", ma_struct); // debug
println!("{:#?}", ma_struct); // pretty debug

eprintln!("This is an error\n"); // stderr

format!("{}", var);

dbg!()
```
