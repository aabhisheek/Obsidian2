
```java
@GetMapping({  
        "/userRequest/get/{fromDate}/{toDate}/{requestType}",  
        "/userRequest/get/{empEmails}/{fromDate}/{toDate}/{requestType}"  
})  
public ResponseEntity<GenericResponseDTO> getUserRequests(  
        @PathVariable(required = false) String empEmails,  
        @PathVariable String fromDate,  
        @PathVariable String toDate,  
        @PathVariable String requestType,  
        @RequestParam(defaultValue = "0") int page,  
        @RequestParam(defaultValue = "10") int size) throws ParseException {  
  
    System.out.println("inside getrequests");  
    try {  
        String emailFromToken = userContext.getEmail();  
        User authUser = userService.getUserByEmail(emailFromToken);  
  
        if (authUser == null) {  
            return ResponseEntity.badRequest()  
                    .body(new GenericResponseDTO("Authenticated user not found", null));  
        }  
  
        List<String> roles = userContext.getRoles();  
        boolean isHR = roles.contains(GlobalConstants.ROLE_HR);  
        boolean isManager = roles.contains(GlobalConstants.ROLE_MANAGER);  
        boolean isEmployee = roles.contains(GlobalConstants.ROLE_EMPLOYEE);  
        System.out.println("roles: " + roles);  
  
        List<User> targetUsers = new ArrayList<>();  
        if (empEmails == null || empEmails.isEmpty()) {  
            if (isHR) {  
                System.out.println("inside hr");  
                targetUsers = userService.getAllUsers();  
            } else if (isManager) {  
                System.out.println("inside manager");  
                targetUsers = userService.getAllEmployeesUnderManager(authUser);  
            } else if (isEmployee) {  
                System.out.println("inside employee");  
                targetUsers.add(authUser);  
            } else {  
                return ResponseEntity.status(HttpStatus.FORBIDDEN)  
                        .body(new GenericResponseDTO("Access denied", null));  
            }  
        } else {  
            System.out.println("inside else");  
            String[] emails = empEmails.split(",");  
            for (String email : emails) {  
                User targetUser = userService.getUserByEmail(email.trim());  
                if (targetUser == null) {  
                    return ResponseEntity.badRequest()  
                            .body(new GenericResponseDTO("User not found: " + email, null));  
                }  
  
                if (isHR ||  
                        (isManager && (emailFromToken.equalsIgnoreCase(email.trim()) ||  
                                userService.isEmployeeUnderManager(authUser, targetUser))) ||  
                        (isEmployee && emailFromToken.equalsIgnoreCase(email.trim()))) {  
                    targetUsers.add(targetUser);  
                } else {  
                    return ResponseEntity.status(HttpStatus.FORBIDDEN)  
                            .body(new GenericResponseDTO("Not authorized to view requests for: " + email, null));  
                }  
            }  
        }  
  
        List<UserRequestDTO> paginatedRequests = new ArrayList<>();  
        long totalElements = 0;  
        int totalPages = 0;  
  
        for (User user : targetUsers) {  
            UserRequestListOutputDTO dto = userRequestService.getUserRequestsByUser(  
                    user, fromDate, toDate, UserRequestType.valueOf(requestType), page, size, isHR ? null : (isManager ? authUser : null));  
  
            paginatedRequests.addAll(dto.getUserRequests());  
            totalElements += dto.getTotalElement();  
            totalPages = dto.getTotalPage();  
        }  
  
        UserRequestListOutputDTO finalDto = new UserRequestListOutputDTO();  
        finalDto.setTotalElement(totalElements);  
        finalDto.setTotalPage((int) Math.ceil((double) totalElements / size));  
        finalDto.setCurrentPage(page);  
        finalDto.setPageSize(size);  
        finalDto.setFromDate(fromDate);  
        finalDto.setToDate(toDate);  
        finalDto.setUserRequests(paginatedRequests);  
  
        LeaveDto leaveData = userRequestService.getMyLeaveData(authUser);  
        finalDto.setLeaves(leaveData.getPrimaryLeave() + leaveData.getSecondaryLeave());  
  
        return ResponseEntity.ok(new GenericResponseDTO("success", finalDto));  
  
    } catch (BadRequestException ex) {  
        return ResponseEntity.badRequest().body(new GenericResponseDTO(ex.getMessage(), null));  
    } catch (Exception e) {  
        return ResponseEntity.internalServerError()  
                .body(new GenericResponseDTO("Something went wrong: " + e.getMessage(), null));  
    }  
}
```




