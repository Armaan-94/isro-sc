# 08. OOP with C++: Inheritance, Polymorphism, Virtual Functions and Templates

> **The two questions examiners love.** (1) "In what order are constructors and destructors called?" and (2) "Which version of the function runs: base or derived?" The answer to (2) depends on just two things: **is the function virtual?** and **are you calling through a pointer/reference or a plain object?** Keep those in mind and this chapter is easy.

---

## 1. Inheritance basics

**Inheritance** lets a **derived** (child) class reuse and extend a **base** (parent) class. It models an **IS-A** relationship: a Car IS-A Vehicle.

```cpp
class Vehicle {
protected:
    int speed;
public:
    void setSpeed(int s) { speed = s; }
};

class Car : public Vehicle {      // public inheritance
public:
    void show() { cout << speed; }  // can use protected member of base
};
```

(Composition models **HAS-A**: a Car HAS-A Engine, i.e. an Engine member object.)

### What is NOT inherited

- **Constructors and destructors** (each class has its own; base ones are **called**, not inherited).
- **Friend** relationships.
- The base's assignment operator (in the sense of being automatically used for derived parts; the derived class gets its own).
- **Private members** are inherited as part of the object's memory but are **not accessible** in the derived class.

### Access after inheritance

| Base member | `public` inheritance | `protected` inheritance | `private` inheritance |
|---|---|---|---|
| public | **public** | protected | private |
| protected | **protected** | protected | private |
| private | **not accessible** | not accessible | not accessible |

Memory trick: the inheritance mode is a **ceiling**. Members keep their level unless it's more open than the mode, in which case they're lowered to the mode. Private stays inaccessible no matter what.

Default inheritance mode: **private** for `class`, **public** for `struct`.

---

## 2. Types of inheritance

```
Single:        A → B
Multiple:      A, B → C           (C inherits from two bases)
Multilevel:    A → B → C
Hierarchical:  A → B, A → C       (one base, many derived)
Hybrid:        a mix (e.g. hierarchical + multiple = the "diamond")
```

