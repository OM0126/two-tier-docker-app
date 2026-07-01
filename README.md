# Flask MySQL Two-Tier Application using Docker

A simple two-tier Flask application containerized using Docker with a MySQL database and deployed on an AWS EC2 Ubuntu instance.

---

# Project Overview

This project demonstrates how to:

* Launch an AWS EC2 Ubuntu instance
* Connect to the EC2 instance using SSH
* Install Docker on Ubuntu
* Clone the project from GitHub
* Build a Docker image for the Flask application
* Create a custom Docker bridge network
* Deploy a MySQL database container
* Deploy a Flask backend container
* Connect multiple containers using Docker networking
* Configure environment variables
* Configure the EC2 Security Group
* Access the application through a web browser
* Push the project to GitHub

---

# Tech Stack

* Python
* Flask
* MySQL
* Docker
* Ubuntu 22.04
* AWS EC2
* Git
* GitHub

---

# Project Structure

```text
two-tier-flask-app/
├── app.py
├── Dockerfile
├── requirements.txt
├── README.md
└── templates/
    └── index.html
```

---

# Step 1: Launch an EC2 Instance

* Launch an Ubuntu EC2 instance.
* Select the **t2.micro** instance type.
* Create or select an existing Key Pair.
* Allow **SSH (Port 22)** in the Security Group.
* Launch the instance.

---

# Step 2: Connect to the EC2 Instance

```bash
chmod 400 my-key.pem
```

```bash
ssh -i my-key.pem ubuntu@<EC2-PUBLIC-IP>
```

---

# Step 3: Update Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

---

# Step 4: Install Docker

```bash
sudo apt install docker.io -y
```

Start Docker:

```bash
sudo systemctl start docker
```

Enable Docker:

```bash
sudo systemctl enable docker
```

Verify Docker installation:

```bash
docker --version
```

Check Docker status:

```bash
sudo systemctl status docker
```

---

# Step 5: Clone the Repository

```bash
git clone https://github.com/<username>/<repository-name>.git
```

```bash
cd two-tier-flask-app
```

---

# Step 6: Dockerfile

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

# Step 7: Build the Docker Image

```bash
sudo docker build -t flask-app .
```

Verify the image:

```bash
sudo docker images
```

---

# Step 8: Create a Docker Network

```bash
sudo docker network create flask-app
```

Verify the network:

```bash
sudo docker network ls
```

---

# Step 9: Run the MySQL Container

```bash
sudo docker run -d \
  --name mysql \
  --network flask-app \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=devops \
  mysql:latest
```

Verify:

```bash
sudo docker ps
```

Check MySQL logs:

```bash
sudo docker logs -f mysql
```

Wait until you see:

```text
MySQL Server: ready for connections
```

---

# Step 10: Run the Flask Backend Container

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

Verify:

```bash
sudo docker ps
```

View logs:

```bash
sudo docker logs flask-backend
```

---

# Step 11: Configure the EC2 Security Group

Add the following inbound rule.

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| Custom TCP | TCP | 5000 | Anywhere (0.0.0.0/0) |

Save the changes.

---

# Step 12: Access the Application

Open the application using:

```text
http://<EC2-PUBLIC-IP>:5000
```

---

# Useful Docker Commands

### Build Image

```bash
sudo docker build -t flask-app .
```

### Create Docker Network

```bash
sudo docker network create flask-app
```

### List Docker Networks

```bash
sudo docker network ls
```

### Inspect Docker Network

```bash
sudo docker network inspect flask-app
```

### Run MySQL Container

```bash
sudo docker run -d --name mysql --network flask-app mysql
```

### Run Flask Container

```bash
sudo docker run -d --name flask-backend -p 5000:5000 --network flask-app flask-app
```

### View Running Containers

```bash
sudo docker ps
```

### View All Containers

```bash
sudo docker ps -a
```

### View Logs

```bash
sudo docker logs flask-backend
```

### Monitor Logs

```bash
sudo docker logs -f flask-backend
```

### Stop Container

```bash
sudo docker stop <container-id>
```

### Remove Container

```bash
sudo docker rm <container-id>
```

### Remove Network

```bash
sudo docker network rm flask-app
```

---

# Git Commands

### Initialize Repository

```bash
git init
```

### Check Status

```bash
git status
```

### Add Files

```bash
git add .
```

### Commit Changes

```bash
git commit -m "Initial commit"
```

### Rename Branch

```bash
git branch -M main
```

### Add Remote Repository

```bash
git remote add origin https://github.com/<username>/<repository-name>.git
```

### Check Remote

```bash
git remote -v
```

### Update Existing Remote

```bash
git remote set-url origin https://github.com/<username>/<repository-name>.git
```

### Push to GitHub

```bash
git push -u origin main
```

### Push Future Changes

```bash
git push
```

---

# Troubleshooting

## Dockerfile Not Found

Run the build command from the project root.

```bash
cd two-tier-flask-app
sudo docker build -t flask-app .
```

---

## MySQL Container Already Exists

Remove the existing container.

```bash
sudo docker rm -f mysql
```

---

## Flask Container Already Exists

Remove the existing container.

```bash
sudo docker rm -f flask-backend
```

---

## MySQL Not Ready

Wait until MySQL finishes initialization.

```bash
sudo docker logs -f mysql
```

Wait for:

```text
MySQL Server: ready for connections
```

---

## Flask Could Not Connect to MySQL

Verify both containers are connected to the same Docker network.

```bash
sudo docker network inspect flask-app
```

Verify the environment variables:

```text
MYSQL_HOST=mysql
MYSQL_USER=root
MYSQL_PASSWORD=root
MYSQL_DB=devops
```

---

## Application Not Accessible

Check the following:

* Docker containers are running.
* Flask is listening on port **5000**.
* Docker port mapping is correct.
* EC2 Security Group allows **TCP Port 5000**.
* The correct EC2 Public IP is being used.

---

## View Application Logs

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

* Building Docker images for Python applications.
* Deploying multi-container applications.
* Docker bridge networking.
* Communication between Flask and MySQL containers.
* Environment variable configuration.
* Running a two-tier architecture.
* Monitoring Docker containers.
* Configuring AWS EC2 Security Groups.
* Deploying Dockerized applications on AWS EC2.
* Managing Git and GitHub repositories.

---

# Author

**OM**
