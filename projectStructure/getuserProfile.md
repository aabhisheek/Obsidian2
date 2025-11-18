
  
http://localhost:8080/api/v1/profile

Controller
```java
@GetMapping("profile")  
public ResponseEntity<GenericResponseDTO> getProfile(){  
    return ResponseEntity.ok(  
            new GenericResponseDTO(  
                    "Profile fetched successfully",  
                    userService.getProfile(userContext.getEmail())  
            )  
    );  
}
```


ServiceLayer

```java
public UserProfileDto getProfile(String email) {  
    User user = userRepository.findByEmail(email);  
    if (user == null) {  
        throw new DataNotFoundException("User not found with mail : " + email);  
    }  
    UserProfile userProfile = user.getProfile();  
    TechSkills skillsSet = null;  
    try {  
        skillsSet = userProfile != null ? userProfile.getSkillsSet() : null;  
    } catch (Exception e) {  
        log.error("Failed to extract tech skills : {}", e.getMessage());  
    }  
    List<KpiDetails> kpis = new ArrayList<>();  
    try {  
        kpis = user.getBand() == null ? new ArrayList<>() : user.getBand().getBandKpis();  
    } catch (Exception e) {  
        log.error("Failed to extract kpis : {}", e.getMessage());  
    }  
    LeaveMapping leaveMapping = leaveMappingRepository.findByUserAndYearAndMonth(user, LocalDate.now().getYear(), LocalDateTime.now().getMonthValue());  
    Map<String, Double> leaveStats = new HashMap<>();  
    if (leaveMapping != null) {  
        Double primaryLeave = leaveMapping.getPrimaryLeave() == null  
                ? 0.0 : leaveMapping.getPrimaryLeave();  
        Double secondaryLeave = leaveMapping.getSecondaryLeave() == null  
                ? 0.0 : leaveMapping.getSecondaryLeave();  
        leaveStats.put(GlobalConstants.TOTAL_LEAVE, primaryLeave + secondaryLeave);  
        leaveStats.put(GlobalConstants.PRIMARY_LEAVE, primaryLeave);  
        leaveStats.put(GlobalConstants.SECONDARY_LEAVE, secondaryLeave);  
        leaveStats.put(GlobalConstants.CARRY_FORWARD_LEAVE, leaveMapping.getCarryForward());  
    } else {  
        leaveStats.put(GlobalConstants.TOTAL_LEAVE, 0.0);  
        leaveStats.put(GlobalConstants.PRIMARY_LEAVE, 0.0);  
        leaveStats.put(GlobalConstants.SECONDARY_LEAVE, 0.0);  
        leaveStats.put(GlobalConstants.CARRY_FORWARD_LEAVE, 0.0);  
    }  
  
    List<Allocation> allocations = allocationsRepository.findByUserId(user.getId());  
    List<ProjectsProfileDto> allocationsStats = allocations.stream()  
            .filter(a -> a.getStartDate().toLocalDate().isBefore(LocalDate.now().plusDays(1)))  
            .map(a -> {  
                return new ProjectsProfileDto(  
                        a.getProject().getProjectName(),  
                        ((double) a.getAllocatedHours() / 8) * 100,  
                        a.getProject().getProjectCode().equals(GlobalConstants.PROJECT_BENCH_CODE)  
                                ? "" : (getPrimaryManagerFromProject(a.getProject()) == null ? ""  
                                : getPrimaryManagerFromProject(a.getProject()).getName())  
                );  
  
            })  
            .collect(Collectors.toList());  
  
    UserProfileDto.UserProfileDtoBuilder userProfileDto = UserProfileDto.builder()  
            .name(user.getName())  
            .email(user.getEmail())  
            .department(user.getDepartment() != null ? user.getDepartment() : "")  
            .empId(user.getEmpId())  
            .band(user.getBand() == null ? null : new BandDto(  
                    user.getBand().getId(),  
                    user.getBand().getName()  
            ))  
            .designation(user.getBand() == null ? "" : user.getBand().getDesignation())  
            .phoneNumber(user.getPhoneNumber() != null ? user.getPhoneNumber() : "")  
            .DOJ(user.getDOJ())  
            .workMode(user.getWorkMode())  
            .userType(user.getType())  
            .documents(user.getDocuments())  
            .userStatus(user.getStatus())  
            .primarySkills(  
                    (skillsSet != null && skillsSet.getPrimarySkills() != null)  
                            ? skillsSet.getPrimarySkills() : new ArrayList<>()  
            )  
            .projects(allocationsStats)  
            .techSkills(  
                    (skillsSet != null && skillsSet.getTechSkills() != null )  
                            ? skillsSet.getTechSkills() : new ArrayList<>()  
            )  
            .kpis(kpis != null ? kpis : new ArrayList<>())  
            .leaveStats(leaveStats);  
  
  
    if (userProfile != null) {  
        userProfileDto  
                .personalEmail(userProfile.getPersonalEmail())  
                .bio(userProfile.getBio())  
                .gender(userProfile.getGender())  
                .dob(userProfile.getDob())  
                .bloodGroup(userProfile.getBloodGroup())  
                .resume(userProfile.getWebknotResume() != null ? userProfile.getWebknotResume() : "")  
                .phoneNumber(userProfile.getPhoneNumber())  
                .address(  
                        Utils.safeCallReturn(() -> userProfile.getUserAddress(), "parsing address")  
                )  
                .personalResume(  
                        userProfile.getPersonalResume() == null  
                                ? "" : fileStorageUtil.getPublicUrl(userProfile.getPersonalResume())  
                )  
                .profilePic(  
                        userProfile.getProfilePic() == null  
                                ? "" : fileStorageUtil.getPublicUrl(userProfile.getProfilePic())  
                );  
        List<String> langKnown = Utils.safeCallReturn(() -> userProfile.getLanguageKnown(), "parsing languages known");  
        userProfileDto.languagesKnown(langKnown == null ? new ArrayList<>() : langKnown);  
        List<EmergencyContact> emergencyContacts = Utils.safeCallReturn(() -> userProfile.getEmergencyContacts(), "parsing emergency contacts");  
        userProfileDto.emergencyContacts(emergencyContacts == null ? new ArrayList<>() : emergencyContacts);  
        List<Education> educations = Utils.safeCallReturn(() -> userProfile.getEdu(), "parsing education");  
        userProfileDto.education(educations == null ? new ArrayList<>() : educations);  
        List<Experience> experiences = Utils.safeCallReturn(() -> userProfile.getExperiences(), "parsing experiences");  
        userProfileDto.experiences(experiences == null ? new ArrayList<>() : experiences);  
  
    }  
    return userProfileDto.build();  
}
```


