# Flask MySQL Two-Tier Application using Docker on AWS EC2

A beginner-friendly **Two-Tier Flask Application** containerized using **Docker** with a **MySQL 8.0** database and deployed on an **AWS EC2 Ubuntu Instance**.

This project demonstrates how to build, containerize, and deploy a Flask application while connecting it to a MySQL database using Docker Bridge Networking.

---

# Features

- Dockerized Flask Application
- MySQL 8.0 Database
- Custom Docker Bridge Network
- Environment Variable Configuration
- AWS EC2 Deployment
- Docker Image Creation
- Container Networking
- Beginner-Friendly Project
- Git & GitHub Integration

---

# Architecture

```
        +-----------------------+
        |      Web Browser      |
        +----------+------------+
                   |
                   |
          EC2 Public IP :5000
                   |
        +----------v------------+
        |   Flask Container     |
        |     flask-backend     |
        +----------+------------+
                   |
        Docker Bridge Network
                   |
        +----------v------------+
        |    MySQL Container    |
        |        mysql          |
        +-----------------------+
```

---

# Tech Stack

- Python
- Flask
- MySQL 8.0
- Docker
- Ubuntu Linux
- AWS EC2
- Git
- GitHub

---

# Project Structure

```text
two-tier-docker-app/
│
├── app.py
├── Dockerfile
├── requirements.txt
├── README.md
│
└── templates/
      └── index.html
```

---

# Step 1 — Launch an EC2 Instance

Launch an Ubuntu EC2 instance from AWS.

Recommended Configuration

- Ubuntu Server
- t3.micro
- Allow SSH (22)
- Create a Key Pair
- Launch Instance

---

# Step 2 — Connect to EC2

```bash
chmod 400 my-key.pem
```

```bash
ssh -i my-key.pem ubuntu@<EC2-PUBLIC-IP>
```

---

# Step 3 — Update Ubuntu

```bash
sudo apt update
```

```bash
sudo apt upgrade -y
```

---

# Step 4 — Install Docker

Install Docker

```bash
sudo apt install docker.io -y
```

Start Docker

```bash
sudo systemctl start docker
```

Enable Docker

```bash
sudo systemctl enable docker
```

Check Version

```bash
docker --version
```

Verify Status

```bash
sudo systemctl status docker
```

---

# Step 5 — Clone the Repository

```bash
git clone https://github.com/OM0126/two-tier-docker-app.git
```

```bash
cd two-tier-docker-app
```

---

# Step 6 — Dockerfile

```dockerfile
FROM python:3.9-slim

WORKDIR /app

RUN apt-get update \
    && apt-get install -y gcc default-libmysqlclient-dev pkg-config

COPY requirements.txt .

RUN pip install mysqlclient

RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python","app.py"]
```

---

# Step 7 — Build Docker Image

```bash
sudo docker build -t flask-app .
```

Verify

```bash
sudo docker images
```

Expected

```
REPOSITORY      TAG
flask-app       latest
```

---

# Step 8 — Create Docker Network

```bash
sudo docker network create flask-app
```

Verify

```bash
sudo docker network ls
```

Expected

```
bridge
host
none
flask-app
```

---

# Step 9 — Run MySQL Container

> **Important**
>
> This project uses **MySQL 8.0**.
>
> Avoid using `mysql:latest` because newer MySQL versions may cause compatibility issues.

Run MySQL

```bash
sudo docker run -d \
  --name mysql \
  --network flask-app \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=devops \
  mysql:8.0
```

Verify

```bash
sudo docker ps
```

View Logs

```bash
sudo docker logs -f mysql
```

Wait until you see

```
mysqld: ready for connections.
```
---

# Step 10 — Run the Flask Backend Container

Run the Flask container and connect it to the MySQL container using the custom Docker network.

```bash
sudo docker run -d \
  --name flask-backend \
  --network flask-app \
  -p 5000:5000 \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=root \
  -e MYSQL_DB=devops \
  flask-app
```

Verify

```bash
sudo docker ps
```

Expected Output

```
CONTAINER ID   IMAGE         STATUS
xxxxxxxxxx     flask-app     Up
xxxxxxxxxx     mysql:8.0     Up
```

View Flask Logs

```bash
sudo docker logs flask-backend
```

---

# Step 11 — Configure AWS Security Group

Add the following inbound rule.

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| SSH | TCP | 22 | Your IP |
| Custom TCP | TCP | 5000 | Anywhere (0.0.0.0/0) |

Save the Security Group.

---

# Step 12 — Access the Application

Open your browser and visit

```
http://<EC2-PUBLIC-IP>:5000
```

Example

```
http://54.xxx.xxx.xxx:5000
```

If everything is configured correctly, your Flask application will open in the browser.

---

# Useful Docker Commands

## Build Image

```bash
sudo docker build -t flask-app .
```

---

## List Images

```bash
sudo docker images
```

---

## Create Network

```bash
sudo docker network create flask-app
```

---

## List Networks

```bash
sudo docker network ls
```

---

## Inspect Network

```bash
sudo docker network inspect flask-app
```

---

## Run MySQL Container

```bash
sudo docker run -d \
  --name mysql \
  --network flask-app \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=devops \
  mysql:8.0
```

