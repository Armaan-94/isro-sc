# 07. OOP with C++: Classes, Objects, Constructors and Overloading

> **The OOP mindset.** In C you write functions and pass data around. In C++ you build **objects** that hold their own data and know how to act on it, and you control who can touch that data. Most exam questions here test the **rules** (what's allowed, what's automatic, in what order things happen), so we'll state each rule clearly with a small example.

---

## 1. C vs C++ and why OOP

- **C** (Dennis Ritchie, 1972, Bell Labs): **procedural**.
- **C++** (Bjarne Stroustrup, 1985, Bell Labs; first called "C with Classes"): adds **object-oriented programming** while keeping almost all of C. C++ supports both styles.
- Both are often called **middle-level** languages (low-level access via pointers + high-level abstractions).

| | Procedural (C) | Object-oriented (C++, Java) |
|---|---|---|
| Organised around | Functions | **Objects** (data + functions) |
| Design | Top-down | **Bottom-up** |
| Data security | Weak (global data) | Strong (**encapsulation**) |
| Reuse | Harder | **Inheritance**, templates |

### The four pillars of OOP

1. **Encapsulation:** bundle data and the functions that operate on it into one unit (a class) and **restrict direct access** to the data (data hiding).
2. **Abstraction:** expose **what** an object does, hide **how** (interfaces, abstract classes).
3. **Inheritance:** a new class reuses and extends an existing one (IS-A relationship).
4. **Polymorphism:** one interface, many forms (overloading at compile time, overriding with virtual functions at run time).

---

## 2. Classes and objects

```cpp
class Student {
private:                 // default for class
    int roll;
    float marks;
public:
    void set(int r, float m) { roll = r; marks = m; }
    void show() { cout << roll << " " << marks; }
};

Student s1, s2;          // two objects
s1.set(1, 92.5);
s1.show();
```

- A **class** is a blueprint (a user-defined type). An **object** is an instance.
- Memory for data members is allocated **per object**; member function **code is shared** by all objects.
- `sizeof` an **empty class** is **1** (so distinct objects have distinct addresses).

### Access specifiers

| Specifier | Accessible from |
|---|---|
| **private** | Only inside the class (and friends) |
| **protected** | Inside the class and **derived classes** (and friends) |
| **public** | Anywhere |

### Defining member functions outside the class

```cpp
class Box { int w; public: void setW(int); };
void Box::setW(int x) { w = x; }     // :: = scope resolution
```

### The `this` pointer

Every non-static member function receives a hidden pointer **`this`** to the object it was called on.

```cpp
class Point {
    int x;
public:
    Point& setX(int x) { this->x = x; return *this; }   // resolves the name clash, enables chaining
};
Point p; p.setX(3).setX(5);
```

### Scope resolution `::` for globals

```cpp
int x = 10;
int main() {
    int x = 20;
    cout << x << " " << ::x;    // 20 10
}
```

C has no way to reach a shadowed global; C++ uses `::x`.

---

## 3. struct vs class in C++

In C++, a `struct` can have member functions, constructors, access specifiers, inheritance: everything a class can. The **only** differences are defaults:

| | struct | class |
|---|---|---|
| Default member access | **public** | **private** |
| Default inheritance | **public** | **private** |

---

## 4. References

```cpp
int x = 10;
int &r = x;     // r is another name for x
r = 20;         // x is now 20
```

| | Reference | Pointer |
|---|---|---|
| Must be initialised? | **Yes** | No |
| Can be reseated to refer elsewhere? | **No** | Yes |
| Can be NULL? | No | Yes |
| Syntax to use | Like the variable | Needs `*` and `->` |
| Own memory? | Conceptually an alias | Yes |

**Call by reference** (true C++ feature):
```cpp
void swap(int &a, int &b) { int t = a; a = b; b = t; }
swap(x, y);     // swaps the caller's variables
```

---

## 5. Inline functions

`inline` asks the compiler to paste the function body at the call site (no call overhead).

- Functions **defined inside the class body** are implicitly inline.
- It's only a **request**. Compilers typically ignore it for functions with **loops, recursion, switch, goto, static variables**, or large bodies.
- Advantage over macros: type checking, arguments evaluated once.

---

## 6. Static members

### Static data member

**One copy shared by all objects** of the class (belongs to the class, not to any object). Must be **defined outside** the class.

```cpp
class Counter {
    static int count;           // declaration
public:
    Counter() { count++; }
    static int get() { return count; }
};
int Counter::count = 0;         // definition (required)

Counter a, b, c;
cout << Counter::get();         // 3
```

### Static member function

