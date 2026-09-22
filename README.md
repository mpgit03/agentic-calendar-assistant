# Agentic Calendar Assistant

An AI-powered calendar assistant that lets users manage their Google
Calendar through natural-language conversations.

The application combines a Next.js frontend, a TypeScript/Express
backend, Mastra-based agent orchestration, Gemini, PostgreSQL, and
Descope authentication and OAuth infrastructure.

## Features

-   Natural-language calendar interactions through an AI agent
-   Google Calendar integration through OAuth 2.0
-   Calendar event creation and scheduling
-   Conversational agent workflow with tool execution
-   Streaming agent responses and tool-call events to the frontend
-   Authenticated user sessions with Descope
-   Persistent user and calendar-connection data in PostgreSQL
-   TypeScript backend with modular middleware, routes, and services
-   Separate frontend/backend deployment architecture using Vercel and
    Render

## Architecture

``` text
                    +----------------------+
                    |       Next.js        |
                    |      Frontend        |
                    | Sign-in / Dashboard  |
                    +----------+-----------+
                               |
                         HTTP / Stream
                               |
                               v
                    +----------------------+
                    |   Express + TS API   |
                    |       Backend        |
                    +-------+-------+------+
                            |       |
                  +---------+       +----------+
                  v                            v
         +-----------------+          +-----------------+
         |  Mastra Agent  |          |   PostgreSQL    |
         |    + Gemini    |          | Users /         |
         |                 |          | Connections     |
         +--------+--------+          +-----------------+
                  |
             Tool execution
                  v
         +-----------------+
         |     Descope     |
         | Outbound OAuth  |
         +--------+--------+
                  |
                  v
         +-----------------+
         | Google Calendar |
         +-----------------+
```

## Request Flow

``` text
User
  -> Next.js UI
  -> Authenticated API request
  -> Express middleware
  -> Calendar agent service
  -> Mastra agent
  -> Gemini reasoning / tool selection
  -> Google Calendar tool
  -> Descope-managed OAuth connection
  -> Google Calendar API
  -> Stream result back to UI
```

## Tech Stack

### Frontend

-   Next.js
-   React
-   TypeScript
-   Tailwind CSS

### Backend

-   Node.js
-   Express.js
-   TypeScript
-   Mastra
-   Google Gemini
-   Descope
-   REST APIs
-   Streaming responses

### Data & Infrastructure

-   PostgreSQL
-   Docker
-   Vercel
-   Render

### Integrations

-   Google Calendar API
-   Descope OAuth / outbound applications

## Project Structure

``` text
agentic-calendar-assistant/
├── backend/
│   ├── src/
│   │   ├── middleware/
│   │   ├── routes/
│   │   ├── services/
│   │   └── index.ts
│   ├── sql/
│   ├── scripts/
│   ├── package.json
│   └── tsconfig.json
│
├── frontend/
│   ├── src/
│   │   └── app/
│   │       ├── dashboard/
│   │       └── sign-in/
│   ├── package.json
│   └── next.config.*
│
├── docker-compose.yml
└── README.md
```

## Authentication & Calendar Authorization

Descope is used for authentication and for managing the external Google
Calendar OAuth connection.

The Google Calendar connection is configured as a Descope outbound
application. This keeps Google OAuth credentials and the calendar
authorization flow outside the application code.

At runtime:

1.  The user authenticates with the application.
2.  The backend validates the authenticated session.
3.  The application resolves the user's calendar connection.
4.  The agent invokes calendar tools when an operation requires Google
    Calendar access.
5.  Descope manages the OAuth connection to the authorized Google
    account.

## Database

The backend uses PostgreSQL for persistent application data.

The current schema includes tables for:

-   Users
-   Calendar connections

Database migrations are stored in `backend/sql/`.

The backend exposes a health endpoint at `GET /health` for application
and database health checks.

## Local Development

### Prerequisites

