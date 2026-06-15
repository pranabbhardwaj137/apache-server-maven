# DevOps Lab Program 3 - Microservices with Docker & Docker Compose

## Aim

Design, deploy, and manage a microservices architecture locally using Docker and Docker Compose.

---

# Project Structure

```text
microservices-lab/
│
├── backend/
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
│
├── frontend/
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
│
└── docker-compose.yml
```

---

# Prerequisites

- Docker Desktop installed
- Docker Compose (bundled with Docker Desktop)

```bash
docker --version
docker compose version
```

---

# Step 1: Backend Service

## `backend/Dockerfile`

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt /app/
RUN pip install --no-cache-dir -r requirements.txt

COPY . /app/

EXPOSE 5000

CMD ["python", "app.py"]
```

## `backend/requirements.txt`

```text
Flask==2.1.1
Werkzeug==2.0.3
```

## `backend/app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World! From Backend!'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
```

---

# Step 2: Frontend Service

## `frontend/Dockerfile`

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt /app/
RUN pip install --no-cache-dir -r requirements.txt

COPY . /app/

EXPOSE 5001

CMD ["python", "app.py"]
```

## `frontend/requirements.txt`

```text
Flask==2.1.1
requests==2.26.0
```

## `frontend/app.py`

```python
from flask import Flask
import requests

app = Flask(__name__)

@app.route('/')
def hello_world():
    response = requests.get('http://backend:5000')
    return f"Frontend says: {response.text}"

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5001)
```

---

# Step 3: Docker Compose

## `docker-compose.yml`

```yaml
version: '3.8'

services:
  backend:
    build:
      context: ./backend
    ports:
      - "5000:5000"
    networks:
      - app-network

  frontend:
    build:
      context: ./frontend
    ports:
      - "5001:5001"
    networks:
      - app-network
    depends_on:
      - backend

networks:
  app-network:
    driver: bridge
```

---

# Step 4: Build & Run

```bash
mkdir microservices-lab
cd microservices-lab
mkdir backend frontend
# create all files above, then:
docker-compose up --build
```

---

# Step 5: Test

| URL | Expected output |
|-----|-----------------|
| http://localhost:5001 | `Frontend says: Hello, World! From Backend!` |
| http://localhost:5000 | `Hello, World! From Backend!` |

---

# Step 6: Clean Up

```bash
docker-compose down
```