logic  flow  


1) get email


in user roles althou in roles there is no user named employee 

but it gets automatically added as employee 


got it in console


inside getrequests
roles: [ROLE_EMPLOYEE]
inside else
inside getrequests
roles: [ROLE_MANAGER, ROLE_EMPLOYEE]

so maager is an employee too 
so both roles are used 

in case of managers 

==the endpoint does not contain empEmails==
==it contains employee email when fetching its own comp_off requests applied== 

==but when manager opens approverequests/comp_off==
==the endpoint does not send emails== 

so thats the difference for approveRequests, does not send employeemail so enters first if condition empEmails null
else a user fetching its own userrequests 
it enters in else


so we want to correct for approveRequest so correct the first if only 


lets handle target users in first case 



although manager has both roles, manager and Employee
but first is the check of hr and manager 
so if anyone matches it wont enter next one 

so its own request would not show under approveRequest,

and if employee it hits the second with employee email 

so that employee logic with no userEmail  is useless althou looks good for syntax


also pay attention 
there is targetusers and targetuser , first one is a list and second one is which we add to the list after certain checks


if (isHR ||  
        (isManager && (emailFromToken.equalsIgnoreCase(email.trim()) ||  
                userService.isEmployeeUnderManager(authUser, targetUser))) ||  
        (isEmployee && emailFromToken.equalsIgnoreCase(email.trim()))) {  
    targetUsers.add(targetUser);



this condition check is wrong 

only if a person is checking its own userRequests 
then only it would enter this block 

so it would always add this user as targetUser.


now lets see what happens after targetUser is added 



after  targetuser 

it just fetches the userRequests of targetUser and paginates it and send 


so what we have to do now 

in case of comp off we have to add userRequest 



if it is a comp_off
fetch all userRequest
run a loop for each and check if userRequest.employee email equals current email of user add that userRequest 



---

1)
OUTPUT DTO leaveRequest

pagination SpringBoot

now lets take 15mins and understand this code while documenting it  for future reference 


```java
 for (User user : targetUsers) {  
        UserRequestListOutputDTO dto = userRequestService.getUserRequestsByUser(  
                user, fromDate, toDate, UserRequestType.valueOf(requestType), page, size, isHR ? null : (isManager ? authUser : null));  
  
        paginatedRequests.addAll(dto.getUserRequests());  
        totalElements += dto.getTotalElement();  
        totalPages = dto.getTotalPage();  
    }  
}  
//}  
  
UserRequestListOutputDTO finalDto = new UserRequestListOutputDTO();  
finalDto.setTotalElement(totalElements);  
finalDto.setTotalPage((int) Math.ceil((double) totalElements / size));  
finalDto.setCurrentPage(page);  
finalDto.setPageSize(size);  
finalDto.setFromDate(fromDate);  
finalDto.setToDate(toDate);  
finalDto.setUserRequests(paginatedRequests);  
  
LeaveDto leaveData = userRequestService.getMyLeaveData(authUser);  
finalDto.setLeaves(leaveData.getPrimaryLeave() + leaveData.getSecondaryLeave());
```


so here we have only one DTO which is 

UserRequestListOutputDTO


```java
@Getter  
@Setter  
public class UserRequestListOutputDTO {  
    private long totalElement;  
    private int totalPage;  
    private int currentPage;  
    private int pageSize;  
    private Double leaves;  
    private String fromDate;  
    private String toDate;  
    private List<UserRequestDTO> userRequests;  
}

now lets see structure of UserRequestDTO


@Getter  
@Setter  
public class UserRequestDTO  {  
    private LocalDateTime createdAt;  
    private LocalDateTime updatedAt;  
    private Long id;  
    private String empEmail;  
    private String requestFromDate;  
    private String requestToDate;  
    private String comments;  
    private UserRequestType requestType;  
    private String hrAction;  
    private int totalProjects;  
    private List<ProjectApprovalStatusDTO> projectApprovalStatuses;  
    private UserRequestStatus status;  
    private UserRequestUserDto user;  
    private String referenceFileUrl;  
    private Boolean isHalfDay;  
    private List<String> managers;  
}
```



