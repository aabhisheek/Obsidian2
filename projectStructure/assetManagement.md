
lets solve this 

first i queried all the database
there is no data in the tables in database 


so the data is coming from the frontend only
lets check the frontend rendering of this part


so in this,
there is 

feature/assets

in this found the dummy data in assets.tsx 

but there is a catch,
i only found 01,02 requests 

in ui its rendering 5 requests

neither getManagers is  working so cant create a asset request
neither able to delete those already created asset requests 



they have getMyManagersWithProjects(User me)

in userRequestService.java 



first i changed the route on frontend,

now there is showing request forbidden, 
only admins roles are allowed to hit that route,

so  i searched where it was ,
it was in SecurityConfig.java

 mvcMatcherBuilder.pattern(HttpMethod.POST, "/api/v1/leaveTransaction"),

                                mvcMatcherBuilder.pattern(HttpMethod.POST, "/api/v1/assets/assign"),

                                mvcMatcherBuilder.pattern(HttpMethod.PUT, "/api/v1/timelog/entry"),

![[Pasted image 20251113125622.png]]





there is a error in sending hit button, 
expected multiform data but data

but in postman we can hit
postman data format 
```json
{
  "assetId": "ASSET-001",
  "assetName": "Dell Latitude 7420 Laptop",
  "description": "High-performance laptop used for software development.",
  "isCompanyOwned": true,
  "ownerId": 1,
  "status": "IN_USE",
  "assetType": "LAPTOP",
  "numberOfRepairs": 2,
  "serialNumber": "SN-DL7420-AB12345",
  "serviceDescription": "Serviced last month for fan replacement and OS update."
}
```


payload from frontend format
```json
  
{"managerName":3,
"priority":"LOW",
"description":"abcd",
"assetType":"LAPTOP",
"requestType":"NEW_ASSET",
"issueDescription":"",
"currentAssetDetails":"",
"preferredSpecifications":"abcd"}
```


![[Pasted image 20251113143954.png]]

now lets see the schema of  the table in which the asset is saved 


AssetRequestDto

```text
public class AssetRequestDto {  
    private String assetName;  
    private String description;  
    private AssetRequestType requestType;  
    private AssetType assetType;  
    private AssetRequestPriority priority;  
    private Long managerId;  
    private String preferredSpecifications;  
    private String currentAssetDetails;  
}
```


```text
inside post-asset-request
{"managerId":3,"priority":"LOW","description":"abcd","assetType":"LAPTOP","requestType":"NEW_ASSET","issueDescription":"","currentAssetDetails":"","preferredSpecifications":"abcd"}
```










Getassetrequest 

```json
{
    "message": "Fetched requests",
    "data": {
        "currentPage": 0,
        "totalPage": 1,
        "pageSize": 10,
        "totalElement": 4,
        "data": [
            {
                "id": 1,
                "description": "Need for development",
                "requestType": "NEW_ASSET",
                "assetType": "LAPTOP",
                "priority": "HIGH",
                "currentAssetDetails": null,
                "preferredSpecifications": "16GB RAM, 512GB SSD",
                "supportingDocumentUrl": null,
                "status": "PENDING",
                "managerApproval": "PENDING",
                "assetManagerApproval": "PENDING",
                "requesterName": "EMPLOYEE9650734",
                "requesterEmail": "anand.abhishek9650734826@gmail.com"
            },
            {
                "id": 2,
                "description": "Need for development",
                "requestType": "NEW_ASSET",
                "assetType": "LAPTOP",
                "priority": "HIGH",
                "currentAssetDetails": null,
                "preferredSpecifications": "16GB RAM, 512GB SSD",
                "supportingDocumentUrl": null,
                "status": "PENDING",
                "managerApproval": "PENDING",
                "assetManagerApproval": "PENDING",
                "requesterName": "EMPLOYEE9650734",
                "requesterEmail": "anand.abhishek9650734826@gmail.com"
            },
            {
                "id": 3,
                "description": "abcd",
                "requestType": "NEW_ASSET",
                "assetType": "LAPTOP",
                "priority": "LOW",
                "currentAssetDetails": "",
                "preferredSpecifications": "abcd",
                "supportingDocumentUrl": null,
                "status": "PENDING",
                "managerApproval": "PENDING",
                "assetManagerApproval": "PENDING",
                "requesterName": "EMPLOYEE9650734",
                "requesterEmail": "anand.abhishek9650734826@gmail.com"
            },
            {
                "id": 4,
                "description": "abcdef",
                "requestType": "NEW_ASSET",
                "assetType": "LAPTOP",
                "priority": "LOW",
                "currentAssetDetails": "",
                "preferredSpecifications": "abcdef",
                "supportingDocumentUrl": null,
                "status": "PENDING",
                "managerApproval": "PENDING",
                "assetManagerApproval": "PENDING",
                "requesterName": "EMPLOYEE9650734",
                "requesterEmail": "anand.abhishek9650734826@gmail.com"
            }
        ]
    }
}

```


same get request is also being made also when we load this page , and the same data is also fetched,

![[Pasted image 20251114010941.png]]


out of this, we just have to show this data,

![[Pasted image 20251114011153.png]]


pick these 6 fileds out of 14 fields in our payload and show it 
