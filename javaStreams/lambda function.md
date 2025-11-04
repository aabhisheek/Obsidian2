
![[Pasted image 20251020090040.png]]



Perfect — this is a **Java functional interface + lambda expression** example. Let’s go line by line 👇

---

### 🧩 Code Overview

```java
interface MathOperation {
    int operate(int a, int b);
}
```

- `MathOperation` is a **functional interface** — it has only **one abstract method**, `operate(int a, int b)`.
    
- This allows us to use **lambda expressions** to provide its implementation inline.
    

---

### 💡 Lambda Implementations

```java
MathOperation sumOperation = (a, b) -> a + b;
MathOperation subtractOperation = (a, b) -> a - b;
```

- `(a, b) -> a + b` is a **lambda expression**.
    
- It’s equivalent to writing an anonymous class like:
    
    ```java
    MathOperation sumOperation = new MathOperation() {
        @Override
        public int operate(int a, int b) {
            return a + b;
        }
    };
    ```
    
- The compiler knows that `sumOperation` must implement `operate()` from the interface.
    

---

### 🧮 Using the Operations

```java
int res = sumOperation.operate(1, 2);
System.out.println(res);
```

- Calls the `operate` method implemented by the lambda.
    
- `sumOperation.operate(1, 2)` → `1 + 2 = 3`
    
- So output will be:
    
    ```
    3
    ```
    

---

### 🔍 Summary

|Concept|Explanation|
|---|---|
|**Functional Interface**|Interface with one abstract method (like `Runnable`, `Comparator`, etc.)|
|**Lambda Expression**|Concise way to implement a functional interface|
|**Syntax**|`(parameters) -> expression` or `(parameters) -> { statements }`|
|**Purpose**|Avoids boilerplate anonymous classes, improves readability|

---

### 🧠 You could also do:

```java
System.out.println(subtractOperation.operate(5, 3)); // prints 2
```

---

Would you like me to show how to make this code use a **method reference** (e.g., `Math::addExact`) or pass these lambdas to another method (higher-order function style)?