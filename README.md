# Oops-in-java
//All Oops concept(Inheritance, Encapsulation, Abstraction, Polymorphism, Interface, getter/setter, constructor) as perspective of SDET roles

import java.util.*;

// ================================================================
// 1. ENCAPSULATION + CONSTRUCTORS (default, parameterized, copy)
// ================================================================
class Employee {
    private int id;
    private String name;
    private double salary;

    // Default constructor
    public Employee() {
        this.id = 0;
        this.name = "Unknown";
        this.salary = 0.0;
    }

    // Parameterized constructor
    public Employee(int id, String name, double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    // Copy constructor
    public Employee(Employee other) {
        this.id = other.id;
        this.name = other.name;
        this.salary = other.salary;
    }

    // Getters & Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public double getSalary() { return salary; }
    public void setSalary(double salary) {
        if (salary < 0) throw new IllegalArgumentException("Salary cannot be negative");
        this.salary = salary;
    }

    public void display() {
        System.out.println("ID: " + id + ", Name: " + name + ", Salary: " + salary);
    }
}

// ================================================================
// 2. INHERITANCE + METHOD OVERRIDING (Runtime Polymorphism)
// ================================================================
class Manager extends Employee {
    private String department;

    public Manager(int id, String name, double salary, String department) {
        super(id, name, salary);   // constructor chaining to parent
        this.department = department;
    }

    @Override
    public void display() {
        super.display();           // reuse parent logic
        System.out.println("Department: " + department);
    }
}

// ================================================================
// 3. ABSTRACTION
// ================================================================
abstract class Shape {
    abstract double area();        // abstract method - no body

    void describe() {              // concrete method
        System.out.println("Area = " + area());
    }
}

class Circle extends Shape {
    private double radius;
    Circle(double radius) { this.radius = radius; }

    @Override
    double area() { return Math.PI * radius * radius; }
}

class Rectangle extends Shape {
    private double length, width;
    Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    double area() { return length * width; }
}

// ================================================================
// 4. COMPILE-TIME POLYMORPHISM (Method Overloading)
// ================================================================
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}

// ================================================================
// 5. INTERFACE + DIAMOND PROBLEM
// ================================================================
// Java blocks multiple inheritance of CLASSES specifically to avoid
// the diamond problem. But interfaces with default methods (Java 8+)
// can reintroduce the same ambiguity -- Java then forces explicit
// resolution instead of guessing.
// ================================================================
// TRUE DIAMOND PROBLEM — 4-LEVEL SHAPE
//
//           GrandParent
//            /        \
//        Father      Mother          <-- both extend GrandParent
//            \        /
//            Child                   <-- implements both
//
// Child inherits TWO different paths down to the same root method,
// and each path overrides it differently. That's the actual diamond.
// ================================================================

interface GrandParent {
    default void greet() {
        System.out.println("GrandParent: Hello from the root interface");
    }
}

// Father overrides GrandParent's default
interface Father extends GrandParent {
    @Override
    default void greet() {
        System.out.println("Father: Good at Business");
    }
}

// Mother overrides GrandParent's default differently
interface Mother extends GrandParent {
    @Override
    default void greet() {
        System.out.println("Mother: Good at Singing");
    }
}

// Child implements BOTH Father and Mother.
// Both branches trace back to the same GrandParent method,
// but each branch gives a DIFFERENT implementation.
// Java cannot pick one over the other -- compile error unless resolved:
//
// "class Child inherits unrelated defaults for greet() from
//  Father and Mother"
class Child implements Father, Mother {
    @Override
    public void greet() {
        Father.super.greet();        // explicitly call Father's path
        Mother.super.greet();        // explicitly call Mother's path
        System.out.println("Child: Manually resolved the diamond");
    }
}
// ================================================================
// DRIVER CLASS
// ================================================================
public class Main {
    public static void main(String[] args) {

        System.out.println("===== 1. ENCAPSULATION + CONSTRUCTORS =====");
        Employee e1 = new Employee();
        Employee e2 = new Employee(101, "John Wick", 50000);
        Employee e3 = new Employee(e2);
        e1.display();
        e2.display();
        e3.setSalary(60000);
        e3.display();

        System.out.println("\n===== 2. INHERITANCE + RUNTIME POLYMORPHISM =====");
        Employee emp = new Manager(201, "Maverick", 80000, "QA"); // upcasting
        emp.display();  // runtime polymorphism -> calls Manager's version

        System.out.println("\n===== 3. ABSTRACTION =====");
        Shape s1 = new Circle(5);
        Shape s2 = new Rectangle(4, 6);
        s1.describe();
        s2.describe();

        System.out.println("\n===== 4. COMPILE-TIME POLYMORPHISM (Overloading) =====");
        Calculator calc = new Calculator();
        System.out.println(calc.add(2, 3));
        System.out.println(calc.add(2.5, 3.5));
        System.out.println(calc.add(1, 2, 3));

        System.out.println("\n===== 5. INTERFACE + DIAMOND PROBLEM =====");
         Child c = new Child();
        c.greet();    }
}
