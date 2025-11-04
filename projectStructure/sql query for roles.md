

List<String> roles = userContext.getRoles();


the most confusing since the starting 

```sql
SELECT r.name
FROM roles r
JOIN user_roles ur ON ur.role_id = r.id
JOIN users u ON u.id = ur.user_id
WHERE u.email = ?;
```



