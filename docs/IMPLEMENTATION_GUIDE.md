# Véridion Implementation Guide

## Quick Start

### Prerequisites
- Python 3.10+
- Node.js 18+
- PostgreSQL 14+
- MongoDB 6+
- Redis 7+
- Docker & Docker Compose

### Installation Steps

#### 1. Clone Repository
```bash
git clone https://github.com/yourorg/veridion-ai.git
cd veridion-ai
```

#### 2. Set Up Environment Variables
```bash
cp .env.example .env
# Edit .env with your configuration
```

#### 3. Start Infrastructure
```bash
docker-compose up -d postgres mongodb redis
```

#### 4. Install Backend Dependencies
```bash
cd backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

#### 5. Run Database Migrations
```bash
alembic upgrade head
```

#### 6. Install Frontend Dependencies
```bash
cd ../frontend
npm install
```

#### 7. Start Development Servers
```bash
# Terminal 1: Backend
cd backend
uvicorn main:app --reload --port 8000

# Terminal 2: Frontend
cd frontend
npm run dev
```

#### 8. Access Application
- Frontend: http://localhost:3000
- Backend API: http://localhost:8000
- API Docs: http://localhost:8000/docs

---

## Project Structure

```
veridion-ai/
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   ├── v1/
│   │   │   │   ├── endpoints/
│   │   │   │   │   ├── requirements.py
│   │   │   │   │   ├── test_cases.py
│   │   │   │   │   ├── scripts.py
│   │   │   │   │   ├── executions.py
│   │   │   │   │   └── bugs.py
│   │   │   │   └── router.py
│   │   │   └── deps.py
│   │   ├── core/
│   │   │   ├── config.py
│   │   │   ├── security.py
│   │   │   └── database.py
│   │   ├── models/
│   │   │   ├── requirement.py
│   │   │   ├── test_case.py
│   │   │   ├── script.py
│   │   │   ├── execution.py
│   │   │   └── bug.py
│   │   ├── schemas/
│   │   │   ├── requirement.py
│   │   │   ├── test_case.py
│   │   │   └── ...
│   │   ├── services/
│   │   │   ├── ai/
│   │   │   │   ├── requirement_synthesizer.py
│   │   │   │   ├── test_case_generator.py
│   │   │   │   ├── script_generator.py
│   │   │   │   └── root_cause_analyzer.py
│   │   │   ├── requirement_service.py
│   │   │   ├── test_case_service.py
│   │   │   └── ...
│   │   └── utils/
│   │       ├── ai_client.py
│   │       ├── jira_client.py
│   │       └── github_client.py
│   ├── alembic/
│   │   └── versions/
│   ├── tests/
│   │   ├── unit/
│   │   ├── integration/
│   │   └── e2e/
│   ├── main.py
│   ├── requirements.txt
│   └── Dockerfile
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Dashboard/
│   │   │   ├── Requirements/
│   │   │   ├── TestCases/
│   │   │   ├── Scripts/
│   │   │   └── Analytics/
│   │   ├── pages/
│   │   ├── services/
│   │   │   └── api.ts
│   │   ├── store/
│   │   │   ├── requirements.ts
│   │   │   ├── testCases.ts
│   │   │   └── ...
│   │   ├── types/
│   │   ├── utils/
│   │   ├── App.tsx
│   │   └── main.tsx
│   ├── public/
│   ├── package.json
│   ├── tsconfig.json
│   ├── vite.config.ts
│   └── Dockerfile
├── ai-workers/
│   ├── workers/
│   │   ├── requirement_worker.py
│   │   ├── test_gen_worker.py
│   │   └── triaging_worker.py
│   ├── requirements.txt
│   └── Dockerfile
├── scripts/
│   ├── setup_db.sh
│   ├── seed_data.py
│   └── migrate.sh
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## Configuration Files

### .env.example
```env
# Application
APP_NAME=Véridion
APP_ENV=development
DEBUG=true
SECRET_KEY=your-secret-key-change-in-production

# Database
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=veridion
POSTGRES_USER=veridion_user
POSTGRES_PASSWORD=secure_password

# MongoDB
MONGODB_HOST=localhost
MONGODB_PORT=27017
MONGODB_DB=veridion_logs

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DB=0

# AI Services
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-4
OPENAI_MAX_TOKENS=2000
OPENAI_TEMPERATURE=0.7

# External Integrations
JIRA_URL=https://your-domain.atlassian.net
JIRA_USER=your-email@example.com
JIRA_API_TOKEN=your-jira-api-token

GITHUB_TOKEN=ghp_...
GITHUB_REPO=your-org/your-repo

# Email/Notifications
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=noreply@veridion.ai
SMTP_PASSWORD=your-app-password

SLACK_WEBHOOK_URL=https://hooks.slack.com/services/...

# Security
JWT_SECRET=your-jwt-secret
JWT_ALGORITHM=HS256
JWT_EXPIRATION_MINUTES=30

# Rate Limiting
RATE_LIMIT_REQUESTS=100
RATE_LIMIT_PERIOD=60
```

