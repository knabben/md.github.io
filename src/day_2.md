### Day 2 - 1/3/2023

Structs are cool, methods can access the struct pointer
Possible to use a tuple struct - normally used for single field wrappers

```rust
enum TShirt {
    L,
    M,
    P,
}

struct Newtons(f64);

struct Member {
    name :String,
    force :Newtons,
    tshirt :TShirt,
}

impl Member {
    fn set_force(&mut self, force :Newtons) {
       self.force = force 
    }
}
```

Usage of method receivers:

* &self : borrows the object from the caller using a shared and immutable reference.
* &mut self: borrows the object from the caller using a unique and mutable reference.
* self: takes ownership of the object and moves it away from the caller.
* none: status method on the struct.

after taking ownership with self, the variable is moved and cannot be borrowed back.

### Pattern matching

This is an important feature of switch case that can be extended to more complex pattterns
latter, 

```rust
enum Result {
    Ok(i32),
    Error(String),
}

fn main() {
    let has_result = Result::Error("msg".to_string());
    match has_result {
        Result::Ok(n) => println!("received {n}"),
        _ => println!("default"),
    }
}
```

Deconstructuring enums/struct and Arrays - 

```rust,editable
enum Temperature {
    Celsius(i32),
}

struct SomeStruct {
    a: u32,
    b: u32,
}

fn main() {
   let ss = SomeStruct{a: 2000, b: 0 };
    match ss {
        SomeStruct { a: 1, b } => println!(" a = {b} "),
        SomeStruct { .. } => println!(" default a "),
    }

    // match guards
    let temp = Temperature::Celsius(36);
    match temp {
        Temperature::Celsius(t) if t > 40 => println!("Is high {t}"),
        Temperature::Celsius(t) => println!("Is high {t}"),
    }
}
```
