# CICD-2 FastAPI User API

This lab contains a FastAPI application for managing users with SQLAlchemy and SQLite, plus a separate product service used to demonstrate calls between services over HTTP.

## Features

- Health check at `GET /health`.
- User endpoints to create, list, retrieve, and delete users.
- Request validation for names, email addresses, ages, and student IDs. Student IDs must match `S` followed by seven digits; ages must be between 19 and 119.
- Duplicate email addresses or student IDs return `409 Conflict`; missing users return `404 Not Found`.
- Remote product lookup at `GET /api/products/{product_id}`. A missing product returns `404`, and an unavailable product service returns `503`.
- Automated tests use an isolated in-memory SQLite database and replace the remote product fetcher with test doubles.

## API Endpoints

| Method | Path | Description |
| --- | --- | --- |
| `GET` | `/health` | Check that the API is running. |
| `POST` | `/api/users` | Create a user. |
| `GET` | `/api/users` | List users in ID order. |
| `GET` | `/api/users/{user_id}` | Retrieve one user. |
| `DELETE` | `/api/users/{user_id}` | Delete one user. |
| `GET` | `/api/products/{product_id}` | Fetch a product from the product service. |

## Run Locally

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

Start the user API:

```bash
uvicorn app.main:app --reload
```

The API is available at `http://127.0.0.1:8000`; interactive documentation is at `/docs`. The SQLite database uses `./app.db` by default, and the `users` table is created when the application is imported.

To try the remote product endpoint, start the product service in a second terminal:

```bash
uvicorn product_service.main:app --reload --port 8001
```

The client uses `http://127.0.0.1:8001` by default. Set `PRODUCT_SERVICE_URL` to use a different product-service address.

## Run Tests

```bash
pytest
```

The test suite covers health checks, user creation and validation, duplicate and missing-user responses, deletion, and remote product success and error handling.

## Repository Structure

```text
app/
	database.py         SQLAlchemy engine, sessions, and database dependency
	main.py             User API and remote product endpoint
	models.py           SQLAlchemy user model
	product_client.py   HTTP client for the product service
	schemas.py          Pydantic request and response schemas
product_service/
	main.py             Standalone product API
tests/
	conftest.py         Shared test client and isolated database fixtures
	test_health.py      Health endpoint tests
	test_remote_products.py
	test_users.py       User API tests
requirements.txt
pytest.ini
```