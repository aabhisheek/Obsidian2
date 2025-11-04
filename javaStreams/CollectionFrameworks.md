![[Pasted image 20251027153609.png]]
![[Pasted image 20251027153710.png]]



```
Array.asList is a List type which have fixed size , like array 
so when you write List<T> , its actually a ArrayList.
```

---


### ✅ `Arrays.asList(...)` — What it _actually_ does

`Arrays.asList()` returns a **fixed-size list view** of the given array.  
This means:

- It **is a `List`**, but **not** an `ArrayList` (it just implements the `List` interface).
    
- It is **backed by the original array** — meaning if you change the array, the list changes too, and vice versa.
    
- You **cannot add or remove elements** from this list — only **modify existing elements**.



so array.asList returns a list view of fixed size , you can only modify existing elements , not add 

as it is backed y array not arrayList, 


![[Pasted image 20251027154800.png]]




```java
String[] arr = {"A", "B", "C"};
List<String> list = Arrays.asList(arr);

list.set(1, "Z"); // ✅ works — modifies both list and array
System.out.println(Arrays.toString(arr)); // [A, Z, C]

list.add("D"); // ❌ throws UnsupportedOperationException
list.remove("A"); // ❌ throws UnsupportedOperationException

```




![[Pasted image 20251027155101.png]]




```java
import java.util.ArrayList;

public class Main {
  public static void main(String[] args) {
    ArrayList<String> cars = new ArrayList<String>();
    cars.add("Volvo");
    cars.add("BMW");
    cars.add("Ford");

    cars.add(0, "Mazda"); // Insert element at the beginning of the list (0)

     cars.get(0);  // Get the first element
     
     cars.set(0, "Opel");
    
    // cars.clear();   to remove all elements of array  

     cars.remove(0);   // to remove element at 0 position
     
     

    System.out.println(cars);
  }
}
```




