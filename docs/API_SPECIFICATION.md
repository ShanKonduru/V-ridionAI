# Véridion API Specification

## Base URL
```
https://api.veridion.ai/v1
```

## Authentication
All API requests require authentication using Bearer tokens:
```http
Authorization: Bearer {access_token}
```

---

## 1. Requirements API

### 1.1 Synthesize Requirement
**Endpoint:** `POST /requirements/synthesize`

**Description:** Process unstructured input and generate structured requirements.

**Request Body:**
```json
{
  "raw_input": "Users want to search products faster. It should show results as they type...",
  "input_type": "text",
  "project_id": "PROJ-123",
  "created_by": "user@example.com"
}
```

**Response (201 Created):**
```json
{
  "req_id": "REQ-2145",
  "user_story": "As a customer, I want to search for products with real-time autocomplete...",
  "acceptance_criteria": "Given I am on the product search page\nWhen I type at least 3 characters...",
  "clarity_score": 0.73,
  "ambiguity_flags": [
    {
      "term": "faster",
      "type": "subjective",
      "suggestion": "Define SLA (e.g., results within 200ms)"
    }
  ],
  "suggested_questions": [
    "What is the maximum acceptable response time?",
    "Should suggestions include product images?"
  ],
  "status": "REVIEW",
  "created_date": "2025-11-07T14:32:15Z"
}
```

### 1.2 Get Requirement
**Endpoint:** `GET /requirements/{req_id}`

**Response (200 OK):**
```json
{
  "req_id": "REQ-2145",
  "req_title": "Product Search Autocomplete",
  "user_story": "...",
  "acceptance_criteria": "...",
  "clarity_score": 0.73,
  "status": "APPROVED",
  "version": 1,
  "created_by": "user@example.com",
  "approved_by": "qa_lead@example.com",
  "approved_date": "2025-11-07T15:00:00Z"
}
```

### 1.3 Approve Requirement
**Endpoint:** `PATCH /requirements/{req_id}/approve`

**Request Body:**
```json
{
  "approved_by": "qa_lead@example.com",
  "comments": "Looks good, proceed with test case generation"
}
```

**Response (200 OK):**
```json
{
  "req_id": "REQ-2145",
  "status": "APPROVED",
  "approved_date": "2025-11-07T15:00:00Z"
}
```

### 1.4 List Requirements
**Endpoint:** `GET /requirements?status={status}&page={page}&limit={limit}`

**Query Parameters:**
- `status`: DRAFT, REVIEW, APPROVED, REJECTED
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20)

**Response (200 OK):**
```json
{
  "total": 127,
  "page": 1,
  "limit": 20,
  "data": [
    {
      "req_id": "REQ-2145",
      "req_title": "Product Search Autocomplete",
      "clarity_score": 0.73,
      "status": "APPROVED",
      "created_date": "2025-11-07T14:32:15Z"
    }
  ]
}
```

---

## 2. Test Cases API

### 2.1 Generate Test Cases
**Endpoint:** `POST /test-cases/generate`

**Description:** Generate comprehensive test cases from approved requirements.

**Request Body:**
```json
{
  "req_id": "REQ-2145",
  "coverage_types": ["POSITIVE", "NEGATIVE", "BOUNDARY", "EDGE"],
  "priority_filter": ["CRITICAL", "HIGH"]
}
```

**Response (201 Created):**
```json
{
  "req_id": "REQ-2145",
  "test_cases": [
    {
      "tc_id": "TC-8763",
      "tc_title": "Valid 3-character search",
      "test_type": "POSITIVE",
      "priority": "CRITICAL",
      "preconditions": "User is on search page",
      "test_steps": [
        {
          "step_number": 1,
          "description": "Navigate to search page",
          "expected": "Search page loads successfully"
        },
        {
          "step_number": 2,
          "description": "Type 'lap' in search box",
          "expected": "Autocomplete suggestions appear within 200ms"
        }
      ],
      "expected_result": "Relevant suggestions displayed",
      "test_data": {
        "query": "lap",
        "expected_suggestions": ["Laptop", "Lapel Pin", "Lap Desk"]
      },
      "automation_ready": true
    }
  ],
  "total_generated": 12,
  "coverage_score": 0.95
}
```

### 2.2 Get Test Case
**Endpoint:** `GET /test-cases/{tc_id}`

**Response (200 OK):**
```json
{
  "tc_id": "TC-8763",
  "req_id": "REQ-2145",
  "tc_title": "Valid 3-character search",
  "test_type": "POSITIVE",
  "priority": "CRITICAL",
  "test_steps": [...],
  "test_data": {...},
  "status": "READY",
  "automation_ready": true,
  "created_date": "2025-11-07T15:05:00Z"
}
```

### 2.3 Update Test Case
**Endpoint:** `PUT /test-cases/{tc_id}`

