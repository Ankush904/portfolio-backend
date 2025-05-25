# Portfolio Management API

A Flask-based RESTful API for managing professional portfolios with admin authentication, profile management, skills, projects, and public portfolio views.

## Features

- Admin authentication with JWT
- Profile management (personal info, skills, projects)
- Public portfolio generation with custom slugs
- Contact message system
- Swagger API documentation

## Technology Stack

- Python 3.x
- Flask
- SQLAlchemy
- PostgreSQL
- Pydantic for validation
- JWT for authentication

## Setup Instructions

### Prerequisites

- Python 3.7+
- PostgreSQL database

### Environment Variables

Create a `.env` file in the root directory with the following variables:

```
# Database Configuration
PGHOST=localhost
PGPORT=5432
PGUSER=postgres
PGPASSWORD=your_password
PGDATABASE=portfolio_db

# Or use a single connection string:
# DATABASE_URL=postgresql://user:password@host:port/dbname

# Application Configuration
SECRET_KEY=your_secret_key  # Used for Flask session encryption
JWT_SECRET_KEY=your_jwt_secret  # Used for JWT token signing
```

### Application Security Configuration

The application requires two secret keys for security:

1. **SECRET_KEY**: Used by Flask for session management, CSRF protection, and other security features.
2. **JWT_SECRET_KEY**: Used specifically for signing JSON Web Tokens (JWTs) for authentication.

#### Generating Secure Keys

For first-time users or local development, you should generate secure random keys. **Never use the example values** in production environments.

You can generate secure keys using Python:

```python
# Run this in a Python console to generate secure keys
import secrets
print("SECRET_KEY:", secrets.token_hex(32))
print("JWT_SECRET_KEY:", secrets.token_hex(32))
```

Example output:
```
SECRET_KEY: 3d6f45a5fc12445dbac2f59c3b6c7cb1d8c93d7cd7c4fe2b1d2785abc456df4a
JWT_SECRET_KEY: 7c8a8b65fb10469a9ad2d01e9c9c57a6d57f1e6b5a5d4e3c2b1a0f9e8d7c6b5
```

#### Setting Up Keys

1. For local development:
   - Copy the generated keys to your `.env` file
   - These keys will be loaded automatically by the application

2. For production:
   - Set these as environment variables in your hosting environment
   - Keep these keys secure and never commit them to version control
   - Rotate these keys periodically for enhanced security

3. If you're using Docker:
   - Pass these as environment variables in your docker-compose.yml or Docker run command
   - Example: `docker run -e SECRET_KEY=your_key -e JWT_SECRET_KEY=your_jwt_key ...`

### Installation

1. Clone the repository:
   ```
   git clone https://github.com/your-username/portfolio-api.git
   cd portfolio-api
   ```

2. Create a virtual environment:
   ```
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:
   ```
   pip install -r requirements.txt
   ```
   
   Required dependencies:
   ```
   Flask==2.3.3
   Flask-Cors==4.0.0
   Flask-JWT-Extended==4.5.3
   Flask-Login==0.6.2
   Flask-SQLAlchemy==3.1.1
   Flask-Swagger-UI==4.11.1
   Flask-WTF==1.2.1
   SQLAlchemy==2.0.23
   Werkzeug==2.3.7
   gunicorn==23.0.0
   psycopg2-binary==2.9.9
   email-validator==2.1.0
   pydantic==2.5.2
   python-dotenv==1.0.0
   marshmallow==3.20.1
   python-dateutil==2.8.2
   pytz==2023.3
   ```

4. Set up the database:
   ```
   flask db init
   flask db migrate
   flask db upgrade
   ```

### Running the Application

```
gunicorn --bind 0.0.0.0:5000 main:app
```

For development:
```
python -m flask run --host=0.0.0.0
```

## API Documentation

Once the application is running, access the Swagger UI documentation at:

```
http://localhost:5000/api/docs
```

## API Endpoints

### Authentication
- `POST /api/auth/signup` - Create a new user account
- `POST /api/auth/login` - Admin login
- `POST /api/auth/logout` - Logout
- `GET /api/auth/check` - Check authentication status

### Profile Management
- `GET /api/profile` - Get admin profile
- `PUT /api/profile` - Update admin profile
- `POST /api/profile/public` - Generate public portfolio
- `DELETE /api/profile/public` - Deactivate public portfolio

### Skills
- `GET /api/skills` - Get all skills
- `POST /api/skills` - Add new skill
- `PUT /api/skills/:id` - Update skill
- `DELETE /api/skills/:id` - Delete skill

### Projects
- `GET /api/projects` - Get all projects
- `POST /api/projects` - Add new project
- `PUT /api/projects/:id` - Update project
- `DELETE /api/projects/:id` - Delete project

### Contacts
- `GET /api/contacts` - Get contact messages
- `POST /api/contacts` - Send contact message

### Public View
- `GET /api/public/:slug` - Get public profile
- `GET /api/public/:slug/skills` - Get public skills
- `GET /api/public/:slug/projects` - Get public projects
- `POST /api/public/:slug/contact` - Send message to profile owner

## API Examples

### Authentication

#### Signup

```
POST /api/auth/signup
```

Request body:
```json
{
  "name": "First Last",
  "email": "first@example.com",
  "phone": "+1234567890",
  "password": "securepassword123",
  "profile_slug": "first-last"
}
```

Response:
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "user": {
      "id": 2,
      "name": "First Last",
      "email": "first@example.com",
      "phone": "+1234567890",
      "profile_slug": "first-last"
    },
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

#### Login

```
POST /api/auth/login
```

Request body:
```json
{
  "email": "admin@example.com",
  "password": "securepassword123"
}
```

Response:
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 1,
      "name": "John Doe",
      "email": "admin@example.com",
      "phone": "+1234567890",
      "profile_slug": "john-doe"
    }
  }
}
```

