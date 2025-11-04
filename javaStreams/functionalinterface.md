
functional interfaces are those interfaces which have only 1 abstract  function.

that is only one method which does not have any body.
and we can implement them using lambda funtions.

then we have a runnable interface which has run function 
which we need to use to make thread.

![[Pasted image 20251023161831.png]]

![[Pasted image 20251023161859.png]]



![[Pasted image 20251020090922.png]]

explain this especially what does integer represent 


its just like in case of maps and  sets and vector we define what types of datatypes we are going to work with , same way

so now connect and dont confuse 


Great question — let’s break down that snippet step by step 👇

---

### 💡 Code Recap

```java
// Predicate --> Functional interface (Boolean valued function)
Predicate<Integer> isEven = x -> x % 2 == 0;
System.out.println(isEven.test(4));
```

---

### 🔍 What’s Happening Here

#### 1. **Predicate**

`Predicate` is a **functional interface** in Java (`java.util.function.Predicate<T>`).  
It represents a function that takes **one input** and returns a **boolean** value.

So basically:

```java
Predicate<T>   -->   takes T as input, returns boolean
```

---

#### 2. **`<Integer>` — What It Means**

`<Integer>` is a **generic type parameter** — it defines **what type of data** this Predicate works with.

just like maps,sets.

✅ Here:  
`Predicate<Integer>` means this predicate takes an **Integer input** and returns a **boolean**.

So the method signature is effectively like:

```java
boolean test(Integer x);
```

---

#### 3. **Lambda Expression:**

```java
x -> x % 2 == 0
```

- This is the **implementation** of the Predicate’s `test()` method.
    
- For any integer `x`, it checks if `x % 2 == 0` → returns `true` if even, `false` otherwise.
    

So internally:

```java
isEven.test(4)
```

calls the lambda:

```java
return 4 % 2 == 0;  // true
```

---

#### 4. **`test(4)`**

- The `test()` method of Predicate executes the lambda.
    
- It returns a boolean (`true` if even, `false` if odd).
    

So:

```java
System.out.println(isEven.test(4)); // Output: true
```

---

### 🧠 Analogy

Think of it like a **boolean function**:

```java
boolean isEven(int x) {
    return x % 2 == 0;
}
```

But instead of writing a full method, Predicate + Lambda lets you write it inline.

---

### ✅ Summary Table

| Part              | Meaning                                                          |
| ----------------- | ---------------------------------------------------------------- |
| `Predicate<T>`    | Functional interface that takes one argument and returns boolean |
| `<Integer>`       | Type parameter → means input will be Integer                     |
| `x -> x % 2 == 0` | Lambda implementation of the test method                         |
| `isEven.test(4)`  | Calls the lambda with argument `4`                               |
| Output            | `true`                                                           |

---

Would you like me to show how `Predicate` can be used in **filtering a list** (e.g., filtering even numbers from a list using streams)? That’s where it becomes really useful in real-world Java code.



Functional Interface vs predicate 

![[Pasted image 20251102095638.png]]