### docker-compose.yml
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:14-alpine
    container_name: veridion-postgres
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - veridion-network

  mongodb:
    image: mongo:6
    container_name: veridion-mongodb
    environment:
      MONGO_INITDB_DATABASE: ${MONGODB_DB}
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
    networks:
      - veridion-network

  redis:
    image: redis:7-alpine
    container_name: veridion-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - veridion-network

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: veridion-backend
    environment:
      - POSTGRES_HOST=postgres
      - MONGODB_HOST=mongodb
      - REDIS_HOST=redis
    env_file:
      - .env
    ports:
      - "8000:8000"
    depends_on:
      - postgres
      - mongodb
      - redis
    volumes:
      - ./backend:/app
    networks:
      - veridion-network
    command: uvicorn main:app --host 0.0.0.0 --port 8000 --reload

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: veridion-frontend
    environment:
      - VITE_API_URL=http://localhost:8000
    ports:
      - "3000:3000"
    depends_on:
      - backend
    volumes:
      - ./frontend:/app
      - /app/node_modules
    networks:
      - veridion-network
    command: npm run dev

  ai-worker:
    build:
      context: ./ai-workers
      dockerfile: Dockerfile
    container_name: veridion-ai-worker
    env_file:
      - .env
    depends_on:
      - redis
      - postgres
      - mongodb
    volumes:
      - ./ai-workers:/app
    networks:
      - veridion-network
    command: python -m workers.requirement_worker

volumes:
  postgres_data:
  mongodb_data:
  redis_data:

networks:
  veridion-network:
    driver: bridge
```

---

## Backend Implementation Examples

### requirements.txt
```txt
# FastAPI Framework
fastapi==0.104.1
uvicorn[standard]==0.24.0
pydantic==2.5.0
pydantic-settings==2.1.0

# Database
sqlalchemy==2.0.23
asyncpg==0.29.0
alembic==1.12.1
pymongo==4.6.0
motor==3.3.2

# Redis
redis==5.0.1
celery==5.3.4

# AI/ML
openai==1.3.5
langchain==0.0.335
tiktoken==0.5.1

# Authentication & Security
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6

# HTTP Clients
httpx==0.25.1
aiohttp==3.9.0

# External Integrations
jira==3.5.2
PyGithub==2.1.1

# Email
fastapi-mail==1.4.1

# Testing
pytest==7.4.3
pytest-asyncio==0.21.1
pytest-cov==4.1.0
httpx==0.25.1

# Utilities
python-dotenv==1.0.0
pyyaml==6.0.1
```

### main.py (FastAPI Application)
```python
from fastapi import FastAPI, Request
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import JSONResponse
from app.api.v1.router import api_router
from app.core.config import settings
from app.core.database import engine, Base
import logging

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

# Create database tables
Base.metadata.create_all(bind=engine)

