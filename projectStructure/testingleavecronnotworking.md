
```java
    @Scheduled(cron = "0 0 0 1 * *")  
    @Transactional  
    public void addMonthlyLeavesAndCarryForward() {  
        int currentMonth = LocalDate.now().getMonthValue();  
        int currentYear = LocalDate.now().getYear();  
  
       // For Testing uncomment the lines  
//        int currentYear=simulatedYear;  
//        int currentMonth=simulatedMonth;  
  
        int lastYear = currentYear - 1;  
        int lastMonth =  currentMonth - 1;  
        List<User> users = userRepository.findAll();  
        for (User user : users) {  
            LeaveMapping currentMonthMapping = leaveMappingRepository.findByUserAndYearAndMonth(user, currentYear, currentMonth);  
            LeaveMapping lastMonthMapping = leaveMappingRepository.findByUserAndYearAndMonth(user, currentYear, lastMonth);  
            LeaveMapping lastYearMapping = leaveMappingRepository.findByUserAndYearAndMonth(user, lastYear, 12);  
  
            // If no mapping for this year exists, create a new one  
            if (currentMonthMapping == null) {  
                currentMonthMapping = new LeaveMapping();  
                currentMonthMapping.setUser(user);  
                currentMonthMapping.setYear(currentYear);  
                currentMonthMapping.setMonth(currentMonth);  
                currentMonthMapping.setPrimaryLeave(0.0);  
                currentMonthMapping.setSecondaryLeave(0.0);  
                currentMonthMapping.setCarryForward(0.0);  
            }  
            if(user.getType().equals(UserType.INTERN)){  
                leaveMappingRepository.save(currentMonthMapping);  
                continue;  
            }  
            if (currentMonth == 1 && lastYearMapping != null) {  
                double carry = lastYearMapping.getPrimaryLeave() != null  
                        ? lastYearMapping.getPrimaryLeave() : 0.0;  
                currentMonthMapping.setSecondaryLeave(carry);  
                currentMonthMapping.setCarryForward(carry);  
                leaveMappingRepository.save(lastYearMapping);  
            }  
            if (currentMonth <= 7) {  
                if(currentMonth == 1){  
                    currentMonthMapping.setPrimaryLeave(1.5);  
                }else {  
                    double currentPrimary = lastMonthMapping != null && lastMonthMapping.getPrimaryLeave() != null  
                            ? lastMonthMapping.getPrimaryLeave() : 0.0;  
                    currentMonthMapping.setPrimaryLeave(currentPrimary < 0 ? 1.5 : (currentPrimary + 1.5));  
  
                    double currentSecondary = lastMonthMapping != null && lastMonthMapping.getSecondaryLeave() != null  
                            ? lastMonthMapping.getSecondaryLeave() : 0.0;  
                    currentMonthMapping.setSecondaryLeave(currentSecondary < 0 ? 0 : currentSecondary);  
                    currentMonthMapping.setCarryForward(lastMonthMapping != null && lastMonthMapping.getCarryForward() != null  
                            ? lastMonthMapping.getCarryForward() : 0.0);  
                }  
            } else {  
                double currentSecondary = lastMonthMapping != null && lastMonthMapping.getSecondaryLeave() != null  
                        ? lastMonthMapping.getSecondaryLeave() : 0.0;  
                currentMonthMapping.setSecondaryLeave(currentSecondary < 0 ? 1.5 : (currentSecondary + 1.5));  
  
                double currentPrimary = lastMonthMapping != null && lastMonthMapping.getPrimaryLeave() != null  
                        ? lastMonthMapping.getPrimaryLeave() : 0.0;  
                currentMonthMapping.setPrimaryLeave(currentPrimary < 0 ? 0 : currentPrimary);  
                currentMonthMapping.setCarryForward(lastMonthMapping != null && lastMonthMapping.getCarryForward() != null  
                        ? lastMonthMapping.getCarryForward() : 0.0);  
            }  
  
            leaveMappingRepository.save(currentMonthMapping);  
  
        }  
  
        userRepository.saveAll(users);  
  
        //For testing uncomment this lines  
//        simulatedMonth++;  
//        if (simulatedMonth > 12) {  
//            simulatedMonth = 1;  
//            simulatedYear+=1;  
//        }  
    }

```


working in case of  normal employees and not working in case of intern 