<h1 align="center">S.O.L.I.D. PRINCIPLES</h1>

The SOLID principles are a set of five design principles intended to make software designs more understandable, flexible, and maintainable. Here's an explanation of each principle with Java examples:

## 1. Single Responsibility Principle (SRP)
A class should have only one reason to change, meaning it should have only one job or responsibility.

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
Software entities should be open for extension but closed for modification.

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
Objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.

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
Clients should not be forced to depend on interfaces they do not use.

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
High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

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

These principles help in creating a more modular, maintainable, and extensible codebase.
