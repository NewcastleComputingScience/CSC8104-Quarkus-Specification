# Supplied Log and Code Extracts

*For Tutorial - Understanding the Contacts Quickstart with GitHub Copilot*

These extracts support the standalone Copilot tutorial. They provide a reproducible troubleshooting case and short code excerpts for students to inspect. The troubleshooting evidence is sanitised so that students can reason from the evidence without relying on the Area service being unavailable during the teaching session.

# Extract 1 - HTTP result from POST /contacts

Request:  
POST /contacts  
Content-Type: application/json

Request body:
```
{

"firstName": "Test",

"lastName": "Contact",

"email": "testcontact002@example.com",

"phoneNumber": "(212) 456-7890",

"birthDate": "1990-01-01"

}
```
Observed result in supplied case:  
HTTP/1.1 500 Internal Server Error

Response body:

{

"error": "An unexpected error occurred whilst processing the request",

"reasons": {}

}

# Extract 2 - Sanitised application log excerpt

This log extract was taken after the POST /contacts request failed. Some technical details, such as full timestamps and environment-specific details, have been removed.

INFO \[ContactService\] ContactService.create() - Creating Test Contact

Hibernate:
```
select

contact0\_.id as id1_1\_,

contact0\_.birth_date as birth_da2_1\_,

contact0\_.email as email3_1\_,

contact0\_.first_name as first_na4_1\_,

contact0\_.last_name as last_nam5_1\_,

contact0\_.phone_number as phone_nu6_1\_,

contact0\_.state as state7_1\_

from

contact contact0\_

where

contact0\_.email=?
```
SEVERE \[RestServiceExceptionMapper\] Mapping RestServiceException with status + "Internal Server Error", message: "An unexpected error occurred whilst processing the request" and stack trace:

RestServiceException: An unexpected error occurred whilst processing the request

# Extract 3 - Direct Area-service test result

Direct test:  
GET \[AREA_SERVICE_HOST\]/areas/212

Observed result in supplied case:  
Request timed out.

Browser message:

The Area service took too long to respond.

ERR_CONNECTION_TIMED_OUT

# Extract 4 - ContactService.create() flow
```
validator.validateContact(contact);

Area area = areaService.getAreaById(

Integer.parseInt(contact.getPhoneNumber().substring(1, 4))

);

contact.setState(area.getState());

return crud.create(contact);
```
Use this to challenge two Copilot mistakes from testing:  
(1) the incorrect claim that ContactValidator calls AreaService;  
(2) the later claim that ContactService.create() contains only database operations.

# Extract 5 - RestServiceExceptionMapper response construction
```
Response.ResponseBuilder builder = Response

.status(e.getStatus())

.entity(new ErrorMessage(e.getMessage(), e.getReasons()));

return builder.build();
```
Students can infer that the HTTP status comes from e.getStatus(). They must then inspect ErrorMessage.java or the actual HTTP response to confirm that the response body is built from the error and reasons fields.