---

## Run Flask Container

```bash
sudo docker run -d \
  --name flask-backend \
  --network flask-app \
  -p 5000:5000 \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=root \
  -e MYSQL_DB=devops \
  flask-app
```

---

## View Running Containers

```bash
sudo docker ps
```

---

## View All Containers

```bash
sudo docker ps -a
```

---

## View Docker Images

```bash
sudo docker images
```

---

## View Container Logs

```bash
sudo docker logs flask-backend
```

---

## Monitor Logs

```bash
sudo docker logs -f flask-backend
```

---

## Stop a Container

```bash
sudo docker stop <container-name>
```

Example

```bash
sudo docker stop mysql
```

---

## Start a Container

```bash
sudo docker start <container-name>
```

Example

```bash
sudo docker start mysql
```

---

## Restart a Container

```bash
sudo docker restart <container-name>
```

---

## Remove a Container

```bash
sudo docker rm -f <container-name>
```

---

## Remove Docker Image

```bash
sudo docker rmi <image-name>
```

---

## Remove Docker Network

```bash
sudo docker network rm flask-app
```

---

# Docker Cleanup

## Stop All Containers

```bash
sudo docker stop $(sudo docker ps -aq)
```

---

## Remove All Containers

```bash
sudo docker rm -f $(sudo docker ps -aq)
```

---

## Remove All Images

```bash
sudo docker rmi -f $(sudo docker images -q)
```

---

## Remove All Unused Volumes

```bash
sudo docker volume prune -f
```

---

## Remove Custom Network

```bash
sudo docker network rm flask-app
```

---

## Remove Everything

```bash
sudo docker system prune -a --volumes -f
```

---

# Git Commands

## Initialize Repository

```bash
git init
```

---

## Check Status

```bash
git status
```

---

## Add Files

```bash
git add .
```

---

## Commit Changes

```bash
git commit -m "Initial Commit"
```

---

## Rename Branch

```bash
git branch -M main
```

---

## Add Remote Repository

```bash
git remote add origin https://github.com/OM0126/two-tier-docker-app.git
```

---

## Check Remote

```bash
git remote -v
```

---

## Push Code

```bash
git push -u origin main
```

---

## Push Future Changes

```bash
git push
```---

# Troubleshooting

## Dockerfile Not Found

Make sure you are inside the project directory before building the Docker image.

```bash
cd two-tier-docker-app
```

```bash
sudo docker build -t flask-app .
```

---

## MySQL Container Already Exists

If a MySQL container with the same name already exists, remove it first.

```bash
sudo docker rm -f mysql
```

Then recreate the container.

---

## Flask Container Already Exists

If the Flask container already exists, remove it before creating a new one.

```bash
sudo docker rm -f flask-backend
```

---

## MySQL Container Exits Immediately

If the MySQL container exits with status **Exited (1)**, check the logs.

```bash
sudo docker logs mysql
```

Make sure you are using:

```text
mysql:8.0
```

instead of

```text
mysql:latest
```

Then recreate the container.

```bash
sudo docker rm -f mysql

sudo docker run -d \
  --name mysql \
  --network flask-app \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=devops \
  mysql:8.0
```

---

## MySQL Not Ready

Wait until MySQL finishes initialization.

```bash
sudo docker logs -f mysql
```

Continue only after you see

```text
mysqld: ready for connections.
```

---

## Flask Could Not Connect to MySQL

Verify that both containers are connected to the same Docker network.

```bash
sudo docker network inspect flask-app
```

Verify the environment variables.

```text
MYSQL_HOST=mysql
MYSQL_USER=root
MYSQL_PASSWORD=root
MYSQL_DB=devops
```

Check whether MySQL is running.

```bash
sudo docker ps
```

---

## Application Not Accessible

Verify the following:

- Docker containers are running.
- Flask is listening on port **5000**.
- Docker port mapping is correct.
- AWS Security Group allows TCP Port **5000**.
- The correct EC2 Public IP is being used.

Verify running containers.

```bash
sudo docker ps
```

---

## View Flask Logs

```bash
sudo docker logs -f flask-backend
```

---

## View MySQL Logs

```bash
sudo docker logs -f mysql
```

---

# Learning Outcomes

After completing this project, you will understand:

- Building Docker images for Python applications.
- Deploying multi-container applications.
- Docker Bridge Networking.
- Communication between Flask and MySQL containers.
- Environment Variable Configuration.
- AWS EC2 Deployment.
- Docker Container Management.
- Docker Image Management.
- Git and GitHub Workflow.
- Basic DevOps Deployment Process.

---

# Future Improvements

This project can be enhanced by adding:

- Docker Compose
- Persistent Docker Volumes
- Nginx Reverse Proxy
- HTTPS using Let's Encrypt
- GitHub Actions CI/CD Pipeline
- Kubernetes Deployment
- AWS ECS Deployment
- Terraform Infrastructure as Code
- Monitoring with Prometheus & Grafana

---

# Repository

GitHub Repository

```text
https://github.com/OM0126/two-tier-docker-app
```

Clone Repository

```bash
git clone https://github.com/OM0126/two-tier-docker-app.git
```

---

# Author

**Om Yengantiwar**