1)fetch userByemail

2)then fetch userprofilebyuser

3) then fetch skillset,kpis, then leavemapping 

if not null fetch else set to 0

4) then fetch allocations 
this is where the pb is 

lets see what we are getting into allocations 


```java
```java
List<Allocation> allocations = allocationsRepository.findByUserId(user.getId());  
    List<ProjectsProfileDto> allocationsStats = allocations.stream()  
            .filter(a -> a.getStartDate().toLocalDate().isBefore(LocalDate.now().plusDays(1)))  
            .map(a -> {  
                return new ProjectsProfileDto(  
                        a.getProject().getProjectName(),  
                        ((double) a.getAllocatedHours() / 8) * 100,  
                        a.getProject().getProjectCode().equals(GlobalConstants.PROJECT_BENCH_CODE)  
                                ? "" : (getPrimaryManagerFromProject(a.getProject()) == null ? ""  
                                : getPrimaryManagerFromProject(a.getProject()).getName())  
                );  
  
            })  
            .collect(Collectors.toList()); 
```

lets focus on this allocations code , because here lies the real problem.


so in UserProfileDto,
we have  a ==field projects of type== 
==ProjectProfileDto== 

which is the one getting fetched 

```java
@Data  
@AllArgsConstructor  
@NoArgsConstructor  
public class ProjectsProfileDto{  
    String name;  
    Double percent;  
    String primaryManager;  
}

```


so we have to check where this is being fetched 

```java
  .projects(allocationsStats) 
```

this is where that is being set