# Initialize FastAPI app
app = FastAPI(
    title=settings.APP_NAME,
    description="AI-Driven Verification & Quality Orchestrator",
    version="1.0.0",
    docs_url="/docs",
    redoc_url="/redoc"
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Include API routes
app.include_router(api_router, prefix="/v1")

@app.get("/")
async def root():
    return {
        "message": "Welcome to Véridion API",
        "version": "1.0.0",
        "docs": "/docs"
    }

@app.get("/health")
async def health_check():
    return {"status": "healthy"}

@app.exception_handler(Exception)
async def global_exception_handler(request: Request, exc: Exception):
    logger.error(f"Unhandled exception: {str(exc)}", exc_info=True)
    return JSONResponse(
        status_code=500,
        content={
            "error": "INTERNAL_ERROR",
            "message": "An unexpected error occurred",
            "request_id": str(id(request))
        }
    )

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(
        "main:app",
        host="0.0.0.0",
        port=8000,
        reload=settings.DEBUG
    )
```

### app/services/ai/requirement_synthesizer.py
```python
from typing import Dict, List
from openai import OpenAI
from app.core.config import settings
import json
import logging

logger = logging.getLogger(__name__)

class RequirementSynthesizer:
    def __init__(self):
        self.client = OpenAI(api_key=settings.OPENAI_API_KEY)
        self.model = settings.OPENAI_MODEL
    
    async def synthesize(self, raw_input: str) -> Dict:
        """
        Synthesize structured requirements from unstructured input.
        
        Args:
            raw_input: Unstructured requirement text
            
        Returns:
            Dict containing user story, acceptance criteria, clarity score, etc.
        """
        try:
            prompt = self._build_synthesis_prompt(raw_input)
            
            response = self.client.chat.completions.create(
                model=self.model,
                messages=[
                    {
                        "role": "system",
                        "content": "You are a Business Analyst AI specializing in requirement engineering."
                    },
                    {
                        "role": "user",
                        "content": prompt
                    }
                ],
                temperature=0.7,
                max_tokens=2000,
                response_format={"type": "json_object"}
            )
            
            result = json.loads(response.choices[0].message.content)
            logger.info(f"Successfully synthesized requirement with clarity score: {result.get('clarity_score')}")
            
            return result
            
        except Exception as e:
            logger.error(f"Error synthesizing requirement: {str(e)}")
            raise
    
    def _build_synthesis_prompt(self, raw_input: str) -> str:
        return f"""
You are a Business Analyst AI. Given the following unstructured input:

"{raw_input}"

Generate a comprehensive requirement analysis with the following:

1. **User Story**: Follow the format "As a [role], I want [feature], so that [benefit]"
2. **Acceptance Criteria**: Use Gherkin format (Given-When-Then) with multiple scenarios
3. **Clarity Score**: A decimal from 0.00 to 1.00 based on:
   - Completeness (all requirements specified)
   - Specificity (concrete, measurable criteria)
   - Unambiguity (no vague or subjective terms)
4. **Ambiguity Flags**: List any:
   - Subjective terms (e.g., "fast", "user-friendly")
   - Missing information
   - Conflicting requirements
   For each flag, provide the term, type, and a specific suggestion
5. **Suggested Questions**: 3-5 clarification questions for stakeholders

Output Format (JSON):
{{
  "user_story": "...",
  "acceptance_criteria": "Given...\\nWhen...\\nThen...",
  "clarity_score": 0.85,
  "ambiguity_flags": [
    {{
      "term": "faster",
      "type": "subjective",
      "suggestion": "Define SLA (e.g., response time < 200ms)"
    }}
  ],
  "suggested_questions": [
    "What is the maximum acceptable response time?",
    "..."
  ]
}}
"""
```

---

## Frontend Implementation Examples

### package.json
```json
{
  "name": "veridion-frontend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "test": "vitest"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "antd": "^5.11.5",
    "@ant-design/icons": "^5.2.6",
    "axios": "^1.6.2",
    "recharts": "^2.10.3",
    "react-flow-renderer": "^10.3.17",
    "monaco-editor": "^0.45.0",
    "@monaco-editor/react": "^4.6.0",
    "zustand": "^4.4.7",
    "dayjs": "^1.11.10"
  },
  "devDependencies": {
    "@types/react": "^18.2.43",
    "@types/react-dom": "^18.2.17",
    "@typescript-eslint/eslint-plugin": "^6.14.0",
    "@typescript-eslint/parser": "^6.14.0",
    "@vitejs/plugin-react": "^4.2.1",
    "eslint": "^8.55.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.5",
    "typescript": "^5.2.2",
    "vite": "^5.0.8",
    "vitest": "^1.0.4"
  }
}
```

### src/services/api.ts
```typescript
import axios, { AxiosInstance } from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:8000';

class ApiClient {
  private client: AxiosInstance;

