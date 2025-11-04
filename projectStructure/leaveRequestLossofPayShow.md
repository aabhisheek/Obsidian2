

  
http://localhost:8080/api/v1/leave-summary?page=0&size=8&month=11&year=2025


create fetchleaveSummary only for hr 

and loss of pay 

So:

- If `search` is **null**, the condition becomes `true` (no filtering by name).
    
- If `search` is **non-null**, it filters users where **`u.name` contains the search term (case-insensitive)**.


### 🔧 Example:

| Search Value | Query Filters Users Where...                                   |
| ------------ | -------------------------------------------------------------- |
| `"john"`     | `u.name` contains `"john"` (e.g., “John Doe”, “Johnson”, etc.) |
| `null`       | All users are returned (no name filtering)                     |
|              |                                                                |
|              |                                                                |

```java
 @GetMapping("leave-summary")  
    public ResponseEntity<GenericResponseDTO> fetchLeaveSummary(  
            @RequestParam(defaultValue = "0") int page,  
            @RequestParam(defaultValue = "10") int size,  
            @RequestParam(required = false) String search,  
            @RequestParam(required = false) UserType type,  
            @RequestParam(required = false) String band,  
            @RequestParam(required = false) Integer year,  
            @RequestParam(required = false) Integer month  
    ) {  
        return ResponseEntity.ok(  
                new GenericResponseDTO(  
                        "leave summary fetched successfully",  
                        leaveTransactionService.fetchLeaveSummary(page, size, type, band, search, year, month)  
                )  
        );  
    }  
}


public GenericPaginationDto<LeaveSummaryDto> fetchLeaveSummary(int page, int size, UserType type, String band, String search, Integer year, Integer month){  
    LocalDate localDate = LocalDate.now();  
    List<User> users = userRepository.findAllWithFilters(search, band, type, null);  
    if(users == null || users.isEmpty()){  
        return GenericPaginationDto.<LeaveSummaryDto>builder()  
                .data(new ArrayList<>())  
                .pageSize(0)  
                .totalPage(0)  
                .totalElement(0)  
                .currentPage(0)  
                .build();  
    }  
    int yearValue = year == null ? localDate.getYear() : year;  
    int monthValue = month == null ? localDate.getMonthValue() : month;  
    if(monthValue > localDate.getMonthValue()){  
        throw new BadRequestException("Invalid month - Future months not allowed");  
    }  
    List<LeaveSummaryDto> filteredResponse = users.stream()  
            .map(u -> {  
                LeaveSummaryDto summaryDto = new LeaveSummaryDto();  
                summaryDto.setName(u.getName());  
                summaryDto.setEmail(u.getEmail());  
                summaryDto.setEmpId(u.getEmpId());  
                summaryDto.setRole(""); // TODO need to add after onboarding branch merged  
                summaryDto.setBand(u.getBand() != null ? u.getBand().getName() : "");  
                summaryDto.setType(u.getType());  
                LeaveMapping leaveMapping = leaveMappingRepository.findByUserAndYearAndMonth(  
                        u, yearValue, monthValue  
                );  
                summaryDto.setLop(0.0);  
                summaryDto.setLeaves(0.0);  
                List<LeaveTransaction> leaveTransactionsForMonth = getLeaveTransactionsForMonth(  
                        u, monthValue, yearValue  
                );  
                if(leaveMapping != null){  
                    double pl = leaveMapping.getPrimaryLeave() == null ? 0 : leaveMapping.getPrimaryLeave();  
                    double sl = leaveMapping.getSecondaryLeave() == null ? 0 : leaveMapping.getSecondaryLeave();  
                    double tl = pl + sl;  
                    if(tl < 0)summaryDto.setLop(tl * (-1));  
                    summaryDto.setLeaves(leaveTransactionsForMonth.stream().mapToDouble(LeaveTransaction::getValue).sum());  
                }  
                if (summaryDto.getLop() > 0) {  
                    summaryDto.setLeaveDetails(  
                            getLeaveDetailsForLop(  
                                    u, monthValue, yearValue,  
                                    summaryDto.getLop()  
                            )  
                    );  
                }  
                return summaryDto;  
            })  
            .filter(ls -> ls.getLop() > 0)  
            .collect(Collectors.toList());  
  
    List<LeaveSummaryDto> paginatedResponse = PaginationUtils.getPageFromList(filteredResponse, page, size);  
  
    return GenericPaginationDto.<LeaveSummaryDto>builder()  
            .data(paginatedResponse)  
            .pageSize(size)  
            .totalPage((filteredResponse.size() < size) ? 1 : (int)Math.ceil((double)filteredResponse.size() / size ))  
            .totalElement(filteredResponse.size())  
            .currentPage(page)  
            .build();  
  
}

LeaveMapping findByUserAndYearAndMonth(User user, Integer year, Integer month);





```




LOGIC

So first we findAllwithFilters

we fetch all users with given filters

then we have LeaveSummaryDto

```java
@Data  
public class LeaveSummaryDto {  
    private String name;  
    private String email;  
    private String empId;  
    private String role;  
    private UserType type;  
    private String band;  
    private Double leaves;  
    private Double lop;  
    private List<LeaveDetails> leaveDetails;  
}
```



so forLeave we have 2 types of  tables

leaveTransactions
leavemapping


leave mapping contains all details like
primaryLeaves
SecondaryLeaves
carryForward 
and user_id through which it connects to users 


i cant   get what does leaveTransactions does 



in updateUserrequestStatus Put method 
```java
leaveMappingRepository.save(leaveMapping);  
LeaveTransaction lt = new LeaveTransaction();  
lt.setUpdatedBy(approver.getId());  
lt.setUserRequest(optionalRequest.get());  
lt.setComments("COMP_OFF Approved");  
lt.setForDate(optionalRequest.get().getRequestFromDate());  
lt.setUser(user);  
lt.setValue(1.0);  
lt.setTransactionType(TransactionType.AWARD);  
leaveTransactionService.createNewLeaveTransaction(lt);
```

so everytime any approver takes any action it gets stored in leaveTransaction


    

---

### ✅ In simple words:

`search` represents a **partial match for the user's name**.  
It is used to find users whose name includes the text you pass — like a search bar for user names.



