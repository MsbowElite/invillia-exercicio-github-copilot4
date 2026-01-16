# GitHub Copilot Instructions - Mergington High School Activities

## Project Overview

This is a **teaching project** for GitHub Copilot learning. It's a full-stack web application for Mergington High School that allows students to view and sign up for extracurricular activities.

**Stack:** FastAPI (Python backend) + Vanilla JavaScript (frontend) + static HTML/CSS

## Architecture

### Backend ([src/app.py](src/app.py))
- **Framework:** FastAPI with static file mounting
- **Data Storage:** In-memory dictionary (`activities`) - data resets on server restart
- **Design Pattern:** Activity name is the primary identifier (used as URL parameter)
- **Key Data Model:**
  - Activity: `{description, schedule, max_participants, participants: [emails]}`
  - No persistent database; all data is transient

### Frontend ([src/static/](src/static/))
- **Architecture:** Event-driven vanilla JavaScript (no frameworks)
- **Key Pattern:** DOM manipulation with `document.getElementById()` and event listeners
- **API Communication:** Fetch API with query parameters for POST signup (non-standard but intentional)
- **Error Handling:** User-facing messages via message div with auto-hide after 5 seconds

## Critical Workflows

### Running the Application
```bash
# Install dependencies
pip install -r requirements.txt  # Contains: fastapi, uvicorn

# Start the server
python src/app.py

# Access at http://localhost:8000
# API docs: http://localhost:8000/docs
```

### API Endpoints
- `GET /activities` - Returns all activities with current participant counts
- `POST /activities/{activity_name}/signup?email=...` - Enroll a student

**Note:** Signup uses query parameters (not body) for email—this is deliberate for the teaching context.

## Code Patterns & Conventions

### Activity Data Structure
Always follow the model in [app.py](src/app.py#L20-L41):
```python
{
    "activity_name": {
        "description": "...",
        "schedule": "...",
        "max_participants": N,
        "participants": ["email@mergington.edu", ...]
    }
}
```

### Validation Rules
- Activity names must exist in the `activities` dict (case-sensitive)
- Email validation: must be present, already-signed-up emails are rejected
- Cannot exceed `max_participants` - enforce this if adding functionality

### Frontend Query Handling
The JavaScript [app.js](src/static/app.js) uses:
- `encodeURIComponent()` for URL-safe activity names and emails
- Fetch API with async/await pattern
- Message div for feedback (class: `success` or `error`)

## File Structure Reference

```
src/
  app.py              # FastAPI backend with in-memory data
  static/
    index.html        # Page structure with form and activity list
    app.js            # Event handlers and API communication
    styles.css        # Visual styling
requirements.txt      # Python dependencies (fastapi, uvicorn)
```

## Integration Points

1. **Root route** → Redirects to `/static/index.html`
2. **Static files** mounted at `/static/` with FastAPI's StaticFiles
3. **Frontend to Backend:** Fetch requests to `/activities` (GET) and `/activities/{name}/signup` (POST)
4. **CORS:** Not explicitly configured—runs on same domain for development

## Common Modification Patterns

When extending this codebase:
- **Add new activity:** Add entry to `activities` dict in [app.py](src/app.py)
- **Modify validation:** Update check in `/signup` endpoint before `activity["participants"].append()`
- **Change UI feedback:** Update message class logic in [app.js](src/static/app.js#L56-L57)
- **Add student info:** Extend activity model with `"students": {email: {name, year}}` dict

## Project Context

This is an **exercise repository** for learning GitHub Copilot features (chat, edits, PR reviews). Expect code to be intentionally simple for pedagogical purposes, not production-optimized.

---

**Questions?** Check [src/README.md](src/README.md) for quick start or examine the API docs at `/docs` when running.
