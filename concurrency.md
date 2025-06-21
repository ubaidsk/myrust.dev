# Concurrency

Learn how to write programs that do multiple things at once safely.

## How do I write programs that do many things at once, like multi-threading in C++ or Python?

You can create separate execution paths within your program. The language ensures data safety across these paths at compile time, preventing data races.

**Basic threading:**

```rust
use std::thread;
use std::time::Duration;

fn main() {
    // Create a new thread
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("Thread: count {}", i);
            thread::sleep(Duration::from_millis(100));
        }
    });

    // Main thread continues
    for i in 1..5 {
        println!("Main: count {}", i);
        thread::sleep(Duration::from_millis(150));
    }

    // Wait for the thread to finish
    handle.join().unwrap();
}
```

**Moving data into threads:**

```rust
use std::thread;

fn main() {
    let data = vec![1, 2, 3, 4, 5];

    let handle = thread::spawn(move || {  // 'move' transfers ownership
        let sum: i32 = data.iter().sum();
        println!("Sum from thread: {}", sum);
    });

    // data is no longer available in main thread
    handle.join().unwrap();
}
```

Compare to C++:
```cpp
#include <thread>
#include <vector>
#include <iostream>

void worker_function(std::vector<int> data) {
    int sum = 0;
    for (int x : data) sum += x;
    std::cout << "Sum: " << sum << std::endl;
}

int main() {
    std::vector<int> data = {1, 2, 3, 4, 5};
    std::thread t(worker_function, data);  // Copies data
    t.join();
}
```

Compare to Python:
```python
import threading
import time

def worker_function():
    for i in range(1, 10):
        print(f"Thread: count {i}")
        time.sleep(0.1)

thread = threading.Thread(target=worker_function)
thread.start()

for i in range(1, 5):
    print(f"Main: count {i}")
    time.sleep(0.15)

thread.join()
```

**Multiple threads processing data:**

```rust
use std::thread;

fn main() {
    let numbers = vec![1, 2, 3, 4, 5, 6, 7, 8];
    let mut handles = vec![];

    for chunk in numbers.chunks(2) {
        let chunk = chunk.to_vec();  // Create owned copy
        let handle = thread::spawn(move || {
            let sum: i32 = chunk.iter().sum();
            println!("Chunk sum: {}", sum);
            sum
        });
        handles.push(handle);
    }

    // Collect results from all threads
    let mut total = 0;
    for handle in handles {
        total += handle.join().unwrap();
    }

    println!("Total sum: {}", total);
}
```

The compiler ensures:
- Data moved to threads cannot be accessed from other threads
- No data races at compile time
- Memory safety across thread boundaries

Types that can be moved between threads are **Send**. Types that can be shared between threads are **Sync**.

Most standard types are both Send and Sync. The compiler automatically figures this out and prevents unsafe sharing.

These execution paths are called **threads**, and the safety guarantees are **Send** and **Sync**.

## How do I safely share data between different parts of my program running at the same time?

**Shared mutable data with locks:**

When multiple threads need to modify the same data, you use a lock to ensure only one thread can access it at a time.

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    // Shared counter protected by a lock
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);  // Clone the reference
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();  // Acquire lock
            *num += 1;  // Safely modify data
            // Lock automatically released when 'num' goes out of scope
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Final count: {}", *counter.lock().unwrap());
}
```

Compare to C++:
```cpp
#include <thread>
#include <mutex>
#include <vector>
#include <memory>

int main() {
    auto counter = std::make_shared<int>(0);
    std::mutex mtx;
    std::vector<std::thread> threads;

    for (int i = 0; i < 10; ++i) {
        threads.emplace_back([counter, &mtx]() {
            std::lock_guard<std::mutex> lock(mtx);
            (*counter)++;
        });
    }

    for (auto& t : threads) {
        t.join();
    }

    std::cout << "Final count: " << *counter << std::endl;
}
```

Compare to Python:
```python
import threading

counter = 0
lock = threading.Lock()

def increment():
    global counter
    with lock:
        counter += 1

threads = []
for _ in range(10):
    t = threading.Thread(target=increment)
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print(f"Final count: {counter}")
```

**Message passing between threads:**

Instead of sharing mutable data, you can send messages between threads. This is often safer and easier to reason about.

```rust
use std::sync::mpsc;  // multi-producer, single-consumer
use std::thread;
use std::time::Duration;

fn main() {
    let (sender, receiver) = mpsc::channel();

    // Spawn producer threads
    for i in 0..3 {
        let sender = sender.clone();
        thread::spawn(move || {
            for j in 0..5 {
                let message = format!("Thread {} message {}", i, j);
                sender.send(message).unwrap();
                thread::sleep(Duration::from_millis(100));
            }
        });
    }

    // Drop original sender so receiver knows when all are done
    drop(sender);

    // Receive messages
    for received in receiver {
        println!("Got: {}", received);
    }
}
```

**More complex communication patterns:**

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let (task_sender, task_receiver) = mpsc::channel();
    let (result_sender, result_receiver) = mpsc::channel();

    // Worker thread
    thread::spawn(move || {
        for task in task_receiver {
            let result = task * task;  // Process the task
            result_sender.send(result).unwrap();
        }
    });

    // Send tasks
    for i in 1..6 {
        task_sender.send(i).unwrap();
    }
    drop(task_sender);  // Signal no more tasks

    // Collect results
    for result in result_receiver {
        println!("Result: {}", result);
    }
}
```

Compare to Python queues:
```python
import queue
import threading

def worker(q, results):
    while True:
        task = q.get()
        if task is None:
            break
        result = task * task
        results.put(result)
        q.task_done()

q = queue.Queue()
results = queue.Queue()

t = threading.Thread(target=worker, args=(q, results))
t.start()

for i in range(1, 6):
    q.put(i)

q.put(None)  # Signal worker to stop
t.join()

while not results.empty():
    print(f"Result: {results.get()}")
```

Key advantages:
- No shared mutable state means no data races
- Clear ownership of data
- Compiler prevents sending unsafe types between threads
- Backpressure handling built in

The lock type is called **Mutex** and the message passing system uses **channels**.
