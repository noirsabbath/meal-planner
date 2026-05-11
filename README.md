# Protein-Rich Eggetarian Meal Planner

Static single-page meal planner. Served via nginx on EC2, deployed automatically on every push to `main`.

## Stack

- **App:** Static HTML/JS (no server-side code)
- **Web server:** nginx:alpine (Docker)
- **CI/CD:** GitHub Actions → SSH → `docker-compose up`

---

## EC2 First-Time Setup

Run these once on a fresh EC2 instance (Ubuntu 22.04 LTS recommended):

```bash
# 1. Install Docker
sudo apt-get update
sudo apt-get install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker

# 2. Add your user to the docker group (optional, avoids needing sudo)
sudo usermod -aG docker $USER

# 3. Clone the repo
git clone https://github.com/YOUR_USERNAME/meal-planner.git ~/meal-planner

# 4. Start the app
cd ~/meal-planner
sudo docker-compose up -d

# 5. Verify it's running
sudo docker-compose ps
curl http://localhost
```

Make sure **port 80** is open in your EC2 security group (inbound rule: HTTP, 0.0.0.0/0).

---

## GitHub Secrets Required

Go to your repo → Settings → Secrets and variables → Actions → New repository secret:

| Secret | Value |
|---|---|
| `EC2_HOST` | Your EC2 public IP or domain |
| `EC2_USER` | SSH username (usually `ubuntu`) |
| `EC2_KEY` | Contents of your `.pem` private key file |

---

## Deployment Flow

```
git push origin main
       ↓
GitHub Actions triggered
       ↓
SSH into EC2
       ↓
git pull origin main
       ↓
docker-compose up -d --build
       ↓
nginx serves index.html on port 80
```

Every push to `main` deploys automatically. No manual steps after the first setup.
