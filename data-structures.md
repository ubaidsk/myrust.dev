# Data Structures and Types

Learn how to organize and structure your data effectively.

## How do I define my own custom data types, like `struct` in C++ or classes in Python?

You can group related data fields together into a single type, just like structs in C++ or classes in Python.

**Grouping related data:**

```rust
struct Person {
    name: String,
    age: u32,
    email: String,
}

fn main() {
    let person = Person {
        name: String::from("Alice"),
        age: 30,
        email: String::from("alice@example.com"),
    };

    println!("Name: {}", person.name);
    println!("Age: {}", person.age);
}
```

Compare to C++:
```cpp
struct Person {
    std::string name;
    unsigned int age;
    std::string email;
};

int main() {
    Person person = {"Alice", 30, "alice@example.com"};
    std::cout << "Name: " << person.name << std::endl;
}
```

Compare to Python:
```python
class Person:
    def __init__(self, name, age, email):
        self.name = name
        self.age = age
        self.email = email

person = Person("Alice", 30, "alice@example.com")
print(f"Name: {person.name}")
```

**Creating different choices for a type:**

Sometimes you need a type that can be one of several different things. Like a traffic light that can be red, yellow, or green.

```rust
enum Status {
    Active,
    Inactive,
    Pending,
}

enum Message {
    Text(String),
    Image(String, u32, u32),  // filename, width, height
    Video { file: String, duration: u32 },
}

fn main() {
    let status = Status::Active;

    let msg = Message::Text(String::from("Hello!"));

    match msg {
        Message::Text(content) => println!("Text: {}", content),
        Message::Image(file, w, h) => println!("Image: {} ({}x{})", file, w, h),
        Message::Video { file, duration } => println!("Video: {} ({}s)", file, duration),
    }
}
```

Compare to C++ enum classes:
```cpp
enum class Status {
    Active,
    Inactive,
    Pending
};

// C++ doesn't have built-in variant types like this
// You'd need std::variant or unions
```

Compare to Python:
```python
from enum import Enum

class Status(Enum):
    ACTIVE = 1
    INACTIVE = 2
    PENDING = 3

# Python doesn't have built-in variant types
# You might use inheritance or type annotations
```

These grouped data types are called **struct** and the choice types are called **enum**.

## How can I store collections of items like lists, arrays, or maps?

**Dynamic lists (like Python lists or C++ vectors):**

```rust
fn main() {
    // Create an empty list
    let mut numbers = Vec::new();
    numbers.push(1);
    numbers.push(2);
    numbers.push(3);

    // Or create with initial values
    let fruits = vec!["apple", "banana", "orange"];

    // Access items
    println!("First fruit: {}", fruits[0]);

    // Iterate through items
    for fruit in &fruits {
        println!("Fruit: {}", fruit);
    }

    // Get length
    println!("We have {} fruits", fruits.len());
}
```

Compare to Python:
```python
# Python lists
numbers = []
numbers.append(1)
numbers.append(2)

fruits = ["apple", "banana", "orange"]
print(f"First fruit: {fruits[0]}")

for fruit in fruits:
    print(f"Fruit: {fruit}")
```

Compare to C++:
```cpp
#include <vector>
std::vector<int> numbers;
numbers.push_back(1);
numbers.push_back(2);

std::vector<std::string> fruits = {"apple", "banana", "orange"};
std::cout << "First fruit: " << fruits[0] << std::endl;

for (const auto& fruit : fruits) {
    std::cout << "Fruit: " << fruit << std::endl;
}
```

**Key-value pairs (like Python dictionaries or C++ maps):**

```rust
use std::collections::HashMap;

fn main() {
    // Create an empty map
    let mut scores = HashMap::new();
    scores.insert("Alice", 95);
    scores.insert("Bob", 87);
    scores.insert("Carol", 92);

    // Access values
    match scores.get("Alice") {
        Some(score) => println!("Alice's score: {}", score),
        None => println!("Alice not found"),
    }

    // Iterate through key-value pairs
    for (name, score) in &scores {
        println!("{}: {}", name, score);
    }

    // Check if key exists
    if scores.contains_key("Bob") {
        println!("Bob is in the scores");
    }
}
```

Compare to Python:
```python
# Python dictionaries
scores = {}
scores["Alice"] = 95
scores["Bob"] = 87

# Or create with initial values
scores = {"Alice": 95, "Bob": 87, "Carol": 92}

print(f"Alice's score: {scores['Alice']}")

for name, score in scores.items():
    print(f"{name}: {score}")
```

Compare to C++:
```cpp
#include <map>
std::map<std::string, int> scores;
scores["Alice"] = 95;
scores["Bob"] = 87;

// Access with safety check
auto it = scores.find("Alice");
if (it != scores.end()) {
    std::cout << "Alice's score: " << it->second << std::endl;
}

for (const auto& pair : scores) {
    std::cout << pair.first << ": " << pair.second << std::endl;
}
```

These dynamic lists are called **Vec** and the key-value collections are called **HashMap**.

## How do I handle situations where a value might or might not be present, like `nullptr` in C++ or `None` in Python?

Instead of using null pointers that can cause crashes, this language uses a special type that explicitly represents "something" or "nothing".

```rust
fn find_person(name: &str) -> Option<Person> {
    if name == "Alice" {
        Some(Person {
            name: String::from("Alice"),
            age: 30,
            email: String::from("alice@example.com"),
        })
    } else {
        None  // Person not found
    }
}

fn main() {
    let result = find_person("Alice");

    // Safe way to handle the result
    match result {
        Some(person) => println!("Found: {}", person.name),
        None => println!("Person not found"),
    }

    // Alternative shorter syntax
    if let Some(person) = find_person("Alice") {
        println!("Found: {}", person.name);
    }

    // Get value with default
    let person = find_person("Bob").unwrap_or(Person {
        name: String::from("Unknown"),
        age: 0,
        email: String::from(""),
    });
}
```

Compare to C++ (dangerous):
```cpp
Person* find_person(const std::string& name) {
    if (name == "Alice") {
        return new Person{"Alice", 30, "alice@example.com"};
    }
    return nullptr;  // Danger: easy to forget to check
}

int main() {
    Person* person = find_person("Alice");
    if (person != nullptr) {  // Must remember to check
        std::cout << "Found: " << person->name << std::endl;
        delete person;  // Must remember to free
    }
}
```

Compare to Python:
```python
def find_person(name):
    if name == "Alice":
        return Person("Alice", 30, "alice@example.com")
    return None  # Could cause AttributeError if not checked

person = find_person("Alice")
if person is not None:
    print(f"Found: {person.name}")
```

The compiler forces you to handle both cases (something and nothing). This prevents:
- Null pointer crashes
- `AttributeError` in Python
- Segmentation faults from accessing invalid memory

You cannot accidentally use a "nothing" value without checking first.

This type is called **Option enum**.
