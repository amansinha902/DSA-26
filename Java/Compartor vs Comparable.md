# Comparable vs Comparator in Java (Interview-Level Explanation)

## Why Do We Need Them?

Suppose you have a class:

``` java
class Employee {
    int id;
    String name;
    double salary;
}
```

Now create a list:

``` java
List<Employee> employees = new ArrayList<>();
```

How should Java sort these employees?

-   By ID?
-   By Name?
-   By Salary?

Java has no idea. Unlike `Integer` or `String`, `Employee` is a custom
class. Java needs to know **how to compare two Employee objects**.

That's exactly what **Comparable** and **Comparator** provide.

------------------------------------------------------------------------

# Comparable

Comparable defines the **natural ordering** of objects.

If employees should naturally be sorted by **Employee ID**, implement
`Comparable` inside the class.

``` java
class Employee implements Comparable<Employee> {

    int id;
    String name;
    double salary;

    Employee(int id, String name, double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    @Override
    public int compareTo(Employee other) {
        return Integer.compare(this.id, other.id);
    }
}
```

Usage:

``` java
Collections.sort(employeeList);
```

### `compareTo()` Return Values

``` text
Negative -> Current object comes first
Zero     -> Both are equal
Positive -> Other object comes first
```

Examples:

``` java
5.compareTo(8); // Negative
8.compareTo(5); // Positive
7.compareTo(7); // Zero
```

------------------------------------------------------------------------

# Comparator

Suppose tomorrow HR wants to sort by salary, next day by name, and later
by department.

Instead of changing the `Employee` class every time, create separate
comparison strategies.

``` java
Comparator<Employee> salaryComparator =
    (e1, e2) -> Double.compare(e1.salary, e2.salary);

Collections.sort(employeeList, salaryComparator);
```

Sort by name:

``` java
Comparator<Employee> nameComparator =
    (e1, e2) -> e1.name.compareTo(e2.name);
```

------------------------------------------------------------------------

# Comparable vs Comparator

Think like this:

-   **Comparable:** The object itself decides its default ordering.
-   **Comparator:** Someone outside decides how to compare the objects
    today.

------------------------------------------------------------------------

# Real-Life Analogy

## Comparable

Every student naturally has a Roll Number.

So:

``` java
class Student implements Comparable<Student>
```

Sort by Roll Number.

## Comparator

Teachers may want to sort students by:

-   Marks
-   Age
-   Name

Each requires a different Comparator.

------------------------------------------------------------------------

# Java 8 Comparator Features

``` java
Comparator<Employee> bySalary =
    Comparator.comparing(Employee::getSalary);
```

Descending:

``` java
Comparator<Employee> bySalary =
    Comparator.comparing(Employee::getSalary)
              .reversed();
```

Multiple fields:

``` java
Comparator<Employee> comparator =
    Comparator.comparing(Employee::getDepartment)
              .thenComparing(Employee::getSalary)
              .thenComparing(Employee::getName);
```

------------------------------------------------------------------------

# Collections That Use Them

Examples:

-   TreeSet
-   TreeMap
-   PriorityQueue

If no Comparator is supplied, these expect the object to implement
Comparable.

------------------------------------------------------------------------

# Internal Working

With Comparable:

``` java
if (obj1.compareTo(obj2) > 0)
    swap();
```

With Comparator:

``` java
if (comparator.compare(obj1, obj2) > 0)
    swap();
```

The sorting algorithm remains the same. Only the comparison method
changes.

------------------------------------------------------------------------

# Why does compareTo() return int instead of boolean?

Sorting needs three outcomes:

-   Negative
-   Zero
-   Positive

A boolean cannot represent equality and ordering together.

------------------------------------------------------------------------

# Best Practices

Instead of:

``` java
return this.salary - other.salary;
```

Use:

``` java
return Integer.compare(this.salary, other.salary);
```

For doubles:

``` java
return Double.compare(this.salary, other.salary);
```

This avoids overflow and handles floating-point values correctly.

------------------------------------------------------------------------

# Comparable vs Comparator Summary

  -------------------------------------------------------------------------------------
  Feature             Comparable                 Comparator
  ------------------- -------------------------- --------------------------------------
  Package             `java.lang`                `java.util`

  Method              `compareTo()`              `compare()`

  Implemented by      Same class                 Separate class/object/lambda

  Natural ordering    Yes                        No

  Multiple sorting    No                         Yes
  criteria                                       

  Modify original     Yes                        No
  class                                          

  Default sorting     Yes                        No

  Usage               `Collections.sort(list)`   `Collections.sort(list, comparator)`
  -------------------------------------------------------------------------------------

------------------------------------------------------------------------

# Interview Answer

> **Comparable** is used to define the natural ordering of a class. The
> comparison logic is implemented inside the class using the
> `compareTo()` method, making it suitable when there is a single
> default sorting order.
>
> **Comparator** is used when multiple sorting strategies are needed
> without modifying the original class. It defines comparison logic
> externally through the `compare()` method. Since Java 8, features like
> `Comparator.comparing()`, `thenComparing()`, and `reversed()` make
> Comparator the preferred approach for flexible and reusable sorting
> logic.
