
The **Builder pattern** in Spring Boot (and in Java in general) is used to create **complex objects step-by-step** in a **clean, readable, and immutable** way — especially when a class has **many fields or optional parameters**.

Let’s go step-by-step 👇


.builder()......defineparameters.......build()   this is the syntax

```java

User user = new User("Abhishek", "abhi@gmail.com", "ADMIN", 25);

to 

User user = User.builder()
        .name("Abhishek")
        .email("abhi@gmail.com")
        .role("ADMIN")
        .age(25)
        .build();
```


---

## 🧩 1. The Problem Without Builder

Suppose you have a class like this:

```java
public class User {
    private String name;
    private String email;
    private String role;
    private int age;

    public User(String name, String email, String role, int age) {
        this.name = name;
        this.email = email;
        this.role = role;
        this.age = age;
    }
}
```

Creating a new `User` object looks like:

```java
User user = new User("Abhishek", "abhi@gmail.com", "ADMIN", 25);
```

This works fine — **but**:

- You must remember the **order of parameters**.
    
- It’s **hard to skip optional fields**.
    
- It’s not very readable.
    

---

## 🏗️ 2. Using Builder Pattern

With **Lombok’s `@Builder` annotation** (commonly used in Spring Boot), you can write:

```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class User {
    private String name;
    private String email;
    private String role;
    private int age;
}
```

Now you can create an object like this:

```java
User user = User.builder()
        .name("Abhishek")
        .email("abhi@gmail.com")
        .role("ADMIN")
        .age(25)
        .build();
```

✅ **Benefits:**

- Easy to read
    
- No confusion about parameter order
    
- Optional fields can be skipped
    
- Produces **immutable-like** objects (if no setters are exposed)
    

---

## ⚙️ 3. How It’s Used in Spring Boot

The builder pattern is heavily used in Spring Boot code for creating:

### 🧾 DTOs (Data Transfer Objects)

Example:

```java
LeaveSummaryDto myLeaveSummary = LeaveSummaryDto.builder()
        .data(new ArrayList<>())
        .pageSize(0)
        .totalPage(0)
        .totalElement(0)
        .currentPage(0)
        .build();
```

You can easily pass this DTO as a response in a controller:

```java
return ResponseEntity.ok(myLeaveSummary);
```

---

### 🏢 Entities or Configurations

Some developers use builders even for **Entity objects** or **configuration beans**, e.g.:

```java
Employee emp = Employee.builder()
        .id(101)
        .name("John")
        .department("HR")
        .build();
```

---

### ⚡ REST Response Example

For REST APIs in Spring Boot, builder is often used to return **standardized responses**:

```java
return ApiResponse.builder()
        .status("SUCCESS")
        .message("User created successfully")
        .data(user)
        .build();
```

---

## 🧠 4. What `@Builder` Does Behind the Scenes

When you annotate a class with `@Builder`, Lombok **generates**:

- A **static inner Builder class**
    
- Fluent setter methods (returning the builder itself)
    
- A `build()` method that constructs the object
    

Essentially, it’s equivalent to this code:

```java
public static class UserBuilder {
    private String name;
    private String email;
    private String role;
    private int age;

    public UserBuilder name(String name) {
        this.name = name;
        return this;
    }

    public UserBuilder email(String email) {
        this.email = email;
        return this;
    }

    public UserBuilder role(String role) {
        this.role = role;
        return this;
    }

    public UserBuilder age(int age) {
        this.age = age;
        return this;
    }

    public User build() {
        return new User(name, email, role, age);
    }
}
```

---

## 🧰 5. Common Use Cases in Spring Boot

|Use Case|Example|
|---|---|
|DTO creation|`UserResponseDto.builder().name(...).build()`|
|Entity creation|`User.builder().id(...).role(...).build()`|
|Response structure|`ApiResponse.builder().status(...).message(...).build()`|
|Complex configuration objects|Builders in YAML/Java config for beans|

---

## 🧾 6. Pro Tip

You can combine it with `@AllArgsConstructor` and `@NoArgsConstructor` if you also want constructors:

```java
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class LeaveSummaryDto {
    private String name;
    private String email;
    private String empId;
    private String role;
    private int totalLeaves;
}
```

---

Would you like me to show **how the builder integrates with a Spring REST API response** (e.g., in a controller + service + DTO flow)?  
It will make it very clear where it fits in a real Spring Boot project.