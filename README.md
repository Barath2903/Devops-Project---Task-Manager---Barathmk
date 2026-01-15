# Task Manager Microservices

A microservice-based task management application with complete DevOps pipeline.

## Architecture

The application consists of three microservices:

1. **Task Service** (Port 8081) - Manages tasks CRUD operations
2. **User Service** (Port 8082) - Manages user information
3. **API Gateway** (Port 8080) - Routes requests to appropriate microservices

## Tech Stack

- **Java 17** with Spring Boot 3.2.0
- **PostgreSQL** for data persistence
- **Spring Cloud Gateway** for API routing
- **Maven** for build management
- **Docker** for containerization
- **Kubernetes/Minikube** for orchestration
- **JUnit 5** and **Testcontainers** for testing
- **Checkstyle** and **SonarQube** for code quality

## Prerequisites

- JDK 17
- Maven 3.8+
- Docker and Docker Compose
- Kubernetes/Minikube (for deployment)
- PostgreSQL (or use Docker Compose)

## Local Development

### Using Docker Compose

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Restart services
docker-compose restart
```

### Manual Setup

1. Start PostgreSQL databases:
```bash
docker run -d --name postgres-task -e POSTGRES_DB=taskdb -e POSTGRES_USER=taskuser -e POSTGRES_PASSWORD=taskpass -p 5432:5432 postgres:15-alpine
docker run -d --name postgres-user -e POSTGRES_DB=userdb -e POSTGRES_USER=useruser -e POSTGRES_PASSWORD=userpass -p 5433:5432 postgres:15-alpine
```

2. Build the project:
```bash
mvn clean install
```

3. Run services:
```bash
# Terminal 1 - Task Service
cd task-service && mvn spring-boot:run

# Terminal 2 - User Service
cd user-service && mvn spring-boot:run

# Terminal 3 - API Gateway
cd api-gateway && mvn spring-boot:run
```

## Testing

### Unit Tests
```bash
mvn test
```

### Integration Tests
```bash
mvn verify
```

### Code Quality Checks
```bash
# Checkstyle
mvn checkstyle:check

# SonarQube (requires SonarQube server)
mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.login=YOUR_TOKEN
```

To run Future analysis
```
cd /Users/barath/Task\ Manager\ Devops
mvn clean package -DskipTests
mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.token=squ_2fccecf06a02c7811d561cdc31a55235b17c02ac
```

## API Endpoints

### Task Service
- `GET /api/tasks` - Get all tasks (optional ?userId=1)
- `GET /api/tasks/{id}` - Get task by ID
- `POST /api/tasks` - Create task
- `PUT /api/tasks/{id}` - Update task
- `DELETE /api/tasks/{id}` - Delete task

### User Service
- `GET /api/users` - Get all users
- `GET /api/users/{id}` - Get user by ID
- `GET /api/users/username/{username}` - Get user by username
- `POST /api/users` - Create user
- `PUT /api/users/{id}` - Update user
- `DELETE /api/users/{id}` - Delete user

### API Gateway
All requests should go through the gateway at `http://localhost:8080`

## Docker

### Build Images
```bash
docker build -t task-service:latest -f task-service/Dockerfile .
docker build -t user-service:latest -f user-service/Dockerfile .
docker build -t api-gateway:latest -f api-gateway/Dockerfile .
```

### Push to Registry
```bash
docker tag task-service:latest <registry>/task-service:latest
docker push <registry>/task-service:latest
```

## Kubernetes Deployment

### Prerequisites
- Minikube installed and running
- kubectl configured

### Deploy

1. Create secrets:
```bash
kubectl apply -f k8s/secrets.yaml
```

2. Deploy databases:
```bash
kubectl apply -f k8s/postgres-task-deployment.yaml
kubectl apply -f k8s/postgres-user-deployment.yaml
```

3. Deploy services (update image tags in YAML files):
```bash
export DOCKER_REGISTRY=your-registry
export VERSION=latest
envsubst < k8s/task-service-deployment.yaml | kubectl apply -f -
envsubst < k8s/user-service-deployment.yaml | kubectl apply -f -
envsubst < k8s/api-gateway-deployment.yaml | kubectl apply -f -
```

4. Check status:
```bash
kubectl get pods
kubectl get services
```

## CI/CD Pipeline

The project includes a GitHub Actions workflow (`.github/workflows/ci-cd-pipeline.yml`) that:

1. **Build & Test**
   - Code compilation with Maven
   - Unit tests with JUnit
   - Integration tests with Testcontainers
   - Static analysis with Checkstyle
   - SonarQube code quality scan

2. **Docker Build & Push**
   - Builds Docker images for each service
   - Pushes to Docker registry
   - Pushes to Nexus/Artifactory (if configured)

3. **Kubernetes Deployment**
   - Deploys to Minikube/Kubernetes
   - Applies all manifests
   - Waits for deployment readiness

4. **Release Management**
   - Automated versioning with Git tags
   - Generates release notes from commits
   - Updates GitHub releases

### Required Secrets

Configure these secrets in GitHub:

- `DOCKER_REGISTRY` - Docker registry URL
- `DOCKER_USERNAME` - Docker registry username
- `DOCKER_PASSWORD` - Docker registry password
- `NEXUS_URL` - Nexus/Artifactory URL
- `NEXUS_USERNAME` - Nexus username
- `NEXUS_PASSWORD` - Nexus password
- `SONAR_TOKEN` - SonarQube token
- `SONAR_HOST_URL` - SonarQube server URL

## Artifact Management

### Nexus/Artifactory Integration

JARs and Docker images are automatically pushed to Nexus/Artifactory during the CI/CD pipeline.

Configure in `.mvn/settings.xml` or via environment variables:
- `NEXUS_URL`
- `NEXUS_USERNAME`
- `NEXUS_PASSWORD`

## Release Management

### Creating a Release

1. Create a Git tag:
```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

2. Create a GitHub release with the same tag name

3. The pipeline will automatically:
   - Build and tag Docker images with the version
   - Deploy to Kubernetes
   - Generate release notes from commits
   - Update the GitHub release with notes

## Project Structure

```
.
├── task-service/          # Task management microservice
├── user-service/          # User management microservice
├── api-gateway/           # API Gateway service
├── k8s/                   # Kubernetes manifests
├── .github/workflows/     # CI/CD pipelines
├── docker-compose.yml     # Local development setup
├── checkstyle.xml         # Code style configuration
└── pom.xml                # Parent POM
```

