### Day 3 - 1/4/2023

## Traits

Traits are like interfaces and define a required amount of methods your struct needs to have.

```rust,edditable
fn main() {
    trait Cars {
        fn model(self) -> String;
        fn board(&self, number: i32) {
            println!("ABC{number}")
        }
    }

    struct Hundai {
        model :String
    }

    impl Cars for Hundai {
        fn model(self) -> String {
            self.model
        }
    }
}
```

Traits can be derived and implement default behaviors

* `Iterator` and `IntoIterator` for loops.
* `From` and `Into` to convert values.
* `Read` and `Write` for IO
* `Add, Mul` for operator overloading
* `Drop` for destructors

```rust
trait Cars {
    fn new() -> Self;
}

struct Hundai {
    model :String
}

impl Cars for Hundai {
    fn new() -> Self {
        Hundai{model: String::from("h") }
    }
}

impl Drop for Hundai {
    fn drop(&mut self) {
        println!("dropping here");
    }
}

fn main() {
    {
        let h = Hundai::new();
        println!("{}", h.model);
    }
}
```

## Generics

Are used to create definitions for items like function sign or structs, which
we can then use with many different concrete data types.

Placing generics in signature allow reusability.
Different types are allowed in the same struct

```rust
struct Point<T, U> {
    x: T,
    y: U,
}

fn first<T>(list: &[T]) -> &T {
    &list[0]
}

fn main() {
    println!("{}", first(&vec![1,2,3,5]));
    println!("{}", first(&vec!["a", "b"]));
    let p = Point{x:5, y: 10};
    println!("{}", p.x);
}
```

The usage of trait (interfaces) with generics exists its possible to use a trait bound
and fix an allowed type of generic, its possible to return
an `impl Trait` on a function.

```rust
use std::fmt::Display;

trait Form {
    fn area(&self) -> f32 ;
}

struct Square {
    side :f32
}

impl Form for Square {
    fn area(&self) -> f32 {
        self.side * self.side
    }
}

struct Circle {
    perimeter :f32
}

impl Form for Circle {
    fn area(&self) -> f32 {
        self.perimeter * self.perimeter * 3.14
    }
}

fn print_data(title: impl Display) -> impl Display {
    format!(" My area {title}")
}

fn main() {
    let forms: Vec<Box<dyn Form>> = vec![
        Box::new(Square{ side: 10.0 }),
        Box::new(Circle{perimeter: 10.0 })
    ];

    for f in forms.iter() {
        let area = f.area();
        println!("{}", print_data(area));
    }
}
```