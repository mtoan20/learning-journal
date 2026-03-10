# Const Correctness

## Problem

How can we guarantee that certain functions **do not modify the state of an object**?

In large systems, accidental modification of object state can cause subtle bugs.
C++ provides **const correctness** to enforce read-only access at compile time.

---

## Key Idea

A **const member function** promises not to modify the object's state.

Example:

```cpp
class Example {
public:
    int getValue() const {
        return value;
    }

private:
    int value = 0;
};
```

The keyword `const` after the function declaration means:

* The function **cannot modify member variables**
* The `this` pointer becomes:

```cpp
const Example* this;
```

Therefore only **const member functions** can be called.

---

## Calling Rules

Inside a const function:

| Function Type             | Allowed |
| ------------------------- | ------- |
| const member function     | ✅       |
| non-const member function | ❌       |

Example:

```cpp
class Example {
public:
    void update() {
        value++;
    }

    int getValue() const {
        update();  // ❌ compile error
        return value;
    }

private:
    int value = 0;
};
```

Reason:

```cpp
const Example* this
```

Calling `update()` would allow modifying the object.

---

## Const Overloading

C++ allows **overloading based on constness**.

Example:

```cpp
class Buffer {
public:
    char& operator[](size_t i) {
        return data[i];
    }

    const char& operator[](size_t i) const {
        return data[i];
    }

private:
    char data[100];
};
```

Usage:

```cpp
Buffer buf;
buf[0] = 'a';   // uses non-const version

const Buffer cbuf;
cbuf[0];        // uses const version
```

---

## Logical Constness and `mutable`

Sometimes a function should be logically const but still modify internal data (e.g., caching).

Example:

```cpp
class Cache {
public:
    int getValue() const {
        if (!cached) {
            cache = compute();
            cached = true;
        }
        return cache;
    }

private:
    int compute() const;

    mutable int cache = 0;
    mutable bool cached = false;
};
```

`mutable` allows modification even inside a const function.

---

## Pitfalls

### 1. Calling non-const function from const function

```cpp
void foo() const {
    bar(); // ❌ if bar is non-const
}
```

---

### 2. Returning non-const reference from const function

```cpp
int& getValue() const; // ❌ breaks const safety
```

---

### 3. Forgetting const overload for operators

Containers and buffers often require both:

```
T& operator[]
const T& operator[] const
```

---

## Why Const Correctness Matters

Benefits:

* Prevents accidental mutation
* Improves API clarity
* Helps compiler catch bugs early
* Enables safer multithreaded design

---

## Related Concepts

* [[this-pointer]]
* [[mutable-keyword]]
* [[const-reference]]
* [[method-overloading]]

---

## Example Interview Question

Why can't a const member function call a non-const member function?

Answer:

Because inside a const member function the `this` pointer is treated as:

```cpp
const ClassName* this
```

Calling a non-const member function would require a non-const object, which violates const correctness.

---

## Tags

#cpp
#knowledge
#const-correctness
