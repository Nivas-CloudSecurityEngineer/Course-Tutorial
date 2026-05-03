# 🐳 Docker + Jenkins Integration Script (Only Docker part)

```copy

#!/bin/bash

set -e

echo "🔄 Updating system..."
sudo apt update -y

echo "📦 Installing prerequisites..."
sudo apt install -y ca-certificates curl gnupg lsb-release

echo "🐳 Setting up Docker repository..."

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y

echo "🐳 Installing Docker Engine..."
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

echo "🚀 Enabling Docker service..."
sudo systemctl enable docker
sudo systemctl start docker

echo "👤 Adding users to Docker group..."

# Allow current user
sudo usermod -aG docker ubuntu || true

# Allow Jenkins to use Docker (IMPORTANT for CI/CD)
sudo usermod -aG docker jenkins || true

echo "🔁 Restarting services..."
sudo systemctl restart docker
sudo systemctl restart jenkins || true

echo "✅ Docker installation complete!"
echo "----------------------------"
echo "Docker version:"
docker --version
```
