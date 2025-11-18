

1) userroles 

userid
projectid
roleid


roleid to roles 

roles
rolename
id



so if you want to get roles names  from users 
join user id 
userroles and then roles

then from userRoles roleid 
join roles to get rolename 


role is not directly connected to users 



2)

now lets see what happens how managers are alloted during allocations 

create project, project id 

then 

select user id , project id , designation role   in allocations table 


project table is connected to  user roles using project id 

in designation role it should roles id
nope 
it would be allocation_roles table with name entity being postitions



INSERT INTO public.allocation_roles (id, name) VALUES (1, 'UI Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (2, 'Associate UI Developer'); 


designation roles we are getting this from allocations roles tables not userroles 
conflict two types of roles userroles and allocation_roles

it should be centralized




![[Pasted image 20251102114526.png]]


![[Pasted image 20251102114609.png]]


3)
  http://localhost:8080/api/v1/allocation/roles

this api hits when we enter into project 



  
@Repository  
public interface PositionsRepository extends JpaRepository<Positions, Long> {  
  
    @Query(value = "select p from Positions p where :search is null or lower(p.name) like concat('%', lower(:search), '%')")  
    List<Positions> findAllPositionNamesWithSearch(String search);  
}


entity name is positions table name is allocation_roles

@Table(name = "allocation_roles")  
public class Positions {  
    @Id





4) so now since there are two types of roles so 
manager should be fetched from designation_role or user_role 


5)public enum AllocationType {  
    STAFFING("STAFFING"),  
    DEPLOYABLE("DEPLOYABLE"),  
    NONDEPLOYABLE("NON-DEPLOYABLE"),  
    LOCKED("LOCKED"),  
    NONBILLABLE("NON-BILLABLE");

we also have a allocationType  which  at most times  is Deployable , 
so you can just know it and ignore it most of the time 



5) lets first see how roles are assigned during creating allocation
  
![[Pasted image 20251102121403.png]]


![[Pasted image 20251102121439.png]]


lets see where does 


allocationController   logic

send allocationCreator through oauth and  user which is being added , and dto 


createallocation  Service Logic


```java

Project project = fetchProject(allocationRequestDTO.getProjectCode());  
validateExistingAllocation(user, project);  
validateManagerRole(allocationCreator);  
validateAllocationType(allocationRequestDTO, project);  
if (allocationRequestDTO.isManager()) {  
    assignManagerRole(user, project);  
}


// this assign manager role 
// assigns role user_Role manager in roles
private void assignManagerRole(User user, Project project) {  
    Role managerRole = roleRepository.findByNameIgnoreCase(GlobalConstants.USER_ROLE_MANAGER);  
    if (managerRole == null) {  
        throw new BadRequestException("Manager role does not exist in the system");  
    }  
  
    UserRole userRole = new UserRole();  
    userRole.setUser(user);  
    userRole.setRole(managerRole);  
    userRole.setProject(project);  
  
    userRole.setId(new UserRole.UserRoleId(user.getId(), managerRole.getId(), project.getId()));  
    userRoleRepository.save(userRole);  
}


// compute and create allocation  
computeAllocation(user, allocationRequestDTO.getStartDate(), allocationRequestDTO.getEndDate(), allocationRequestDTO.getAllocatedHours());  
Allocation newAllocation = buildAllocation(allocationRequestDTO, user, project);  
applyAllocationOverrides(allocationRequestDTO, newAllocation);  
Allocation savedAllocation = allocationsRepository.save(newAllocation);  
log.info("Successfully Created allocation with ID: {}", savedAllocation.getId());  
  
// recompute BENCH allocations  
recomputeBenchAllocation(user);  
log.info("Successfully recomputed Bench allocations");  
return savedAllocation;
```

but i created one new user through employee Onboarding Hr
its role was not defined even as bench 
![[Pasted image 20251102161012.png]]
![[Pasted image 20251102161242.png]]

```sql

Select  r.scope,r.name,ur.project_id,u.role,u.name,u.email  from users u 
JOIN user_roles ur ON u.id=ur.user_id
JOIN roles r ON  ur.role_id=r.id
```

very important query in understanding the  database.
scope,role,name 
the name is actually role name  from roles
and the role is designation_role  from users



userroles are only assigned through assignManager in allocation
for manager and  hr  only i guess only 
not for normal employees they only have designation_roles

and  we can see for each manager in different project a new row in user role is created
for example in manager2, there are two entries for project id 4 and 3.





now lets look at    computeAllocation,buildAllocation  and applyToAllocationOverrides functionality 

==compute allocation== checks for the current user allocations in different projects ,only if 8-allAllocations >=current requiredAllocationHours 
else throws error
"No sufficient hours to allocate - Only " + (MAX_HOURS - maxAvailHours) + " hours can be allocated");

==BuildAllocationss==
it just set few allocationdatatypeentries through setters of alocation table 


==ApplyAllocationOverrides== 
 if  notdeployable or notBillable 
 then create one extra table also besides allocation to store reason for this beside the normalflow

```java 
private void applyAllocationOverrides(AllocationRequestDTO dto, Allocation allocation) {  
    AllocationType type = dto.getAllocationType(); // <-- directly from DTO  
    if (type == AllocationType.NONDEPLOYABLE || type == AllocationType.NONBILLABLE) {  
        AllocationTypeOverride override = new AllocationTypeOverride();  
        override.setAllocation(allocation);  
        override.setAllocationType(type);  
        override.setReason("Allocation type override: could not infer type '" + type + "'");  
        allocation.setOverride(override);  
    }  
}
```

but for one field why to create extra table we could have this field in allocations, which would be null for normal allocations

but its good for auditability, and see who was billed in which period and so onnn........

![[Pasted image 20251102155835.png]]





a)assignManagerRole(user, project);   makes user Project_Level_Manager 
      package com.webknot.webtrak.constants;  

through the check isManager  in that form,we create a user_role through which we get Managers.
the designation role does not have any significance, its just a name 

so we will have to call this assignManager Role on that createUserDto when we get mail of manager
and change that query for comp_Off 
change target_users  for comp_OFF

first comp_Off, set user_request Manager Email in user_request only for comp_off
and during get user_request only for that user 
  
public class GlobalConstants {  
    private GlobalConstants(){}  
  
    // roles  
    public static final String ROLE_HR = "ROLE_HR";  
    ==public static final String ROLE_MANAGER = "ROLE_MANAGER";==  
    public static final String ROLE_ADMIN = "ROLE_ADMIN";  
    public static final String ROLE_ASSET_MANAGER = "ROLE_ASSET_MANAGER";  
    public static final String ROLE_EMPLOYEE = "ROLE_EMPLOYEE";  
    public static final String ROLE_FINANCE = "ROLE_FINANCE";  
    public static final String USER_ROLE_HR = "HR";  
    ==public static final String USER_ROLE_MANAGER = "MANAGER";==

not only manager, hr also we have 2 sets   GLOBAL level and PROJECTS level 



![[Pasted image 20251102124953.png]]

 so manager also has two levellogic  just like roles and allocation roles

b)also roles we have two scopes 

Select * from roles;*
![[Pasted image 20251102125904.png]]


c)Select * from projects;
we by default have 2 projects global and bench ,
all new allocations are created after that 
![[Pasted image 20251102125749.png]]


d)  what does recomputeBenchAllocation(user);    does , i want to know about this 

   so if  the employee is on bench,  change that as this allocates him to a project 









