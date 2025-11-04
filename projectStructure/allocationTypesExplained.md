
Excellent — this `AllocationType` enum defines **categories of project allocations**, i.e., _how a user is assigned to a project and what that assignment means_ in terms of billing, availability, or staffing.

Let’s go through each type in practical, real-world terms 👇

---

## 🧩 `AllocationType` — what it represents

This enum describes **the nature or business meaning** of a user’s project allocation.  
Every time an employee is allocated to a project, they are marked as one of these types.

---

### 🧱 1️⃣ `STAFFING`

> `"STAFFING"` means the user is **planned** or **reserved** for a project, but not yet actively working on it.

- Used during **resource planning** or **pipeline** phase.
    
- The user hasn’t started billing time or performing tasks yet.
    
- Often temporary — later converted to `DEPLOYABLE` once confirmed.
    

**Example:**

> HR or PM reserves “Riya” for an upcoming fintech project starting next month.

---

### ⚙️ 2️⃣ `DEPLOYABLE`

> `"DEPLOYABLE"` means the user is **ready and available** to be deployed or assigned to a live project.

- The person can be picked up for new work immediately.
    
- They’re not yet on an active client project but are _free_ and _billable_.
    
- In practice, “bench” employees are often marked as `DEPLOYABLE`.
    

**Example:**

> “Arjun” just rolled off one project and is now available for redeployment.

---

### 🚫 3️⃣ `NONDEPLOYABLE` (value: `"NON-DEPLOYABLE"`)

> `"NONDEPLOYABLE"` means the user **cannot be assigned** to any project for now.

- Often used for employees on **long leave**, **training**, **notice period**, or **internal non-project work**.
    
- They are **not billable** and **not available** for deployment.
    

**Example:**

> “Priya” is on maternity leave — marked as NONDEPLOYABLE.

---

### 🔒 4️⃣ `LOCKED`

> `"LOCKED"` means the user is **currently locked to a specific project** and **cannot be reassigned** elsewhere.

- Used to prevent managers from accidentally re-allocating or freeing the resource.
    
- Common for long-term client projects or where the contract binds a specific developer.
    

**Example:**

> “Rahul” is working on a critical government project — marked LOCKED to prevent changes.

---

### 💼 5️⃣ `NONBILLABLE` (value: `"NON-BILLABLE"`)

> `"NONBILLABLE"` means the user **is working**, but their effort is **not charged** to a client.

- Typically applies to **internal** or **support** work (e.g., R&D, training, HR portal, product maintenance).
    
- Useful for tracking productivity but not client revenue.
    

**Example:**

> “Sneha” is maintaining an internal automation tool — NONBILLABLE allocation.

---

## 🔁 How these interact in real systems

|Type|Billable?|Deployable?|Example Scenario|
|---|---|---|---|
|STAFFING|❌ (planned only)|✅ (future)|Planned for upcoming project|
|DEPLOYABLE|✅ (potentially)|✅|Ready to assign to new project|
|NONDEPLOYABLE|❌|❌|On leave/training/notice|
|LOCKED|✅|❌|Bound to existing project|
|NONBILLABLE|❌|✅|Internal project or training|

---

## ⚡ Integration with `applyAllocationOverrides()`

When an allocation request comes in as `NONBILLABLE` or `NONDEPLOYABLE`, the system **cannot infer this automatically** (since it’s outside normal deploy/billable flow).  
So the `applyAllocationOverrides()` method attaches an `AllocationTypeOverride` reason.

---

Would you like me to show a visual lifecycle (diagram/table) of how a user might transition between these allocation types during their project lifecycle?