---
description: Set up Django backend (REST API) and connect to existing frontend
---

## Overview
This workflow creates a fresh Django project (with **Django REST Framework** and **CORS** support), configures a super‑user using the credentials you provided, and wires the frontend to consume the API.

## Prerequisites
- Python 3.11+ installed on the system.
- `pip` available.
- The repository root is `/home/junior/Desktop/Dev Section/diaryhub`.
- Your existing frontend lives in a sub‑folder called `client`.

## Steps
1. **Create a server directory**
   ```bash
   mkdir -p server && cd server
   ```
2. **Initialize a virtual environment** (optional but recommended)
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```
3. **Install required packages**
   // turbo
   ```bash
   pip install django djangorestframework django-cors-headers python-dotenv
   ```
4. **Start a new Django project** named `diaryhub_server`
   // turbo
   ```bash
   django-admin startproject diaryhub_server .
   ```
5. **Create an app for the API** called `api`
   // turbo
   ```bash
   python manage.py startapp api
   ```
6. **Add the new app and third‑party packages to `INSTALLED_APPS`**
   Edit `diaryhub_server/settings.py`:
   ```python
   INSTALLED_APPS = [
       ...
       "rest_framework",
       "corsheaders",
       "api",
   ]
   ```
7. **Configure CORS** (allow the frontend to talk to the API)
   ```python
   MIDDLEWARE = [
       "corsheaders.middleware.CorsMiddleware",
       "django.middleware.common.CommonMiddleware",
       ...
   ]
   CORS_ALLOW_ALL_ORIGINS = True  # Change to a whitelist for production
   ```
8. **Create a `.env` file** with the super‑user credentials (kept out of VCS)
   ```text
   DJANGO_SUPERUSER_USERNAME=admin
   DJANGO_SUPERUSER_EMAIL=protasjunior254@gmail.com
   DJANGO_SUPERUSER_PASSWORD=Protas@01
   ```
9. **Load environment variables in settings**
   ```python
   from pathlib import Path
   from dotenv import load_dotenv
   import os

   BASE_DIR = Path(__file__).resolve().parent.parent
   load_dotenv(BASE_DIR / ".." / ".env")
   ```
10. **Run migrations**
    // turbo
    ```bash
    python manage.py migrate
    ```
11. **Create the super‑user automatically**
    // turbo
    ```bash
    python manage.py createsuperuser --noinput \
        --username $DJANGO_SUPERUSER_USERNAME \
        --email $DJANGO_SUPERUSER_EMAIL
    ```
    (The password is read from `DJANGO_SUPERUSER_PASSWORD` via the environment.)
12. **Add a simple API endpoint** (optional starter)
    - In `api/views.py`:
      ```python
      from rest_framework.views import APIView
      from rest_framework.response import Response

      class HealthCheck(APIView):
          def get(self, request):
              return Response({"status": "ok"})
      ```
    - In `api/urls.py`:
      ```python
      from django.urls import path
      from .views import HealthCheck

      urlpatterns = [
          path("health/", HealthCheck.as_view(), name="health-check"),
      ]
      ```
    - Include the app URLs in the project `urls.py`:
      ```python
      from django.urls import include, path

      urlpatterns = [
          path("api/", include("api.urls")),
          path("admin/", admin.site.urls),
      ]
      ```
13. **Start the development server**
    // turbo
    ```bash
    python manage.py runserver 0.0.0.0:8000
    ```
14. **Configure the client**
    - If the client uses environment variables (e.g., React), create `client/.env`:
      ```text
      VITE_API_URL=http://localhost:8000/api/
      ```
    - Update any fetch/axios base URLs to use `import.meta.env.VITE_API_URL`.

## Verification
- Visit `http://localhost:8000/api/health/` → should return `{"status": "ok"}`.
- Log into Django admin at `http://localhost:8000/admin/` using the credentials above.
- Ensure the frontend can successfully call an endpoint (e.g., the health check) without CORS errors.

---
*This workflow is written to be copy‑pasted into the repository. Adjust paths or settings as needed for your specific project layout.*
