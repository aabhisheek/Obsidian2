
1) if in entity we have  something like 

    ```java
    @Table(name = "allocations", uniqueConstraints = {  
        @UniqueConstraint(columnNames = {"user_id", "project_id"}, name ="uk_user_project")  
})  
@Where(clause = "is_active = true")  
public class Allocation extends BaseEntity {  
  
  @Id  
  @GeneratedValue(strategy = GenerationType.IDENTITY)  
  @Column(name = "id", updatable = false, nullable = false)  
  private Long id;  
  
  @ManyToOne  
  @JoinColumn(name = "project_id", nullable = false)  
  private Project project;
    ```

when we will query it through postgre sql then we will get only the joining column 
```java
 @ManyToOne  
  @JoinColumn(name = "project_id", nullable = false)  
  private Project project;
```

that is project_id 

it will not automatically perform join operations by itself.



2)in allocations in my database when i remove a allocation record i don't delete the 
record instead we just set the is_active=false.




