# CRUD Operations and HTTP verbs
- Refers to the operations we perform using REST APIs
- **Create**: used to create net variables and set their initial values
- **Read**: used to retrieve the value of a variable
- **Update**: used to change the value of a variable
- **Delete**: used to delete variables
### HTTP Verbs
- HTTP uses verbs (aka methods) that map to the CRUD operations
	- Thus REST APIs typically use HTTP as their application layer protocol

| Purpose                      | CRUD Operation | HTTP Verb  |
| ---------------------------- | -------------- | ---------- |
| Create new variable          | **Create**         | **POST**       |
| Retrieve value of variable   | **Read**           | **GET**        |
| Change the value of variable | **Update**         | **PUT, PATCH** |
| Delete variable              | **Delete**         | **DELETE**     |
### HTTP Request
- When an HTTP request is sent to a server, the header includes info like the HTTP Verb and the URI (Uniform Resource Identifier) to indicate the resource it is trying to access

![[HTTP Request Example.png]]

- Scheme = protocol
- Authority: the address or hostname the request is being sent to
- Path: the resource being accessed

![[HTTP Request Packet.png]]

### HTTP Response
- Includes a status code indicating if the request succeeded or failed as well as other details
	- The first digit indicates the class of the response
		- **1xx** = informational - the request was received, continuing process
		- **2xx** = successful - the request was successfully received, understood, and accepted
		- **3xx** = redirection - further action needs to be taken in order to complete the request
		- **4xx** = client error - there was an error in the request, indicating the request couldn't be processed
		- **5xx** = server error - the server failed to fulfill an apparently valid request
# REST APIs
- **Representational State Transfer**
- Set of rules for how an API should work
- Six constraints of RESTful Architecture:
	1. Uniform Interface
	2. Client-server
		- The client uses API calls (HTTP requests) to access the resources on the server
		- The separation between client and server means they can both change and evolve independently of each other
	3. Stateless
		- Each API exchange is a separate event, independent of all past exchanges between the client and server
			- Thus, the server does not store info about previous requests from the client to determine how it should behave
		- If authentication is being used, this means the client must authenticate with the server for every request
		- TCP is stateful, UDP is stateless
			- Despite this, HTTP and REST APIs themselves aren't stateful; the functions of each layer in the OSI model are separate
	4. Cacheable or non-cacheable
		- Must support caching of data for future use
		- Improves performance for the client and reduces the load on the server
			- Not all resources have to be cacheable, but cacheable resources MUST be declared as cacheable
	1. Layered system
	2. Code-on-demand (optional)
#### Practice API calls with Cisco DevNet
