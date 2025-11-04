
1)

   now as you have learned to draw er diagrams


  another ways to understand are 

```java
@Entity
@Table(name = "user_request_tracking")
public class UserRequestTracking {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_request_id", nullable = false)
    private UserRequest userRequest;
}
```


### How to understand mapping:

| Annotation                  | Meaning                                 |
| --------------------------- | --------------------------------------- |
| `@Entity`                   | Marks it as a DB table                  |
| `@Table(name="...")`        | The actual table name                   |
| `@Id`                       | Primary key                             |
| `@GeneratedValue`           | Auto increment or sequence              |
| `@Column(name="...")`       | Maps a field to a DB column             |
| `@OneToMany` / `@ManyToOne` | Defines relationship with another table |
| `@JoinColumn(name="...")`   | The foreign key column                  |



