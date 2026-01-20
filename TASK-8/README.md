# DevOps Task 8 — Docker Containerization Task

## Objective
Students will learn how to containerize an application using Docker, manage Docker images and containers, and understand basic DevOps workflows.

## ✅ Requirements Covered

### Part 1: Environment Setup
- Install Docker on your system
- Verify Docker is working

### Part 2: Create a Simple Application
Chosen option: **Python Flask App**

The application:
- Runs locally
- Shows the message: **Hello from Docker – DevOps Task**

### Part 3: Dockerfile Creation
Dockerfile includes:
- Base image
- Working directory
- Copy application files
- Install dependencies
- Expose port
- CMD to run the app

### Part 4: Build & Run Docker Image
- Build image using `docker build`
- Run container using `docker run`
- Access application in browser

### Part 5: Docker Commands Practice
- Practice common Docker commands

---

# Part 1: Environment Setup (Install Docker)

## Install Docker

Verify installation:
```bash
docker --version
docker ps
```

### Linux
```bash
sudo yum update
sudo yum install -y
sudo systemctl start docker
sudo systemctl enable docker
docker --version
```
---

# Part 2: Create a Simple Application (Python Flask)

## Step 1: Create a project folder
```bash
mkdir devops-docker-task
cd devops-docker-task
```

## Step 2: Create the required files
Your folder must contain:
```
devops-docker-task/
│── app.py
│── requirements.txt
│── Dockerfile
│── README.md
```

---

## app.py
Create `app.py` and paste this code:
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Docker – DevOps Task"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
```

---

## requirements.txt
Create `requirements.txt` and add:
```txt
flask
```

---

# Part 3: Dockerfile Creation

Create a file named `Dockerfile` (no extension) and paste:

```dockerfile
# Base image
FROM python:3.11-slim

# Working directory
WORKDIR /app

# Copy application files
COPY . .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose required port
EXPOSE 8080

# Run the application
CMD ["python", "app.py"]
```

---

# Part 4: Build & Run Docker Image

## Step 1: Build the Docker image
Run this inside the project folder:
```bash
docker build -t devops-docker-task .
```

## Step 2: Run the container
```bash
docker run -d -p 8080:8080 devops-docker-task
```

## Step 3: Check container status
```bash
docker ps
```

## Step 4: Open in browser
Go to:
```
http://localhost:8080
```

Expected output:
```
Hello from Docker – DevOps Task
```

---

# Part 5: Docker Commands Practice

## View Docker images
```bash
docker images
```

## View running containers
```bash
docker ps
```

## View all containers (including stopped)
```bash
docker ps -a
```

## View container logs
```bash
docker logs <container_id>
```

## Stop a running container
```bash
docker stop <container_id>
```

## Remove a container
```bash
docker rm <container_id>
```

## Remove an image
```bash
docker rmi devops-docker-task
```

---

# ✅ Quick Test Commands (Optional)

## Run app locally without Docker (for testing)
Install dependencies:
```bash
pip install -r requirements.txt
```

Run the app:
```bash
python app.py
```

Open:
```
http://localhost:8080
```


