# RaceDayApi

# RaceDayApi — RaceDay Event Management System

A RESTful API for the RaceDay event management system, developed using **ASP.NET Core Web API (.NET 8), Entity Framework Core 8, and SQL Server**.

The API supports user authentication, event and category management, participant enrolments, and race result tracking. It implements role-based access control for Organisers and Participants and provides interactive API documentation through Swagger UI.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Technologies Used](#technologies-used)
- [Features](#features)
- [Setup Instructions](#setup-instructions)
- [User Roles](#user-roles)
- [API Endpoints](#api-endpoints)
- [Project Structure](#project-structure)
- [Authentication and Security](#authentication-and-security)
- [CI/CD Pipeline](#cicd-pipeline)
- [References](#references)
- [Author](#author)

---

## Project Overview

RaceDay is an event management platform designed for running, walking, and cycling events. It allows Organisers to manage events and categories while Participants can browse events, enrol in categories, and view race results.

This project provides the backend API for the RaceDay system, which can be integrated with a frontend application.

## Technologies Used

| Component | Technology |
|---|---|
| Programming Language | C# |
| Framework | ASP.NET Core Web API (.NET 8) |
| ORM | Entity Framework Core 8 |
| Database | SQL Server / LocalDB |
| Authentication | Cookie-based authentication |
| Password Security | BCrypt.Net-Next |
| API Documentation | Swagger UI / Swashbuckle |
| CI/CD | GitHub Actions |
| Development Environment | Visual Studio 2022 |

## Features

- User registration, login, and logout.
- Secure password hashing using BCrypt.
- Cookie-based session management.
- Event creation, updating, viewing, and deletion.
- Category management for events.
- Participant enrolments and cancellations.
- Race result recording and viewing.
- Role-based authorisation for Organisers and Participants.
- Resource ownership checks on protected operations.
- Interactive API testing through Swagger UI.
- Automated builds using GitHub Actions.

## Setup Instructions

### Prerequisites

Install the following before running the project:

- Visual Studio 2022
- .NET 8 SDK
- SQL Server Express or SQL Server LocalDB
- SQL Server Management Studio (SSMS), optional

### 1. Clone the Repository

```bash
git clone https://github.com/Ndamulelo15/RaceDayApi.git
cd RaceDayApi
```

### 2. Open the Solution

Open `RaceDayApi.sln` in Visual Studio 2022.

### 3. Configure the Database

Open `appsettings.json` and verify the connection string:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=RaceDayDb;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
}
```

Change the server name if your SQL Server instance uses a different configuration.

### 4. Apply Database Migrations

In Visual Studio, open **Tools → NuGet Package Manager → Package Manager Console**.

Set the Default Project to `RaceDayApi` and execute:

```powershell
Update-Database
```

This applies the Entity Framework Core migrations to the configured database.

### 5. Run the Application

1. Set `RaceDayApi` as the startup project.
2. Press **F5** or click the Start button.
3. Open Swagger UI using the HTTPS URL displayed by the application, followed by `/swagger`.

Example:

```text
https://localhost:<port>/swagger
```

Replace `<port>` with the port configured for your application.

Use Swagger UI to register a user, log in, and test the available endpoints.

## User Roles

### Organiser

Organisers can:

- Register and log in.
- Create new events.
- Update and delete their own events.
- Create, update, and delete categories for their events.
- View enrolments for their own events.
- Record and update race results.

### Participant

Participants can:

- Register and log in.
- Browse public events and categories.
- Enrol in event categories.
- Cancel their own enrolments.
- View their own race results.

### Access Control

The API enforces authentication, role permissions, and resource ownership where required.

| HTTP Status | Meaning |
|---|---|
| `200 OK` | Request completed successfully |
| `201 Created` | Resource created successfully |
| `400 Bad Request` | Request contains invalid data |
| `401 Unauthorized` | Authentication is required or has failed |
| `403 Forbidden` | User does not have permission |
| `404 Not Found` | Requested resource does not exist |

Actual responses depend on the endpoint implementation and the request being made.

## API Endpoints

The following tables summarise the main API routes.

### Authentication — `/api/auth`

| Method | Endpoint | Access | Description |
|---|---|---|---|
| POST | `/api/auth/register` | Public | Register a new user |
| POST | `/api/auth/login` | Public | Log in and create a session |
| POST | `/api/auth/logout` | Authenticated | Log out |

### Users — `/api/users`

| Method | Endpoint | Access | Description |
|---|---|---|---|
| GET | `/api/users/me` | Authenticated | View own profile |
| PUT | `/api/users/me` | Authenticated | Update own profile |

### Events — `/api/events`

| Method | Endpoint | Access | Description |
|---|---|---|---|
| GET | `/api/events` | Public | List all events |
| GET | `/api/events/{id}` | Public | View an event |
| POST | `/api/events` | Organiser | Create an event |
| PUT | `/api/events/{id}` | Organiser | Update own event |
| DELETE | `/api/events/{id}` | Organiser | Delete own event |

### Categories

| Method | Endpoint | Access | Description |
|---|---|---|---|
| GET | `/api/events/{eventId}/categories` | Public | List event categories |
| POST | `/api/events/{eventId}/categories` | Organiser | Create a category |
| PUT | `/api/categories/{id}` | Organiser | Update own category |
| DELETE | `/api/categories/{id}` | Organiser | Delete own category |

### Enrolments

| Method | Endpoint | Access | Description |
|---|---|---|---|
| POST | `/api/enrolments` | Participant | Enrol in a category |
| GET | `/api/enrolments/me` | Participant | View own enrolments |
| GET | `/api/events/{eventId}/enrolments` | Organiser | View event enrolments |
| PUT | `/api/enrolments/{id}/cancel` | Participant | Cancel own enrolment |

### Results

| Method | Endpoint | Access | Description |
|---|---|---|---|
| GET | `/api/events/{eventId}/results` | Public | View event results |
| POST | `/api/results` | Organiser | Record a race result |
| PUT | `/api/results/{id}` | Organiser | Update a race result |
| GET | `/api/participants/me/results` | Participant | View own results |


## Project Structure

```text
RaceDayApi/
├── .github/
│   └── workflows/
│       └── ci.yml
├── Controllers/
│   ├── AuthController.cs
│   ├── UsersController.cs
│   ├── EventsController.cs
│   ├── CategoriesController.cs
│   ├── EnrolmentsController.cs
│   └── ResultsController.cs
├── DTOs/
│   ├── RegisterDTO.cs
│   ├── LoginDTO.cs
│   ├── UpdateProfileDTO.cs
│   └── EnrolRequestDTO.cs
├── Data/
│   └── ApplicationDBContext.cs
├── Models/
│   ├── User.cs
│   ├── Organiser.cs
│   ├── Participant.cs
│   ├── Event.cs
│   ├── Category.cs
│   ├── EventEnrolment.cs
│   └── Result.cs
├── Properties/
│   └── launchSettings.json
├── appsettings.json
├── Program.cs
├── RaceDayApi.csproj
└── RaceDayApi.sln
```

## Authentication and Security

### Password Security

Passwords are hashed using BCrypt.Net-Next before storage. Plain-text passwords should never be stored or returned in API responses.

### Session Management

The API uses cookie-based authentication. The authentication cookie is named `RaceDayAuth`.

The configured authentication settings determine cookie security, expiration, and session behaviour.

### Role-Based Authorisation

Authorisation attributes and ownership checks are used to restrict access to protected operations.

| Access Rule | Purpose |
|---|---|
| `[Authorize]` | Requires an authenticated user |
| `[Authorize(Roles = "Organiser")]` | Restricts access to Organisers |
| `[Authorize(Roles = "Participant")]` | Restricts access to Participants |
| Ownership checks | Prevent users from modifying resources they do not own |

## CI/CD Pipeline

The project uses **GitHub Actions** to restore dependencies and build the API automatically when code is pushed or a pull request targets a configured branch.

Workflow file: `.github/workflows/ci.yml`

The workflow includes:

1. Checking out the repository.
2. Setting up the .NET 8 SDK.
3. Restoring NuGet dependencies.
4. Building the project in Release configuration.

<img width="1354" height="639" alt="Screenshot 2026-10-16 204644" src="https://github.com/user-attachments/assets/87237a80-64c8-489e-bfd3-823bcc574a8c" />


## References

Coronel, C. and Morris, S. (2019) *Database Systems: Design, Implementation, & Management*. 13th edn. Boston: Cengage Learning.

Microsoft (2024) *ASP.NET Core Web API documentation*. Microsoft Learn. Available at: https://learn.microsoft.com/en-us/aspnet/core/web-api/ (Accessed: 3 September 2026).

Microsoft (2024) *Entity Framework Core documentation*. Microsoft Learn. Available at: https://learn.microsoft.com/en-us/ef/core/ (Accessed: 3 September 2026).

## Author

**Ndamulelo**

- **Module:** PROG6212 — Programming 2B
- **Institution:** Rosebank International
---

