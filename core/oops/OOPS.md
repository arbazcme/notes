# OOPS Interview Notes (C++)



```text

OOPS:
Encapsulation -> which data belongs to which class, what methods belong to which class, easier modification and maintainnence, 
	      -> Data Hiding, Makes private , public, protected access
		Full Encapsulation -> Access of data via getters and setters , Why ? extra layer to further control should i give access or not ! 

Inheritence -> code reusable, Data Hiding -> bw parent and child class (modes of access) which controls the flow/chain,
	       protected -> private (now no further child class can access)
		Extensability -> with some changes reuse most of code for new entity

Late binding if not placed early binding happens and always base class method is called 
Virtual keyword -> what happens initially say i have many objects each have eat method from base class for each derived class i need to define its own method
		what i can do is base class pointer with derived class reference , so i can access but early binding happens so ill call the base class method 
		not the corresponding class method , so virtual keywords indicates this is involved in inheritence bind based on the obj in runtime.

Abstraction : say 3 classes in level by level inheritence, and i do entirely private for both child and subchild classes, through parent class+ run time binding
		i can achieve abstraction, can call childs with help of a interface which accepts the base class object ! 

Interface/abstract class is different and implemenmtation is different , and also seperat from other code which commuinicates , changing the internal implementation will not
affect the other code which communicates.

Implement later to do :
Interface, 2 sub classes implementing the interface, some seperate code with a late binding func which is reusable, called differently,3 blocks,implementation,interface,code

 

```


---

# 1. Encapsulation

## Definition
- Bundle data and methods together into one class.
- Object manages its own state.
- Improves maintainability and modularity.
- Achieved using classes and access specifiers.

## Data Hiding
- Hide implementation/data from outside.
- Usually make data members `private`.

## Getters / Setters
- Controlled access to private data.
- Validation before modifying.
- Read-only / Write-only support.
- Internal implementation can change without affecting users.

> Encapsulation = Bundle data + methods.

> Data Hiding = Restrict access to internal implementation.

---

# 2. Inheritance

## Purpose
- Code reuse.
- Extensibility.
- Represents an **IS-A** relationship.

## Benefits
- Reuse parent implementation.
- Override only required behavior.
- Easily create new entities.

## Access Modes

### Public Inheritance

```
Parent public    -> public
Parent protected -> protected
```

### Protected Inheritance

```
Parent public    -> protected
Parent protected -> protected
```

### Private Inheritance

```
Parent public    -> private
Parent protected -> private
```

---




# 3. Runtime Polymorphism

Without `virtual`

```cpp
Base* b = new Child();

b->fun();        // Base::fun()
```

Compile-time binding chooses the base implementation.

---

With `virtual`

```cpp
Base* b = new Child();

b->fun();        // Child::fun()
```

Compile Time

- Checks whether `Base::fun()` is accessible.

Runtime

- Checks actual object.
- Executes the overridden implementation.

## Important Rule

Reference / Pointer Type

↓

Determines **WHAT** members are accessible.

Actual Object Type

↓

Determines **WHICH** overridden function executes.

---

# 4. Abstraction

## Definition

Expose **WHAT** an object can do.

Hide **HOW** it does it.

Usually achieved using

- Abstract Classes
- Interfaces
- Pure Virtual Functions

Example

```cpp
shape->draw();
```

User should not know

```cpp
calculatePixels();
loadTexture();
rotateVertices();
```

Those are implementation details.

> Abstraction hides implementation, not functionality.

---

# 5. Interface / Abstract Class

An interface defines a **contract**.

It specifies operations every implementation must provide.

Implementation classes decide **HOW**.

Communication code depends only on the interface.

Changing implementation should not affect communication code.

Think

```
Interface

↓

WHAT can be done

Implementation

↓

HOW it is done
```

---

# 6. Virtual Function

Without virtual

```cpp
Base* b = new Child();

b->fun();
```

