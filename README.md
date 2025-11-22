
---

# API Health Checker

API Health Checker is a web application that monitors HTTP APIs and reports their status over time.
It regularly sends requests to configured endpoints and records whether they are **up**, **slow**, or **down**, so you can catch issues early.

---

## Features

* Monitor multiple APIs with different HTTP methods (GET/POST)
* Configure check intervals and timeouts per endpoint
* Multi-region checks (run the same check from different locations/servers)
* Email alerts when an API goes down or recovers
* Simple dashboard showing current status and recent history
* Basic response time metrics for each API
* Historical data for the last 7 days (or more, depending on configuration)

---

## Prerequisites

* Python 3.11 or later
* `pip` (Python package manager)
* Git (to clone the repository)
* A supported database (SQLite by default; no separate install required)

Optional (for production):

* A WSGI/ASGI server such as Gunicorn or Uvicorn
* A reverse proxy such as Nginx

---

## Installation

1. **Clone the repository**

   ```bash
   git clone <your-repo-url>.git
   cd <your-project-folder>
   ```

2. **Create and activate a virtual environment**

   ```bash
   python -m venv venv
   # Windows
   venv\Scripts\activate
   # Linux / macOS
   source venv/bin/activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

4. **Apply database migrations**

   ```bash
   python manage.py migrate
   ```

5. **Create a superuser (optional, for admin access)**

   ```bash
   python manage.py createsuperuser
   ```

6. **Run the development server**

   ```bash
   python manage.py runserver
   ```

Then open your browser at:

```text
http://127.0.0.1:8000/
```

---

## Development

* Start the server with:

  ```bash
  python manage.py runserver
  ```

* Code changes in views, templates, and static files are picked up automatically in development.

* Use the Django admin at `/admin/` to:

  * Manage users
  * View or edit stored checks (if you exposed them in admin)

When you add new models or change existing ones:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Building

For a simple local/demo setup, there is no special build step beyond installing dependencies and running the server.

For a production-like setup:

1. Set `DEBUG = False` and configure `ALLOWED_HOSTS` in `settings.py`.

2. Configure your database (if you want PostgreSQL or another DB instead of SQLite).

3. Collect static files:

   ```bash
   python manage.py collectstatic
   ```

4. Run the app behind a production server (e.g. Gunicorn + Nginx or Uvicorn + Nginx).

---

## Project Structure

Example structure (your names may differ slightly):

```text
api_health_checker/
├─ manage.py
├─ api_health_checker/        # Django project settings
│  ├─ __init__.py
│  ├─ settings.py
│  ├─ urls.py
│  └─ wsgi.py / asgi.py
├─ checker/                   # Main app for health checks
│  ├─ migrations/
│  ├─ templates/checker/      # HTML templates
│  ├─ static/checker/         # CSS / JS / images
│  ├─ models.py               # Database models (API, CheckResult, etc.)
│  ├─ views.py                # Web views and API views
│  ├─ urls.py                 # App-specific URLs
│  └─ tasks.py                # Background/cron-style health checks (if used)
├─ requirements.txt
└─ README.md
```

You can adjust folder names to match your actual project.

---

## Usage

1. **Open the dashboard**

   Go to:

   ```text
   http://127.0.0.1:8000/
   ```

2. **Log in (if required)**
   Use the superuser or a normal user depending on how you configured access.

3. **Add a new API check**

   * Enter the API name (e.g. “User Service – Production”)
   * Enter the URL (e.g. `https://api.example.com/health`)
   * Choose HTTP method (GET/POST)
   * Configure:

     * Check interval (e.g. every 1/5/10 minutes)
     * Timeout in seconds
     * Region/source (if you support multi-region)
   * Set alert email address if you want notifications

4. **View status**

   * Dashboard shows:

     * Current status: Up / Down / Slow
     * Last checked time
     * Average response time

5. **View history**

   * Click into a specific API to see recent check results and trends (e.g. last 7 days).

---

## API Endpoints

Exact paths may differ, but a typical setup looks like this:

* `GET /api/health/`
  Simple health endpoint for the API Health Checker itself.

* `GET /api/checks/`
  List all configured API checks.

* `POST /api/checks/`
  Create a new API check.
  Example body:

  ```json
  {
    "name": "User Service - Prod",
    "url": "https://api.example.com/health",
    "method": "GET",
    "interval_seconds": 60,
    "timeout_seconds": 5,
    "region": "ap-south-1",
    "alert_email": "you@example.com"
  }
  ```

* `GET /api/checks/<id>/`
  Get details of a single check.

* `GET /api/checks/<id>/history/`
  Get recent check results for a single API.

* `POST /api/checks/<id>/run/`
  Trigger an on-demand check for a specific API (useful for testing).

Adjust the paths to match your actual `urls.py`. If you want, you can list the real paths exactly as they are in your code.

---

## Performance Optimization

Some tips to keep checks fast and efficient:

* **Use background jobs**
  Run the health checks via a scheduled task (e.g. Celery + Beat, cron jobs, or a custom scheduler) instead of blocking web requests.

* **Set sensible intervals**
  Very frequent checks on many APIs can increase load. Start with 30–60 seconds for critical services and higher intervals for less important ones.

* **Use timeouts**
  Always set timeouts on HTTP requests so slow endpoints don’t block your worker.

* **Limit stored history**
  Keep only as much historical data as you need (e.g. 7–30 days) and archive or delete older rows.

* **Index database fields**
  Add indexes on fields you filter by often (e.g. `api_id`, `created_at`) to speed up history queries.

---

## Troubleshooting

**Server doesn’t start / migration errors**

* Run:

  ```bash
  python manage.py makemigrations
  python manage.py migrate
  ```
* Check that your database settings in `settings.py` are correct.

---

**Static files (CSS/JS) not loading**

* In development, make sure `DEBUG = True`.

* In production, run:

  ```bash
  python manage.py collectstatic
  ```

* Confirm your web server is serving the `STATIC_ROOT` directory.

---

**Emails are not sending**

* Verify email configuration in `settings.py` (`EMAIL_HOST`, `EMAIL_PORT`, `EMAIL_HOST_USER`, etc.).
* Test sending a simple email from the Django shell to confirm setup.

---

**API checks always fail**

* Check that the URL is correct and reachable from the server running the checks.
* Verify you are using the correct HTTP method (GET/POST).
* Increase timeout if the endpoint is slow.
* If auth is required, make sure headers or tokens are being sent correctly.

---