**Request Body:**
```json
{
  "tc_title": "Updated title",
  "priority": "HIGH",
  "test_steps": [...],
  "automation_ready": true
}
```

### 2.4 List Test Cases
**Endpoint:** `GET /test-cases?req_id={req_id}&test_type={type}&status={status}`

---

## 3. Scripts API

### 3.1 Generate Script
**Endpoint:** `POST /scripts/generate`

**Description:** Generate automation scripts from test cases.

**Request Body:**
```json
{
  "tc_id": "TC-8763",
  "framework": "PLAYWRIGHT",
  "language": "PYTHON",
  "naming_convention": "snake_case",
  "pom_enabled": true,
  "ci_cd_config": {
    "trigger": ["on_commit", "scheduled"],
    "schedule": "0 2 * * *",
    "environment": "QA"
  }
}
```

**Response (201 Created):**
```json
{
  "script_id": "SCR-4521",
  "tc_id": "TC-8763",
  "script_name": "test_product_search_autocomplete.py",
  "framework": "PLAYWRIGHT",
  "language": "PYTHON",
  "script_content": "from playwright.sync_api import Page, expect\n...",
  "script_path": "/tests/product/test_product_search_autocomplete.py",
  "dependencies": [
    "playwright==1.40.0",
    "pytest==7.4.0"
  ],
  "ci_cd_config": {
    "pipeline_file": ".github/workflows/veridion_tests.yml",
    "config_content": "name: Véridion Tests\non:\n  push:\n..."
  },
  "status": "GENERATED",
  "created_date": "2025-11-07T15:15:00Z"
}
```

### 3.2 Get Script
**Endpoint:** `GET /scripts/{script_id}`

### 3.3 Deploy to CI/CD
**Endpoint:** `POST /scripts/{script_id}/deploy`

**Request Body:**
```json
{
  "repository": "github.com/org/repo",
  "branch": "main",
  "auto_commit": true,
  "commit_message": "Add automated test for product search"
}
```

**Response (200 OK):**
```json
{
  "script_id": "SCR-4521",
  "deployment_status": "SUCCESS",
  "repository_url": "https://github.com/org/repo",
  "commit_sha": "a1b2c3d4e5f6",
  "pipeline_url": "https://github.com/org/repo/actions/runs/12345"
}
```

### 3.4 List Scripts
**Endpoint:** `GET /scripts?tc_id={tc_id}&framework={framework}&status={status}`

---

## 4. Execution API

### 4.1 Submit Execution Results
**Endpoint:** `POST /executions/results`

**Description:** Submit test execution results from CI/CD pipeline.

**Request Body:**
```json
{
  "script_id": "SCR-4521",
  "execution_env": "QA",
  "trigger_type": "CI_CD",
  "status": "FAIL",
  "duration_ms": 5200,
  "test_results": {
    "total": 1,
    "passed": 0,
    "failed": 1,
    "skipped": 0
  },
  "error_logs": "TimeoutError: Locator '#search-input' not found",
  "stack_trace": "Traceback (most recent call last):\n  File...",
  "screenshots": [
    {
      "timestamp": "2025-11-07T16:00:15Z",
      "path": "/artifacts/screenshot_1.png",
      "description": "Error state"
    }
  ],
  "video_path": "/artifacts/test_recording.webm"
}
```

**Response (201 Created):**
```json
{
  "exec_id": "EXEC-7821",
  "script_id": "SCR-4521",
  "status": "FAIL",
  "execution_date": "2025-11-07T16:00:00Z",
  "triaging_triggered": true,
  "triaging_job_id": "TRIAGE-9432"
}
```

### 4.2 Get Execution Details
**Endpoint:** `GET /executions/{exec_id}`

**Response (200 OK):**
```json
{
  "exec_id": "EXEC-7821",
  "script_id": "SCR-4521",
  "tc_id": "TC-8763",
  "req_id": "REQ-2145",
  "execution_date": "2025-11-07T16:00:00Z",
  "status": "FAIL",
  "duration_ms": 5200,
  "error_logs": "...",
  "triaging_analysis": {
    "root_cause_category": "LOCATOR_ISSUE",
    "confidence": 0.95,
    "explanation": "The search input selector '#search-input' has changed...",
    "suggested_fix": "Update locator from page.locator('#search-input') to..."
  },
  "bug_id": "BUG-3421"
}
```

### 4.3 List Executions
**Endpoint:** `GET /executions?script_id={script_id}&status={status}&date_from={date}&date_to={date}`

---

## 5. Bug Reports API

### 5.1 Create Bug Report
**Endpoint:** `POST /bugs`

**Description:** Create bug report (can be triggered automatically by AI triager).

