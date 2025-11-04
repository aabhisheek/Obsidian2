In **Spring Boot**, the `@Transactional` annotation is used to **manage transactions** automatically — it tells Spring that a particular method or class should run inside a **database transaction**.

Let’s break this down simply 👇

---

### 🔹 What is a Transaction?

A **transaction** is a single unit of work that:

- Groups multiple database operations together
    
- Ensures **ACID** properties:
    
    - **A**tomicity – all succeed or all fail
        
    - **C**onsistency – keeps data valid
        
    - **I**solation – one transaction doesn’t affect another
        
    - **D**urability – once committed, changes are permanent
        

Example:

```java
public void transferMoney(Long fromAccount, Long toAccount, double amount) {
    withdraw(fromAccount, amount);
    deposit(toAccount, amount);
}
```

If the `withdraw()` succeeds but `deposit()` fails, the whole operation should **roll back** — otherwise money is lost!.

---

### 🔹 What `@Transactional` Does

When you put `@Transactional` on a method or class, Spring:

1. Starts a transaction before the method runs
    
2. Executes all the database operations inside it
    
3. **Commits** the transaction if the method completes successfully
    
4. **Rolls back** if a runtime exception (unchecked) occurs
    

---

### 🔹 Example Usage

```java
@Service
public class BankService {

    @Autowired
    private AccountRepository accountRepository;

    @Transactional
    public void transfer(Long fromId, Long toId, double amount) {
        Account from = accountRepository.findById(fromId).get();
        Account to = accountRepository.findById(toId).get();

        from.setBalance(from.getBalance() - amount);
        to.setBalance(to.getBalance() + amount);

        accountRepository.save(from);
        accountRepository.save(to);
    }
}
```

✅ If everything goes fine → `COMMIT`  
❌ If any exception occurs → `ROLLBACK` (no money lost)

---

### 🔹 Common Options of `@Transactional`

| Attribute     | Description                                                            | Example                                         |
| ------------- | ---------------------------------------------------------------------- | ----------------------------------------------- |
| `propagation` | Defines how transactions behave when called inside another transaction | `Propagation.REQUIRED` (default)                |
| `isolation`   | Defines how much one transaction is isolated from others               | `Isolation.READ_COMMITTED`                      |
| `readOnly`    | Optimizes for read-only queries (no updates)                           | `@Transactional(readOnly = true)`               |
| `rollbackFor` | Specify which exceptions trigger rollback                              | `@Transactional(rollbackFor = Exception.class)` |
| `timeout`     | Max time (in seconds) before rollback                                  | `@Transactional(timeout = 10)`                  |

---

### 🔹 Where to Use It

- **Service layer** (recommended): Keeps transaction management separate from controllers and repositories.
    
- Avoid using it directly in **controllers** or **repositories**.
    

---

### 🔹 Summary

| Behavior             | Description                                |
| -------------------- | ------------------------------------------ |
| Starts a transaction | Before method call                         |
| Commits              | On success                                 |
| Rolls back           | On runtime exception                       |
| Declarative          | No need to write transaction code manually |

---

Would you like me to show how it behaves **with and without** `@Transactional` using a small real example (like transferring money or saving multiple entities)?