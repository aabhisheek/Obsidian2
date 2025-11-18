
Perfect — `@Data` is another **Lombok** annotation you’ll see all the time in modern Java, especially in **Spring Boot projects**.

Let’s go through it **step-by-step**, from what it does, what it generates, how it works internally, where to use it, and when not to.

---

## 🧩 1. What is `@Data`?

`@Data` is a **Lombok annotation** that **automatically generates** the most common boilerplate code for a Java class.

When you annotate a class with `@Data`, Lombok automatically creates:

- **Getters** for all fields
    
- **Setters** for all non-final fields
    
- **`toString()`** method
    
- **`equals()` and `hashCode()`** methods
    
- **`RequiredArgsConstructor`** (constructor for `final` and `@NonNull` fields)
    

---

### 🔹 Example Without Lombok

Normally, you might write:

```java
public class User {
    private Long id;
    private String name;
    private String email;

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }

    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "', email='" + email + "'}";
    }

    @Override
    public boolean equals(Object o) {
        // complex equals() code
    }

    @Override
    public int hashCode() {
        // complex hashCode() code
    }
}
```

That’s a lot of boilerplate code.

---

### 🔹 With Lombok `@Data`

```java
import lombok.Data;

@Data
public class User {
    private Long id;
    private String name;
    private String email;
}
```

✅ Done. Lombok generates everything automatically at **compile-time**.

You can now call:

```java
User user = new User();
user.setName("Abhishek");
System.out.println(user.getName());
System.out.println(user);
```

---

## ⚙️ 2. What Exactly `@Data` Includes

Internally, `@Data` is a **shortcut** for combining several Lombok annotations:

```java
@Data = @Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor
```

So, it’s equivalent to writing:

```java
@Getter
@Setter
@ToString
@EqualsAndHashCode
@RequiredArgsConstructor
public class User { ... }
```

---

## 🧠 3. Each Part Explained

Let’s understand what each generated method does:

|Method|Purpose|Example|
|---|---|---|
|**Getters/Setters**|Access or modify private fields|`getName()`, `setName()`|
|**`toString()`**|Useful for debugging/logging|`User(id=1, name=Abhishek, email=...)`|
|**`equals()` / `hashCode()`**|Used for comparing objects or using them in sets/maps|`user1.equals(user2)`|
|**`RequiredArgsConstructor`**|Generates constructor for all `final` or `@NonNull` fields|`new User(id, name)`|

---

## 🧩 4. How It Works Internally

When you compile a class with `@Data`, Lombok uses an **annotation processor** that runs during compilation.  
It modifies the **Abstract Syntax Tree (AST)** — basically injecting extra methods into your class before bytecode is created.

That’s why when you open compiled `.class` files, you’ll see those methods exist even though you didn’t write them.

---

## 🧰 5. Real-World Usage Examples

### ✅ In Entity Classes

Commonly used in JPA or Hibernate entities.

```java
import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import lombok.Data;

@Data
@Entity
public class Book {
    @Id
    private Long id;
    private String title;
    private String author;
}
```

✅ No need to manually create getters/setters for every field.  
✅ Works perfectly with ORM frameworks like Hibernate.

---

### ✅ In DTOs (Data Transfer Objects)

When sending/receiving data in REST APIs:

```java
@Data
public class UserDTO {
    private String name;
    private String email;
}
```

Used for serialization/deserialization (e.g., JSON ↔ Java Object).

---

### ✅ In POJOs (Plain Old Java Objects)

For config classes or internal data holders:

```java
@Data
public class Config {
    private String host;
    private int port;
}
```

---

## ⚠️ 6. When **Not** to Use `@Data`

While it’s convenient, you **shouldn’t always use it** — especially in **entities** or **classes with complex relationships**.

### 🚫 1. JPA Entities with bidirectional relationships

In such cases, `@ToString()` or `@EqualsAndHashCode()` may cause **infinite recursion**.

Example:

```java
@Data
@Entity
public class User {
    @OneToMany(mappedBy = "user")
    private List<Order> orders;
}
```

Here, `User.toString()` calls `Order.toString()`, which calls back `User.toString()` — 💥 infinite loop!

**Fix:** Replace with:

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class User {
    // manually control equals, hashCode, toString
}
```

---

### 🚫 2. Performance-sensitive objects

`equals()` and `hashCode()` are automatically generated using **all fields** — sometimes that’s inefficient if you only need a subset.

---

### 🚫 3. Immutable Classes

If you want immutability (fields should not change after creation), use:

```java
import lombok.Value;

