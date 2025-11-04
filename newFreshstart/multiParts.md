`MultipartHttpServletRequest` is used in **Spring Boot** when your API endpoint needs to handle **file uploads** _along with_ normal form data — i.e., when the HTTP request is of type **multipart/form-data**.

Let’s break it down clearly 👇

---

### 🔹 Normal Request vs Multipart Request

#### 1️⃣ Normal HTTP Request

- Content-Type: `application/json` or `application/x-www-form-urlencoded`
    
- Example: Sending plain JSON or form data (no files)
    

```json
{
  "name": "Abhishek",
  "email": "abhishek@example.com"
}
```

#### 2️⃣ Multipart Request (File + Data)

- Content-Type: `multipart/form-data`
    
- Example: Uploading a file and some JSON/text fields together (like a form with file input)
    

```
Content-Type: multipart/form-data
```

The request contains _multiple parts_ —  
one for the file, and one for each text field.

---

### 🔹 What is `MultipartHttpServletRequest`

`MultipartHttpServletRequest` is a **Spring interface** (extends `HttpServletRequest`) that allows you to:

- Access uploaded files (`MultipartFile`)
    
- Access form fields (`getParameter()`)
    
- Treat the multipart request as a normal servlet request
    

---

### 🔹 When to Use It

You use `MultipartHttpServletRequest` when:

- You want to receive **both files and form data** in the same request.
    
- You need **multiple files** or **custom handling** of each uploaded part.
    

---

### 🔹 Example

Let’s say you have a form or frontend sending:

```json
{
  "request": {
    "title": "Leave Request",
    "reason": "Medical emergency"
  },
  "file": <uploaded_pdf_file>
}
```

---

### 🔹 Controller Example

```java
@PostMapping("/userRequest")
public ResponseEntity<String> createUserRequest(MultipartHttpServletRequest userRequest) throws IOException {
    
    // 1️⃣ Access normal parameters
    String requestData = userRequest.getParameter("request");
    System.out.println("Request JSON: " + requestData);
    
    // 2️⃣ Convert JSON string to DTO (if needed)
    ObjectMapper mapper = new ObjectMapper();
    CreateUserRequestDTO dto = mapper.readValue(requestData, CreateUserRequestDTO.class);
    
    // 3️⃣ Access file
    MultipartFile file = userRequest.getFile("file");
    System.out.println("Uploaded File: " + file.getOriginalFilename());
    
    // 4️⃣ Process the file or save it to S3 / DB
    // file.transferTo(new File("uploads/" + file.getOriginalFilename()));

    return ResponseEntity.ok("Request saved successfully");
}
```

---

### 🔹 Alternative (Simpler) Approach

If your endpoint expects only **one file and a few fields**, you can use:

```java
@PostMapping("/upload")
public ResponseEntity<String> uploadFile(
    @RequestParam("file") MultipartFile file,
    @RequestParam("title") String title
) {
    System.out.println("File: " + file.getOriginalFilename());
    System.out.println("Title: " + title);
    return ResponseEntity.ok("Uploaded!");
}
```

---

### 🔹 Summary Table

|Use Case|Best Option|
|---|---|
|Single file + few params|`@RequestParam` approach|
|Multiple files + dynamic params|`MultipartHttpServletRequest`|
|File upload with JSON body|`MultipartHttpServletRequest` + `ObjectMapper`|

---

Would you like me to show how this looks in a **Postman example** (with both file and JSON part)?



Excellent question 👏

Let’s look at **exactly when and why** you use this line:

```java
MultipartFile file = userRequest.getFile("file");
```

---

## 🎯 In One Line:

It is used when your API endpoint receives a **multipart/form-data request** (usually from a form or frontend) that **contains a file upload**, and you want to **extract the uploaded file** from it.

---

## 🧩 The Scenario

Let’s say your frontend sends this kind of request (typical in forms with file upload):

**Content-Type: `multipart/form-data`**

|Field name|Type|Value|
|---|---|---|
|`file`|File|`resume.pdf`|
|`request`|Text|`{"title": "Leave Request", "reason": "Medical Emergency"}`|

---

### In Your Controller:

```java
@PostMapping("/userRequest")
public ResponseEntity<String> createUserRequest(MultipartHttpServletRequest userRequest) throws IOException {
    
    // 🔹 Extract the file part
    MultipartFile file = userRequest.getFile("file");
    
    // 🔹 Extract the text (JSON) part
    String requestJson = userRequest.getParameter("request");
    
    // 🔹 Convert JSON to DTO
    ObjectMapper mapper = new ObjectMapper();
    CreateUserRequestDTO dto = mapper.readValue(requestJson, CreateUserRequestDTO.class);
    
    // 🔹 Access file info
    System.out.println("File Name: " + file.getOriginalFilename());
    System.out.println("File Size: " + file.getSize());
    
    // 🔹 Process file (save, upload to S3, etc.)
    // file.transferTo(new File("uploads/" + file.getOriginalFilename()));

    return ResponseEntity.ok("File and request processed successfully");
}
```

---

## 💡 Explanation

### 🔸 `MultipartFile`

- It represents the **uploaded file** sent in the HTTP request.
    
- Comes from the `org.springframework.web.multipart` package.
    
- It is automatically populated by Spring when the request’s `Content-Type` is `multipart/form-data`.
    

---

### 🔸 `userRequest.getFile("file")`

- `"file"` is the **name of the form field** (key in the multipart request).
    
- It returns a `MultipartFile` object that lets you:
    
    - Get the file name: `file.getOriginalFilename()`
        
    - Get size: `file.getSize()`
        
    - Get bytes: `file.getBytes()`
        
    - Save the file: `file.transferTo(new File(path))`
        

---

### 🔸 Common Example — Upload Profile Picture

Frontend (React, Angular, Postman, etc.) sends:

```js
formData.append("file", profilePicture);
formData.append("userId", 42);
```

Spring Controller:

```java
@PostMapping("/upload-profile")
public ResponseEntity<String> uploadProfile(MultipartHttpServletRequest request) {
    MultipartFile file = request.getFile("file");
    String userId = request.getParameter("userId");

    System.out.println("Uploading profile picture for user " + userId);
    System.out.println("File name: " + file.getOriginalFilename());
    
    // Save file
    // file.transferTo(new File("/uploads/" + file.getOriginalFilename()));

    return ResponseEntity.ok("Profile picture uploaded!");
}
```

---

## 🧠 Summary

|Concept|Description|
|---|---|
|**`MultipartFile`**|Represents an uploaded file in a multipart request|
|**`userRequest.getFile("file")`**|Retrieves the file part from the request (key must match frontend form field)|
|**Used When**|Your API accepts a file upload (`multipart/form-data`)|
|**Alternative**|Use `@RequestParam("file") MultipartFile file` if you don’t need `MultipartHttpServletRequest`|

---

Would you like me to show how this looks in **Postman (with JSON + file upload)** so you can test your endpoint easily?