**Request Body:**
```json
{
  "exec_id": "EXEC-7821",
  "req_id": "REQ-2145",
  "bug_title": "Search autocomplete test fails - locator changed",
  "bug_description": "The automated test for product search autocomplete...",
  "severity": "MAJOR",
  "priority": "P2",
  "root_cause_analysis": "Frontend code changed element ID to class...",
  "suggested_fix": "Update the test script to use the new selector...",
  "jira_integration": {
    "create_ticket": true,
    "project_key": "WEBQA",
    "issue_type": "Bug",
    "assign_to": "frontend-team"
  }
}
```

**Response (201 Created):**
```json
{
  "bug_id": "BUG-3421",
  "exec_id": "EXEC-7821",
  "bug_title": "Search autocomplete test fails - locator changed",
  "severity": "MAJOR",
  "priority": "P2",
  "status": "NEW",
  "jira_ticket_id": "WEBQA-1234",
  "jira_url": "https://jira.example.com/browse/WEBQA-1234",
  "created_date": "2025-11-07T16:05:00Z"
}
```

### 5.2 Get Bug Report
**Endpoint:** `GET /bugs/{bug_id}`

### 5.3 Update Bug Status
**Endpoint:** `PATCH /bugs/{bug_id}/status`

**Request Body:**
```json
{
  "status": "RESOLVED",
  "assigned_to": "dev@example.com",
  "resolution_notes": "Fixed the selector in the UI component"
}
```

### 5.4 List Bug Reports
**Endpoint:** `GET /bugs?severity={severity}&status={status}&assigned_to={user}`

---

## 6. Analytics API

### 6.1 Get Dashboard Metrics
**Endpoint:** `GET /analytics/dashboard?period={period}`

**Query Parameters:**
- `period`: 7d, 30d, 90d, 1y

**Response (200 OK):**
```json
{
  "period": "30d",
  "metrics": {
    "total_requirements": 127,
    "total_test_cases": 543,
    "automation_coverage": 0.98,
    "total_scripts": 312,
    "test_pass_rate": 0.873,
    "avg_execution_time_ms": 252000,
    "total_bugs": 23,
    "critical_bugs": 2
  },
  "trends": {
    "pass_rate_change": 0.021,
    "coverage_change": 0.053,
    "execution_time_change": -0.18,
    "defect_density_change": -0.25
  },
  "top_failing_modules": [
    {
      "module_name": "Login Module",
      "failure_count": 18,
      "root_cause_distribution": {
        "BACKEND_FAILURE": 0.67,
        "TIMING_ISSUE": 0.33
      }
    }
  ],
  "quality_gate_status": {
    "pass_rate_threshold": 0.85,
    "pass_rate_actual": 0.873,
    "pass_rate_met": true,
    "coverage_threshold": 0.90,
    "coverage_actual": 0.925,
    "coverage_met": true,
    "critical_bugs_threshold": 0,
    "critical_bugs_actual": 2,
    "critical_bugs_met": false,
    "overall_status": "CONDITIONAL_PASS"
  }
}
```

### 6.2 Generate Executive Report
**Endpoint:** `POST /analytics/reports/executive`

**Request Body:**
```json
{
  "period": "30d",
  "format": "PDF",
  "include_charts": true,
  "recipients": ["exec@example.com", "qa_lead@example.com"]
}
```

**Response (200 OK):**
```json
{
  "report_id": "RPT-8821",
  "report_url": "https://storage.veridion.ai/reports/RPT-8821.pdf",
  "generated_date": "2025-11-07T16:30:00Z",
  "delivery_status": "SENT"
}
```

---

## Error Responses

### 400 Bad Request
```json
{
  "error": "VALIDATION_ERROR",
  "message": "Invalid request parameters",
  "details": [
    {
      "field": "clarity_score",
      "issue": "Must be between 0.00 and 1.00"
    }
  ]
}
```

### 401 Unauthorized
```json
{
  "error": "UNAUTHORIZED",
  "message": "Invalid or expired access token"
}
```

### 404 Not Found
```json
{
  "error": "RESOURCE_NOT_FOUND",
  "message": "Requirement with ID REQ-9999 not found"
}
```

### 429 Too Many Requests
```json
{
  "error": "RATE_LIMIT_EXCEEDED",
  "message": "API rate limit exceeded",
  "retry_after": 60
}
```

### 500 Internal Server Error
```json
{
  "error": "INTERNAL_ERROR",
  "message": "An unexpected error occurred",
  "request_id": "req_abc123def456"
}
```

---

## Rate Limits
- Standard tier: 100 requests/minute
- Premium tier: 1000 requests/minute
- Enterprise tier: Custom limits

## Webhooks
Véridion can send webhook notifications for:
- Requirement approved
- Test case generated
- Script execution completed
- Bug created

**Webhook Payload Example:**
```json
{
  "event_type": "test_execution_completed",
  "timestamp": "2025-11-07T16:00:00Z",
  "data": {
    "exec_id": "EXEC-7821",
    "script_id": "SCR-4521",
    "status": "FAIL",
    "triaging_analysis": {...}
  }
}
```
