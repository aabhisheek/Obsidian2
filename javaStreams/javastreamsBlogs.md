

- ==Streams do not modify the original data; they only produce results using their methods.==
- - Intermediate operations (like filter, map, etc.) ==are lazy and return another Stream, so you can chain them together.==
- A terminal operation (like collect, forEach, count) ends the stream and gives the final result.
1)
![](https://www.geeksforgeeks.org/java/stream-in-java/)


2)map
filter
sorted
flatmap




3)there are more than the above mentioned 6 intermediate methods and 7 terminal methods , 

but the above mentioned are the most commonly used 80 percent of the time.


4)there is also a term called primitive streams and normal streams 

normal streams use wrapper class datatype like Integer


whereas  primitive streams use non wrapper classes primitive datatypes like int .





5)

Nice — let’s walk through this stream pipeline step-by-step and see exactly what each line does, how the pipeline is evaluated, and what the final outputs are.

**Input data**

```java
List<List<String>> listOfLists = Arrays.asList(
    Arrays.asList("Reflection", "Collection", "Stream"),
    Arrays.asList("Structure", "State", "Flow"),
    Arrays.asList("Sorting", "Mapping", "Reduction", "Stream")
);
```

So `listOfLists` contains 3 inner lists of strings.

You also create an empty set for side effects:

```java
Set<String> intermediateResults = new HashSet<>();
```

This will be used later with `peek` to capture values as they flow through the pipeline.

---

Now the stream pipeline:

```java
List<String> result = listOfLists.stream()
    .flatMap(List::stream)
    .filter(s -> s.startsWith("S"))
    .map(String::toUpperCase)
    .distinct()
    .sorted()
    .peek(s -> intermediateResults.add(s))
    .collect(Collectors.toList());
```

I'll explain each operation (in pipeline order), what it _does_, whether it is **intermediate** or **terminal**, and any important behavior/notes:

1. **`listOfLists.stream()`**
    
    - Creates a `Stream<List<String>>` from the outer list.
        
    - This is the _source_ of the pipeline. By default it's a **sequential** stream (not parallel).
        
    - No elements are processed yet; stream setup is lazy.
        
2. **`.flatMap(List::stream)`**
    
    - `flatMap` is an _intermediate_ operation that transforms each element (here each inner `List<String>`) into a stream (`List::stream`), then _flattens_ all those inner streams into a single `Stream<String>`.
        
    - Effectively converts `Stream<List<String>>` → `Stream<String>` by concatenating all inner lists’ elements in encounter order.
        
    - After this step, the sequence of items entering the rest of pipeline (in encounter order) will be:  
        `Reflection, Collection, Stream, Structure, State, Flow, Sorting, Mapping, Reduction, Stream`
        
3. **`.filter(s -> s.startsWith("S"))`**
    
    - An _intermediate_ _stateless_ operation that keeps only elements satisfying the predicate.
        
    - Here it keeps strings whose first character is `'S'` (capital S). It does **not** match lowercase `s`.
        
    - From the flattened sequence above, the elements that pass are:  
        `Stream, Structure, State, Sorting, Stream`
        
    - Important: `filter` is lazy — it only tests elements as they are requested by downstream.
        
4. **`.map(String::toUpperCase)`**
    
    - An _intermediate_ _stateless_ operation that maps each passing element to another value.
        
    - Here it converts each string to uppercase, so the sequence becomes:  
        `STREAM, STRUCTURE, STATE, SORTING, STREAM`
        
    - Mapping happens element-by-element as they flow downstream.
        
5. **`.distinct()`**
    
    - An _intermediate_ **stateful** operation that removes duplicate elements (according to `equals()`).
        
    - Because the stream elements are now uppercase, case differences are already normalized.
        
    - It retains only the first occurrence of each unique value in the _encounter order_ of the stream.
        
    - From `STREAM, STRUCTURE, STATE, SORTING, STREAM` → duplicates removed → `STREAM, STRUCTURE, STATE, SORTING`
        
    - Note: `distinct()` may require remembering seen elements (so it can buffer/state).
        
6. **`.sorted()`**
    
    - An _intermediate_ **stateful** operation that sorts the elements according to natural order (here lexicographic order of strings).
        
    - Because it must see all elements to sort, this causes the pipeline to collect elements internally, sort them, and then emit sorted elements.
        
    - Sorting the distinct items `STREAM, STRUCTURE, STATE, SORTING` yields (lexicographically):  
        `SORTING, STATE, STREAM, STRUCTURE`  
        (explanation: "SO…" < "ST…" and among "STREAM" vs "STRUCTURE" "E" < "U", so STREAM < STRUCTURE)
        
    - After `sorted()`, the stream’s encounter order is the sorted order.
        
7. **`.peek(s -> intermediateResults.add(s))`**
    
    - `peek` is an _intermediate_ operation intended for debugging/side-effects; it receives each element as it passes and runs the given action.
        
    - In your code, `peek` adds each element to the `intermediateResults` `HashSet`.
        
    - Important notes:
        
        - `peek` is only executed when the pipeline is evaluated by a terminal operation (here `.collect(...)`).
            
        - Because you’re using a `HashSet`, the order stored in `intermediateResults` is undefined (HashSet does not preserve insertion order). It will contain the same elements as the final stream output, but order is not guaranteed.
            
        - Using `peek` for side effects is allowed but often discouraged in production code because it mixes functional and side-effecting styles. If you need to collect elements into a set, better do it via a collector. `peek` is primarily for debugging or very specific side effects.
            
8. **`.collect(Collectors.toList())`**
    
    - This is a **terminal** operation — it causes the entire pipeline to execute.
        
    - `Collectors.toList()` collects the stream elements into a `List` (normally an `ArrayList`) preserving stream encounter order.
        
    - Because the encounter order after `sorted()` is `SORTING, STATE, STREAM, STRUCTURE`, the resulting `List<String> result` will be:  
        `["SORTING", "STATE", "STREAM", "STRUCTURE"]`
        

---

**Evaluation & execution notes (important behavior)**

- The stream pipeline is **lazy**: nothing happens until the terminal operation (`collect`) runs.
    
- `distinct()` and `sorted()` are **stateful** intermediate ops — they may buffer elements and need to see multiple (or all) items before emitting any downstream.
    
- `peek` runs for each element _after sorting_ (because it’s placed after `sorted()`), so it will observe elements in sorted order.
    
- Since you used `map(String::toUpperCase)` before `distinct()`, duplicates are removed in a case-insensitive way (because everything is uppercase at that point).
    
- If you ran the stream **in parallel** (e.g., `listOfLists.parallelStream()`), behavior of `peek` and order-sensitive operations could be different — `sorted()` will still produce sorted results but `peek` may be invoked concurrently from multiple threads, and `HashSet` is not thread-safe. As written, this pipeline is sequential and safe.
    

**Result summary**

- `result` (the collected List) = `["SORTING", "STATE", "STREAM", "STRUCTURE"]`
    
- `intermediateResults` (the HashSet) will contain the same four strings but in arbitrary order, e.g. `[STREAM, STRUCTURE, SORTING, STATE]` (HashSet order not guaranteed).
    

**Small advice**

- If you need to _both_ collect into a `List` and a `Set`, prefer doing that explicitly with collectors (or collect into a `LinkedHashSet` for predictable insertion order), rather than relying on `peek` side-effects.
    
- Use `peek` mainly for debugging (e.g., `peek(System.out::println)`) or very controlled side-effects.
    

If you want, I can show a quick runnable snippet (with `System.out.println`) to demonstrate the intermediate steps and final values. Want that?









