# Dynamic Docker Build + Run Script (Jenkins Freestyle)

```
set -e

echo "📥 Cleaning workspace..."
rm -rf devops

echo "📥 Cloning repository..."
git clone https://github.com/nivas-22/devops.git
cd devops

# -----------------------------
# Dynamic variables
# -----------------------------
TAG=$(date +%s)
IMAGE="cloud:$TAG"
CONTAINER="cloudyfood-$TAG"
PORT=8082

echo "🐳 Building Docker image: $IMAGE"
docker build -t $IMAGE .

echo "🧹 Cleaning old containers (same app only)..."
docker ps -a | grep cloudyfood || true

# Stop and remove ALL old containers for this app
docker ps -aq --filter "name=cloudyfood" | xargs -r docker stop || true
docker ps -aq --filter "name=cloudyfood" | xargs -r docker rm || true

echo "🚀 Running new container: $CONTAINER"
docker run -d \
  --name $CONTAINER \
  -p $PORT:80 \
  $IMAGE

echo "✅ Deployment successful!"
echo "--------------------------------"
echo "Image: $IMAGE"
echo "Container: $CONTAINER"
echo "Port: $PORT"

docker ps

```
