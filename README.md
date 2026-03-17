# Online Bookstore API (Flask + MySQL)

A simple REST API for an online bookstore built with **Flask**, **MySQL**, **JWT authentication**, and **Swagger/OpenAPI** docs.

> Server runs on **http://localhost:3000** and serves interactive Swagger UI at **/swagger**.

## Features

- User registration and login (JWT access tokens)
- Public catalog endpoints to browse books
- Admin-only endpoints to add/update/delete books
- MySQL persistence
- Swagger UI + `swagger.json` served by the app

## Tech stack

- Python / Flask
- MySQL (via `flask-mysqldb`)
- JWT auth (`flask-jwt-extended`)
- Password hashing (`bcrypt`)
- Swagger UI (`flask-swagger-ui`)

## Project structure

- `app.py` — Flask app entrypoint (registers blueprints, JWT, MySQL, Swagger UI)
- `config.py` — loads environment variables (`python-dotenv`)
- `user_routes.py` — `/api/register`, `/api/login`
- `book_routes.py` — book CRUD endpoints
- `swagger.json` — OpenAPI spec used by Swagger UI
- `*_schema.sql` — example SQL schema for users/books

## Prerequisites

- Python 3.9+ (3.10/3.11 also fine)
- A running MySQL server

## Setup

### 1) Clone

```bash
git clone https://github.com/Ayushyadav00/Online-Bookstore.git
cd Online-Bookstore
```

### 2) Create and activate a virtual environment

```bash
python -m venv .venv
# macOS/Linux
source .venv/bin/activate
# Windows
# .venv\Scripts\activate
```

### 3) Install dependencies

This repository does not currently include a `requirements.txt`. Install the dependencies used in the code:

```bash
pip install Flask flask-restful flask-jwt-extended flask-mysqldb mysqlclient python-dotenv bcrypt flask-swagger-ui
```

> Note: `mysqlclient` may require build tools and MySQL dev headers on your OS.

### 4) Configure environment variables

Create a `.env` file in the project root (next to `config.py`):

```dotenv
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=your_password
MYSQL_DB=bookstore
SECRET_KEY=change-me
JWT_SECRET_KEY=change-me-too
```

### 5) Create database + tables

Create the database configured in `MYSQL_DB` and run the schema files:

```sql
-- in MySQL
CREATE DATABASE bookstore;
USE bookstore;

-- then run
-- user_schema.sql
-- book_schema.sql
```

### 6) Run the API

```bash
python app.py
```

API will be available at:

- Base URL: `http://localhost:3000`
- Swagger UI: `http://localhost:3000/swagger`
- OpenAPI spec: `http://localhost:3000/swagger.json`

## API overview

All routes are under the `/api` prefix.

### Auth

- `POST /api/register` — Register a user
- `POST /api/login` — Login and receive a JWT access token

`/api/register` expects a `registered_type` field (for example: `admin` or `user`). Admin privileges are checked from the `users.registered_type` column.

### Books

Public:

- `GET /api/books` — List all books
- `GET /api/book/<isbn>` — Get a book by ISBN

Admin-only (requires `Authorization: Bearer <token>` and the user must be `registered_type = admin`):

- `POST /api/book` — Add one book
- `POST /api/books` — Add many books (JSON list)
- `PUT /api/book/<isbn>` — Update a book (expects `multipart/form-data`)
- `DELETE /api/book/<isbn>` — Delete a book

## Example requests

### Register

```bash
curl -X POST http://localhost:3000/api/register \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"pass123","registered_type":"admin"}'
```

### Login

```bash
curl -X POST http://localhost:3000/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"pass123"}'
```

### Use token to add a book

```bash
TOKEN="<paste-access-token-here>"

curl -X POST http://localhost:3000/api/book \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"title":"Clean Code","author":"Robert C. Martin","ISBN":"9780132350884","price":39.99,"quantity":10}'
```

## Notes / known gaps

- `swagger.json` contains a `/api/signup` endpoint, but the code implements `/api/register`. Prefer `/api/register`.
- Consider adding a `requirements.txt` and a `.env.example` to make setup easier.

## License

Free Open Source License
