
where is the port defined in springboot and if i have to change it 
the port is  defined on application.properties yml file.


if you dont define port in application.yml explicitly ,
then the application wont show error instead it would take the port by default as 8080.

and the port is sent either via   application.yml/environment variables ;

 we provide that 

and the setting part is done by spring autoconfiguration.








