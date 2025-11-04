
java does not understand 

## ⚙️ 4. Why you don’t see `UserType` in SQL

Because SQL databases are **language-agnostic** — they don’t understand Java classes, objects, or enums.  
They only know about simple column types: `VARCHAR`, `INT`, `DATE`, etc.

So:

- Java sees: `UserType.EMPLOYEE` (object)
    
- SQL stores: `'EMPLOYEE'` (string)
    
- SQL can’t show: `UserType.EMPLOYEE`, because that’s a Java concept.



```java
@Getter  
@Setter  
@Entity  
@Table(name = "users")  
public class User extends BaseEntity {  
  
  @Id  
  @GeneratedValue(strategy = GenerationType.IDENTITY)  
  private Long id;  
  
  @Column(name = "emp_id")  
  private String empId;  
  
  @Column(name = "email")  
  private String email;  
  
  @Column(name = "name")  
  private String name;  
  
  @Enumerated(EnumType.STRING)  
  private UserType type;
```


so UserType does not shows in sql.