Perfect 👍 Let’s break down exactly **how**  
`getUserRequestsByUser()` computes `totalElements` and `totalPages` internally through Spring Data’s `Pageable` system.

We’ll go layer-by-layer — from the repository to DTO — so you understand precisely how these values are set and how they interact with your earlier loop.

---

## 🧩 Step 1: The `Pageable` setup

```java
Pageable pageable = PageRequest.of(page, size, Sort.by("requestFromDate").descending());

//byDefault if nothing passed 
//page is 0 and size is 10 set in endpoint
```

This line tells Spring Data JPA to:

- Fetch records **page by page**.
    
- Each page has **`size`** records.
    
- Use **0-based indexing** (page 0 = first page).
    
- Sort results by `requestFromDate` descending.
    

---

## 🧩 Step 2: The Repository Query

```java
Page<UserRequest> userRequestsPage =
    userRequestRepository.findByUserAndRequestFromDateBetweenAndRequestTypeAndDeleted(
        user,
        new java.sql.Date(parsedFromDate.getTime()),
        new java.sql.Date(parsedToDate.getTime()),
        userRequestType,
        false,
        pageable
    );
```

This repository method returns a ==**`Page<UserRequest>`** —==  
==a powerful Spring Data structure that automatically carries pagination metadata.==

Internally, Spring Data executes **two SQL queries**:

1. **Main query (to fetch the data for the requested page)**
    
    ```sql
    SELECT * FROM user_request
    WHERE user_id = ? 
      AND request_from_date BETWEEN ? AND ? 
      AND request_type = ? 
      AND deleted = false
    ORDER BY request_from_date DESC
    LIMIT size OFFSET (page * size);
    ```
    
2. **Count query (to get total records for this filter)**
    
    ```sql
    SELECT COUNT(*) FROM user_request
    WHERE user_id = ?
      AND request_from_date BETWEEN ? AND ?
      AND request_type = ?
      AND deleted = false;
    ```
    

That **second query** gives Spring the **totalElements** value.

---

## 🧩 Step 3: The `Page<UserRequest>` object

After executing the above, `userRequestsPage` will have:

|Method|Meaning|
|---|---|
|`getContent()`|The **list of results** for the current page|
|`getTotalElements()`|Total number of matching rows across all pages|
|`getTotalPages()`|`ceil(totalElements / size)`|
|`getNumber()`|The current page number (0-indexed)|
|`getSize()`|The page size you requested|
|`hasNext()` / `hasPrevious()`|Whether pagination can continue|

---

## 🧩 Step 4: Mapping to DTO

```java
listOutputDTO.setTotalElement(userRequestsPage.getTotalElements());
listOutputDTO.setTotalPage(userRequestsPage.getTotalPages());
listOutputDTO.setCurrentPage(userRequestsPage.getNumber());
listOutputDTO.setPageSize(userRequestsPage.getSize());
```

That means:

- **`totalElement`** → from the count query
    
- **`totalPage`** → calculated as
    
    ```java
    totalPage = (int) Math.ceil((double) totalElements / pageSize)
    ```
    
- **`currentPage`** → the requested `page` parameter
    
- **`pageSize`** → the requested `size` parameter
    

Spring Data does all the pagination math automatically.

---

## 🧩 Step 5: Combined Scenario (Your outer loop)

Now back to your earlier loop:

```java
List<UserRequestDTO> paginatedRequests = new ArrayList<>();  
long totalElements = 0;  
int totalPages = 0;


for (User user : targetUsers) {
    UserRequestListOutputDTO dto = getUserRequestsByUser(...);
    paginatedRequests.addAll(dto.getUserRequests());
    totalElements += dto.getTotalElement(); // sum across users
    totalPages = dto.getTotalPage();        // last user’s page count
}
```

Each call of `getUserRequestsByUser()` computes `totalElements` and `totalPages` _for that specific user only._

After looping through all users:

```java
finalDto.setTotalElement(totalElements);
finalDto.setTotalPage((int) Math.ceil((double) totalElements / size));
```

You recompute **global totals** to cover all users combined.

---