@Value   // immutable alternative to @Data
public class User {
    String name;
    String email;
}
```

`@Value` creates an **immutable** class — all fields `private` and `final`, only getters, and a constructor.

---

## 🧾 7. Common Lombok Annotations Related to `@Data`

|Annotation|Description|
|---|---|
|`@Getter`|Generates only getters|
|`@Setter`|Generates only setters|
|`@ToString`|Generates `toString()`|
|`@EqualsAndHashCode`|Generates equality and hash methods|
|`@NoArgsConstructor`|Generates no-argument constructor|
|`@AllArgsConstructor`|Generates constructor with all fields|
|`@RequiredArgsConstructor`|Constructor for `final` / `@NonNull` fields|
|`@Value`|Immutable version of `@Data`|

---

## 🚀 8. Example in a Real Spring Boot Application

```java
@Data
@Entity
@Table(name = "employees")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String department;
}
```

In a service class:

```java
@Slf4j
@Service
public class EmployeeService {

    public void printEmployee(Employee emp) {
        log.info("Employee info: {}", emp);
    }
}
```

When you run this:

```
Employee info: Employee(id=1, name=Abhishek, department=IT)
```

Everything — getters, setters, and `toString()` — is auto-generated by `@Data`.

---

## ✅ Summary Table

|Feature|Description|
|---|---|
|**Annotation**|`@Data`|
|**Provided by**|Lombok|
|**Generates**|Getters, Setters, `toString()`, `equals()`, `hashCode()`, Required constructor|
|**Best For**|DTOs, POJOs, simple entities|
|**Avoid In**|Complex JPA entities, immutable objects|
|**Alternative**|`@Value` (immutable), `@Getter` + `@Setter` (for more control)|

---

Would you like me to show a **before-and-after comparison** of a real JPA entity class with and without `@Data` — including the pitfalls and recommended alternative annotations (`@Getter`, `@Setter`, etc.)?  
That’s very useful to see how professionals use it in Spring Boot production code.






Good question — these three methods (`toString()`, `equals()`, and `hashCode()`) are **fundamental methods** defined in Java’s base class `Object`, and they are **commonly used or overridden** in data classes like `@Data` in **Lombok** or POJOs (Plain Old Java Objects).

Let’s break down each one 👇

---

### 🧩 1. **`toString()`**

**Purpose:**  
Returns a human-readable **string representation** of an object.

**Default behavior (from `Object`):**  
Prints something like:

```java
com.example.User@6d06d69c
```

(that’s the class name + hash code in hex)

**When overridden:**  
It shows the **fields and their values**, making debugging or logging much easier.

✅ **Example:**

```java
public class User {
    private String name;
    private int age;

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}
```

**Usage:**

```java
User u = new User("Abhishek", 22);
System.out.println(u); 
// Output → User{name='Abhishek', age=22}
```

> 🔹 In Lombok’s `@Data`, `toString()` is **auto-generated** for you — it includes all fields.

---

### ⚖️ 2. **`equals(Object obj)`**

**Purpose:**  
Used to **compare two objects for equality** (logical equality, not reference equality).

**Default behavior (from `Object`):**  
Compares **references** — two objects are equal only if they are the **same instance**.

**When overridden:**  
It compares **values of fields**, so two objects with the same data are considered equal.

✅ **Example:**

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true; // same reference
    if (o == null || getClass() != o.getClass()) return false;
    User user = (User) o;
    return age == user.age && Objects.equals(name, user.name);
}
```

**Usage:**

```java
User u1 = new User("Abhishek", 22);
User u2 = new User("Abhishek", 22);

System.out.println(u1.equals(u2)); // true if overridden
```

> 🔹 Lombok’s `@Data` automatically generates this, so you don’t have to.

---

### 🧮 3. **`hashCode()`**

**Purpose:**  
Returns an integer **hash value** used in hash-based collections like:

- `HashMap`
    
- `HashSet`
    
- `HashTable`
    

**Rule:**  
If two objects are **equal** (via `equals()`), they **must have the same `hashCode()`**.

✅ **Example:**

```java
@Override
public int hashCode() {
    return Objects.hash(name, age);
}
```

**Usage:**  
Used internally by collections:

```java
Set<User> users = new HashSet<>();
users.add(new User("Abhishek", 22));
users.add(new User("Abhishek", 22)); // Won’t be added again if equals/hashCode overridden
```

> 🔹 Lombok’s `@Data` generates both `equals()` and `hashCode()` automatically — consistent with all fields.

---

### 🧠 Summary Table

|Method|Purpose|Default Behavior|When Overridden|
|---|---|---|---|
|`toString()`|String representation|ClassName@hash|Field values shown|
|`equals()`|Logical comparison|Compares memory refs|Compares field values|
|`hashCode()`|Hash for collections|Object identity|Based on fields|

---

### 💡 In short:

If you use **Lombok’s `@Data`**, it automatically generates:

- `toString()` ✅
    
- `equals()` ✅
    
- `hashCode()` ✅
    
- Getters & setters ✅
    
- `@RequiredArgsConstructor` ✅
    

So your data class becomes fully functional without boilerplate.

---

Would you like me to show how **Lombok’s generated versions** of these methods look under the hood for a sample `@Data` class?