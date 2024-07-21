<h1 align="center">S.O.L.I.D. PRINCIPLES</h1>

## Introduction to SOLID Principles

The <b>SOLID</b> principles were introduced by [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_C._Martin), also known as "Uncle Bob," in the early 2000s. These principles were introduced as a part of his work in [Agile Software Development](https://www.atlassian.com/agile/software-development) and are intended to improve the design and maintainability of software systems. The principles provide guidelines for developers to create more understandable, flexible, and maintainable software.

### Why SOLID Principles Were Introduced

The SOLID principles were introduced to address common problems in software development, such as:
- **Rigidity**: Difficulties in changing code because every change affects too many other parts of the system.
- **Fragility**: The tendency of the software to break in many places when a single change is made.
- **Immobility**: Difficulty in reusing code from other projects or modules due to dependencies.
- **Viscosity**: Making changes is difficult and takes a lot of effort, which discourages proper code changes.
- **Needless Complexity**: Overcomplicated designs that aren't necessary.
- **Needless Repetition**: Duplicate code or logic scattered across the codebase.
- **Opacity**: Hard-to-understand code.

By following these principles, developers can create systems that are easier to manage, understand, and extend.

### Advantages of Using SOLID Principles

1. **Improved Maintainability**: Code adhering to SOLID principles is easier to understand, modify, and maintain.
2. **Enhanced Flexibility**: Systems designed with SOLID principles are more adaptable to changing requirements.
3. **Increased Reusability**: Components are designed to be reused across different parts of the application or even across different projects.
4. **Better Testability**: SOLID principles encourage writing code that is easier to test, promoting better testing practices.
5. **Reduced Risk of Bugs**: By following these principles, the likelihood of introducing bugs when making changes is reduced.

### Disadvantages of Using SOLID Principles

1. **Increased Complexity**: Applying SOLID principles can sometimes introduce additional complexity and overhead, especially in smaller or simpler projects.
2. **Over-engineering**: There is a risk of over-engineering solutions by applying these principles too rigidly, which can lead to unnecessary abstraction and complexity.
3. **Learning Curve**: For developers unfamiliar with these principles, there can be a significant learning curve, and it may take time to fully understand and correctly apply them.

## 1. Single Responsibility Principle (SRP)
- **Definition**: A class should have only one reason to change, meaning it should have only one job or responsibility.
- **Advantages**: Easier to understand, modify, and maintain classes.
- **Disadvantages**: Can lead to a large number of small classes, increasing the complexity of the project structure.
#### Example:
```java
// Violates SRP: Class has more than one responsibility.
public class User {
    private String name;
    private String email;
    
    public void getUserDetails() {
        // code to get user details
    }
    
    public void saveToDatabase() {
        // code to save user details to the database
    }
}

// Follows SRP: Each class has a single responsibility.
public class User {
    private String name;
    private String email;
    
    public void getUserDetails() {
        // code to get user details
    }
}

public class UserRepository {
    public void saveToDatabase(User user) {
        // code to save user details to the database
    }
}
```

## 2. Open/Closed Principle (OCP)
- **Definition**: Software entities should be open for extension but closed for modification.
- **Advantages**: Makes systems more robust to change and easier to extend.
- **Disadvantages**: Requires careful planning and design to create the right abstractions.
#### Example:
```java
// Violates OCP: Modifications are required for adding new functionalities.
public class Shape {
    public void drawCircle() {
        // code to draw circle
    }
    
    public void drawSquare() {
        // code to draw square
    }
}

// Follows OCP: New shapes can be added without modifying existing code.
abstract class Shape {
    abstract void draw();
}

public class Circle extends Shape {
    @Override
    void draw() {
        // code to draw circle
    }
}

public class Square extends Shape {
    @Override
    void draw() {
        // code to draw square
    }
}
```

## 3. Liskov Substitution Principle (LSP)
- **Definition**: Objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.
- **Advantages**: Ensures that a system using polymorphism will function correctly.
- **Disadvantages**: Can be challenging to ensure all subclasses conform to this principle.

#### Example:
```java
// Violates LSP: Subclass doesn't fully adhere to superclass contract.
public class Rectangle {
    private int width;
    private int height;
    
    public void setWidth(int width) {
        this.width = width;
    }
    
    public void setHeight(int height) {
        this.height = height;
    }
    
    public int getArea() {
        return width * height;
    }
}

public class Square extends Rectangle {
    @Override
    public void setWidth(int width) {
        super.setWidth(width);
        super.setHeight(width);
    }
    
    @Override
    public void setHeight(int height) {
        super.setWidth(height);
        super.setHeight(height);
    }
}

// Follows LSP: Adheres to the contract of the superclass.
public interface Shape {
    int getArea();
}

public class Rectangle implements Shape {
    private int width;
    private int height;
    
    public void setWidth(int width) {
        this.width = width;
    }
    
    public void setHeight(int height) {
        this.height = height;
    }
    
    @Override
    public int getArea() {
        return width * height;
    }
}

public class Square implements Shape {
    private int side;
    
    public void setSide(int side) {
        this.side = side;
    }
    
    @Override
    public int getArea() {
        return side * side;
    }
}
```

## 4. Interface Segregation Principle (ISP)
- **Definition**: Clients should not be forced to depend on interfaces they do not use.
- **Advantages**: Leads to more decoupled and flexible designs.
- **Disadvantages**: Can result in a large number of interfaces, which can be overwhelming to manage.

#### Example:
```java
// Violates ISP: Interface has more methods than necessary for some clients.
public interface Worker {
    void work();
    void eat();
}

public class Employee implements Worker {
    @Override
    public void work() {
        // code to work
    }
    
    @Override
    public void eat() {
        // code to eat
    }
}

public class Robot implements Worker {
    @Override
    public void work() {
        // code to work
    }
    
    @Override
    public void eat() {
        // code to eat (not applicable for robots)
    }
}

// Follows ISP: Interfaces are segregated based on client needs.
public interface Workable {
    void work();
}

public interface Eatable {
    void eat();
}

public class Employee implements Workable, Eatable {
    @Override
    public void work() {
        // code to work
    }
    
    @Override
    public void eat() {
        // code to eat
    }
}

public class Robot implements Workable {
    @Override
    public void work() {
        // code to work
    }
}
```

## 5. Dependency Inversion Principle (DIP)
- **Definition**: High-level modules should not depend on low-level modules. Both should depend on abstractions.
- **Advantages**: Leads to more decoupled and flexible systems.
- **Disadvantages**: Can be difficult to design appropriate abstractions.

#### Example:
```java
// Violates DIP: High-level module depends on low-level module.
public class LightBulb {
    public void turnOn() {
        // code to turn on the light
    }
}

public class Switch {
    private LightBulb lightBulb;
    
    public Switch(LightBulb lightBulb) {
        this.lightBulb = lightBulb;
    }
    
    public void operate() {
        lightBulb.turnOn();
    }
}

// Follows DIP: High-level module depends on abstraction.
public interface Switchable {
    void turnOn();
}

public class LightBulb implements Switchable {
    @Override
    public void turnOn() {
        // code to turn on the light
    }
}

public class Switch {
    private Switchable device;
    
    public Switch(Switchable device) {
        this.device = device;
    }
    
    public void operate() {
        device.turnOn();
    }
}
```

1. **Official Documentation and Articles:**
   - [Wikipedia: SOLID](https://en.wikipedia.org/wiki/SOLID) - Overview of SOLID principles with examples.
   - [Microsoft Documentation](https://docs.microsoft.com/en-us/dotnet/standard/modern-web-apps-azure-architecture/principles-devops) - Explanation of SOLID principles in the context of .NET.

2. **Tutorials and Blogs:**
   - [SOLID Principles Explained](https://medium.com/@doabledanny/solid-principles-explained-7bcdb9dc87c9) - An easy-to-understand article on Medium.
   - [GeeksforGeeks: SOLID Principles](https://www.geeksforgeeks.org/solid-principles-in-java/) - A detailed explanation with Java examples.

3. **Books and Resources:**
   - [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.goodreads.com/book/show/3735293-clean-code) by Robert C. Martin - A must-read book for understanding SOLID principles and writing clean code.
   - [Design Principles and Design Patterns](http://www.objectmentor.com/resources/articles/Principles_and_Patterns.pdf) - An article by Robert C. Martin, explaining the principles in-depth.

4. **Videos:**
   - [SOLID Principles by Derek Banas](https://www.youtube.com/watch?v=rtmFCcjEgEw) - A YouTube video tutorial.
   - [SOLID Principles in 8 Minutes](https://www.youtube.com/watch?v=GcUZLdhaIj0) - A quick overview on YouTube.


## Follow my [GitHub](https://www.github.com/AquibMS/) & [LinkedIn](https://www.linkedin.com/in/aquib-mohammad/) account for more
