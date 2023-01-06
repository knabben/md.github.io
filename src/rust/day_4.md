### Day 4 - 1/5/2023

### Concurrency 


Starting with threads, variables are shared via scope

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let s = String::from("hello") ;
    thread::scope(|scope| {
        scope.spawn(|| {
            println!("str: {}", s.as_str());

            for i in 1..10 {
                println!("Count in thread: {i}! {s}");
                thread::sleep(Duration::from_millis(5));
            }
        });

        scope.spawn(|| {
            thread::sleep(Duration::from_millis(500));
            println!("new thread str: {}", s.as_str());
        });
    });
}
```

### Channels

Available as `Sender<T>` and `Receiver<T>`, uses the module str::sync:mpsc

Unbounded and async channels with `mpsc::channel` and bounded sync `mpsc::sync_channel`

```rust
fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let thread_id = thread::current().id();
        for i in 1..10 {
            tx.send(format!("Message {i}")).unwrap();
            println!("{thread_id:?}: sent Message {i}");
        }
        println!("{thread_id:?}: done");
    });
    thread::sleep(Duration::from_millis(100));

    for msg in rx.iter() {
        println!("Main: got {}", msg);
    }
}
```

### Shared state
* `Arc<T>` allows shared read-only access via its `arc.clone()` methods across threads.
* `Mutex<T>` ensure mutual exclusion and allows mutable access to T behind a read-only interface.
 
```rust
use std::thread;
use std::sync::Arc;

fn main() {
    let mut v = Arc::new("Shared data");
    let mut handles =  Vec::new();

    for _ in 1..5 {
        let v = v.clone();
        handles.push(thread::spawn(move || {
            println!("bla {v:?}")
        }))
    }

    handles.into_iter().for_each(|h| h.join().unwrap());
    println!("v: {v:?}");
}
```