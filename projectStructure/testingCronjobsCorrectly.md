
now we can't hit the cron jobs apis either through postman or forntend having routes integrated 

so how do we test the cron jobs 

chatgpt suggests 

unitTest,integrationTest or manual trigger

more interested in manual trigger



so lets manually create endpoints for those same functions, and hit them and check each one of those correctly 




lets first hit this endpoint 

```java
@GetMapping("notify/timelogs/")  
public ResponseEntity<GenericResponseDTO> sendTimeLogsEmail(@RequestHeader("Authorization") String authorization) {  
  
    Utils.verifyPassword(adminPassword, authorization);  
  
    try {  
        scheduledJobs.sendNotificationToTimeLogDefaults();  
    } catch (Exception e) {  
        return ResponseEntity.badRequest().body(new GenericResponseDTO(e.getMessage(), null));  
    }  
  
    return ResponseEntity.ok().body(new GenericResponseDTO("success", null));  
  
}
```


just checks the admin password and executes the function , 
so just focus on the function 


```java

```