# Rust Design patterns

## Type state pattern

```rust
#[derive(Debug)]
struct Locked;
#[derive(Debug)]
struct Unlocked;

#[derive(Debug)]
struct VaultData {
    login: String,
    password: String,
}

#[derive(Debug)]
struct Vault<State = Locked> {
    data: VaultData,
    state: State,
}

impl Vault {
    fn new(login: &str, password: &str) -> Vault<Locked> {
        Vault {
            data: VaultData {
                login: login.to_string(),
                password: password.to_string(),
            },
            state: Locked,
        }
    }
}

impl Vault<Locked> {
    fn unlock(self) -> Vault<Unlocked> {
        self.map_state(Unlocked)
    }
}

impl Vault<Unlocked> {
    fn lock(self) -> Vault<Locked> {
        self.map_state(Locked)
    }

    fn login(&self) -> &str {
        &self.data.login
    }

    fn password(&self) -> &str {
        &self.data.password
    }
}

impl<State> Vault<State> {
    fn version(&self) -> &str {
        "v1.0.0"
    }

    fn map_state<NewState>(self, state: NewState) -> Vault<NewState> {
        Vault::<NewState> {
            data: self.data,
            state,
        }
    }
}

fn main() {
    let v = Vault::new("anthony.minotti", "azerty");
    println!("{v:#?}");
    println!("Login : {}", v.version());
    let v = v.unlock();
    println!("{v:#?}");
    println!("Login : {}", v.login());
    println!("Password : {}", v.password());
    println!("Login : {}", v.version());
    let v = v.lock();
    println!("{:#?}", v.state);
}
```
