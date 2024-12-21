# Migration Plan: MindfulPath Monorepo to JustBreatheApp Microservices

## Project Context

MindfulPath began as a monolithic application that grew rapidly to over 20,000 lines of code. As the application scaled, we identified the need to modernize our infrastructure and deployment strategy. Key decisions were made to:

1. [DONE] Split the application into two separate repositories:

   - [DONE] Frontend service (Vue.js)
   - [DONE] Backend service (FastAPI)

2. [DONE] Adopt DigitalOcean App Platform for deployment:

   - [DONE] Direct deployment from Git repositories
   - [DONE] No GitHub Actions required
   - [DONE] Automatic deployments on commit
   - [DONE] Separate deployment pipelines for frontend and backend

3. [DONE] Containerize both services:

   - [DONE] Standardized development environments
   - [DONE] Consistent deployment artifacts
   - [DONE] Improved scalability and maintainability

This migration plan outlines the step-by-step process of transitioning from our current monolithic structure to a modern, containerized microservices architecture while maintaining all existing functionality.

The original application (referenced in lines 202-223 of justbreatheapp-context.txt) includes features such as:

- User authentication with Google OAuth 2.0
- Service management and booking system
- Google Calendar integration
- Client management
- Gamification elements
- Accessibility-first design

Our technical stack is being modernized (referenced in lines 7-23 of notes-bootstrap.md) to include:

- Docker-based development and deployment
- Poetry for Python dependency management
- Vue 3 with TypeScript
- FastAPI with SQLAlchemy 2.0
- Comprehensive testing and linting

## Phase 1: Core Infrastructure [IN PROGRESS]

1. Backend Setup [DONE]

   a. Core Files Migration [DONE]
   - Migrate essential Python files to /backend/app:
     - [DONE] database.py (DB connection handling)
     - [DONE] models.py (SQLAlchemy models)
     - [DONE] schemas.py (Pydantic schemas)
     - [DONE] config.py (Environment configuration)
     - [DONE] main.py (FastAPI application with health checks)

   b. Authentication System [DONE]
   - [DONE] Port authentication router
   - [DONE] Migrate JWT utilities
   - [DONE] Google OAuth integration
   - [DONE] User session management

   c. Database Configuration [DONE]
   - [DONE] PostgreSQL setup on DigitalOcean
   - [DONE] Alembic migrations
   - [DONE] Database connection pooling
   - [DONE] Environment-specific configurations

   d. Deployment Pipeline [DONE]
   - [DONE] Multi-stage Dockerfile
   - [DONE] Development environment with hot reload
   - [DONE] Production environment with workers
   - [DONE] Health checks and monitoring
   - [DONE] Database migration automation
   - [DONE] SSL/TLS certificate configuration

2. Frontend Setup [IN PROGRESS]

   a. Core Vue.js Setup [DONE]
   - Port essential configurations:
     - [DONE] Dockerfile configuration
     - [DONE] Nginx configuration
     - [DONE] DigitalOcean App Platform deployment
     - [DONE] JavaScript-based Vue 3 setup
     - [DONE] Vite configuration
     - [DONE] tailwind.config.js
     - [DONE] .env structure
     - [DONE] router setup

   b. Authentication Views [IN PROGRESS]
   - [DONE] Login page
   - [DONE] Signup page          # Working in staging
   - Password reset flow
   - Email verification

   c. Essential Components [DONE]
   - [DONE] NavigationBar
   - [DONE] FooterBar
   - [DONE] Toast notifications
   - [DONE] Card components
   - [DONE] Form components      # Basic forms are working in staging

   d. Core Features [IN PROGRESS]
   - [DONE] User authentication store  # Google OAuth working
   - [DONE] API integration           # Backend communication working
   - [DONE] Route guards             # Auth protection working
   - Error handling

## Phase 2: Feature Migration

1. Service Management [TODO]
   - Service CRUD operations
   - Service discovery
   - Booking integration
   - Calendar integration

2. Client Management [TODO]
   - Client profiles
   - Interaction tracking
   - Form management
   - Communication tools

3. Booking System [TODO]
   - Appointment scheduling
   - Calendar integration
   - Notification system
   - Email confirmations

4. Additional Features [TODO]
   - Gamification system
   - Email templates
   - Intake forms
   - Public profiles

## Technical Requirements

1. Backend
   - FastAPI 0.100+
   - SQLAlchemy 2.0+
   - Python 3.11+
   - Poetry for dependency management
   - Alembic for migrations

2. Frontend
   - Vue 3
   - Vite
   - TailwindCSS
   - TypeScript
   - Pinia for state management

3. Infrastructure
   - Docker containers
   - DigitalOcean App Platform
   - PostgreSQL database

## Technical Achievements

1. Backend Infrastructure [DONE]
   - FastAPI 0.100+ implementation
   - SQLAlchemy 2.0+ with async support
   - Python 3.11+ compatibility
   - Poetry for dependency management
   - Alembic migrations automation
   - Docker containerization
   - DigitalOcean App Platform deployment
   - Automated health checks
   - Multi-worker production setup
   - Database connection pooling
   - SSL/TLS security
   - Environment-specific configurations