# Spring Data JPA — Derived Query Methods, DTO Mapping & @PathVariable

---

# The Bigger Picture

So far, we have learned how data travels through layers:

```text
Client
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

But one major question remains:

> How do we retrieve exactly the data we want from the database without writing SQL manually?

Spring Data JPA solves this.

---

# Requirement 1

```text
Input:
Contact Number

Output:
Location Name
Contact Number
Pincode
```

---

# Situation

A client sends a contact number.

Example:

```text
9876543210
```

The application must return pharmacy details.

---

## Problem

Repository returns:

```java
Optional<PharmacyLocation>
```

Controller should not expose database entities directly.

Need a response object.

---

## Solution

Use:

```text
Entity
    ↓
Response DTO
```

---

# Why Separate Response Classes?

---

## Situation

Entity:

```java
PharmacyLocation
```

contains database representation.

Controller returns data to external clients.

---

## Problem

Returning Entities directly causes problems:

```text
Database structure leaks
Sensitive fields exposed
API becomes tightly coupled to DB
Changing DB affects clients
```

---

## Solution

Use DTOs.

```text
Request DTO
Response DTO
Entity
```

Separately.

---

# Mental Model

Think of it as:

```text
Database Language
        ↓
Entity
        ↓
Service Translation
        ↓
Response DTO
        ↓
Client Language
```

---

# Response DTO

```java
public class PharmacyLocationResponse {

    private String locationName;
    private String conatcNumber;
    private int pincode;

    public PharmacyLocationResponse() {
    }

    public PharmacyLocationResponse(
            String locationName,
            String conatcNumber,
            int pincode) {

        this.locationName = locationName;
        this.conatcNumber = conatcNumber;
        this.pincode = pincode;
    }

    // getters and setters

}
```

---

# Fetching By Contact Number

---

# Service Layer

```java
public PharmacyLocationResponse
getLocationDetailsByContactNumber(
        ContactNumberRequest request) {

    Optional<PharmacyLocation> data =
            repository.findById(
                    request.getConatcNumber());

    PharmacyLocationResponse response = null;

    if (data.isPresent()) {

        PharmacyLocation location = data.get();

        response =
                new PharmacyLocationResponse();

        response.setConatcNumber(
                location.getConatcNumber());

        response.setLocationName(
                location.getLocationName());

        response.setPincode(
                location.getPincode());
    }

    return response;
}
```

---

# Why Optional?

---

## Situation

Suppose contact number does not exist.

Example:

```text
9999999999
```

Database returns nothing.

---

## Problem

Without Optional:

```java
PharmacyLocation location
```

could become:

```java
null
```

leading to:

```text
NullPointerException
```

---

## Solution

Spring Data JPA returns:

```java
Optional<T>
```

---

# Internal Flow

```text
findById()
      │
      ▼
Record Found?
      │
 ┌────┴────┐
 │         │
Yes        No
 │         │
Optional   Optional
Present    Empty
```

---

# Result

Safer code.

---

# Requirement 2

```text
Input:
Location Name

Output:
Multiple Pharmacy Locations
```

Example:

```text
Hyderabad
```

Output:

```text
Pharmacy 1
Pharmacy 2
Pharmacy 3
```

---

# Problem

Location Name is NOT unique.

Example:

```text
Hyderabad
```

can exist many times.

Need multiple records.

---

# Solution

Return:

```java
List<PharmacyLocation>
```

---

# Derived Query Methods

---

# Situation

Need this SQL:

```sql
SELECT *
FROM pharmacy_location
WHERE location_name = ?
```

---

## Problem

Should developers write SQL for every simple query?

Example:

```java
@Query(...)
```

for everything?

Too much work.

---

## Solution

Spring Data JPA derives queries automatically.

Using method names.

---

# Mental Model

Think of it as:

> Spring reads English-like method names and generates SQL.

---

# Repository

```java
@Repository
public interface PharmacyRepository
        extends CrudRepository<
                PharmacyLocation,
                String> {

    List<PharmacyLocation>
    findByLocationName(String locationName);

}
```

---

# Internal Flow

Application Startup:

```text
Repository Interface
       │
Method Name Read
       │
findByLocationName(...)
       │
Spring Understands:
WHERE location_name = ?
       │
SQL Generated
```

---

# Generated SQL

Internally Hibernate executes:

```sql
select
    p1_0.contact_number,
    p1_0.location_name,
    p1_0.pincode
from pharmacy_location p1_0
where p1_0.location_name=?
```

---

# Key Observation

> You never wrote SQL.

Spring generated it.

---

# Derived Query Method Structure

---

## Formula

```text
Introducer
      +
By
      +
Criteria
```

---

# Example

```java
findByLocationName(...)
```

Breaks into:

```text
find
 ↓
Introducer

By
 ↓
Separator

LocationName
 ↓
Criteria
```

---

# Supported Introducers

| Introducer | Meaning |
|-----------|-----------|
| find | Retrieve |
| read | Retrieve |
| query | Retrieve |
| get | Retrieve |
| count | Count Records |

---

# Examples

---

## Equality

```java
findByName(String name)
```

SQL:

```sql
WHERE name = ?
```

---

## More Readable Equality

```java
findByNameIs(String name)
```

SQL:

```sql
WHERE name = ?
```

---

## Equals

```java
findByNameEquals(String name)
```

SQL:

```sql
WHERE name = ?
```

---

## Not Equal

```java
findByNameIsNot(String name)
```

SQL:

```sql
WHERE name <> ?
```

---

# Key Observation

> Method names become query definitions.

---

# Controller Endpoint

---

## Situation

Need pharmacy locations by location name.

---

# Controller

```java
@GetMapping("/load/location")
public List<PharmacyLocationResponse>
getLocationDetailsBylocationName(
        @RequestBody
        LocationNameRequest request) {

    return pharmacyService
            .getLocationDetailsBylocationName(
                    request);
}
```

---

# Request DTO

```java
public class LocationNameRequest {

