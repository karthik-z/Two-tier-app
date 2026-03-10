1. Launch EC2 Instance
      AMI: Ubuntu 22.04 LTS
      Instance Type: t2.medium (recommended for Jenkins)
      Security Group — Open these ports: Port Purpose (22 SSH) (8080 Jenkins) (5000 Flask App) (3306 MySQL)
2. Install Java + Jenkins
3. Install Docker & Docker Compose
4. Jenkins Configuration
      Click New Item → Pipeline
      Name it: two-tier-flask-app
      Under Pipeline, select Pipeline script from SCM
      SCM: Git → Enter your GitHub repo URL
      Branch: main
      Script Path: Jenkinsfile
      Save
5. GitHub Webhook (Auto Trigger)
      In your GitHub repo → Settings → Webhooks → Add webhook
      Payload URL: http://<EC2-IP>:8080/github-webhook/
      Content type: application/json
      Trigger: Just the push event
      In Jenkins job → Build Triggers → ✅ Check GitHub hook trigger for GITScm polling
6. Test the Pipeline

What you've successfully built:
      AWS EC2 Instance
      Jenkins Pipeline
      GitHub Webhook
      Docker (Flask image)
      Docker Compose (Flask + MySQL)
      Two-tier app (frontend + DB)
