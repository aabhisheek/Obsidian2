// Set up for Global project assignment  
AssignRoleDTO assignRoleDTO = new AssignRoleDTO();  
assignRoleDTO.setUserEmail(user.getEmail());  
assignRoleDTO.setRoleName(assignRoleRequestDTO.getRoleName());  
assignRoleDTO.setProjectCode(GlobalConstants.PROJECT_GLOBAL_CODE);


when we assign role of hr and admin, 

we by default also assign a global_project in its project.



and through assign roles endpoint  it is only possible to set admin and hr .



if we dont assign any role to hr or admin, 
then automatically employee gets allocated.