    private String locationName;

    // getters and setters

}
```

---

# Internal Flow

```text
JSON Request
      │
@RequestBody
      │
LocationNameRequest
      │
Controller
      │
Service
      │
Repository
      │
Database
```

---

# Entity → Response Mapping

---

## Situation

Repository returns:

```java
List<PharmacyLocation>
```

Controller expects:

```java
List<PharmacyLocationResponse>
```

---

## Problem

Need conversion.

---

## Solution

Map Entity Objects to DTO Objects.

---

# Service Layer

```java
List<PharmacyLocationResponse>
listOfLocations =

    data.stream()

        .map(l ->
            new PharmacyLocationResponse(

                l.getLocationName(),
                l.getConatcNumber(),
                l.getPincode()
            ))

        .collect(Collectors.toList());
```

---

# Internal Flow

```text
Entity List
     │
Stream()
     │
map()
     │
Response DTO Created
     │
collect()
     │
Response DTO List
```

---

# Result

Client receives:

```json
[
    {
        "locationName":"Hyderabad",
        "conatcNumber":"1111",
        "pincode":500072
    },
    {
        "locationName":"Hyderabad",
        "conatcNumber":"2222",
        "pincode":500073
    }
]
```

---

# Why Not Return Entities?

---

## Recommended Practice

```text
Controller
    ↓
Request DTO
    ↓
Service
    ↓
Entity
    ↓
Repository
```

Response:

```text
Repository
    ↓
Entity
    ↓
Service
    ↓
Response DTO
    ↓
Controller
```

---

# Path Variables

---

# Situation

Current API:

```http
POST /load/location
```

Body:

```json
{
    "locationName":"Hyderabad"
}
```

---

## Problem

Sometimes only one simple value is needed.

Sending JSON becomes unnecessary.

---

## Solution

Use Path Variables.

---

# Mental Model

Think of Path Variables as:

> Values embedded inside URLs.

---

# URI Template

```text
/location/{locationName}
```

---

# Actual URL

```text
/location/Hyderabad
```

---

# What Happened?

Spring replaces:

```text
{locationName}
```

with:

```text
Hyderabad
```

---

# Controller

```java
@GetMapping(
        "/location/{locationName}")
public List<PharmacyLocationResponse>
loadLocationByLocationName(

        @PathVariable(
            "locationName")
        String locationName) {

    return pharmacyService
            .loadLocationByLocationName(
                    locationName);
}
```

---

# Internal Flow

```text
GET /location/Hyderabad
        │
DispatcherServlet
        │
@PathVariable Found
        │
Extract Hyderabad
        │
Method Parameter Assigned
        │
Controller Executes
```

---

# Service

```java
public List<PharmacyLocationResponse>
loadLocationByLocationName(
        String locationName) {

    List<PharmacyLocation> data =
            repository.findByLocationName(
                    locationName);

    return data.stream()

            .map(l ->
                    new PharmacyLocationResponse(

                        l.getLocationName(),
                        l.getConatcNumber(),
                        l.getPincode()))

            .collect(Collectors.toList());
}
```

---

# Result

Request:

```http
GET /location/Hyderabad
```

Response:

```json
[
    ...
]
```

---

# Multiple Path Variables

---

# Situation

Need:

```text
Location
+
Pincode
```

---

# URI Template

```text
/pharmacy/{location}/pincode/{pincode}
```

---

# Actual URL

```text
/pharmacy/Hyderabad/pincode/500072
```

---

# Controller

```java
@GetMapping(
"/pharmacy/{location}/pincode/{pincode}")

public String
getPharmacyByLocationAndPincode(

        @PathVariable String location,

        @PathVariable String pincode) {

    return "Location Name : "
            + location
            + ", Pincode: "
            + pincode;
}
```

---

# Internal Flow

```text
Request URL
      │
Template Matching
      │
Extract Variables
      │
Bind To Parameters
      │
Controller Executes
```

---

# Result

```text
Location Name : Hyderabad,
Pincode : 500072
```

---

# Using Map for Path Variables

---

# Situation

Too many path variables.

---

## Solution

Capture all variables together.

---

# Example

```java
@GetMapping(
"/pharmacy/{location}/pincode/{pincode}")

public String
getPharmacyByLocationAndPincode(

        @PathVariable
        Map<String,String> values) {

    String location =
            values.get("location");

    String pincode =
            values.get("pincode");

    if(location != null &&
       pincode != null) {

        return "Location Name : "
                + location
                + ", Pin code: "
                + pincode;
    }

    return "Missing Parameters";
}
```

---

# Internal Flow

```text
URL Variables
      │
Map Created
      │
Key → Value Pairs Stored
      │
Controller Reads Values
```

---

# Final Mental Model

```text
Client
   │
GET /location/Hyderabad
   │
@PathVariable
   │
Controller
   │
Service
   │
Repository
   │
Derived Query Method
   │
Spring Generates SQL
   │
Hibernate Executes Query
   │
Entities Returned
   │
DTO Mapping
   │
Jackson Serialization
   │
JSON Array Response
   │
Client
```

Spring Data JPA removes the burden of writing simple SQL, while DTO mapping and path variables help build clean, maintainable REST APIs. The result is an architecture where URLs remain expressive, database access remains simple, and API contracts stay independent from database structures.