#### Check Authentication

```
GET /api/auth/check
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Response:
```json
{
  "success": true,
  "message": "Authentication valid",
  "data": {
    "authenticated": true,
    "user": {
      "id": 1,
      "name": "John Doe",
      "email": "admin@example.com"
    }
  }
}
```

### Profile Management

#### Get Admin Profile

```
GET /api/profile
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Response:
```json
{
  "success": true,
  "message": "Profile retrieved successfully",
  "data": {
    "user": {
      "id": 1,
      "name": "John Doe",
      "email": "admin@example.com",
      "phone": "+1234567890",
      "profile_slug": "john-doe"
    },
    "profile": {
      "id": 1,
      "user_id": 1,
      "headline": "Full Stack Developer",
      "short_bio": "Experienced developer with 5+ years of experience",
      "profile_image_url": "https://example.com/profile.jpg",
      "resume_drive_url": "https://drive.google.com/file/d/xyz",
      "resume_view_path": "/view/resume/xyz"
    }
  }
}
```

#### Update Profile

```
PUT /api/profile
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Request body:
```json
{
  "user": {
    "name": "John Doe",
    "email": "john.updated@example.com",
    "phone": "+1987654321",
    "profile_slug": "john-doe-developer"
  },
  "profile": {
    "headline": "Senior Full Stack Developer",
    "short_bio": "Experienced developer specializing in web applications",
    "profile_image_url": "https://example.com/new-profile.jpg",
    "resume_drive_url": "https://drive.google.com/file/d/abc"
  }
}
```

Response:
```json
{
  "success": true,
  "message": "Profile updated successfully",
  "data": {
    "user": {
      "id": 1,
      "name": "John Doe",
      "email": "john.updated@example.com",
      "phone": "+1987654321",
      "profile_slug": "john-doe-developer"
    },
    "profile": {
      "id": 1,
      "user_id": 1,
      "headline": "Senior Full Stack Developer",
      "short_bio": "Experienced developer specializing in web applications",
      "profile_image_url": "https://example.com/new-profile.jpg",
      "resume_drive_url": "https://drive.google.com/file/d/abc",
      "resume_view_path": "/view/resume/abc"
    }
  }
}
```

#### Generate Public Portfolio

```
POST /api/profile/public
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Response:
```json
{
  "success": true,
  "message": "Public portfolio generated successfully",
  "data": {
    "slug": "john-doe-developer",
    "public_url": "https://example.com/portfolio/john-doe-developer"
  }
}
```

### Skills Management

#### Get All Skills

```
GET /api/skills
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Response:
```json
{
  "success": true,
  "message": "Skills retrieved successfully",
  "data": [
    {
      "id": 1,
      "user_id": 1,
      "name": "JavaScript",
      "category": "Frontend",
      "icon_url": "https://example.com/js-icon.png",
      "level": "Expert"
    },
    {
      "id": 2,
      "user_id": 1,
      "name": "Python",
      "category": "Backend",
      "icon_url": "https://example.com/python-icon.png",
      "level": "Advanced"
    }
  ]
}
```

#### Add a New Skill

```
POST /api/skills
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Request body:
```json
{
  "name": "React",
  "category": "Frontend",
  "icon_url": "https://example.com/react-icon.png",
  "level": "Expert"
}
```

Response:
```json
{
  "success": true,
  "message": "Skill added successfully",
  "data": {
    "id": 3,
    "user_id": 1,
    "name": "React",
    "category": "Frontend",
    "icon_url": "https://example.com/react-icon.png",
    "level": "Expert"
  }
}
```

#### Update a Skill

```
PUT /api/skills/3
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Request body:
```json
{
  "name": "React",
  "category": "Frontend Framework",
  "icon_url": "https://example.com/react-new-icon.png",
  "level": "Master"
}
```

Response:
```json
{
  "success": true,
  "message": "Skill updated successfully",
  "data": {
    "id": 3,
    "user_id": 1,
    "name": "React",
    "category": "Frontend Framework",
    "icon_url": "https://example.com/react-new-icon.png",
    "level": "Master"
  }
}
```

#### Delete a Skill

```
DELETE /api/skills/3
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Response:
```json
{
  "success": true,
  "message": "Skill deleted successfully",
  "data": null
}
```

