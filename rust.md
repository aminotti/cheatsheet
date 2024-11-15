# Rust

* [Cargo](#cargo)
* [Types scalaires](#types-scalaires)
* [Types composés et collections](#types-composes-et-collections)
  * [Slices](#slices)
  * [Vector](#vector)
  * [String](#string)
  * [HashMap](#hashmap)
* [Déclarations variable et constantes](#declarations-variable-et-constantes)
* [Opérateurs binaires](#operateurs-binaires)
* [Fonctions](#fonctions)
* [Control Flow](#control-flow)
* [Structures](#structures)
* [Enum](#enum)
* [Iterators](#iterators)
* [Match](#match)
* [Modules](#modules)
* [Generic types](#generic-types)
* [Traits](#traits)
* [Librairie standard](#librairie-standard)
  * [Printing and formating](#printing-and-formating)
  * [Erreurs](#erreurs)
* [Tests](#tests)

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

## Types composes et collections

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

### Slices

* pointeur et longeur stoqués dans **stack**
* Litteral string == string slice
* String slice : core language

```rust
let s = String::from("hello new world");
let hello : &str = &s[..5]; // hello
let new : &str = &s[6..9]; // new
let world : &str = &s[10..]; // world
let fullstring : &str = &s[..];
let fullstring : &str = &s;
let fullstring : &str = s.as_str();

// String literals
// data stocké dans binaire
let s = "Hello, world!";

// arg accept String ou string literal
fun ma_fc(arg: &str) {}

let a = [1, 2, 3, 4, 5];
let slice : &[i32] = &a[1..3];
assert_eq!(slice, &[2, 3]);
```

### Vector

```rust
// Valeurs en memoire next to each other
let v1: Vec<i32> = vec![1, 2, 3];
let mut v2 = Vec::new();
v2.push(5);

// Acces par index
/// Panic si pas de 3ème élément
let third: &i32 = &v1[2];
// Acces plus safe
let third: Option<&i32> = v1.get(2);
match third {
  Some(third) => println!("{third}"),
  None => println!("404"),
}

// Iterating over the Values
for i in &v1 {
  println!("{i}");
}
// Equivaut à
for i in v1.iter() {
  println!("{i}");
}
// Effectur un move (prend ownership)
for i in v1.into_iter() {
  println!("{i}");
}

// Modif element du vecteur
for i in &mut v2 {
  *i += 50;
}
```

### String

* standart library
* wrapper de vecteur de bytes

```rust
let mut s1 = String::new();
let s2 = "initial contents".to_string();
let s3 = String::from("initial contents");

// Updating a String
let s = "Hell";
// Prend pas ownership, &str transmis
s1.push_str(s);
s1.push('o');

// s1 is moved and can no longer be used
let s = s1 + "-" + &s2 + "-" + &s3;

// Iteration sur une chaîne
for c in "Зд".chars() {
  println!("{c}"); // affiche З puis д
}
// affiche 208 puis 151 puis 208 puis 180
for b in "Зд".bytes() {
    println!("{b}");
}

// String literals
let byte_escape = "I'm writing Ru\x73__!";
let unicode_codepoint = "\u{211D}";
let raw_str = r"No Escapes \x3F \u{211D}";
// # si besion de " dans la string
let quotes = r#"I said: Hello!""#;
// Si # dans string ajouter plus de #
let delimiter = r###"String with "##" "###;
let long_string = "String literals
    can span multiple lines.
    The linebreak and indentation here \
    can be escaped too!";

// ASCII (&str et String sont UTF8)
let bytestring: &[u8; 21] = b"A byte string";
let escaped = b"\x52\x75\x73\x74 as bytes";
let raw_bytestring = br"\u{211D} no escape";
```

### HashMap

```rust
use std::collections::HashMap;

let mut s = HashMap::new();
s.insert(String::from("Blue"), 10);
let key = String::from("Blue");
// copied() renvoi un Option<i32>
// au lieu de Option<&i32>
let v = s.get(&key).copied().unwrap_or(0);

//Itération
for (key, value) in &s {
  println!("{key}: {value}");
}

// Insert si clé existe pas
// entry() returns Enum Entry
// crée red avec valeur 45
s.entry(String::from("red")).or_insert(45);
// or_insert returns a mutable reference
// Blue existe, ne change pas sa valeur
s.entry(String::from("Blue")).or_insert(50);
```

## Declarations variable et constantes

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

// Raw identifiers to use reserved keywords
let r#match = "tennis"

// Lifetime
// a reference with an explicit lifetime
&'a i32
// a mutable reference with an explicit lifetime
&'a mut i32
```
## Operateurs binaires

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
      age: 19,
      sign_in_count: 1,
    }

  }

  // $mut self si on voulais modifié age
  // self si on transforme en autre chose
  // et invalid l'original pour le caller
  fn majeur(&self) -> bool {
    self.age => 18
  }
}

let u1 = User::new_user(String::from("joe"));

let u2 = User {
  email: String::from("martin@domain.tld"),
  ..u1 // u1 plus utilisable due to username
};
```

## Enum

```rust
enum MyMsg {
  Quit,
  Move { x: i32, y: i32 },
  Write(String),
  ChangeColor(i32, i32, i32),
}

impl MyMsg {
  fn call(&self) {
    // method body would be defined here
  }
}

let m = MyMsg::Write(String::from("hello"));
m.call();

enum IpAddrKind {
    V4 = 0,
    V6, // Ici V6 vaut 1
}

// enum converti en int using as u8
assert_eq!(IpAddrKind::V4 as u8, 0);
assert_eq!(IpAddrKind::V6 as u8, 1);

// Generics
enum Option<T> {
  None,
  Some(T),
}
let some_char = Some('e');
let absent_number: Option<i32> = None;
```

## Iterators

## Match

```rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
}
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

let c = Coin::Quarter(UsState::Alaska);
match c {
  Coin::Penny => 1,
  Coin::Nickel => 5,
  Coin::Dime => 10,
  Coin::Quarter(state) => {
    println!("{:?}!", state);
    25
  }
}

// Macthing with Option<T>
fn plus_one() -> Option<i32> {
  let x = Some(5);
  match x {
    None => None,
    Some(i) => Some(i + 1),
  }
}

// Catch-all pattern
let dice_roll = 9;
match dice_roll {
  3 => add_fancy_hat(),
  7 => remove_fancy_hat(),
  // Catch all, doit être en dernier
  o => move_player(o),
}
// Catch-all sans utiliser la value : _
match dice_roll {
  3 => add_fancy_hat(),
  7 => remove_fancy_hat(),
  _ => (), // Ne fait rien
}

// Equivalant de match : if let
let mut count = 0;
match coin {
  Coin::Quarter(s) => println!("{:?}!", s),
  _ => count += 1,
}
// Pareil que
let mut count = 0;
if let Coin::Quarter(s) = coin {
  println!("{:?}!", s),
} else {
  count += 1;
}
```

## Modules

Import

*fonction prefixé par nom du module dans le code*

```rust
use std::io::Result as IoResult;
use crate::my_module::*;
use std::{cmp::Ordering, io};
// import io et write
use std::io::{self, Write};
```

Definition

```rust
// src/lib.rs
mode my_module;


// src/my_module.rs
// src/my_module/mod.rs
// mod.r : old style, deprecated
mode sub_module;
// Code du module


// src/my_module/sub_module.rs
// src/my_module/sub_module/mod.rs
// Code submodule
```

Ou Definition inline

```rust
// src/lib.rs
mode my_module {
  pub mode sub_module {
    // Code submodule
  }
  // Code du module
}
```

## Generic types

```rust
struct Pt<X1, Y1> {
 x: X1,
 y: Y1,
}

impl<X1, Y1> Pt<X1, Y1> {
 fn mixup<X2, Y2>(self, other: Pt<X2, Y2>) -> Pt<X1, Y2> {
  ...
 }
}

// Implement que pour un type concret
impl Pt<f32, f32> {
 fn distance_from_origin(&self) -> f32 {
  ...
 }
}
```

## Traits

```rust
pub trait Summary {
 fn summarize_author(&self) -> String;
 // Default implementation
 fn summarize(&self) -> String {
  return self.summarize_author() 
 }
}

struct Tweet {}

impl Summary for Tweet {
 fn summarize(&self) -> String {
  format!("{}", self.content)
 }
}


// MyTrait a des implementations par defaut
impl MyTrait for Tweet {}


// Trait dans les appels de fonctions
fn notify(item: &(impl Summary + Display)) {}
fn notify<T: Summary + Display>(item: &T) {}

fn some_function<T, U>(t: &T, u: &U) -> i32
where
 T: Display + Clone,
 U: Clone + Debug,
{
}

// 1 seul type concret peut etre renvoyé
fn summarizable(switch: bool) -> impl Month {}
```

## Librairie standard

```rust
// Variable d'env boolean
std::env::var("MA_VAR_BOOL").is_ok();
 
// params CLI
std::env::args().collect();

// Quit avec code erreur 1
std::process::exit(1);
```

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

panic!();
```

### Erreurs

```rust
Result<T, E>.unwrap_or_else(||);
Result<T, E>.unwrap(); // panic
Result<T, E>.expect(""); // panic
// ? sur types qui impl FromResidual
Result<T, E>?

// Using match
let file = match file_result {
  Ok(file) => file,
  Err(e) => panic!("Error : {:?}", e);
};

// OurError implement From<io::Error>
fn get_u() -> Result<String, OurError> {
  // Error type io::Error
  let mut f = File::open("hello.txt")?; 
  let mut u = String::new();
  // Error type io::Error
  f.read_to_string(&mut u)?;
  Ok(u)
}

// Box<dyn Error> any type qui implement
// le trait std::error::Error
fn main() -> Result<(), Box<dyn Error>> {
  let f = File::open("hello.txt")?;
  Ok(())
}
```

## Tests

### Tests unitaires

* Dans fichier où sont les fonctions à tester
* ``cargo test <module_name>::``
* ``cargo test <func_name>``
* ``cargo test <prefix_func>``
* ``cargo test -- --ignored``
* ``cargo test -- --include-ignored``
* ``cargo test -- --show-output``

```rust
#[cfg(test)]
mod tests {
  // access outer module
  use super::*;

  #[test] 
  fn it_adds_two() {
    assert_eq!(4, add_two(2), "msg");
    assert_ne!(3, add_two(3));
  }

  #[test]
  fn another() {
    assert!(func2Test(&arg), "{}", arg);
    assert!(!func2Test(&arg)); 
    panic!("Make this test fail");
  }

  #[test]
  #[should_panic]
  fn greater_than_100() {
    panic!("Make this test pass");
  }

  #[test]
  #[should_panic(expected = "custom msg")]
  fn greater_than_100() {
    panic!("custom msg");
  }

  // Using Result<T, E>
  #[ignore] // test ignoré
  #[test]
  fn fc_t() -> Result<(), String> {
    if 2 + 2 == 4 {
      Ok(())  // Test pass
    } else {
      Err(String::from("error messager"))
    }
  }
}
```

## Tests d'intégration

* Sur fonctions publiques definies dans ``src/lib.rs``
* ``cargo test --test integration_test``

```bash
.
├── src/
│   └── lib.rs
└── tests/
    ├── common.rs
    └── my_crate_test.rs
```

```rust
// common.rs
pub fn setup() {
  // setup code
}
```

```rust
// my_crate_testt.rs
use adder;

mod common;

#[test]
fn it_adds_two() {
  common::setup();
  assert_eq!(4, adder::add_two(2));
}
```
