

so in dto we were taking taking list of managers 

but in userRequest there is no manager 


in createUserRequest what the logic is that 

 List<String> managerEmails = dto.getManagers();
==they just check whether managers are provided== 


then 


 validating selected managers and initiating the user request status tracking  
  
if (!dto.getUserRequestType().equals(UserRequestType.COMP_OFF)) {   removed the check for comp-off as it can be approved by any manager  
    List<String> myManagers = userRepository.getManagersForUser(user.getId());  
    for (String managerEmail : managerEmails) {  
        if (!myManagers.contains(managerEmail)) {  
            throw new BadRequestException("The manager selected is not your manager, " + managerEmail);  
        }  
    }  
}


then they validate whether each manager provided is really a manager of user 


get request they dont take manager from dto they send from that query only

need to change that 

















