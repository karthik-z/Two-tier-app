# 🚀 DevOps Project — Two-Tier Flask App CI/CD Pipeline

![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=flat&logo=jenkins&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazonaws&logoColor=white)

> A fully automated CI/CD pipeline to deploy a two-tier Flask + MySQL application on AWS EC2 using Jenkins, Docker, and Docker Compose — with GitHub Webhooks for hands-free deployments.

---

## 📌 Project Overview

This project demonstrates a complete **DevOps CI/CD pipeline** where every `git push` automatically triggers a Jenkins build that deploys the latest version of the application on AWS EC2 — with zero manual intervention.

### 🏗️ Architecture

```
Developer (git push)
        │
        ▼
   GitHub Repo  ──── Webhook ────▶  Jenkins (EC2 :8080)
                                          │
                                    Docker Build
                                          │
                                   Docker Compose
                                    ┌────┴────┐
                                    │         │
                               Flask App   MySQL DB
                               (:5000)     (:3306)
                                    │
                                    ▼
                             Browser (User)
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| **Application** | Python 3.9, Flask |
| **Database** | MySQL 5.7 |
| **Containerization** | Docker, Docker Compose |
| **CI/CD** | Jenkins (Pipeline via Jenkinsfile) |
| **Cloud** | AWS EC2 (Ubuntu 22.04) |
| **Version Control** | GitHub + Webhooks |

---

## 📁 Project Structure

```
DevOps-Project-Two-Tier-Flask-App/
├── app.py                 # Flask application & MySQL connection
├── requirements.txt       # Python dependencies
├── Dockerfile             # Flask image build instructions
├── docker-compose.yml     # Multi-container orchestration
├── Jenkinsfile            # 5-stage CI/CD pipeline
└── templates/
    └── index.html         # Jinja2 frontend template
```

---

## ⚙️ Jenkins Pipeline Stages

```
┌─────────────────────────────────────────────────────────┐
│  Stage 1: Clone Repository  →  git pull from GitHub     │
│  Stage 2: Build Docker Image →  docker build            │
│  Stage 3: Stop Old Containers → docker-compose down     │
│  Stage 4: Deploy with Compose → docker-compose up -d    │
│  Stage 5: Verify Deployment  →  docker ps               │
└─────────────────────────────────────────────────────────┘
```

---

## 🚀 Getting Started

### Prerequisites

- AWS Account
- GitHub Account
- Basic Linux knowledge

---

### Step 1 — Launch AWS EC2 Instance

- **AMI:** Ubuntu 22.04 LTS
- **Instance Type:** t2.medium
- **Security Group — Inbound Rules:**

| Port | Protocol | Purpose |
|------|----------|---------|
| 22 | TCP | SSH access |
| 8080 | TCP | Jenkins UI |
| 5000 | TCP | Flask App |
| 3306 | TCP | MySQL (optional) |

---

### Step 2 — Install Dependencies on EC2

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Java (Jenkins requirement)
sudo apt install openjdk-17-jdk -y

# Install Jenkins
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update && sudo apt install jenkins -y
sudo systemctl start jenkins && sudo systemctl enable jenkins

# Install Docker
sudo apt install docker.io -y
sudo systemctl start docker && sudo systemctl enable docker
sudo usermod -aG docker jenkins
sudo usermod -aG docker ubuntu
sudo systemctl restart jenkins

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
  -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

---

### Step 3 — Configure Jenkins

1. Access Jenkins at `http://<EC2-PUBLIC-IP>:8080`
2. Get initial password:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
3. Install **suggested plugins**
4. Create a new **Pipeline** job
5. Under **Pipeline → Definition**, select `Pipeline script from SCM`
6. Set SCM to **Git** and enter your repository URL
7. Branch: `main` | Script Path: `Jenkinsfile`
8. Under **Build Triggers**, check ✅ `GitHub hook trigger for GITScm polling`

---

### Step 4 — Configure GitHub Webhook

1. Go to your GitHub repo → **Settings → Webhooks → Add webhook**
2. **Payload URL:** `http://<EC2-PUBLIC-IP>:8080/github-webhook/`
3. **Content type:** `application/json`
4. **Events:** Just the push event
5. Click **Add webhook**

---

### Step 5 — Deploy

```bash
# Push any change to GitHub — Jenkins auto-triggers!
git add .
git commit -m "deploy: initial release"
git push origin main
```

✅ App will be live at: `http://<EC2-PUBLIC-IP>:5000`

---

## 🐳 Docker Commands

```bash
# Navigate to project workspace
cd /var/lib/jenkins/workspace/two-tier-flask-app

# Check running containers
docker ps

# View Flask logs
docker logs two-tier-backend

# View MySQL logs
docker logs mysql

# Access MySQL shell
docker exec -it mysql mysql -uroot -proot

# Rebuild and restart manually
docker-compose down
docker-compose up -d --build
```

---

## 🔐 Environment Variables

Configured via `docker-compose.yml`:

| Variable | Value | Description |
|----------|-------|-------------|
| `MYSQL_HOST` | mysql | MySQL container hostname |
| `MYSQL_USER` | root | Database user |
| `MYSQL_PASSWORD` | root | Database password |
| `MYSQL_DB` | devops | Database name |

> ⚠️ For production, use `.env` files or Jenkins credentials — never hardcode secrets.

---

## 🌐 Port Reference

| Port | Service | Access |
|------|---------|--------|
| `22` | SSH | EC2 terminal access |
| `8080` | Jenkins | CI/CD dashboard |
| `5000` | Flask App | Web application |
| `3306` | MySQL | Internal Docker network |

---

## 📸 Screenshots

> App running at `http://<EC2-IP>:5000`

<img width="739" height="147" alt="App-screenshot" src="https://github.com/user-attachments/assets/a2798100-4dea-4908-9be0-df1f3a8a391a" />


> Jenkins Pipeline - All stages passing

<img width="353" height="360" alt="Jenkins-pipeline-stages" src="https://github.com/user-attachments/assets/c755e058-07cf-4ee7-bcb7-95875f7546d3" />


---

## 📈 CI/CD Flow Summary

```
1. Developer pushes code to GitHub (main branch)
2. GitHub Webhook notifies Jenkins
3. Jenkins clones the latest code
4. Docker builds a fresh Flask image
5. Docker Compose tears down old containers
6. Docker Compose starts Flask + MySQL containers
7. App is live and accessible on port 5000
```

---

## 🔮 Future Improvements

- [ ] Push Docker image to DockerHub registry
- [ ] Add Nginx as reverse proxy
- [ ] Configure SSL/HTTPS with Let's Encrypt
- [ ] Add Slack/Email notifications in Jenkins
- [ ] Use AWS RDS instead of containerized MySQL
- [ ] Add health check endpoint to Flask app
- [ ] Implement `.env` file for secret management

---

## 👨‍💻 Author

**Your Name**
- GitHub: [@YOUR_USERNAME](https://github.com/karthik-z)
- LinkedIn: [Your LinkedIn](https://linkedin.com/in/karthik03)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

⭐ **If you found this project helpful, please give it a star!**
