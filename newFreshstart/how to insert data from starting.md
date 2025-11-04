1)INSERT INTO band (name, designation)  
VALUES ('sr', 'tl');
first insert band from sql terminal


2)then onboard user with role hr and bove mentioned band



3)then add this roles script 
INSERT INTO public.allocation_roles (id, name) VALUES (1, 'UI Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (2, 'Associate UI Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (3, 'Senior UI Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (4, 'UI Lead');  
INSERT INTO public.allocation_roles (id, name) VALUES (5, 'Cross Platform Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (6, 'Associate Cross Platform Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (7, 'Senior Cross platform Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (8, 'iOS Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (9, 'Associate IOS Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (10, 'Senior IOS Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (11, 'Android Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (12, 'Associate Android Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (13, 'Senior Android Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (14, 'Mobile Lead');  
INSERT INTO public.allocation_roles (id, name) VALUES (15, 'Backend Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (16, 'Associate Backend Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (17, 'Senior Backend Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (18, 'Backend Architect');  
INSERT INTO public.allocation_roles (id, name) VALUES (19, 'Devops Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (20, 'Associate Devops Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (21, 'Senior Devops Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (22, 'Devops Lead');  
INSERT INTO public.allocation_roles (id, name) VALUES (23, 'Scrum Master / APM');  
INSERT INTO public.allocation_roles (id, name) VALUES (24, 'Project Manager');  
INSERT INTO public.allocation_roles (id, name) VALUES (25, 'Delivery Manager');  
INSERT INTO public.allocation_roles (id, name) VALUES (26, 'Fullstack Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (27, 'Associate Fullstack Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (28, 'Senior Fullstack Developer');  
INSERT INTO public.allocation_roles (id, name) VALUES (29, 'Manual Tester');  
INSERT INTO public.allocation_roles (id, name) VALUES (30, 'Senior Manual Tester');  
INSERT INTO public.allocation_roles (id, name) VALUES (31, 'Automation Tester');  
INSERT INTO public.allocation_roles (id, name) VALUES (32, 'Senior Automation Tester');  
INSERT INTO public.allocation_roles (id, name) VALUES (33, 'QA Lead');  
INSERT INTO public.allocation_roles (id, name) VALUES (34, 'Business Analyst');  
INSERT INTO public.allocation_roles (id, name) VALUES (35, 'Senior Business Analyst');  
INSERT INTO public.allocation_roles (id, name) VALUES (36, 'Data Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (37, 'Associate Data Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (38, 'Senior Data Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (39, 'Data Engineer Lead');  
INSERT INTO public.allocation_roles (id, name) VALUES (40, 'ML Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (41, 'Senior ML Engineer');  
INSERT INTO public.allocation_roles (id, name) VALUES (42, 'ML Lead');  
INSERT INTO public.allocation_roles (id, name) VALUES (43, 'Designer');  
INSERT INTO public.allocation_roles (id, name) VALUES (44, 'Associate designer');  
INSERT INTO public.allocation_roles (id, name) VALUES (45, 'Senior Designer');  
INSERT INTO public.allocation_roles (id, name) VALUES (46, 'Design Lead');  
INSERT INTO public.allocation_roles (id, name) VALUES (47, 'HR');  
INSERT INTO public.allocation_roles (id, name) VALUES (48, 'Senior HR');  
INSERT INTO public.allocation_roles (id, name) VALUES (49, 'Associate PM');  
INSERT INTO public.allocation_roles (id, name) VALUES (50, 'Finance');  
INSERT INTO public.allocation_roles (id, name) VALUES (51, 'Senior Finance');



4)then assign role hr endpoint  

5)then do query ;  Select  *  from  users;
then click and change status from Onboarding to  ACTIVE.

now you can see them in a drop down list.


NOW ADD PROJECTS BY HR ACOCUNT AND ASSIGN MANAGER AND EMPLOYEES IN THAT PROJECT ,

BEFORE ADDING 
```sql
alter table allocations drop constraint uk_user_project;
```
  
**make a note:** we need to drop this constraint while assigning to the user to any project in development;


NOW add employees and managers in project.


now employees and managers are connected through same project.
the table connection.










errors when wrong linode configuration ;
i could not get it ;
i could not spot where the error was;

{"@timestamp":"2025-10-21T21:27:17.516+05:30","@version":"1","message":"   \uD83D\uDCA1 Suggested fix: Verify Linode endpoint URL and bucket permissions","logger_name":"com.webknot.webtrak.util.FileStorageUtil","thread_name":"http-nio-8080-exec-5","level":"ERROR","level_value":40000,"env":"prod"}



{"@timestamp":"2025-10-21T21:27:17.516+05:30","@version":"1","message":"   \uD83D\uDD0C Connection closed prematurely - check endpoint configuration","logger_name":"com.webknot.webtrak.util.FileStorageUtil","thread_name":"http-nio-8080-exec-5","level":"ERROR","level_value":40000,"env":"prod"}

*****
{"@timestamp":"2025-10-21T21:27:23.903+05:30","@version":"1","message":"   Error message: org.apache.http.ConnectionClosedException: Premature end of Content-Length delimited message body (expected: 244; received: 0)","logger_name":"com.webknot.webtrak.util.FileStorageUtil","thread_name":"http-nio-8080-exec-5","level":"ERROR","level_value":40000,"env":"prod"}



{"@timestamp":"2025-10-21T21:27:23.903+05:30","@version":"1","message":"   Error type: java.io.UncheckedIOException","logger_name":"com.webknot.webtrak.util.FileStorageUtil","thread_name":"http-nio-8080-exec-5","level":"ERROR","level_value":40000,"env":"prod"}

