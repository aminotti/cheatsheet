# Rust Design patterns

* [Le Pattern Builder (Constructeur)](#builder-pattern)
* [Le Pattern Strategy (via les Traits)](#strategy-pattern)
* [Le Pattern Typestate](#type-state-pattern)
* [RAII (Resource Acquisition Is Initialization)](#raii)
* [Le Newtype Pattern](#newtype-pattern)
* [L'Extension via les Traits ("Extension Traits")](#)

## Builder Pattern

Comme Rust n'a pas d'arguments par défaut ni de surcharge de fonctions, le Builder permet de créer des objets complexes étape par étape de manière lisible.

```rust
#[derive(Debug)]
struct Serveur {
    host: String,
    port: u16,
    tls: bool,
}

struct ServeurBuilder {
    host: String,
    port: Option<u16>,
    tls: Option<bool>,
}

impl ServeurBuilder {
    // 1. On initialise le builder avec des valeurs par défaut ou minimales
    fn new(host: &str) -> Self {
        Self {
            host: host.to_string(),
            port: None,
            tls: None,
        }
    }

    // 2. Des méthodes pour configurer chaque champ (on retourne Self)
    fn port(mut self, port: u16) -> Self {
        self.port = Some(port);
        self
    }

    fn avec_tls(mut self, active: bool) -> Self {
        self.tls = Some(active);
        self
    }

    // 3. La méthode finale qui assemble l'objet
    fn build(self) -> Serveur {
        Serveur {
            host: self.host,
            port: self.port.unwrap_or(80), // Valeur par défaut
            tls: self.tls.unwrap_or(false),
        }
    }
}

fn main() {
    // Utilisation fluide (Fluent Interface)
    let mon_serveur = ServeurBuilder::new("localhost")
        .port(443)
        .avec_tls(true)
        .build();

    println!("{:?}", mon_serveur);
}
```
## Strategy Pattern

```rust
trait MethodePaiement {
    fn payer(&self, montant: u32);
}

struct Paypal;
struct CarteBancaire;
struct Bitcoin;

impl MethodePaiement for Paypal {
    fn payer(&self, montant: u32) {
        println!("Paiement de {}€ via PayPal (redirection...).", montant);
    }
}

impl MethodePaiement for CarteBancaire {
    fn payer(&self, montant: u32) {
        println!("Paiement de {}€ par Carte. Transaction sécurisée.", montant);
    }
}

struct Panier<T: MethodePaiement> {
    montant: u32,
    strategie: T,
}

impl<T: MethodePaiement> Panier<T> {
    fn valider_achat(&self) {
        self.strategie.payer(self.montant);
    }
}
```

```rust
// Static dispatch(Choix fait à la compilation) - 0 cost abstraction 
let panier = Panier { montant: 50, strategie: Paypal };
panier.valider_achat();

// Dynamic dispatch (Choix fait à l'execution) - Trait Objects 
struct PanierDynamique {
    montant: u32,
    strategie: Box<dyn MethodePaiement>, // Choix fait à l'exécution
}

fn main() {
    let choix_utilisateur = "bitcoin";
    
    let methode: Box<dyn MethodePaiement> = match choix_utilisateur {
        "paypal" => Box::new(Paypal),
        _ => Box::new(CarteBancaire),
    };

    let panier = PanierDynamique { montant: 100, strategie: methode };
    panier.strategie.payer(panier.montant);
}
```

## Type state pattern

*Pour utiliser les enum, il faut voir avec les const et types génériques : https://github.com/rust-lang/rust/issues/95174*

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
