# Handling Errors

Learn how to write robust programs that handle failures gracefully.

## How does this language handle problems or failures without exceptions like in C++ or Python?

Instead of throwing exceptions that can crash your program, this language uses a special type that represents either "success with a value" or "failure with an error".

Think of it like a delivery box. It either contains your package (success) or a note explaining why delivery failed (error). You must open the box and check what's inside.

```rust
use std::fs;

fn read_file_content(filename: &str) -> Result<String, std::io::Error> {
    fs::read_to_string(filename)  // Returns Result
}

fn main() {
    let result = read_file_content("config.txt");

    match result {
        Ok(content) => {
            println!("File content: {}", content);
        },
        Err(error) => {
            println!("Failed to read file: {}", error);
        }
    }
}
```

Compare to C++ exceptions:
```cpp
#include <fstream>
#include <stdexcept>

std::string read_file_content(const std::string& filename) {
    std::ifstream file(filename);
    if (!file.is_open()) {
        throw std::runtime_error("Cannot open file");  // Exception thrown
    }
    // Read file...
    return content;
}

int main() {
    try {
        std::string content = read_file_content("config.txt");
        std::cout << "File content: " << content << std::endl;
    } catch (const std::exception& e) {  // Must remember to catch
        std::cout << "Error: " << e.what() << std::endl;
    }
}
```

Compare to Python exceptions:
```python
def read_file_content(filename):
    try:
        with open(filename, 'r') as file:
            return file.read()
    except FileNotFoundError as e:
        raise e  # Exception propagated

try:
    content = read_file_content("config.txt")
    print(f"File content: {content}")
except FileNotFoundError as e:
    print(f"Error: {e}")
```

**More examples of error handling:**

```rust
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}

fn main() {
    // Handle individual operations
    match divide(10, 2) {
        Ok(result) => println!("10 / 2 = {}", result),
        Err(msg) => println!("Error: {}", msg),
    }

    // Chain operations
    let result = divide(10, 2)
        .and_then(|x| divide(x, 2))  // Only runs if first succeeded
        .and_then(|x| divide(x, 2)); // Only runs if second succeeded

    match result {
        Ok(final_result) => println!("Final result: {}", final_result),
        Err(msg) => println!("Error somewhere: {}", msg),
    }
}
```

Key advantages:
- The compiler forces you to handle errors
- No silent failures or crashes
- Clear separation between success and failure paths
- Errors are part of the function's type signature

You cannot accidentally ignore an error without the compiler warning you.

This type is called **Result enum**.

## How do I manage errors easily in functions that might return a value that could fail?

There's a convenient operator that automatically propagates errors up the call stack, making error handling much cleaner.

```rust
use std::fs;
use std::io;

fn process_config() -> Result<String, io::Error> {
    let content = fs::read_to_string("config.txt")?;  // ? propagates errors
    let processed = content.trim().to_uppercase();
    Ok(processed)
}

fn main() {
    match process_config() {
        Ok(result) => println!("Processed config: {}", result),
        Err(e) => println!("Failed to process config: {}", e),
    }
}
```

The `?` operator means: "If this is an error, return the error immediately. If it's success, give me the value and continue."

**Without the `?` operator (verbose):**

```rust
fn process_config() -> Result<String, io::Error> {
    let content = match fs::read_to_string("config.txt") {
        Ok(content) => content,
        Err(e) => return Err(e),  // Manually propagate error
    };

    let processed = content.trim().to_uppercase();
    Ok(processed)
}
```

**Chaining multiple operations:**

```rust
fn complex_operation() -> Result<i32, String> {
    let value1 = get_number_from_file("file1.txt")?;
    let value2 = get_number_from_file("file2.txt")?;
    let result = divide(value1, value2)?;
    let final_result = multiply_by_two(result)?;
    Ok(final_result)
}
```

Compare to exception handling in other languages:

**C++ (verbose error checking):**
```cpp
std::optional<int> complex_operation() {
    auto value1 = get_number_from_file("file1.txt");
    if (!value1.has_value()) return std::nullopt;

    auto value2 = get_number_from_file("file2.txt");
    if (!value2.has_value()) return std::nullopt;

    // ... more checks
}
```

**Python (with exceptions):**
```python
def complex_operation():
    # If any function throws an exception, it automatically propagates
    value1 = get_number_from_file("file1.txt")
    value2 = get_number_from_file("file2.txt")
    result = divide(value1, value2)
    return multiply_by_two(result)
```

The `?` operator gives you the convenience of exception propagation but with explicit error types and compiler-enforced handling.

This operator is called the **? operator**.
