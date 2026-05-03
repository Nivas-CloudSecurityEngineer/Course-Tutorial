
# Full Script: Docker + Jenkins (Ubuntu 24.04)

```copy
#!/bin/bash

set -e

echo "🔄 Updating system..."
sudo apt update -y
sudo apt upgrade -y

# -----------------------------
# Install basic dependencies
# -----------------------------
echo "📦 Installing prerequisites..."
sudo apt install -y ca-certificates curl gnupg lsb-release

# -----------------------------
# Install Docker
# -----------------------------
echo "🐳 Installing Docker..."

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y

sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Start Docker
sudo systemctl enable docker
sudo systemctl start docker

# Add ubuntu user to docker group
sudo usermod -aG docker ubuntu || true

echo "🐳 Docker version:"
docker --version

# -----------------------------
# Install Java (required for Jenkins)
# -----------------------------
echo "☕ Installing Java..."
sudo apt install -y openjdk-21-jre

# -----------------------------
# Install Jenkins
# -----------------------------
echo "🔑 Installing Jenkins..."

sudo mkdir -p /etc/apt/keyrings

sudo wget -q -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update -y
sudo apt install -y jenkins

sudo systemctl enable jenkins
sudo systemctl start jenkins

# -----------------------------
# IMPORTANT: Allow Jenkins to use Docker
# -----------------------------
echo "🔧 Configuring Jenkins Docker access..."

sudo usermod -aG docker jenkins

sudo systemctl restart docker
sudo systemctl restart jenkins

# -----------------------------
# Output info
# -----------------------------
echo "✅ INSTALL COMPLETE!"
echo "---------------------------"
echo "Docker version:"
docker --version

echo "Jenkins status:"
sudo systemctl status jenkins --no-pager

echo "Jenkins admin password:"
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

```
