# portfolio-backend

## ✅ Backend Structure Overview
```
src/
├── routes/
│   ├── auth.ts
│   ├── profile.ts
│   ├── projects.ts
│   ├── skills.ts
│   ├── contacts.ts
│   └── public.ts
├── controllers/
├── middleware/
├── model/
├── utils/
├── index.ts
├── server.ts
└── config/
    └── config.json
```

***

### ✅ Core Requirements
	1.	Admin Panel (protected via login):
        •	Edit profile, skills, and projects.
        •	View and manage contact messages.
        •	Toggle visibility of public views.
	2.	Public View:
        •	Fetch and display portfolio data (profile, skills, projects) via slug.
        •	Send contact messages via a public form.

***

### ✅ Key Endpoints
    1. Auth (routes/auth.ts)
        •	POST /api/auth/login – Admin login.
        •	POST /api/auth/logout – Clear session/token.

    2. Profile (routes/profile.ts)
        •	GET /api/profile – Get admin profile.
        •	PUT /api/profile – Update admin profile.

    3. Skills (routes/skills.ts)
        •	GET /api/skills – Get all skills (admin).
        •	POST /api/skills – Add new skill.
        •	PUT /api/skills/:id – Edit skill.
        •	DELETE /api/skills/:id – Delete skill.

    4. Projects (routes/projects.ts)
        •	GET /api/projects – Get all projects (admin).
        •	POST /api/projects – Add project.
        •	PUT /api/projects/:id – Edit project.
        •	DELETE /api/projects/:id – Delete project.

    5. Contacts (routes/contacts.ts)
        •	GET /api/contacts – Admin view of received messages.
        •	POST /api/contacts – Publicly accessible contact form.

    6. Public (routes/public.ts)
        •	GET /api/public/:slug – Get public profile data by slug.
        •	GET /api/public/:slug/skills - Get user’s skills
        •	GET /api/public/:slug/projects - Get user’s projects
        •	POST /api/public/:slug/contact – Send message (same as /contacts).

***

### ✅ Middleware
	•	authMiddleware.ts: Check JWT or session cookie to allow admin access.
	•	errorHandler.ts: Catch and standardize error responses.

***

### ✅ Auth Strategy

    Use JWT or session cookies to protect admin routes. Example:
        •	On login, return a token (JWT).
        •	Admin UI stores the token and sends it with requests.
        •	Middleware checks for and validates token.

***


### Postgres Table:
```typescript
users (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    profile_slug TEXT UNIQUE NOT NULL, // used in public profile URLs
    role TEXT DEFAULT 'user',          // 'user' or 'admin'
    created_at TIMESTAMP DEFAULT now(),
    updated_at TIMESTAMP DEFAULT now()
)
```
```typescript
profiles (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    headline TEXT,
    short_bio TEXT,
    profile_image_url TEXT,
    resume_drive_url TEXT,  -- link to Google Drive file
    resume_view_path TEXT,  -- for routing (e.g., /resume/:slug)
    created_at TIMESTAMP DEFAULT now(),
    updated_at TIMESTAMP DEFAULT now()
)
```
```typescript
skills (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    name TEXT NOT NULL,
    category TEXT,
    icon_url TEXT,
    level TEXT,
    created_at TIMESTAMP DEFAULT now()
)
```
```typescript
projects (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    title TEXT NOT NULL,
    description TEXT,
    image_url TEXT,
    live_demo_url TEXT,
    github_url TEXT,
    featured BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT now()
)
```
```typescript
contact_messages (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    name TEXT,
    email TEXT,
    subject TEXT,
    message TEXT,
    created_at TIMESTAMP DEFAULT now()
)
```
```typescript
public_views (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    slug TEXT UNIQUE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT now()
)
```
