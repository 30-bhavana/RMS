# Recipe Management System

A full-stack recipe manager with login/signup: a Spring Boot REST API backend (JWT auth, persistent H2 database) and a React (Vite) frontend, styled like a recipe box of index cards. Each user has their own private set of recipes.

```
recipe-app/
├── backend/    Spring Boot API (Java 17, Maven, H2 file-based DB, JWT auth)
└── frontend/   React app (Vite)
```

## Backend — `backend/`

Requirements: Java 17+, Maven (or use your IDE's built-in Maven).

```bash
cd backend
mvn spring-boot:run
```

The API starts on **http://localhost:8080**.

Data is stored in a **file-based H2 database** (`backend/data/recipedb.mv.db`), so it now persists across restarts — nothing is lost until you actually delete something. On first startup only, a demo account is seeded:

- **Email:** `demo@example.com`
- **Password:** `password123`
- It owns the two sample recipes.

- H2 console (optional, for poking at the DB): http://localhost:8080/h2-console
  - JDBC URL: `jdbc:h2:file:./data/recipedb`, user `sa`, no password

### Auth endpoints

| Method | Path                | Description                                  |
|--------|---------------------|-----------------------------------------------|
| POST   | `/api/auth/signup`  | Create an account → `{ email, name, password }` |
| POST   | `/api/auth/login`   | Log in → `{ email, password }`                |

Both return `{ token, email, name }`. Every recipe endpoint below now requires an `Authorization: Bearer <token>` header, and only returns/affects recipes owned by that user.

### Recipe endpoints

| Method | Path                          | Description                       |
|--------|-------------------------------|------------------------------------|
| GET    | `/api/recipes`                 | List your recipes                  |
| GET    | `/api/recipes?search=pasta`    | Search your recipes by title       |
| GET    | `/api/recipes?category=Dinner` | Filter your recipes by category    |
| GET    | `/api/recipes/{id}`            | Get one of your recipes            |
| POST   | `/api/recipes`                 | Create a recipe (owned by you)     |
| PUT    | `/api/recipes/{id}`            | Update your recipe                 |
| DELETE | `/api/recipes/{id}`            | Delete your recipe                 |

Example recipe payload (for POST/PUT):

```json
{
  "title": "Garlic Butter Pasta",
  "description": "A quick weeknight pasta.",
  "category": "Dinner",
  "prepTimeMinutes": 10,
  "cookTimeMinutes": 15,
  "servings": 4,
  "ingredients": [
    { "name": "Spaghetti", "quantity": "400 g" },
    { "name": "Garlic cloves, minced", "quantity": "4" }
  ],
  "steps": [
    "Boil salted water and cook spaghetti until al dente.",
    "Sauté garlic in butter, toss with pasta."
  ]
}
```

⚠️ **Before deploying anywhere public:** change `app.jwt.secret` in `application.properties` to a strong random value.

## Frontend — `frontend/`

Requirements: Node.js 18+.

```bash
cd frontend
npm install
npm run dev
```

Opens on **http://localhost:5173** and talks to the backend at `http://localhost:8080` (see `src/api.js` and `src/auth.js` if you need to change the API URL). Make sure the backend is running first.

You'll land on a login/signup screen. Use the demo account (`demo@example.com` / `password123`) or sign up fresh. The session token is stored in the browser's `localStorage`, so you'll stay logged in across page reloads until you log out.

To build for production:

```bash
npm run build
```

## Features

- Sign up / log in; each user's recipes are private to them
- Browse recipes as an index-card grid, search by title, filter by category
- View a recipe's full detail: ingredients, quantities, and numbered method
- Create, edit, and delete recipes with a form for ingredients and steps
- Backend validation surfaced in the UI
- Data persists in a real (file-based) database, not just in memory

