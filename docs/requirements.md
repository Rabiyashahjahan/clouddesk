# CloudDesk — Requirements

## Problem statement

Many organizations manage IT support requests through emails, phone calls, or informal communication. This can make it difficult to track requests, assign them to the right support agent, and monitor their progress.

CloudDesk is a centralized IT service management platform that helps customers create and track service requests while allowing support agents and administrators to manage, prioritize, and resolve those requests efficiently.

## User roles

- Customer — Can register and log in, create service requests, view their own requests, and add comments.

- Agent — Can view all service requests, assign requests to themselves, change request status, and add comments.

- Admin — Can perform everything an agent can do, plus manage users and view the admin dashboard.

## MVP features

- User registration and login
- Role-based authentication and authorization
- Request categories and priorities
- Request status workflow
- Request assignment to agents
- Request creation (title, description, category, priority)
- Customer and agent comments
- Service Level Agreement (SLA) tracking
- Search and filtering
- Pagination
- Customer request tracking
- Agent dashboard
- Admin dashboard
- Input validation
- Error handling
- Password hashing and security controls
- Automated tests
- Docker containerization
- Continuous Integration (CI)
- AWS cloud deployment


## Advanced features (if time allows)

- Email notifications for request updates
- Automatic request categorization
- AI-assisted priority suggestions
- AI-generated response suggestions
- Advanced analytics and reporting
- Real-time notifications
- Advanced audit and activity history
- File attachments and storage using AWS S3
- Internal agent notes

## Future features (not in this version)

- Mobile application for Android and iOS
- Multi-organization or multi-tenant support
- Integration with external IT service management platforms
- Advanced AI-powered support automation
- Voice-based service request creation
- Chatbot-based customer support
- Advanced predictive analytics

## Non-functional requirements

### Security

- Passwords must be securely hashed before being stored.
- Authentication must be implemented securely.
- Role-based authorization must prevent users from accessing unauthorized resources.
- Sensitive configuration values must be stored using environment variables.
- APIs must validate and sanitize user input.
- Production communication must use HTTPS.

### Validation

- All user inputs must be validated on the server side.
- Required fields must be checked before processing requests.
- Invalid data must return clear and appropriate error messages.


### Error handling

- APIs must return appropriate HTTP status codes.
- Errors should provide useful messages without exposing sensitive system information.
- Unexpected server errors should be handled gracefully.

### Performance

- Database queries should be optimized where necessary.
- Search results should support pagination.
- API responses should avoid unnecessary data transfer.
- The application should remain responsive under normal expected usage.

### Availability and reliability

- The application should handle common failures gracefully.
- Important operations should maintain data consistency.
- The system should be designed so that components can be maintained and updated independently.

### HTTPS

- Production APIs and frontend communication must use HTTPS.
- Sensitive information must not be transmitted through unsecured connections.
