# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

RAGFlow is an open-source RAG (Retrieval-Augmented Generation) engine based on deep document understanding. It combines LLMs to deliver truthful Q&A capabilities with citations from complex formatted data.

**Tech Stack:**
- Backend: Python 3.10-3.12, Flask 3.0.3
- Frontend: React 18.2.0, TypeScript, UMI 4.0.90, Ant Design 5.12.7
- Databases: Elasticsearch/Infinity/OpenSearch (vector), MySQL (relational), Redis (cache)
- Infrastructure: Docker, Docker Compose, Kubernetes (Helm)

## Development Commands

### Backend
```bash
# Setup (first time)
pipx install uv pre-commit
uv sync --python 3.10 --all-extras
uv run download_deps.py
pre-commit install

# Start dependent services
docker compose -f docker/docker-compose-base.yml up -d

# Run backend
source .venv/bin/activate
export PYTHONPATH=$(pwd)
bash docker/launch_backend_service.sh

# Testing
pytest  # Run all tests
pytest api/test/test_http_api.py  # Run specific test file
```

### Frontend
```bash
cd web
npm install        # Install dependencies
npm run dev        # Development server (port 3000)
npm run build      # Production build
npm run lint       # Run ESLint
npm test          # Run Jest tests
```

### Docker Operations
```bash
# Full stack deployment
docker compose -f docker-compose.yml up -d

# GPU-enabled deployment
docker compose -f docker-compose-gpu.yml up -d

# Development base services only
docker compose -f docker/docker-compose-base.yml up -d
```

## Architecture

### Directory Structure
```
ragflow/
├── api/           # Flask REST API server
│   ├── apps/      # API endpoints by feature
│   ├── db/        # Database models and operations
│   └── test/      # API tests
├── web/           # React frontend application
│   ├── src/       # Source code
│   │   ├── pages/ # Page components
│   │   ├── components/ # Reusable components
│   │   └── hooks/ # Custom React hooks
├── rag/           # Core RAG functionality
│   ├── llm/       # LLM integrations
│   ├── app/       # RAG application logic
│   └── nlp/       # NLP utilities
├── deepdoc/       # Document parsing engine
│   ├── parser/    # Multi-format parsers
│   └── vision/    # OCR and layout recognition
├── agent/         # Agentic workflow components
├── graphrag/      # Graph-based RAG
└── docker/        # Docker configurations
```

### Key Components

**API Layer (`api/`):**
- Uses Flask with blueprints for modular routing
- Authentication via Flask-Login with session management
- Database operations through Peewee ORM
- RESTful endpoints under `api/apps/`

**Frontend (`web/`):**
- UMI-based React app with TypeScript
- Zustand for state management
- TanStack Query for data fetching
- Ant Design components with Tailwind utilities
- i18n support with multiple languages

**RAG Engine (`rag/`):**
- LLM factory pattern for provider abstraction
- Embedding service with multiple model support
- Retrieval pipeline with reranking
- Conversation management with context handling

**Document Processing (`deepdoc/`):**
- Template-based chunking for intelligent segmentation
- Multi-format support (PDF, DOCX, PPTX, Excel, HTML, Markdown)
- Vision-based layout analysis for complex documents
- OCR integration for scanned documents

## Configuration

### Environment Variables
- Backend: `docker/.env` for Docker deployment
- Service config: `docker/service_conf.yaml.template`
- Frontend: Environment-specific configs in `web/`

### Key Files
- `pyproject.toml` - Python dependencies and project config
- `web/package.json` - Frontend dependencies and scripts
- `docker-compose.yml` - Production deployment config
- `docker/docker-compose-base.yml` - Development services

## Testing Strategy

**Backend Testing:**
- Unit tests for core functionality
- API integration tests in `api/test/`
- SDK tests for Python client
- Use pytest with fixtures for test data

**Frontend Testing:**
- Jest for unit tests
- React Testing Library for component tests
- Located in `web/src/**/__tests__/`

## Development Tips

1. **Dependency Management**: Use `uv` for Python dependencies, not pip directly
2. **Pre-commit Hooks**: Always run `pre-commit install` to ensure code quality
3. **API Development**: New endpoints go in `api/apps/` with corresponding tests
4. **Frontend Routes**: Defined in `web/src/pages/` following UMI conventions
5. **Database Migrations**: Handle schema changes in `api/db/`
6. **Docker Development**: Use base services for local development to save resources
7. **LLM Integration**: Add new providers in `rag/llm/` following factory pattern
8. **Document Parsers**: Extend parsers in `deepdoc/parser/` for new formats

## Common Tasks

### Adding a New API Endpoint
1. Create blueprint in `api/apps/<feature>/`
2. Add database models in `api/db/models.py` if needed
3. Write tests in `api/test/test_http_api.py`
4. Update API documentation

### Adding a Frontend Page
1. Create page component in `web/src/pages/`
2. Add route configuration (UMI auto-routing)
3. Create API hooks in `web/src/hooks/`
4. Add i18n translations in `web/src/locales/`

### Integrating a New LLM Provider
1. Implement provider class in `rag/llm/`
2. Register in LLM factory
3. Add configuration in service_conf.yaml
4. Update frontend model selection UI

### Adding Document Parser
1. Create parser class in `deepdoc/parser/`
2. Register in parser factory
3. Add file type detection logic
4. Write tests for parsing accuracy