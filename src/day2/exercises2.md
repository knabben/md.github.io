
## Exercises Day 2 - Part II

* luhn algorithm 

```rust
pub fn luhn(cc_n: &str) -> bool {
    let mut sum = 0;
    let cc_number :String = cc_n.chars().filter(|c| !c.is_whitespace()).collect();
    let length = cc_number.len();
    let parity = length % 2;

    for (j, x) in cc_number.chars().enumerate() {
        let cc = cc_number.chars().nth(j).unwrap().to_digit(10);
        match cc {
            None => {}
            Some(mut n) => {
                if j % 2 == 0 {
                    n *= 2;
                    if n > 9 {
                        n -= 9;
                    }
                }
                sum += n;
            }
        }
    }
    if sum == 0 {
        false
    } else {
        println!("{} {}", sum % 10, sum);
        sum % 10 == 0
    }
}

#[test]
fn test_non_digit_cc_number() {
    assert!(!luhn("foo"));
}

#[test]
fn test_empty_cc_number() {
    assert!(!luhn(""));
    assert!(!luhn(" "));
    assert!(!luhn("  "));
    assert!(!luhn("    "));
}

#[test]
fn test_single_digit_cc_number() {
    assert!(!luhn("0"));
}

#[test]
fn test_two_digit_cc_number() {
    assert!(luhn(" 0 0 "));
}

#[test]
fn test_valid_cc_number() {
    assert!(luhn("4263 9826 4026 9299"));
    assert!(luhn("4539 3195 0343 6467"));
    assert!(luhn("7992 7398 7121 1114"));
}

#[test]
fn test_invalid_cc_number() {
    assert!(!luhn("4223 9826 4026 9299"));
    assert!(!luhn("4539 3195 0343 6476"));
    assert!(!luhn("8273 1232 7352 0569"));
}

#[allow(dead_code)]
fn main() {}
```
