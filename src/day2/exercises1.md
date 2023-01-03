
## Exercises day 2 - Part I

* Simple struct which tracks health statistics.

```rust
struct User {
    name: String,
    age: u32,
    weight: f32,
}

impl User {
    pub fn new(name: String, age: u32, weight: f32) -> Self {
        return User{name, age, weight}
    }

    pub fn name(&self) -> &str {
        &self.name
    }

    pub fn age(&self) -> u32 {
        self.age
    }

    pub fn weight(&self) -> f32 {
        self.weight
    }

    pub fn set_age(&mut self, new_age: u32) {
        self.age = new_age
    }

    pub fn set_weight(&mut self, new_weight: f32) {
        self.weight = new_weight
    }
}

fn main() {
    let bob = User::new(String::from("Bob"), 32, 155.2);
    println!("I'm {} and my age is {}", bob.name(), bob.age());
}

#[test]
fn test_weight() {
    let bob = User::new(String::from("Bob"), 32, 155.2);
    assert_eq!(bob.weight(), 155.2);
}

#[test]
fn test_set_age() {
    let mut bob = User::new(String::from("Bob"), 33, 155.2);
    assert_eq!(bob.age(), 32);
    bob.set_age(33);
    assert_eq!(bob.age(), 33);
}
```

* Multiple structs and enums for a drawing library.

```rust
use std::ops::Add;

#[derive(Debug, Copy, Clone, PartialEq)]
pub struct Point {
    // add fields
    x :i32,
    y :i32,
}

impl Add for Point {
    type Output = Self;

    fn add(self, other: Self) -> Self {
        Self {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
}

impl Point {
    fn new(x :i32, y :i32) -> Point {
        Point{x, y}
    }
    fn magnitude(&self) -> f64 {
        f64::sqrt( (i32::pow(self.x, 2) + i32::pow(self.y, 2)).into())
    }

    fn dist(&self, p1 :Point) -> f64{
        f64::sqrt( (i32::pow(p1.x - self.x, 2) + i32::pow(p1.y - self.y, 2)).into())
    }
}

pub struct Polygon {
    points :Vec<Point>
}

impl Polygon {
    fn new() -> Polygon {
        Polygon{ points: vec![]}
    }

    fn add_point(&mut self, p :Point) {
        self.points.push(p)
    }
    fn left_most_point(&self) -> Option<&Point> {
       self.points.first()
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    fn round_two_digits(x: f64) -> f64 {
        (x * 100.0).round() / 100.0
    }

    #[test]
    fn test_point_magnitude() {
        let p1 = Point::new(12, 13);
        assert_eq!(round_two_digits(p1.magnitude()), 17.69);
    }

    #[test]
    fn test_point_dist() {
        let p1 = Point::new(10, 10);
        let p2 = Point::new(14, 13);
        assert_eq!(round_two_digits(p1.dist(p2)), 5.00);
    }

    #[test]
    fn test_point_add() {
        let p1 = Point::new(16, 16);
        let p2 = p1 + Point::new(-4, 3);
        assert_eq!(p2, Point::new(12, 19));
    }

    #[test]
    fn test_polygon_left_most_point() {
        let p1 = Point::new(12, 13);
        let p2 = Point::new(16, 16);

        let mut poly = Polygon::new();
        poly.add_point(p1);
        poly.add_point(p2);
        assert_eq!(poly.left_most_point(), Some(&p1));
    }

    #[test]
    fn test_polygon_iter() {
        let p1 = Point::new(12, 13);
        let p2 = Point::new(16, 16);

        let mut poly = Polygon::new();
        poly.add_point(p1);
        poly.add_point(p2);

        let points = poly.points.iter().cloned().collect::<Vec<_>>();
        assert_eq!(points, vec![Point::new(12, 13), Point::new(16, 16)]);
    }
}
```