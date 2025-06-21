# Memory and Data Management

Understanding how this language manages memory is key to writing efficient, safe programs.

## How does this language manage memory without a garbage collector like Python or manual `new`/`delete` like C++?

Every piece of data has exactly one "owner" at any time. When the owner goes away, the data is automatically cleaned up.

Think of it like having a single key for each room. Only the person with the key can access that room. When they leave the building, the room gets locked and cleaned automatically.

**Python approach (Garbage Collection):**
```python
# Python automatically cleans up when no one references the data
data = [1, 2, 3]  # Python manages this memory
# When 'data' goes out of scope, garbage collector cleans up later
```

**C++ approach (Manual Management):**
```cpp
// You must manually manage memory
int* data = new int[3]{1, 2, 3};  // You allocate
delete[] data;  // You must remember to free
```

**This language's approach:**
```rust
{
    let data = vec![1, 2, 3];  // 'data' owns this vector
    // Use data here
}  // 'data' goes out of scope, memory automatically freed
```

When you move data from one variable to another, the ownership transfers:

```rust
let data1 = vec![1, 2, 3];  // data1 owns the vector
let data2 = data1;          // ownership moves to data2
// data1 can no longer be used
// Only data2 can access the vector now
```

Compare to C++:
```cpp
std::vector<int> data1 = {1, 2, 3};
std::vector<int> data2 = data1;  // This copies the entire vector
// Both data1 and data2 exist, using twice the memory
```

This system prevents:
- Memory leaks (forgetting to call `delete`)
- Double-free errors (calling `delete` twice)
- Use-after-free bugs (accessing freed memory)

The compiler enforces these rules at compile time. You get C++ performance with Python-like memory safety.

This concept is called **ownership**.

## How can I pass data around functions without copying it, similar to C++ references or Python object passing?

You can let functions "look at" or "use" your data without giving away ownership. It's like lending a book - you still own it, but someone else can read it.

**Read-only access (multiple readers allowed):**

```rust
fn print_length(data: &Vec<i32>) {  // Function borrows data to read it
    println!("Length: {}", data.len());
}

fn main() {
    let my_data = vec![1, 2, 3];
    print_length(&my_data);  // Lend data to function
    print_length(&my_data);  // Can lend again
    // my_data is still usable here
}
```

**Mutable access (only one editor at a time):**

```rust
fn add_item(data: &mut Vec<i32>) {  // Function borrows data to modify it
    data.push(4);
}

fn main() {
    let mut my_data = vec![1, 2, 3];
    add_item(&mut my_data);  // Lend data for modification
    // my_data is modified and still usable here
}
```

Compare to C++:
```cpp
// Read-only reference
void print_length(const std::vector<int>& data) {
    std::cout << "Length: " << data.size() << std::endl;
}

// Mutable reference
void add_item(std::vector<int>& data) {
    data.push_back(4);
}
```

Compare to Python:
```python
# Python passes object references automatically
def print_length(data):
    print(f"Length: {len(data)}")

def add_item(data):
    data.append(4)  # Modifies the original list
```

Key rules:
- You can have many read-only accesses at the same time
- You can have only one mutable access at a time
- You cannot mix read-only and mutable access

This prevents data races and ensures memory safety without runtime overhead.

This concept is called **borrowing**.

## Why do I see errors about 'lifespans' when working with shared data, and how do I fix them?

The compiler needs to ensure that shared data remains valid for as long as it's being used. It prevents accessing memory that has been freed.

Think of it like borrowing a library book. The library needs to know you'll return it before closing time.

**Problem example:**
```rust
fn get_first_item() -> &i32 {  // This won't compile
    let data = vec![1, 2, 3];
    &data[0]  // Trying to return a reference to local data
}  // data gets destroyed here, but we're returning a reference to it!
```

This is like a C++ dangling pointer:
```cpp
int* get_first_item() {  // Dangerous C++ code
    std::vector<int> data = {1, 2, 3};
    return &data[0];  // Returning pointer to local data
}  // data destroyed, pointer now invalid
```

**Solution - Keep data alive longer:**
```rust
fn main() {
    let data = vec![1, 2, 3];     // data lives in main
    let first = get_first(&data);  // Safe to borrow
    println!("First: {}", first);
}  // data destroyed after we're done with the reference

fn get_first(data: &Vec<i32>) -> &i32 {
    &data[0]  // Safe - data outlives this function
}
```

**Another solution - Return owned data:**
```rust
fn get_first_item() -> i32 {
    let data = vec![1, 2, 3];
    data[0]  // Return a copy, not a reference
}
```

The compiler tracks how long data lives and ensures references don't outlive the data they point to. This prevents:
- Dangling pointers
- Use-after-free errors
- Segmentation faults

Most of the time, the compiler figures this out automatically. When it can't, it asks you to be more specific about how long things should live.

These lifespan annotations are called **lifetimes**.
