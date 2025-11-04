
now the get userRequest for comp_off is sorted, 

now the next thing to work upon is 


but am important thing 
in allocation if we have isManager
on clicking we only get 2 roles pm and dm which is designation role only.

and then userroles user_role manager too


manager storing for comp_off

userRequest 
listStringmanager 



manager is getting sent as a list only so 
{"userRequestType":"COMP_OFF","requestFromDate":"17-10-2025","requestToDate":"17-10-2025","comments":"testing payload","managers":["21052216@kiit.ac.in"],"isHalfDay":false}

so change the logic of fetching 
![[Pasted image 20251103055115.png]]

1)so the manager comp_off_email is not being set on creating user_Request 
2) we are taking list in createuserRequestDto but saving manager in userRequest as a String only 
so just save it 
after that logic would work
lets fix it 




so now comp_off email is being saved and fetched by passed manager 
and now showing to other project manager which is his manager but a small change is needed 
not showing comp_off for employee user



it contains employee email when fetching its own comp_off requests applied== 
here is the problem so this logic lies in second part

but it is not able to enter that part due to if condition 
correc that



we have 
1)comp_off managers 
2) if else 
3) target users


in case of comp_off by managers aprrove request

employeeemail is null and comp_off execute first logic and skip 2 and 3
rest always run 2 and 3 other comp_off also 
employeeemail is not null and comp_off execute 2 and 3 