### Projects Management

#### Get All Projects

```
GET /api/projects
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Response:
```json
{
  "success": true,
  "message": "Projects retrieved successfully",
  "data": [
    {
      "id": 1,
      "user_id": 1,
      "title": "E-commerce Website",
      "description": "A full-stack e-commerce website with payment integration",
      "image_url": "https://example.com/project1.jpg",
      "live_demo_url": "https://project-demo.com",
      "github_url": "https://github.com/user/project1",
      "featured": true
    },
    {
      "id": 2,
      "user_id": 1,
      "title": "Task Management App",
      "description": "A React-based task management application",
      "image_url": "https://example.com/project2.jpg",
      "live_demo_url": "https://task-app-demo.com",
      "github_url": "https://github.com/user/project2",
      "featured": false
    }
  ]
}
```

#### Add a New Project

```
POST /api/projects
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Request body:
```json
{
  "title": "Portfolio Website",
  "description": "A modern portfolio website built with React",
  "image_url": "https://example.com/portfolio.jpg",
  "live_demo_url": "https://portfolio-demo.com",
  "github_url": "https://github.com/user/portfolio",
  "featured": true
}
```

Response:
```json
{
  "success": true,
  "message": "Project added successfully",
  "data": {
    "id": 3,
    "user_id": 1,
    "title": "Portfolio Website",
    "description": "A modern portfolio website built with React",
    "image_url": "https://example.com/portfolio.jpg",
    "live_demo_url": "https://portfolio-demo.com",
    "github_url": "https://github.com/user/portfolio",
    "featured": true
  }
}
```

### Contact Messages

#### Get All Contact Messages

```
GET /api/contacts
```

Headers:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Response:
```json
{
  "success": true,
  "message": "Contact messages retrieved successfully",
  "data": [
    {
      "id": 1,
      "user_id": 1,
      "name": "First Last",
      "email": "first@example.com",
      "subject": "Job Opportunity",
      "message": "I would like to discuss a job opportunity with you.",
      "created_at": "2025-05-20T10:30:00Z"
    },
    {
      "id": 2,
      "user_id": 1,
      "name": "Alex Johnson",
      "email": "alex@example.com",
      "subject": "Project Collaboration",
      "message": "I'm interested in collaborating on a project with you.",
      "created_at": "2025-05-21T14:45:00Z"
    }
  ]
}
```

#### Send a Contact Message (Public)

```
POST /api/public/john-doe-developer/contact
```

Request body:
```json
{
  "name": "Michael Brown",
  "email": "michael@example.com",
  "subject": "Consulting Request",
  "message": "I would like to hire you for a consulting project."
}
```

Response:
```json
{
  "success": true,
  "message": "Message sent successfully",
  "data": null
}
```

### Public Portfolio Views

#### Get Public Profile

```
GET /api/public/john-doe-developer
```

Response:
```json
{
  "success": true,
  "message": "Profile retrieved successfully",
  "data": {
    "user": {
      "name": "John Doe",
      "profile_slug": "john-doe-developer"
    },
    "profile": {
      "headline": "Senior Full Stack Developer",
      "short_bio": "Experienced developer specializing in web applications",
      "profile_image_url": "https://example.com/new-profile.jpg"
    }
  }
}
```

#### Get Public Skills

```
GET /api/public/john-doe-developer/skills
```

Response:
```json
{
  "success": true,
  "message": "Skills retrieved successfully",
  "data": [
    {
      "name": "JavaScript",
      "category": "Frontend",
      "icon_url": "https://example.com/js-icon.png",
      "level": "Expert"
    },
    {
      "name": "Python",
      "category": "Backend",
      "icon_url": "https://example.com/python-icon.png",
      "level": "Advanced"
    }
  ]
}
```

#### Get Public Projects

```
GET /api/public/john-doe-developer/projects
```

Response:
```json
{
  "success": true,
  "message": "Projects retrieved successfully",
  "data": [
    {
      "title": "E-commerce Website",
      "description": "A full-stack e-commerce website with payment integration",
      "image_url": "https://example.com/project1.jpg",
      "live_demo_url": "https://project-demo.com",
      "github_url": "https://github.com/user/project1",
      "app_store_url": "",
      "play_store_url": "",
      "website_url": "",
      "featured": true
    },
    {
      "title": "Portfolio Website",
      "description": "A modern portfolio website built with React",
      "image_url": "https://example.com/portfolio.jpg",
      "live_demo_url": "https://portfolio-demo.com",
      "github_url": "https://github.com/user/portfolio",
      "app_store_url": "",
      "play_store_url": "",
      "website_url": "",
      "featured": true
    }
  ]
}
```