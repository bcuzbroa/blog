---
title: ZeroCostGame CTF
author: bcuzbroa
pubDatetime: 2026-02-12
slug:
featured: true
draft: false
readingTime: 4 min read
tags:
  - CTF
  - ZeroCostGame
  - Rust
description:
    This article will go through my project ZeroCostGame. A Capture The Flag (CTF) style game designed to help with learning Rust by solving coding challenges and winning flags. 
---



- **Reading time** : 4 min.

- **Source code** : [github.com/bcuzbroa/ZeroCostGame](https://github.com/bcuzbroa/ZeroCostGame)

![ZeroCostGame](src/assets/images/ZeroCostGame.png)

## Disclaimer

If you want to try the game before reading the article, find it here : **[ctfd.bcuzbroa.fr](https://ctfd.bcuzbroa.fr)**

## Motivation

Some features of **Rust** are often described as a "zero-cost abstraction" (especially iterators). To make the learning journey more interactive, I developed **ZeroCostGame**, a Capture The Flag inspired project designed to teach Rust through puzzles and automated validation.

## What is the ZeroCostGame?

**ZeroCostGame** is a collection of coding challenges where the player must implement specific logic to "win" a flag. It uses a black-box Oracle to wrap and test your code. The Oracle reveals the flag if your implementation is correct. 

The project is structured as a Rust **Workspace**:
* `challenges/`: Your playground. This is where you write your solutions.
* `oracle/`: The "Black Box" containing encrypted flags and validation logic.

---

## Under the hood:
*This section shows how the oracle works in the game*

### Creating the challenge

First of all, to create a challenge I need to write a function that I find interesting to develop and think about for the player's Rust journey. Then, I needed a structure to store the challenge information, the future key, the tests and the logic associated to it. The following trait is therefore defined in `logic.rs`:
```rust
pub trait ChallengeVerifier {

    type Output : AsRef<[u8]>; //This type can be hashed

    fn id() -> &'static str;

    fn run_code(path : &str) -> Self::Output;

    fn check_code(path: &str) -> bool;

    // Default implementation for compilation purposes
    // &[0] is later replaced with the encrypted flag
    fn secret_data() -> &'static [u8]{&[0]}

    fn run_external(path: &str, wrapper_main: &str) -> String {
        wrapper(path, wrapper_main, Self::id())
    }
}
```

This trait is implemented for each challenge in separate files.

To create the encrypted flag, let's say FLAG{my_blog}, I implemented the `encryp_flag.rs` module in `oracle\bin\`. The encryption uses the awesome [XChaCha20Poly1305](https://en.wikipedia.org/wiki/ChaCha20-Poly1305) algorithm with a key derived from the `run_code` output method !

The module generates the ciphertext which is later stored in `secret_data`. Our trait is now fully implemented, lets give the challenge a try !

### Player Experience

Initially, a challenge is just a `.rs` file implemented by the player. I needed a structure to store the challenge information, the key, the tests and the logic associated with it. The following trait is thus defined in `logic.rs`:


In the `ChallengeVerifier` trait, the  `run_external` function wraps the player code and executes its implemented function (while ignoring its main using some extraordinary regex to do so). The wrapper compiles the code then executes it with the values of our choice. This allows the system to determine if the player respected the challenge or not.

Ok now, we can run the player code, the `check_code` function tests the code.

If `check_code` returns `true`, then the code is run with a specific input to later build the key that should be the correct one, as the tests passed. The flag is then decrypted with ` decrypt.rs`:
```rust
use chacha20poly1305::{
    aead::{Aead, KeyInit},
    XChaCha20Poly1305,
};


pub fn decrypt(
    key: &[u8; 32],
    nonce: &[u8; 24],
    cipher: &[u8],
) -> Option<Vec<u8>> {
    let cipher_algo = XChaCha20Poly1305::new(key.into());
    cipher_algo.decrypt(nonce.into(), cipher).ok()
}
```

the `decrypt` function is called by the `solve` function which is defined for any type `V` that implements the trait `ChallengeVerifier` suchs as :

```rust
pub fn solve<V : ChallengeVerifier>(path: &str) -> Option<String>{
    if !V::check_code(path){ 
        println!("Tests not passed.");
        return None
    }
    let out = V::run_code(path);
    let key = blake3::hash(out.as_ref()); 
    let nonce = [0x42u8; 24]; //Static
    let cipher = V::secret_data();
    let decrypted = crypto::decrypt(key.as_bytes(), &nonce, cipher)?;
    let d_flag = String::from_utf8(decrypted).ok();
    d_flag
}
```

Here is where the tests are passed and the function decrypts the flag.

---
## Testing the player code

As the player code comes from external files, having oracle's point of view, I decided to test its code using the wrapper. Let's take a look at `challenge3`'s tests:

```rust
fn check_code(path: &str) -> bool {
    let test_wrapper = r#"
        fn main() {
            let t1 = value_or_zero(Some(10)) == 10 && value_or_zero(None) == 0;
            
            let list = vec![Some(5), None, Some(15), None];
            let sum = sum_options(list);
            
            let empty_sum = sum_options(vec![]);

            let t2 = sum == 20;
            let t3 = empty_sum == 0;     

            print!("{}", t1 && t2 && t3 );
        }
    "#;
    Self::run_external(path, test_wrapper) == "true"
}
```
This function takes part in the `ChallengeVerifier` trait implementation. The tests are directly written in a main function that will then be replaced with the original player's main. Then the `print!()` will print either `false` or `true` depending on tests results

---

The `main.rs` file finally organizes the overall challenge:

```rust
fn main() {

    let args= Args::parse();

    let path = &args.path;

    // Verify the directory exists before proceeding
    if !std::path::Path::new(&path).exists() {
        println!("Error: The challenge path: '{}' does not exist.", path);
        return;
    }

    let re = Regex::new(r"(\d+)").unwrap();

    let challenge_id = re.captures(path)
        .and_then(|cap| cap.get(1))
        .map(|m| m.as_str())
        .unwrap();
    
    match challenge_id {
        "0" => run_challenge::<Verifier0>("Sanity Check"    , path),
        "1" => run_challenge::<Verifier1>("Ownership"       , path),
        [...]
        "10" => run_challenge::<Verifier10>("Generic Master" , path),
        
        _ => println!("Challenge{} does not exist yet or is not implemented", challenge_id)
    }
}

fn run_challenge<V: ChallengeVerifier>(name: &str, path: &str){
    match solve::<V>(path){ //TURBOFISH
        Some(flag) => println!("{}\n✨ {} ✨ verified: {}", banner(), name, flag),
        None  => println!("❌❌ {}: NOPE ❌❌ Do you even try ??", name)
    }
}
```

---

The overall challenge flow is as follow:

![Challenge_graph](@/assets/images/challenge-draw.png)
I create the challenge then generate the encrypted flag. When the player plays, if its code passes the tests, the flag is decrypted using the key generated by the tests. (The key is derived from the Challenge output).

---

### Note & huge cashprize

I will give $5 to the first person that pwns the oracle. By this I mean, find a way to retrieve the flags without doing the challenges. I guess it is possible to reverse it but I'm not yet used to reversing complex rust projects. Contact me if you manage to crack it.

![Meme](@/assets/images/meme_reverse.jpg)

Thank you for reading !