-   Node.js 20+
-   Docker Desktop
-   PostgreSQL, or the included Docker setup
-   Descope project
-   Google Cloud project with Google Calendar API enabled
-   Gemini API key

### 1. Clone

``` bash
git clone https://github.com/mpgit03/agentic-calendar-assistant.git
cd agentic-calendar-assistant
```

### 2. Start PostgreSQL

``` bash
docker compose up -d
```

### 3. Configure the backend

Create `backend/.env`:

``` env
DATABASE_URL=
DESCOPE_PROJECT_ID=
DESCOPE_MANAGEMENT_KEY=
DESCOPE_CALENDAR_CONNECTION_ID=google-calendar
GOOGLE_GEMINI_API_KEY=
AI_MODEL=gemini-3.5-flash-lite
PORT=4000
```

Never commit real credentials.

### 4. Configure the frontend

Create `frontend/.env`:

``` env
NEXT_PUBLIC_DESCOPE_PROJECT_ID=
NEXT_PUBLIC_API_URL=http://localhost:4000
```

### 5. Install dependencies

``` bash
cd backend
npm install
cd ../frontend
npm install
```

### 6. Run database migrations

From `backend/`:

``` bash
npm run migrate
```

### 7. Start the backend

From `backend/`:

``` bash
npm run dev
```

The API runs on `http://localhost:4000`.

Health check: `http://localhost:4000/health`

### 8. Start the frontend

From `frontend/`:

``` bash
npm run dev
```

## Production Deployment

The application is designed as separate frontend and backend services:

``` text
Frontend  -> Vercel
Backend   -> Render
Database  -> PostgreSQL
```

The backend production service is deployed on Render at:

`https://agentic-calendar-assistant.onrender.com`

The frontend uses the backend URL through `NEXT_PUBLIC_API_URL`.

Production secrets are configured through hosting-provider environment
variables and are not committed to the repository.

## Engineering Highlights

### Modular backend architecture

Routes are kept thin while authentication, agent execution, and calendar
integration are separated into dedicated middleware and service layers.

### Streaming agent execution

The backend consumes the Mastra agent stream and forwards text and tool
execution events to the frontend, allowing the UI to display progress
instead of waiting for the entire agent operation to finish.

### External OAuth abstraction

Google Calendar credentials are not hard-coded into the application.
Descope manages the outbound OAuth connection, allowing the backend to
work with an authorized calendar account.

### Environment-based configuration

Credentials, API endpoints, model configuration, database URLs, and
deployment-specific settings are supplied through environment variables.

### Error handling

Agent and tool errors are propagated through the stream, while required
configuration and database connectivity are validated during backend
startup.

## Current Status

-   [x] Next.js frontend
-   [x] Express + TypeScript backend
-   [x] PostgreSQL persistence
-   [x] Descope authentication
-   [x] Google Calendar OAuth integration
-   [x] Gemini-powered agent
-   [x] Mastra agent orchestration
-   [x] Calendar scheduling flow
-   [x] Backend deployment on Render
-   [x] Frontend production build
-   [ ] Final frontend Vercel routing/domain issue

## Future Improvements

-   Calendar event update and deletion flows
-   Recurring-event management
-   Conversation history persistence
-   Calendar conflict detection
-   Time-zone aware scheduling improvements
-   Background reminders and notifications
-   Structured logging and observability
-   Automated integration tests
-   Production monitoring

## Why I Built This

This project explores how agentic AI systems can be connected to
real-world APIs while maintaining conventional backend engineering
practices.

The main focus areas are:

-   Agent and tool orchestration
-   OAuth and third-party API integrations
-   Streaming AI responses
-   Authentication and authorization
-   PostgreSQL-backed persistence
-   Modular TypeScript backend architecture
-   Production deployment

## Author

**Marut Panwar**

B.Tech --- Electronics & Communication Engineering\
Indian Institute of Information Technology, Ranchi

GitHub: https://github.com/mpgit03
