# Functions and Behavior

Learn how to define actions and make your code work with different data types.

## How do I define my own actions and operations, and how do they work with different data types?

**Basic functions:**

```rust
fn add_numbers(a: i32, b: i32) -> i32 {
    a + b  // Last expression is returned (no semicolon)
}

fn greet(name: &str) {
    println!("Hello, {}!", name);  // No return value
}

fn main() {
    let result = add_numbers(5, 3);
    println!("5 + 3 = {}", result);

    greet("Alice");
}
```

Compare to C++:
```cpp
int add_numbers(int a, int b) {
    return a + b;
}

void greet(const std::string& name) {
    std::cout << "Hello, " << name << "!" << std::endl;
}
```

Compare to Python:
```python
def add_numbers(a, b):
    return a + b

def greet(name):
    print(f"Hello, {name}!")
```

**Adding actions to your custom data types:**

```rust
struct Rectangle {
    width: f64,
    height: f64,
}

impl Rectangle {
    // Associated function (like a static method)
    fn new(width: f64, height: f64) -> Rectangle {
        Rectangle { width, height }
    }

    // Method that reads data
    fn area(&self) -> f64 {
        self.width * self.height
    }

    // Method that modifies data
    fn scale(&mut self, factor: f64) {
        self.width *= factor;
        self.height *= factor;
    }

    // Method that consumes the data
    fn into_square(self) -> Rectangle {
        let side = (self.width + self.height) / 2.0;
        Rectangle::new(side, side)
    }
}

fn main() {
    // Create using associated function
    let mut rect = Rectangle::new(10.0, 5.0);

    // Call methods
    println!("Area: {}", rect.area());

    rect.scale(2.0);
    println!("After scaling, area: {}", rect.area());

    let square = rect.into_square();
    // rect is no longer usable here
}
```

Compare to C++ classes:
```cpp
class Rectangle {
private:
    double width, height;

public:
    Rectangle(double w, double h) : width(w), height(h) {}

    double area() const {
        return width * height;
    }

    void scale(double factor) {
        width *= factor;
        height *= factor;
    }
};
```

Compare to Python classes:
```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def scale(self, factor):
        self.width *= factor
        self.height *= factor
```

Functions are defined with **fn** and method collections are defined with **impl**.

## How can I make my code work with different data types in a general way, similar to C++ templates or Python's duck typing?

You can define shared behaviors that any type can implement. This allows writing flexible functions that work with many different types.

Think of it like defining a contract: "Any type that can do X can be used here."

**Defining a shared behavior:**

```rust
trait Drawable {
    fn draw(&self);
    fn area(&self) -> f64;
}

struct Circle {
    radius: f64,
}

struct Rectangle {
    width: f64,
    height: f64,
}

// Circle implements the Drawable behavior
impl Drawable for Circle {
    fn draw(&self) {
        println!("Drawing a circle with radius {}", self.radius);
    }

    fn area(&self) -> f64 {
        3.14159 * self.radius * self.radius
    }
}

// Rectangle implements the Drawable behavior
impl Drawable for Rectangle {
    fn draw(&self) {
        println!("Drawing a rectangle {}x{}", self.width, self.height);
    }

    fn area(&self) -> f64 {
        self.width * self.height
    }
}

// Function that works with any Drawable type
fn display_shape(shape: &dyn Drawable) {
    shape.draw();
    println!("Area: {}", shape.area());
}

fn main() {
    let circle = Circle { radius: 5.0 };
    let rectangle = Rectangle { width: 10.0, height: 5.0 };

    display_shape(&circle);     // Works with Circle
    display_shape(&rectangle);  // Works with Rectangle
}
```

**Generic functions (like C++ templates):**

```rust
// Function that works with any type that implements Drawable
fn print_area<T: Drawable>(shape: &T) {
    println!("The area is: {}", shape.area());
}

// Generic function with multiple constraints
fn compare_areas<T: Drawable, U: Drawable>(shape1: &T, shape2: &U) {
    if shape1.area() > shape2.area() {
        println!("First shape is larger");
    } else {
        println!("Second shape is larger");
    }
}

fn main() {
    let circle = Circle { radius: 5.0 };
    let rectangle = Rectangle { width: 10.0, height: 5.0 };

    print_area(&circle);
    print_area(&rectangle);
    compare_areas(&circle, &rectangle);
}
```

Compare to C++ templates:
```cpp
template<typename T>
concept Drawable = requires(T t) {
    t.draw();
    { t.area() } -> std::convertible_to<double>;
};

template<Drawable T>
void print_area(const T& shape) {
    std::cout << "Area: " << shape.area() << std::endl;
}

// Or without concepts (older C++)
template<typename T>
void print_area(const T& shape) {
    std::cout << "Area: " << shape.area() << std::endl;
}
```

Compare to Python duck typing:
```python
# Python doesn't enforce interfaces at compile time
def display_shape(shape):
    shape.draw()  # Works if shape has draw() method
    print(f"Area: {shape.area()}")  # Runtime error if no area() method

def print_area(shape):
    print(f"Area: {shape.area()}")  # "If it walks like a duck..."
```

Key advantages:
- Compile-time checking ensures methods exist
- No runtime performance cost
- Clear contracts for what types can do
- Composable behaviors

This shared behavior system is called **trait**.
