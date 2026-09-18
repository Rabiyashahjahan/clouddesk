# CloudDesk — Architecture

## Overview

CloudDesk is designed as a three-layer application consisting of a React frontend, an Express.js backend API, and a PostgreSQL database. The React frontend will provide the user interface for customers, agents, and administrators. The Express.js API will handle authentication, authorization, business logic, and communication with the database. PostgreSQL will store application data such as users, service requests, comments, and request-related information. In the AWS deployment, the frontend, backend API, and database will be hosted using suitable AWS services.

## Diagram

```text
                    ┌─────────────────────┐
                    │       User          │
                    │ Customer / Agent /  │
                    │       Admin         │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   React Frontend    │
                    │   User Interface    │
                    └──────────┬──────────┘
                               │ HTTP/HTTPS
                               ▼
                    ┌─────────────────────┐
                    │    Express API      │
                    │      Backend        │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │     Middleware      │
                    │ JSON parsing, JWT,  │
                    │     role check      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │       Routes        │
                    │ Match URL + method  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Controllers      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │      Services       │
                    │   Business rules    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Database Layer    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │     PostgreSQL      │
                    │      Database       │
                    └─────────────────────┘
```

## Backend layers

- **Middleware** — Handles cross-cutting concerns such as JSON parsing, authentication, JWT verification, authorization, role checking, and request validation before the request reaches the main logic.

- **Routes** — Defines the API endpoints and matches the incoming HTTP method and URL to the appropriate controller.

- **Controllers** — Receives requests from routes, extracts the required information, calls the appropriate service functions, and sends the HTTP response back to the frontend.

- **Services** — Contains the main business logic of the application, such as creating requests, assigning requests to agents, changing status, calculating SLA due dates, and applying application rules.

- **Database layer** — Handles communication between the application and PostgreSQL and performs database operations such as creating, reading, updating, and deleting records.

## Request flow: creating a support request

1. The customer enters the request details in the React frontend and submits the support request.

2. React sends an HTTP request to the appropriate Express API endpoint.

3. The request passes through middleware, where the JWT is verified to identify the user and the user's role is checked to confirm they are allowed to create a support request.

4. The API route matches the HTTP method and URL and sends the request to the appropriate controller.

5. The controller extracts the required data and passes it to the service layer.

6. The service layer applies the business rules. For example, it calculates the SLA due date based on the priority and sends the required database operation to the database layer.

7. The database layer stores the support request in PostgreSQL and returns the result to the service and controller.

8. The Express API sends the response back to the React frontend, which updates the user interface and displays the created request to the customer.

## Why this structure

This structure separates the user interface, API handling, business logic, and database operations so that each part has a clear responsibility. Keeping business logic in the service layer makes the application easier to test, maintain, and extend without putting too much logic inside controllers or routes. Separating the frontend and backend also allows them to be developed, deployed, and scaled independently.