Always executes

```cpp
Base::fun()
```

---

With virtual

```cpp
Base* b = new Child();

b->fun();
```

Executes

```cpp
Child::fun()
```

Virtual enables

- Runtime Polymorphism
- Dynamic Dispatch
- Late Binding

---

# 7. Private Override

```cpp
class Base {

public:

    virtual void fun() = 0;

};

class Child : public Base {

private:

    void fun() override {

    }

};
```

```cpp
Base* b = new Child();

b->fun();
```

This is valid.

Why?

Compiler checks accessibility using `Base`.

`Base::fun()` is public.

Runtime dispatch then executes `Child::fun()`.

Virtual dispatch does **not** perform another access check.

But

```cpp
Child c;

c.fun();
```

is invalid because `Child::fun()` is private.

---

# 8. Why Interfaces?

Without interface

```cpp
walkHuman(Human*)

walkAnimal(Animal*)

walkRobot(Robot*)

walkBird(Bird*)
```

Every new subclass requires another function.

Communication code becomes tightly coupled.

---

With interface

```cpp
void startWalking(Walk* w){

    w->walk();

}
```

Now

- Human
- Animal
- Robot
- Bird

all work automatically.

Benefits

- No modification
- Only extension
- Reusable
- Loose coupling

This follows the **Open/Closed Principle**.

---

# 9. Communication Flow

```
+----------------------+
| Interface            |
| Walk                 |
| walk()               |
+----------------------+
          ▲

     ------------- 

      |         |

+-----------+ +-----------+

| Human     | | Animal    |

+-----------+ +-----------+

Implementation

          ▲

+-------------------------+
| Communication Code      |
| startWalking(Walk*)     |
+-------------------------+
```

Communication code only knows

```cpp
Walk*
```

It never depends on

- Human
- Animal
- Robot

Only the interface.

---

# 10. Interview Example

```cpp
#include <iostream>
using namespace std;

class Walk{

public:

    virtual void walk() = 0;

    virtual ~Walk(){}

};

class Human : public Walk{

private:

    void walk() override{

        cout<<"Human Walking\n";

    }

};

class Animal : public Walk{

private:

    void walk() override{

        cout<<"Animal Walking\n";

    }

};

void startWalking(Walk* w){

    w->walk();

}

int main(){

    Human h;

    Animal a;

    startWalking(&h);

    startWalking(&a);

}
```

---

# 11. Why This Design?

Problem

Need separate function for every subclass.

Solution

Accept the interface.

Runtime polymorphism selects the correct implementation.

Benefits

- One reusable function.
- Loose coupling.
- Easy to extend.
- New subclasses require no modification.
- Interface and implementation remain separate.
- Communication depends only on the contract.
- Internal implementation can change independently.
- Encapsulation hides implementation details.
- Abstraction exposes only required operations.

---

# 12. Distinguish the Four Pillars

## Encapsulation

Bundle data and methods.

Control access.

---

## Inheritance

Reuse and extend existing code.

---

## Polymorphism

One interface.

Many implementations.

Runtime chooses the correct one.

---

## Abstraction

Expose WHAT.

Hide HOW.

---

# 13. One-Line Interview Answers

### Encapsulation

Bundle data and methods together while controlling access to internal state.

---

### Inheritance

Reuse and extend existing classes using an IS-A relationship.

---

### Runtime Polymorphism

One interface, many implementations. Runtime selects the correct overridden function.

---

### Abstraction

Expose only what users need while hiding implementation details.

---

### Interface

Defines a contract that implementations must satisfy.

---

# 14. The Most Important Sentence

Interface tells the outside world **WHAT** an object can do.

Implementation decides **HOW** it actually does it.

Runtime polymorphism allows communication code to work only with the interface while the correct implementation is selected automatically at runtime.

This keeps the code

- Reusable
- Extensible
- Maintainable
- Loosely Coupled
- Easy to Modify

