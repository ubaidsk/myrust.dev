# Getting Started

Welcome to your journey into a new systems programming language! This section will help you take your first steps.

## How do I start writing programs in this new language on my computer?

Download the installer from the official website and run it. The installer sets up everything you need - the compiler, build tools, and package manager.

On macOS and Linux, run this command:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

On Windows, download and run the installer from the website.

After installation, restart your terminal. You now have access to the complete toolchain.

Create a new project like this:
```bash
cargo new hello_world
cd hello_world
```

This creates a folder with everything needed for your project. It's similar to creating a Python virtual environment or setting up a CMake project in C++.

Your project contains:
- A `src` folder with your code files
- A `Cargo.toml` file (like `package.json` for Node.js or `requirements.txt` for Python)
- A `.gitignore` file for version control

Here's your first program in `src/main.rs`:

```rust
fn main() {
    println!("Hello, world!");
}
```

Compare this to other languages:

**C++:**
```cpp
#include <iostream>
int main() {
    std::cout << "Hello, world!" << std::endl;
    return 0;
}
```

**Python:**
```python
print("Hello, world!")
```

## How do I make my first program run?

This language compiles your code before running it, just like C++.

Build and run your program:
```bash
cargo run
```

This single command compiles your code and runs it. It's like running `make && ./program` in C++ or `python script.py` in Python.

If you only want to check for errors without running:
```bash
cargo check
```

To build without running:
```bash
cargo build
```

For faster builds during development, the default build is unoptimized. For production, use:
```bash
cargo build --release
```

This creates an optimized executable, similar to using `-O3` in GCC or Clang.

## What is this 'cargo' thing, and how is it like `pip`/`conda` or `CMake`/`make`?

Think of it as both `pip` and `make` combined into one tool. It handles building your code AND managing external libraries.

**Like pip/conda (Package Management):**
- Add dependencies to your project
- Download and install libraries automatically
- Handle version conflicts
- Manage different versions of the same library

**Like CMake/make (Build System):**
- Compile your code
- Link libraries
- Run tests
- Create documentation
- Handle complex build configurations

Common commands:

```bash
# Build and run (like 'make && ./program')
cargo run

# Add a library (like 'pip install requests')
cargo add serde

# Run tests (like 'make test')
cargo test

# Generate documentation (like 'make docs')
cargo doc

# Format code (like 'black' for Python)
cargo fmt

# Check for common mistakes (like 'pylint')
cargo clippy
```

Your `Cargo.toml` file lists dependencies:

```toml
[dependencies]
serde = "1.0"
reqwest = "0.11"
```

This is like `requirements.txt` in Python or `CMakeLists.txt` in C++.

When you build, it automatically downloads and compiles all dependencies. Everything is managed for you.

This tool is called **Cargo**.
