# Identity Reconciliation Service

A Spring Boot project for advanced identity reconciliation across customer contacts (emails and phone numbers), featuring **asynchronous processing** (`@Async`) and **transactional integrity** (`@Transactional`).  
This service merges contacts into clusters based on shared attributes, ensuring deduplication and proper grouping.

---

## Features

- Create and reconcile customer contact entries via HTTP API.
- Merges contacts by shared email/phone.
- Handles tree/cluster merges, including primary/secondary rules.
- Fully covers all reconciliation edge cases.
- Utilizes async and transactional logic for scalability and consistency.

---

## Setup

### 1. Clone the Repository

git clone https://github.com/yourusername/identity-reconciliation.git

cd identity-reconciliation

### 2. Database Configuration

Update `src/main/resources/application.properties` as needed:

spring.datasource.url=jdbc:mysql://localhost:3306/customer
spring.datasource.username=root
spring.datasource.password=
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=create
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.application.name=Identity-Reconciliation

text

> **Note:** Spring Boot will use environment variables (`SPRING_DATASOURCE_*`) if present, overriding the above values.[2]

---

### 3. Build & Run

./mvnw spring-boot:run

or
mvn spring-boot:run

text

---

## API Usage

### Reconcile or Create a Customer Contact

**Endpoint:**  
`POST /identify`

**Sample Request Body:**
{
"email": "user@mail.com",
"phoneNumber": "1234567890"
}

text

**Example curl:**
curl -X POST http://localhost:8080/identify
-H "Content-Type: application/json"
-d '{"email":"user@mail.com", "phoneNumber":"1234567890"}'

text

---

## Example Reconciliation Scenarios

### 1. Creating Initial Entries

curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"a@mail.com", "phoneNumber":"111"}'
curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"b@mail.com", "phoneNumber":"222"}'
curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"c@mail.com", "phoneNumber":"333"}'
curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"d@mail.com", "phoneNumber":"444"}'

text

### 2. Adding and Merging Related Entries

curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"a@mail.com", "phoneNumber":"555"}'
curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"e@mail.com", "phoneNumber":"555"}'
curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"f@mail.com", "phoneNumber":"555"}'

text
Now, entries 1, 5, 6, 7 form a cluster.

### 3. Cross-linking into Existing Clusters

curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"g@mail.com", "phoneNumber":"444"}'
curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"h@mail.com", "phoneNumber":"444"}'

text

### 4. Primary/Secondary Merges & Cluster Joins

Merge by linking nodes from different primary or secondary clusters:
- When attributes are from primary 3 and secondary 9, merge into primary id 3.
- When both attributes are from different primaries (1 and 2), merge all into the cluster with primary id 1.
- When both attributes are from different secondaries (7 and 9), merge everything (1–9) into the cluster with primary id 1.

**Example curl (primary and secondary merge):**

curl -X POST http://localhost:8080/identify -H "Content-Type: application/json" -d '{"email":"c@mail.com", "phoneNumber":"h@mail.com"}'

text

---

## Cluster Tree Example (after all merges)

(primary)
/ |
5 6 7

(common with 2)
2 -- 3 -- 4 -- 8 -- 9

text

_All nodes linked into a single cluster after full reconciliation._

---

## How to Add Screenshots to the README

1. Save your screenshots in the `screenshots/` folder (e.g., `screenshots/db-table.png`).
2. Reference them in the README like this:

Table after Step 1
![DB Table After Inserts](./screenshots/db-table.png Example

![Clustered Curl Response](./screenshots/c Async and Transactional Features

All reconciliation logic is handled with @Async for concurrency.

Every DB write is wrapped with @Transactional to guarantee atomic reconciliation.

Tech Stack
Spring Boot

Java 17+

MySQL (or compatible relational DB)

Authors
Your Name

License
MIT License

If you see the app connecting to an unexpected database (like Railway), check for overriding environment variables and unset them for local testing!

text
Copy everything inside this code block and paste directly into your `README.md` file—it’s all in one place for a single-click copy!