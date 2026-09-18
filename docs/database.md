# CloudDesk — Database Design

## Entities

- **users** — Stores customer, agent, and admin account information.
- **requests** — Stores customer support requests and their current workflow information.
- **comments** — Stores messages and updates added to support requests by users.

## Tables

### users

| Column | Type | Constraints |
|---|---|---|
| id | UUID | Primary Key |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| password_hash | VARCHAR(255) | NOT NULL |
| role | VARCHAR(20) | NOT NULL, CHECK role IN ('customer', 'agent', 'admin') |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT current timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT current timestamp |

### requests

| Column | Type | Constraints |
|---|---|---|
| id | UUID | Primary Key |
| customer_id | UUID | NOT NULL, Foreign Key → users(id) |
| agent_id | UUID | NULL, Foreign Key → users(id) |
| title | VARCHAR(200) | NOT NULL |
| description | TEXT | NOT NULL |
| category | VARCHAR(50) | NOT NULL, CHECK category IN ('access', 'billing', 'outage', 'new_service', 'other') |
| priority | VARCHAR(20) | NOT NULL, CHECK priority IN ('low', 'medium', 'high', 'critical') |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'open', CHECK status IN ('open', 'in_progress', 'resolved', 'closed') |
| sla_due_at | TIMESTAMPTZ | NOT NULL |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT current timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT current timestamp |

### comments

| Column | Type | Constraints |
|---|---|---|
| id | UUID | Primary Key |
| request_id | UUID | NOT NULL, Foreign Key → requests(id) ON DELETE CASCADE |
| user_id | UUID | NOT NULL, Foreign Key → users(id) |
| content | TEXT | NOT NULL |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT current timestamp |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT current timestamp |

## Relationships

- **users → requests (customer)** — One-to-many. One customer can create many requests, while each request has one customer.
- **users → requests (agent)** — One-to-many. One agent can be assigned many requests, while a request can have zero or one assigned agent.
- **requests → comments** — One-to-many. One request can have many comments, while each comment belongs to one request.
- **users → comments** — One-to-many. One user can create many comments, while each comment is created by one user.

## Design Decisions

### Why UUID primary keys

CloudDesk uses UUIDs as primary keys instead of auto-incrementing integers. UUIDs do not expose the number or sequence of records and are less likely to conflict when data is combined across systems. The trade-off is that UUIDs require more storage than integers (16 bytes compared with 4 bytes) and are not naturally sortable by creation time. Therefore, `created_at` is still required when records need to be ordered by when they were created.

### Why password_hash instead of password

The database should never store a user's plain-text password. The application will hash the password using a secure password-hashing algorithm before storing it. The `password_hash` column makes it clear that the stored value is a hash rather than the original password.

### Why UNIQUE(email) is in the database

The application should check whether an email already exists, but that check alone is not enough. Two requests could arrive at nearly the same time and both could pass the application-level check before either user is saved. A database UNIQUE constraint provides the final guarantee that duplicate email addresses cannot be stored.

### Why CHECK constraints are used for role, category, priority, and status

These fields have a limited set of valid values. Database CHECK constraints prevent invalid values from being stored even if data comes from another application, script, or an API bug. This keeps the database data consistent.

### Why agent_id is nullable but customer_id is not

A customer is required for every support request because every request must belong to the person who created it. An agent may not be assigned immediately when a request is created, so `agent_id` can be NULL until an agent takes ownership of the request.

### Why comments cascade on delete but requests do not

Comments belong directly to a request and do not have meaning without that request. Therefore, if a request is deleted, its comments can also be removed automatically using `ON DELETE CASCADE`. Requests themselves should not be automatically deleted because they are important business records and may need to be retained for history, auditing, or reporting.

## Indexes

- **requests(customer_id)** — Speeds up queries that retrieve all requests created by a particular customer.

- **requests(agent_id)** — Speeds up queries that retrieve requests assigned to a particular agent.

- **requests(status)** — Speeds up queries that filter requests by their current status, such as retrieving all open or in-progress requests for dashboards and request management.

- **comments(request_id)** — Speeds up queries that retrieve all comments belonging to a particular request, especially when displaying the comments for a request detail page.

> Note: `users(email)` is not listed separately because the `UNIQUE(email)` constraint automatically creates an index for the unique email values.

## Summary

The CloudDesk database uses three main tables with clear relationships between users, requests, and comments. Primary keys uniquely identify records, foreign keys maintain relationships between tables, constraints protect data integrity, and indexes improve frequently used queries. The design keeps the MVP schema simple while leaving room for future features such as attachments, audit history, notifications, and analytics.