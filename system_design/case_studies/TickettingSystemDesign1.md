## Ticket Management System Design

### Key Features

- Analytics & Dashboard
- Booking & Scheduling
- Event Registration
- Live streaming
- Multiple payment gateways
- Promo codes functionality
- Push notifications
- QR code generator & scanner

## Key pain-points to solve

- 24\*7 availability
- Eliminate paperwork almost entirely
- Measure events ROI
- Targetted communication
- Improve operational efficiency

### Functional Requirements

- Multiple clients will connect to the ticket management system.
- Tickets can be of different types - gold, silver, bronze, etc, or may not.

### Non-functional Requirements

- Highly available.
- Consistent.
- Ultra-low latency.
- Multi-tenancy

![Base architecture](https://miro.medium.com/v2/resize:fit:720/format:webp/1*ZOWLSRYeAIlRfeWW8ruc4w.png)

### Public Endpoints

- `/create_ticket`
  - inputs: `ticket_type`, `client_id`,
  - outputs: `status`, `ticket_id`.
- `/cancel_ticket`
  - inputs: `ticket_id`,
  - output: `status`
- `/create_account`
  - inputs: `client_name`, `client_email`, `password`,
  - outputs: `status`, `client_id`
- `/login`
  - inputs: `client_id`
  - outputs: `sub_domain`
- `/logout`
  - inputs: `client_id`
  - outputs: `status`
