# Assignment-submission-Grading-System

A comprehensive backend API for educational institutions to digitize
assignment workflows. Built with **FastAPI**, **PostgreSQL**, and **JWT
authentication**.

[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)](https://fastapi.tiangolo.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![JWT](https://img.shields.io/badge/JWT-black?style=for-the-badge&logo=JSON%20web%20tokens)](https://jwt.io/)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)


------------------------------------------------------------------------

##  Table of Contents

-   Features
-   Tech Stack
-   Getting Started
-   API Documentation
-   Project Structure
-   Database Schema
-   Authentication
-   Testing
-   Deployment
-   Contributing
-   License

------------------------------------------------------------------------

## Features

### For Students

-   Submit assignments with file upload (PDF, DOCX, TXT, ZIP, images)
-   View personal dashboard with submission statistics
-   Track grades and feedback on submitted work
-   Automatic submission status (on-time/late) tracking
-   View pending assignments

### For Teachers

-   Create, update, and delete assignments
-   Set due dates and descriptions
-   View all submissions for an assignment
-   Grade submissions and provide feedback
-   Dashboard with pending grading count and assignment statistics

### General Features

-   JWT-based authentication with role-based access control
-   Secure file upload with validation (size & type)
-   PostgreSQL database with proper relationships
-   Auto-generated Swagger API documentation
-   Async SQLAlchemy for high performance

------------------------------------------------------------------------

## Tech Stack

| Component          | Technology                                                                 |
|--------------------|----------------------------------------------------------------------------|
| **Framework**      | [FastAPI](https://fastapi.tiangolo.com/)                                   |
| **Database**       | PostgreSQL with [SQLAlchemy 2.0](https://www.sqlalchemy.org/) (async)     |
| **Migrations**     | [Alembic](https://alembic.sqlalchemy.org/)                                 |
| **Authentication** | JWT (python-jose) + bcrypt (passlib)                                      |
| **File Storage**   | Local filesystem (configurable for AWS S3/Cloudinary)                      |
| **Validation**     | Pydantic v2                                                                |
| **Testing**        | Pytest, httpx, pytest-asyncio                                             |
| **Documentation**  | Swagger UI (built-in) + ReDoc                                             |

------------------------------------------------------------------------

## Getting Started
### Prerequisites
- Python 3.10+
- PostgresSQl 
- Git

### Installation
1. **Clone the repository**

``` bash
git clone https://github.com/FelixOtieno-r/Assignment-submission-Grading-System.git
cd assignment-system
```

2. **Creat and activate virtual envionment**
```bash
python -m venv venv
venv\Scripts\activate
```
3. **Install dependencies**

```bash
pip install -r requirements.txt
```
4. **Set up envionment variables**

Create `.env`:
```bash
    DATABASE_URL=postgresql+asyncpg://username:password@localhost/assignment_db
    JWT_SECRET_KEY=your-secret-key
    ACCESS_TOKEN_EXPIRE_MINUTES=30
    UPLOAD_DIR=./uploads
```
5. **Creat the database**
```bash
createdb -U username assignment_db
```
6. **Run database migrations**
```bash
alembic upgrade head
```
7. **Start the development server**
``` bash
uvicorn app.main:app --reload --host 0.0.0.o --port 8000
```
8. **Access the API**
- API Base URL: `http://localhost:8000`
- Interactive Docs: `http://localhost:8000/docs`
- Alternative Docs: `http://localhost:8000/redoc`

------------------------------------------------------------------------

## API Documentation

Once the server is running, visit `http://localhost:8000/docs` for tthe complete Swagger UI documentation.

### Key Endpoints
#### Authentication

| **Methods**| **Endpoint**| **Description**|
|------------|-------------|----------------|
|POST|`/auth/register`| Register new user|
| POST|`/auth/login`|Login (get JWT token)|
| GET|`/auth/me`|Get current user info|

#### Assignments (Teacher Only)
| **Method**|	**Endpoint**|	**Description**|
|-----------|---------------|------------------|
|POST|`/assignments/`|Create assignment  |
|GET|`/assignments/`|List all assignments |
|GET|`/assignments/{id}`|Get assignment details |
|PUT|`/assignments/{id}`|Update assignment |
|DELETE|`/assignments/{id}`|Delete assignment |

#### Submissions
| **Method**|	**Endpoint**|	**Description**|
|-----------|---------------|------------------|
|POST	|`/submissions/assignments/{id}/submit`	|Submit assignment (file upload)|
|GET	|`/submissions/my`	|Get my submissions|
|GET	|`/submissions/{id}`	|Get submission details|
|PUT	|`/submissions/{id}/grade`	|Grade submission (teacher only)|

#### Dashboard
|**Method**|	**Endpoint**	|**Description**|
|----------|--------------------|---------------|
|GET	|`/dashboard/student`	|Student statistics|
|GET	|`/dashboard/teacher`	|Teacher statistics|

------------------------------------------------------------------------
## Project Structure
```
 assignment-system/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI application entry point
│   ├── config.py               # Environment variables & settings
│   ├── database.py              # Database connection & session
│   ├── models.py                # SQLAlchemy ORM models
│   ├── schemas.py               # Pydantic schemas (request/response)
│   ├── auth.py                  # JWT authentication & dependencies
│   ├── routers/                 # API route handlers
│   │   ├── auth.py
│   │   ├── assignments.py
│   │   ├── submissions.py
│   │   └── dashboard.py
│   ├── services/                # Business logic
│   │   ├── file_storage.py      # File upload handling
│   │   └── submission_status.py # On-time/late calculation
│   └── utils/                   # Utility functions
│       └── dependencies.py       # Role-based access control
├── tests/                        # Pytest test suite
├── uploads/                      # Local file storage directory
├── alembic/                       # Database migrations
├── .env                          # Environment variables
├── requirements.txt               # Python dependencies
├── README.md                      # Project documentation
└── .gitignore                     # Git ignore rules
```
------------------------------------------------------------------------

## Database Schema
### Users Table
|**Column**	|**Type**	|**Description**|
|-----------|------------|--------------|
|id	|UUID	|Primary key|
|email	|String	Unique email address|
|password_hash|	Text	|Bcrypt hashed password|
|full_name|	String	|User's full name|
|role	|Enum	|'student' or 'teacher'|
|created_at|	Timestamp	|Account creation date|

### Assignments Table
|**Column**|	**Type**|	**Description**|
|----------|------------|------------------|
|id|	UUID|	Primary key|
|teacher_id|	UUID|	Foreign key to users|
|title	|String	|Assignment title|
|description	|Text	|Assignment description|
|due_date	|Timestamp|	Submission deadline|
|created_at	|Timestamp	|Creation date|
|updated_at|	Timestamp	|Last update date|

### Submissions Table
|**Column**	|**Type**|	**Description**|
|-----------|---------|----------------|
|id	|UUID|	Primary key|
|assignment_id|	UUID|	Foreign key to assignments|
|student_id|	UUID	|Foreign key to users|
|file_url	|Text|	Path to submitted file|
|submitted_at	|Timestamp|	Submission time|
|status	|Enum	|'on_time' or 'late'|
|grade	|Integer|	Grade (0-100, nullable)|
|feedback	|Text|	Teacher feedback (nullable)|
|graded_at	|Timestamp|	When graded (nullable)|
|graded_by	|UUID|	Teacher who graded (nullable)|


------------------------------------------------------------------------

## Authentication

The API uses JWT (JSON Web Tokens) for authentication.

### How it works:
1. Register or login to receive an access token

2. Include the token in subsequent requests:

```bash
Authorization: Bearer <your-token>
```
3. Tokens expire after 30 minutes (configurable)

### Role-Based Access Control
- Students: Can submit assignments, view own submissions, access student dashboard

- Teachers: Can create/update/delete assignments, grade submissions, access teacher dashboard



------------------------------------------------------------------------

## Testing
- Running the test suite with pytest:

``` bash
pytest -v
```
- For test coverage:
```bash
pytest --cov=app tests/
```
### Example Test
```bash python
async def test_register():
    async with AsyncClient(app=app, base_url="http://test") as ac:
        response = await ac.post("/auth/register", json={
            "email": "test@example.com",
            "password": "secret123",
            "full_name": "Test User",
            "role": "student"
        })
    assert response.status_code == 200
    assert "access_token" in response.json()
 ```

------------------------------------------------------------------------

## Deployment
### Deploy to Render (Free Tier)
1. Push code to GitHub

2. Create a new Web Service on Render

3. Connect your repository

4. Configure:

- Build Command: `pip install -r requirements.txt`

- Start Command: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`

5. Add environment variables (from `.env`)

6. Add a PostgreSQL database via Render Dashboard

7. Deploy!

### Docker Deployment
* Create a `Dockerfile`:
```bash
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```
* Build and run:
``` bash
docker build -t assignment-system .
docker run -p 8000:8000 assignment-system
```
------------------------------------------------------------------------
## Contributing
1. Fork the repository

2. Create a feature branch (`git checkout -b feature/amazing-feature`)

3. Commit changes (`git commit -m 'Add amazing feature'`)

4 Push to branch (`git push origin feature/amazing-feature`)

5. Open a Pull Request

### Development Guidelines
- Follow PEP 8 style guide

- Write tests for new features.

- Update documentation as needed.

- Use type hints.


------------------------------------------------------------------------

## License

This project is licensed under the [MIT License](http://spdx.github.io/license-list-data/MIT.html)

------------------------------------------------------------------------

##  Authors
```

```