  constructor() {
    this.client = axios.create({
      baseURL: `${API_BASE_URL}/v1`,
      headers: {
        'Content-Type': 'application/json',
      },
    });

    // Add request interceptor for authentication
    this.client.interceptors.request.use(
      (config) => {
        const token = localStorage.getItem('access_token');
        if (token) {
          config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
      },
      (error) => Promise.reject(error)
    );

    // Add response interceptor for error handling
    this.client.interceptors.response.use(
      (response) => response,
      (error) => {
        if (error.response?.status === 401) {
          // Handle unauthorized access
          localStorage.removeItem('access_token');
          window.location.href = '/login';
        }
        return Promise.reject(error);
      }
    );
  }

  // Requirements API
  async synthesizeRequirement(rawInput: string, projectId: string) {
    const response = await this.client.post('/requirements/synthesize', {
      raw_input: rawInput,
      input_type: 'text',
      project_id: projectId,
    });
    return response.data;
  }

  async getRequirement(reqId: string) {
    const response = await this.client.get(`/requirements/${reqId}`);
    return response.data;
  }

  async approveRequirement(reqId: string, approvedBy: string) {
    const response = await this.client.patch(`/requirements/${reqId}/approve`, {
      approved_by: approvedBy,
    });
    return response.data;
  }

  async listRequirements(params: {
    status?: string;
    page?: number;
    limit?: number;
  }) {
    const response = await this.client.get('/requirements', { params });
    return response.data;
  }

  // Test Cases API
  async generateTestCases(reqId: string, coverageTypes: string[]) {
    const response = await this.client.post('/test-cases/generate', {
      req_id: reqId,
      coverage_types: coverageTypes,
    });
    return response.data;
  }

  async getTestCase(tcId: string) {
    const response = await this.client.get(`/test-cases/${tcId}`);
    return response.data;
  }

  // Scripts API
  async generateScript(tcId: string, config: any) {
    const response = await this.client.post('/scripts/generate', {
      tc_id: tcId,
      ...config,
    });
    return response.data;
  }

  // Analytics API
  async getDashboardMetrics(period: string) {
    const response = await this.client.get('/analytics/dashboard', {
      params: { period },
    });
    return response.data;
  }
}

export const apiClient = new ApiClient();
```

---

## Testing Strategy

### Backend Unit Tests (pytest)
```python
# tests/unit/services/test_requirement_synthesizer.py
import pytest
from app.services.ai.requirement_synthesizer import RequirementSynthesizer

@pytest.mark.asyncio
async def test_synthesize_requirement():
    synthesizer = RequirementSynthesizer()
    
    raw_input = "Users want to search products faster"
    result = await synthesizer.synthesize(raw_input)
    
    assert "user_story" in result
    assert "acceptance_criteria" in result
    assert "clarity_score" in result
    assert 0.0 <= result["clarity_score"] <= 1.0
```

### Frontend Unit Tests (Vitest)
```typescript
// src/components/__tests__/RequirementSynthesis.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import RequirementSynthesis from '../RequirementSynthesis';

describe('RequirementSynthesis', () => {
  it('should render input textarea', () => {
    render(<RequirementSynthesis />);
    expect(screen.getByPlaceholderText(/paste unstructured input/i)).toBeInTheDocument();
  });

  it('should call API on synthesize button click', async () => {
    const mockSynthesize = vi.fn();
    render(<RequirementSynthesis onSynthesize={mockSynthesize} />);
    
    const textarea = screen.getByPlaceholderText(/paste unstructured input/i);
    await userEvent.type(textarea, 'Test requirement');
    
    const button = screen.getByText(/synthesize with ai/i);
    await userEvent.click(button);
    
    expect(mockSynthesize).toHaveBeenCalledWith('Test requirement');
  });
});
```

---

## Deployment

### Kubernetes Deployment
```yaml
# k8s/backend-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: veridion-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: veridion-backend
  template:
    metadata:
      labels:
        app: veridion-backend
    spec:
      containers:
      - name: backend
        image: veridion/backend:latest
        ports:
        - containerPort: 8000
        env:
        - name: POSTGRES_HOST
          valueFrom:
            configMapKeyRef:
              name: veridion-config
              key: postgres_host
        - name: OPENAI_API_KEY
          valueFrom:
            secretKeyRef:
              name: veridion-secrets
              key: openai_api_key
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
---
apiVersion: v1
kind: Service
metadata:
  name: veridion-backend-service
spec:
  selector:
    app: veridion-backend
  ports:
  - port: 80
    targetPort: 8000
  type: LoadBalancer
```

---

## Next Steps

1. **Set up development environment** using the instructions above
2. **Implement core services** starting with requirement synthesis
3. **Build frontend components** for each major feature
4. **Integrate OpenAI API** for AI-powered features
5. **Set up CI/CD pipelines** for automated testing and deployment
6. **Configure monitoring** with Prometheus and Grafana
7. **Conduct security audit** and implement best practices
8. **Perform load testing** to ensure scalability
9. **Deploy to staging environment** for UAT
10. **Launch MVP** and gather user feedback

---

## Support & Documentation

- **API Documentation**: `/docs` endpoint (Swagger UI)
- **User Guide**: Coming soon
- **Developer Guide**: This document
- **Issue Tracker**: GitHub Issues
- **Community**: Slack channel

---

**Document Version:** 1.0  
**Last Updated:** 2025-11-07
