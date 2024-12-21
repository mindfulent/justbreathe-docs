# FastAPI Service Project Bootstrap Request

I'd like to create a FastAPI service that follows the successful deployment patterns from my Vue.js project. Please help me set up a project with:

## Core Requirements

1. Local Development with Docker
- Multi-stage Dockerfile for development and production
- Docker Compose for local development with hot-reload
- Python 3.11+ base image

2. DigitalOcean App Platform Ready
- Direct deployment support (no GitHub Actions needed)
- App Platform specification file (.do/app.yaml)
- Proper port configuration for FastAPI

3. Project Structure
- FastAPI application with modular routing
- Environment variable configuration
- SQLAlchemy integration (optional)
- Proper CORS handling
- Health check endpoint
- OpenAPI documentation enabled

4. Development Tools
- Poetry for dependency management
- Pre-commit hooks
- Black for code formatting
- Ruff for linting
- Pytest for testing

## Key Files Needed

1. Core Application Files:
- Dockerfile
- docker-compose.yml
- .do/app.yaml
- pyproject.toml
- main.py
- requirements.txt
- .env.example
- README.md
- .gitignore

2. Configuration Files:
- nginx.conf (if needed)
- .pre-commit-config.yaml
- pytest.ini

3. Documentation:
- API documentation
- Local development guide
- Deployment guide

Please provide guidance on setting up these components while following Python best practices and ensuring smooth deployment to DigitalOcean App Platform.

## Reference Implementation

The Vue.js project's successful patterns to adapt:

1. Docker setup (reference Dockerfile):