
In Java (especially in **Spring Framework**), the `@Value` annotation is used to **inject values into fields, methods, or constructor parameters** from various sources, such as:

1. **Property files** (`application.properties` or `application.yml`)
    
2. **Environment variables**
    
3. **System properties**
    
4. **Expressions (SpEL – Spring Expression Language)**
    

---

### **Common Uses of `@Value`**

1. **Injecting a property from `application.properties`**
    

```java
@Value("${app.name}")
private String appName;
```

If `application.properties` has:

```
app.name=MyApp
```

Then `appName` will be `"MyApp"`.

---

2. **Injecting default values**
    

```java
@Value("${app.version:1.0}")
private String appVersion;
```

Here, if `app.version` is not defined in properties, it defaults to `"1.0"`.

---

3. **Injecting environment variables**
    

```java
@Value("${JAVA_HOME}")
private String javaHome;
```

---

4. **Using Spring Expression Language (SpEL)**
    

```java
@Value("#{2 * 5}")
private int calculation; // 10

@Value("#{T(java.lang.Math).random() * 100}")
private double randomValue;
```

---

5. **Injecting lists or arrays**
    

```java
@Value("${app.servers}")
private List<String> servers;
```

If `application.properties` has:

```
app.servers=server1,server2,server3
```

Then `servers` will be a list: `[server1, server2, server3]`.

---

✅ **Summary:**  
`@Value` is primarily used to **inject externalized configuration values into your Spring beans**, making your code more flexible and environment-independent.

---

If you want, I can also explain the **difference between `@Value` and `@ConfigurationProperties`**, which is important for managing multiple configuration values efficiently. Do you want me to explain that?