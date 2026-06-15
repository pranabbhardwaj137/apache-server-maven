# DevOps Lab Program 2 - Docker Containerization and Deployment

## Aim
Install Docker, explore containerization commands, create Docker containers using various operating system images, deploy a containerized application using Docker, and push the image to Docker Hub.

---

# Project Structure

```text
docker-lab-p2/
│
├── app.py
├── Dockerfile
└── README.md
```

---

# Prerequisites

- Windows 10/11
- Docker Desktop installed
- Docker Hub account
- Internet connection

---

# Step 1: Install Docker Desktop

1. Download Docker Desktop from the official Docker website.
2. Run the installer.
3. Follow the installation wizard.
4. Restart the system if prompted.
5. Open Docker Desktop and verify it is running.

Verify installation:

```bash
docker --version
```

---

# Step 2: Basic Docker Commands

### Check Docker Version

```bash
docker --version
```

### Display Docker System Information

```bash
docker info
```

### List Running Containers

```bash
docker ps
```

### List All Containers

```bash
docker ps -a
```

### List Available Images

```bash
docker images
```

### Pull an Image from Docker Hub

```bash
docker pull ubuntu
```

### Run a Container Interactively

```bash
docker run -it ubuntu
```

### Stop a Running Container

```bash
docker stop <container_id>
```

### Remove a Container

```bash
docker rm <container_id>
```

### Remove an Image

```bash
docker rmi <image_id>
```

### Build an Image from Dockerfile

```bash
docker build -t <image_name> .
```

---

# Step 3: Create Containers Using Different Operating System Images

## Ubuntu

```bash
docker pull ubuntu
docker run -it ubuntu
exit
```

## Alpine Linux

```bash
docker pull alpine
docker run -it alpine
exit
```

## CentOS

```bash
docker pull centos
```

## Debian

```bash
docker pull debian
```

## Fedora

```bash
docker pull fedora
```

---

# Step 4: Deploy a Containerized Python Flask Application

## Create app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, Docker!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

---

## Create Dockerfile

Filename must be exactly:

```text
Dockerfile
```

Content:

```dockerfile
# Base Image
FROM python:3.8-slim

# Working Directory
WORKDIR /app

# Copy Application File
COPY app.py .

# Install Dependencies
RUN pip install flask

# Expose Application Port
EXPOSE 5000

# Run Application
CMD ["python", "app.py"]
```

---

## Build Docker Image

```bash
docker build -t python-flask-app .
```

---

## Run Docker Container

```bash
docker run -p 5000:5000 python-flask-app
```

---

## Access Application

Open browser:

```text
http://localhost:5000
```

Expected Output:

```text
Hello, Docker!
```

---

# Step 5: Push Image to Docker Hub

## Login

```bash
docker login
```

Enter Docker Hub username and password.

---

## Tag Image

```bash
docker tag python-flask-app <your_dockerhub_username>/python-flask-app
```

Example:

```bash
docker tag python-flask-app pranab/python-flask-app
```

---

## Push Image

```bash
docker push <your_dockerhub_username>/python-flask-app
```

Example:

```bash
docker push pranab/python-flask-app
```

---

## Pull Image from Docker Hub

```bash
docker pull <your_dockerhub_username>/python-flask-app
```

---

# Complete Command Sequence

```bash
docker --version
docker info

docker pull ubuntu
docker run -it ubuntu

docker pull alpine
docker run -it alpine

docker build -t python-flask-app .

docker run -p 5000:5000 python-flask-app

docker login

docker tag python-flask-app <your_dockerhub_username>/python-flask-app

docker push <your_dockerhub_username>/python-flask-app

docker pull <your_dockerhub_username>/python-flask-app
```

---

# Result

Docker was successfully installed, Docker commands were explored, containers were created using different operating system images, a Flask application was containerized and deployed using Docker, and the image was pushed to Docker Hub.
