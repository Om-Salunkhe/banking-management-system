# Banking Management System

A REST API for managing bank accounts: create, look up, list and delete accounts, and deposit or withdraw money. Built with Spring Boot and MySQL.

## Tech stack

- Java 21
- Spring Boot 3.5.6 (Spring Web, Spring Data JPA)
- MySQL (via `mysql-connector-j`)
- Lombok
- Gradle (wrapper included)

## Architecture

The code follows a layered structure under `src/main/java/net/javaguides/banking_app/`:

| Layer | Package | Responsibility |
|---|---|---|
| Controller | `controller` | `AccountController` exposes the REST endpoints |
| Service | `service`, `service/impl` | `AccountService` interface and `AccountServiceImpl` business logic (balance checks) |
| Repository | `repository` | `AccountRepository`, a Spring Data `JpaRepository` |
| Entity | `entity` | `Account` JPA entity mapped to the `accounts` table |
| DTO + mapper | `dto`, `mapper` | `AccountDto` and `AccountMapper` keep the entity out of the API |

## API

Base path: `/api/accounts`

| Method | Path | Body | Description |
|---|---|---|---|
| `POST` | `/api/accounts` | `{ "id": 1, "accountHolderName": "Asha", "balance": 1000 }` | Create an account |
| `GET` | `/api/accounts` | — | List all accounts |
| `GET` | `/api/accounts/{id}` | — | Get one account |
| `PUT` | `/api/accounts/{id}/deposit` | `{ "amount": 500 }` | Deposit money |
| `PUT` | `/api/accounts/{id}/withdraw` | `{ "amount": 200 }` | Withdraw money (fails if the balance is too low) |
| `DELETE` | `/api/accounts/{id}` | — | Delete an account |

The account `id` is supplied by the client; it is not auto-generated.

### Example

```bash
curl -X POST http://localhost:8080/api/accounts \
  -H "Content-Type: application/json" \
  -d '{"id": 1, "accountHolderName": "Asha", "balance": 1000}'

curl -X PUT http://localhost:8080/api/accounts/1/withdraw \
  -H "Content-Type: application/json" \
  -d '{"amount": 200}'
```

## Getting started

### Prerequisites

- JDK 21
- A running MySQL server

### Setup

1. Create the database:

   ```sql
   CREATE DATABASE banking_app;
   ```

2. Check the connection settings in `src/main/resources/application.properties` and change the username and password to match your MySQL setup:

   ```properties
   spring.datasource.url=jdbc:mysql://localhost:3306/banking_app
   spring.datasource.username=root
   spring.datasource.password=root
   spring.jpa.hibernate.ddl-auto=update
   ```

   `ddl-auto=update` creates the `accounts` table on first run.

3. Run the app:

   ```bash
   ./gradlew bootRun        # macOS / Linux
   gradlew.bat bootRun      # Windows
   ```

   The API starts on `http://localhost:8080`.

### Tests

```bash
./gradlew test
```

## Known limitations

- Errors (unknown account, insufficient balance) are thrown as `RuntimeException`, so they return HTTP 500 rather than 404 or 400.
- There is no authentication; the API is meant for local learning and testing.
- Database credentials are in `application.properties`; use environment variables for anything beyond local development.