## ✅ Summary Diagram

```
 ┌──────────────────────────────┐
 │ getUserRequestsByUser()      │
 │  ├── uses Pageable            │
 │  ├── repository runs 2 queries│
 │  │   ├── main query (LIMIT, OFFSET)
 │  │   └── count query (COUNT *)
 │  ├── Spring computes totals   │
 │  ├── returns Page<UserRequest>│
 │  └── mapped to DTO fields     │
 └──────────────────────────────┘

 Outer loop (multiple users)
       ↓
 Sum totalElements of all users
 Recompute global totalPages = ceil(totalElements / size)
       ↓
 Return final combined DTO
```

---

Would you like me to show the **exact SQL queries Spring Data generates** for this method (including how pagination is applied with `LIMIT` and `OFFSET`)?  
That would complete your understanding end-to-end.


```java
public UserRequestListOutputDTO  getUserRequestsByUser(User user, String fromDate, String toDate,UserRequestType userRequestType, int page, int size, User manager) throws ParseException {  
  
    Date parsedFromDate = sdf.parse(fromDate);  
    Date parsedToDate = sdf.parse(toDate);  
  
    if (parsedToDate.before(parsedFromDate)) {  
        throw new BadRequestException("FromDate cannot be after ToDate");  
    }  
  
    Pageable pageable = PageRequest.of(page, size, Sort.by("requestFromDate").descending());  
    Page<UserRequest> userRequestsPage = userRequestRepository.findByUserAndRequestFromDateBetweenAndRequestTypeAndDeleted(  
            user,  
            new java.sql.Date(parsedFromDate.getTime()),  
            new java.sql.Date(parsedToDate.getTime()),  
            userRequestType,  
            false,  
            pageable  
    );  
  
    List<UserRequestDTO> requestDTOs = userRequestsPage.getContent().stream()  
            .filter(ur -> {  
                if (manager == null) return true;  
                if (ur.getRequestType().equals(UserRequestType.COMP_OFF)) {  
                    return isSelectedManager(ur, manager);  
                }  
                return userService.isEmployeeUnderManagerWhenRequested(manager, user, ur);  
            })  
            .map(this::convertToDto)  
            .toList();  
  
    UserRequestListOutputDTO listOutputDTO = new UserRequestListOutputDTO();  
    listOutputDTO.setTotalElement(userRequestsPage.getTotalElements());  
    listOutputDTO.setTotalPage(userRequestsPage.getTotalPages());  
    listOutputDTO.setCurrentPage(userRequestsPage.getNumber());  
    listOutputDTO.setPageSize(userRequestsPage.getSize());  
    listOutputDTO.setFromDate(sdf.format(parsedFromDate));  
    listOutputDTO.setToDate(sdf.format(parsedToDate));  
    listOutputDTO.setUserRequests(requestDTOs);  
  
    return listOutputDTO;  
  
}



for (User user : targetUsers) {  
    UserRequestListOutputDTO dto = userRequestService.getUserRequestsByUser(  
            user, fromDate, toDate, UserRequestType.valueOf(requestType), page, size, isHR ? null : (isManager ? authUser : null));  
  
    paginatedRequests.addAll(dto.getUserRequests());  
    totalElements += dto.getTotalElement();  
    totalPages = dto.getTotalPage();  
}


UserRequestListOutputDTO finalDto = new UserRequestListOutputDTO();  
finalDto.setTotalElement(totalElements);  
finalDto.setTotalPage((int) Math.ceil((double) totalElements / size));  
finalDto.setCurrentPage(page);  
finalDto.setPageSize(size);  
finalDto.setFromDate(fromDate);  
finalDto.setToDate(toDate);  
finalDto.setUserRequests(paginatedRequests);


```
















in case of employee 
it sends empEmail

lets check in case of hr
in case of hr this endpoint is not even used 

for comp_off
```text
  
http://localhost:8080/api/v1/userRequest/forDate/02-11-2025/COMP_OFF
```
for leave
```text
http://localhost:8080/api/v1/userRequest/forDate/02-11-2025/LEAVE
```
for wfh 
```text
http://localhost:8080/api/v1/userRequest/forDate/02-11-2025/WFH
```


and hr is working fine 
 move to previous controller and  check manageLogic