(Java doesn't allow multiple inheritance of classes; C++ does.)

---

## 3. Ambiguity problem vs diamond problem

### Ambiguity problem

Two **unrelated** bases have a member with the **same name**:

```cpp
class A { public: void show() {} };
class B { public: void show() {} };
class C : public A, public B {};
C c;
c.show();        // ERROR: ambiguous
c.A::show();     // fix: scope resolution
```

### Diamond problem

The **same** base is inherited along **two paths**:

```
      A
     / \
    B   C
     \ /
      D
```

```cpp
class A { public: int x; };
class B : public A {};
class C : public A {};
class D : public B, public C {};
D d;
d.x = 5;         // ERROR: D has TWO copies of A (one via B, one via C)
```

**Fix: virtual inheritance** so only **one shared A** exists in D:

```cpp
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};   // one A subobject
```

With virtual inheritance, the **most derived class (D)** is responsible for constructing the virtual base A, and A's constructor runs **first**.

| | Ambiguity problem | Diamond problem |
|---|---|---|
| Cause | Same name in **unrelated** bases | **Same** base via two paths |
| Symptom | Name conflict | **Duplicate** base subobjects |
| Fix | Scope resolution `::` | **Virtual inheritance** |

---

## 4. Constructor and destructor order

**Construction:** base first, then members (in declaration order), then the derived class body.
**Destruction:** exactly the **reverse**.

### Single and multilevel

```cpp
class A { public: A() { cout << "A"; } ~A() { cout << "a"; } };
class B : public A { public: B() { cout << "B"; } ~B() { cout << "b"; } };
class C : public B { public: C() { cout << "C"; } ~C() { cout << "c"; } };
int main() { C obj; }
// ABCcba
```

### Multiple inheritance

Bases are constructed in the order they're **listed in the class declaration** (not the order in the initialiser list):

```cpp
class D : public B, public A { ... };   // B's constructor, then A's, then D's
```

### Passing arguments to a base constructor

If the base has **no default constructor**, the derived constructor **must** call a base constructor in its **initialiser list**:

```cpp
class Base { public: Base(int x) { cout << x; } };
class Derived : public Base {
public:
    Derived(int y) : Base(y) {}     // required
    // Derived() {}                 // ERROR: no Base() to call
};
```

---

## 5. Object slicing

Assigning a derived object **by value** to a base object copies only the **base part**; the derived-specific data is "sliced off".

```cpp
class Employee { public: string name; virtual void show() { cout << "E"; } };
class Manager : public Employee { public: int team; void show() { cout << "M"; } };

Manager m;
Employee e = m;      // sliced: e is a pure Employee
e.show();            // "E": polymorphism lost for this copy
```

Use **pointers or references** to keep polymorphic behaviour.

---

## 6. Polymorphism

| | Compile-time (static) | Run-time (dynamic) |
|---|---|---|
| Mechanism | Function overloading, operator overloading, templates | **Overriding + virtual functions** |
| Decided by | Compiler | At run time via the **vtable** |
| Binding | **Early** | **Late** |
| Needs `virtual`? | No | **Yes** |
| Speed | Faster | Slight overhead |

### Overloading vs overriding

| | Overloading | Overriding |
|---|---|---|
| Where | Same scope (usually same class) | Base and derived classes |
| Signature | **Different** parameters | **Same** name, parameters (and compatible return type) |
| Resolved | Compile time | Run time (if virtual) |
| Needs inheritance? | No | Yes |

---

## 7. Virtual functions

### 7.1 The key rule

**Which function runs when you call `ptr->f()` through a base-class pointer?**

| f in base is... | Called through | Version that runs |
|---|---|---|
| **non-virtual** | base pointer/reference | **Base** version (decided by pointer type) |
| **virtual** | base pointer/reference | **Derived** version (decided by actual object type) |
| either | a plain object (not pointer/ref) | The object's own class version |

```cpp
class Animal {
public:
    void eat()           { cout << "Animal eats "; }
    virtual void sound() { cout << "... "; }
};
class Dog : public Animal {
public:
    void eat()   { cout << "Dog eats "; }
    void sound() { cout << "Bark "; }
};

Animal *p = new Dog();
p->eat();     // "Animal eats "  (non-virtual: pointer type wins)
p->sound();   // "Bark "         (virtual: object type wins)
```

### 7.2 How it works: vtable and vptr

- Each class with virtual functions has a **vtable**: a table of addresses of its virtual functions.
- Each object of such a class contains a hidden **vptr** pointing to its class's vtable.
- A virtual call looks up the function through the vptr at run time.
- So `sizeof` an object grows by one pointer (e.g. 8 bytes on 64-bit) when the class has virtual functions.

### 7.3 Rules

- Must be a non-static **member** function.
- Once virtual in the base, it's **automatically virtual** in all derived classes (the keyword is optional there; C++11's `override` keyword helps catch mistakes).
- **Constructors cannot be virtual.** Destructors **can** (and should be in polymorphic bases).
- **Static** functions and **friend** functions can't be virtual.
- Inside a constructor or destructor, virtual calls do **not** dispatch to derived versions (the derived part doesn't exist yet / anymore).

### 7.4 Virtual destructors

```cpp
class Base { public: ~Base() { cout << "~Base "; } };
class Derived : public Base {
    int *buf = new int[100];
public:
    ~Derived() { delete[] buf; cout << "~Derived "; }
};

Base *p = new Derived();
delete p;    // prints only "~Base ": ~Derived never runs → buf LEAKS
```

Make `~Base()` **virtual** and `delete p` runs **~Derived then ~Base**. Rule: **any class meant to be used polymorphically needs a virtual destructor.**

---

## 8. Pure virtual functions and abstract classes

```cpp
class Shape {
public:
    virtual double area() = 0;     // pure virtual: no body here
    virtual ~Shape() {}
};

class Circle : public Shape {
    double r;
public:
    Circle(double x) : r(x) {}
    double area() override { return 3.14159 * r * r; }
};
```

- A class with **at least one pure virtual function** is **abstract**: you **cannot create objects** of it (`Shape s;` → error).
- You **can** have pointers/references to an abstract class: `Shape *s = new Circle(2);`.
- A derived class must override **all** pure virtual functions to become **concrete**; otherwise it's abstract too.
- Abstract classes can have constructors, data members and normal functions.
- A pure virtual function **may** still be given a body (defined outside the class), though it's rare (a pure virtual destructor must have one).
- A class with only pure virtual functions acts like an **interface**.

| | Virtual function | Pure virtual function |
|---|---|---|
| Body in base | Yes | No (`= 0`) |
| Override required? | Optional | **Mandatory** (for a concrete class) |
| Base class instantiable? | Yes | **No** (abstract) |

---

## 9. Templates and the STL

### Function templates

```cpp
template <typename T>
T maxOf(T a, T b) { return a > b ? a : b; }

maxOf(3, 7);        // T = int
maxOf(2.5, 1.5);    // T = double
```

### Class templates

```cpp
template <class T>
class Stack {
    T data[100]; int top = -1;
public:
    void push(T x) { data[++top] = x; }
    T pop() { return data[top--]; }
};
Stack<int> s;
```

Templates are instantiated at **compile time**: a form of static (compile-time) polymorphism, also called **generic programming**.

### STL (Standard Template Library): three parts

1. **Containers:** `vector` (dynamic array), `list` (doubly linked list), `deque`, `stack`, `queue`, `priority_queue`, `set`, `map`, `unordered_map`, ...
2. **Algorithms:** `sort`, `find`, `binary_search`, `reverse`, `count`, ...
3. **Iterators:** objects that **traverse** containers (`begin()`, `end()`).

`vector` grows automatically; `push_back` is amortised O(1).

---

## 10. Exam traps

1. Private base members are never accessible in derived classes.
2. Constructors/destructors aren't inherited; base ctor runs first, destructors in reverse.
3. Multiple inheritance: bases constructed in **declaration order**.
4. Base without default constructor → derived must call it in the initialiser list.
5. Ambiguity → `::`. Diamond → **virtual inheritance**.
6. Object slicing on assignment by value.
7. Non-virtual call via base pointer → base version; virtual → derived version.
8. Constructors can't be virtual; destructors should be virtual in polymorphic bases.
9. Abstract class = at least one pure virtual function; can't instantiate; can use pointers.
10. Derived class remains abstract until it overrides all pure virtuals.
11. Overloading = compile time; overriding with virtual = run time.

---

## 11. Practice questions

**Q1.** Output?
```cpp
class Base { public: Base() { cout << "B"; } ~Base() { cout << "b"; } };
class Derived : public Base { public: Derived() { cout << "D"; } ~Derived() { cout << "d"; } };
int main() { Derived obj; }
```
(a) BDbd (b) DBbd (c) BDdb (d) DBdb

**Answer: (c).**

---

**Q2.** Output?
```cpp
class A { public: void f() { cout << "A"; } };
class B : public A { public: void f() { cout << "B"; } };
int main() { A *p = new B(); p->f(); }
```
(a) A (b) B (c) AB (d) error

**Answer: (a).** f isn't virtual.

---

**Q3.** Same as Q2 but `virtual void f()` in A. Output?
(a) A (b) B (c) AB (d) error

**Answer: (b).**

---

**Q4.** Output?
```cpp
class A { public: A() { cout << "1"; } };
class B { public: B() { cout << "2"; } };
class C : public B, public A { public: C() { cout << "3"; } };
int main() { C c; }
```
(a) 123 (b) 213 (c) 321 (d) 312

**Answer: (b).** Bases in declaration order: B then A, then C.

---

**Q5.** Without a virtual destructor in Base, `Base *p = new Derived(); delete p;` calls:
(a) both destructors (b) only Base's destructor (c) only Derived's destructor (d) neither

**Answer: (b).**

---

**Q6.** Which can NOT be virtual?
(a) destructor (b) normal member function (c) constructor (d) overloaded operator member

**Answer: (c).**

---

**Q7.** The diamond problem is solved by:
(a) scope resolution (b) virtual inheritance (c) friend functions (d) templates

**Answer: (b).**

---

**Q8.** A class Shape has `virtual void draw() = 0;`. Which is valid?
(a) `Shape s;` (b) `Shape *p;` (c) `Shape s = Shape();` (d) `new Shape();`

**Answer: (b).**

---

**Q9.** Under protected inheritance, a public member of the base becomes:
(a) public (b) protected (c) private (d) inaccessible

**Answer: (b).**

---

**Q10.** Output?
```cpp
class A { public: virtual void show() { cout << "A"; } };
class B : public A { public: void show() { cout << "B"; } };
int main() { B b; A a = b; a.show(); }
```
(a) A (b) B (c) error (d) AB

**Answer: (a).** Object slicing: `a` is a real A object; calling on an object (not via pointer/reference) uses A's version.

---

**Q11.** Output?
```cpp
class A { public: virtual void show() { cout << "A"; } };
class B : public A { public: void show() { cout << "B"; } };
int main() { B b; A &r = b; r.show(); }
```
(a) A (b) B (c) error (d) AB

**Answer: (b).** Reference keeps polymorphism.

---

**Q12.** Function overloading is an example of:
(a) run-time polymorphism (b) compile-time polymorphism (c) inheritance (d) encapsulation

**Answer: (b).**

---

**Q13.** Output?
```cpp
class X { public: X() { cout << "X"; } ~X() { cout << "x"; } };
class Y : public X { public: Y() { cout << "Y"; } ~Y() { cout << "y"; } };
class Z : public Y { public: Z() { cout << "Z"; } ~Z() { cout << "z"; } };
int main() { Z *p = new Z(); delete p; }
```
(a) XYZzyx (b) ZYXxyz (c) XYZxyz (d) XYZ

**Answer: (a).** Deleting through a `Z*` (its own type) runs all destructors correctly.

---

**Q14.** The three main components of the STL are:
(a) classes, objects, functions (b) containers, algorithms, iterators (c) templates, macros, pointers (d) vectors, lists, maps

**Answer: (b).**

---

**Q15.** A derived class that overrides only some of its abstract base's pure virtual functions is:
(a) concrete (b) still abstract (c) a compile error (d) a template

**Answer: (b).**