- Called with the class name: `Counter::get()` (an object isn't needed).
- **Has no `this` pointer**, so it can access **only static members** directly.

---

## 7. Friend functions and friend classes

A **friend function** is **not a member** but is allowed to access the class's private and protected members.

```cpp
class Box {
    int w;
public:
    Box(int x) : w(x) {}
    friend int getW(Box b);     // grant access
};
int getW(Box b) { return b.w; } // non-member, yet sees private w
```

A **friend class**: all its member functions get access: `friend class Inspector;`.

Rules of friendship:
- **Not mutual** (A is a friend of B doesn't make B a friend of A).
- **Not inherited.**
- **Not transitive.**
- Friend functions are called like normal functions (no object and dot).

---

## 8. Constructors

A **constructor** is a special member function that **initialises** an object.

Rules:
- **Same name** as the class.
- **No return type** (not even void).
- Called **automatically** when an object is created.
- Can be **overloaded** (several constructors with different parameters).
- **Cannot be virtual.** Cannot be called explicitly with the dot operator on an existing object.
- Usually public.

### Types

```cpp
class Point {
    int x, y;
public:
    Point() { x = y = 0; }                       // default
    Point(int a, int b) : x(a), y(b) {}          // parameterised (with initialiser list)
    Point(const Point &p) { x = p.x; y = p.y; }  // copy constructor
};

Point p1;            // default
Point p2(3, 4);      // parameterised
Point p3 = p2;       // copy constructor
Point p4(p2);        // copy constructor
```

**The default-constructor trap:** if you write **any** constructor yourself, the compiler **stops** generating the default one.

```cpp
class A { public: A(int x) {} };
A a;          // ERROR: no default constructor
```

### When is the copy constructor called?

1. Initialising an object from another: `A b = a;` or `A b(a);`.
2. **Passing an object by value** to a function.
3. **Returning an object by value** (often optimised away by the compiler: copy elision).

(Not on plain assignment `b = a;` between existing objects: that's the **assignment operator**.)

Why must the copy constructor take a **reference** (`const A &`)? If it took `A` by value, calling it would require copying the argument, which calls the copy constructor again: **infinite recursion**.

### Shallow vs deep copy (most tested)

```cpp
class Str {
    char *data;
public:
    Str(const char *s) { data = new char[strlen(s) + 1]; strcpy(data, s); }
    ~Str() { delete[] data; }
};
Str a("hi");
Str b = a;     // default copy constructor: shallow copy
```

The compiler-generated copy constructor copies the **pointer value**, so a.data and b.data point to the **same** memory. Changing one changes the other; when both destructors run, the memory is **deleted twice** (crash).

| | Shallow copy | Deep copy |
|---|---|---|
| Copies | Pointer values (addresses) | New memory + the pointed-to contents |
| Objects share memory? | **Yes** | No |
| Risk | Double free, unintended sharing | None |
| Provided by | **Compiler (default)** | You must write it |

Deep copy constructor:
```cpp
Str(const Str &o) { data = new char[strlen(o.data) + 1]; strcpy(data, o.data); }
```

(Rule of three: if a class needs a custom destructor, copy constructor or copy assignment operator, it usually needs all three.)

---

## 9. Destructors

```cpp
~Point() { cout << "destroyed"; }
```

- Name `~ClassName`, **no arguments**, **no return type**.
- **Cannot be overloaded** (exactly one per class).
- **Can be virtual** (and should be in polymorphic base classes; Chapter 08).
- Called automatically when the object goes out of scope or is `delete`d.
- Objects are destroyed in the **reverse order of construction**.

```cpp
class T { char c; public: T(char x) : c(x) { cout << c; } ~T() { cout << (char)(c - 32); } };
int main() { T a('a'), b('b'); }      // prints "abBA"
```

### Order with member objects

Members are constructed **before** the containing class's constructor body (in **declaration order**) and destroyed **after** its destructor body.

```cpp
class A { public: A() { cout << "A"; } ~A() { cout << "a"; } };
class B { A m; public: B() { cout << "B"; } ~B() { cout << "b"; } };
B obj;     // prints "ABba"
```

---

## 10. Function overloading

**Same name, different parameter lists** (number, types or order of parameters), in the same scope.

```cpp
int area(int s);              // square
int area(int l, int b);       // rectangle
double area(double r);        // circle
```

- Resolved at **compile time**: **compile-time (static) polymorphism / early binding**.
- **Return type alone can't distinguish** overloads (`int f(int)` and `double f(int)` → error).
- Default arguments can cause **ambiguity**: `void f(int a, int b = 0)` and `void f(int a)` → `f(5)` is ambiguous.

---

## 11. Operator overloading

Give existing operators meaning for your classes.

```cpp
class Complex {
    double re, im;
public:
    Complex(double r = 0, double i = 0) : re(r), im(i) {}
    Complex operator+(const Complex &o) const { return Complex(re + o.re, im + o.im); }
    friend ostream& operator<<(ostream &out, const Complex &c);
};
ostream& operator<<(ostream &out, const Complex &c) { return out << c.re << "+" << c.im << "i"; }
```

Rules:
- Only **existing** operators; you can't invent new ones (no `**`).
- At least one operand must be a **user-defined** type.
- **Precedence, associativity and number of operands can't change.**
- **Cannot be overloaded:** `.` , `.*` , `::` , `?:` , `sizeof` (also `typeid`, casts like `static_cast`).
- **Must be member functions** (not friends): `=`, `[]`, `()`, `->`.
- `<<` and `>>` for streams are usually **friend** functions (the left operand is the stream, not your object).
- Unary operator as member: no arguments; as friend: one. Binary as member: one argument; as friend: two.
- Postfix `++` is distinguished by a dummy int: `operator++(int)`.

---

## 12. Exam traps

1. struct vs class: only the default access/inheritance differ.
2. References can't be reseated or null.
3. `inline` is a request; ignored for loops/recursion.
4. Static data member: one copy, defined outside the class. Static function: no `this`.
5. Friendship: not mutual, not inherited, not transitive.
6. Writing any constructor removes the implicit default constructor.
7. Copy constructor parameter must be a reference.
8. Default copy = **shallow**.
9. Destructors: no args, no overloading, can be virtual; reverse order of construction.
10. Constructors can't be virtual.
11. Overloading can't differ by return type only.
12. Can't overload `.`, `::`, `?:`, `sizeof`, `.*`. Must be members: `=`, `[]`, `()`, `->`.

---

## 13. Practice questions

**Q1.** Output?
```cpp
class Counter {
    static int count;
public:
    Counter() { count++; }
    static int getCount() { return count; }
};
int Counter::count = 0;
int main() { Counter a, b, c; cout << Counter::getCount(); }
```
(a) 0 (b) 1 (c) 3 (d) error

**Answer: (c).**

---

**Q2.** Output?
```cpp
class T {
public:
    T()  { cout << "C"; }
    ~T() { cout << "D"; }
};
int main() { T a; { T b; } T c; }
```
(a) CCDCDD (b) CCCDDD (c) CDCDCD (d) CCDDCD

**Answer: (a).** a constructed (C), b constructed (C), b destroyed at the end of its block (D), c constructed (C), then at the end of main c and a destroyed (D D).

---

**Q3.** What is the only default difference between struct and class in C++?
(a) structs can't have functions (b) default access: public for struct, private for class (c) structs can't be inherited (d) structs can't have constructors

**Answer: (b).**

---

**Q4.** Which operator cannot be overloaded?
(a) + (b) [] (c) ?: (d) ==

**Answer: (c).**

---

**Q5.** Which operator must be overloaded as a member function?
(a) + (b) << (c) = (d) ==

**Answer: (c).**

---

**Q6.** Compile result?
```cpp
class A { public: A(int x) {} };
int main() { A obj; }
```
(a) Compiles (b) Error: no default constructor (c) Runs with garbage (d) Runtime error

**Answer: (b).**

---

**Q7.** When is the copy constructor NOT invoked?
(a) `A b = a;` (b) passing an object by value (c) `b = a;` where b already exists (d) `A b(a);`

**Answer: (c).** That's the assignment operator.

---

**Q8.** Why must a copy constructor's parameter be a reference?
(a) For speed only (b) Passing by value would call the copy constructor recursively forever (c) References are required for all constructors (d) To allow NULL

**Answer: (b).**

---

**Q9.** A static member function can access:
(a) all members (b) only static members directly (c) only non-static members (d) nothing

**Answer: (b).**

---

**Q10.** Output?
```cpp
class A { public: A() { cout << "A"; } ~A() { cout << "a"; } };
class B { A m; public: B() { cout << "B"; } ~B() { cout << "b"; } };
int main() { B obj; }
```
(a) BAab (b) ABba (c) ABab (d) BAba

**Answer: (b).**

---

**Q11.** Which pair of declarations is an INVALID overload?
(a) `int f(int); int f(double);` (b) `int f(int); int f(int, int);` (c) `int f(int); double f(int);` (d) `void f(char); void f(char*);`

**Answer: (c).** Return type alone doesn't distinguish.

---

**Q12.** A reference variable:
(a) can be reassigned to another variable later (b) must be initialised when declared (c) can be NULL (d) occupies separate memory like a pointer always

**Answer: (b).**

---

**Q13.** `sizeof` an empty class object is:
(a) 0 (b) 1 (c) 4 (d) undefined

**Answer: (b).**

---

**Q14.** The default copy constructor performs:
(a) deep copy (b) shallow (member-wise) copy (c) no copy (d) reference copy

**Answer: (b).**

---

**Q15.** Friendship in C++ is:
(a) mutual (b) inherited (c) transitive (d) none of these

**Answer: (d).**
