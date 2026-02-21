# CloudSpace Academy - Jenkins Docker Demo

A simple HTML website for teaching Jenkins CI/CD pipeline with Docker deployment.

## 📁 Project Structure

```
├── index.html      # Simple HTML website
├── Dockerfile      # Docker configuration
├── Jenkinsfile     # Jenkins pipeline (bonus)
└── README.md       # This file
```

## Quick Start

### View Website Locally
Open `index.html` directly in your browser to see the design.

### Build and Run with Docker

1. **Build the Docker image:**
   ```bash
   docker build -t cloudspace-demo .
   ```

2. **Run the container:**
   ```bash
   docker run -p 8080:80 cloudspace-demo
   ```

3. **View in browser:**
   - Open http://localhost:8080

4. **Stop the container:**
   ```bash
   docker ps                    # Find container ID
   docker stop <container-id>   # Stop container
   ```

## 🏗️ Jenkins Pipeline Setup

### Prerequisites
- Jenkins with Docker plugin installed
- DockerHub account and credentials configured in Jenkins

### Method 1: Jenkins UI (Recommended for Learning)

1. **Create new Pipeline job:**
   - New Item → Pipeline
   - Name: `cloudspace-academy-demo`

2. **Configure pipeline script:**
   ```groovy
   pipeline {
       agent any
       
       environment {
           DOCKER_IMAGE = 'your-dockerhub-username/cloudspace-demo'
           DOCKER_TAG = "${BUILD_NUMBER}"
       }
       
       stages {
           stage('Checkout') {
               steps {
                   git 'https://github.com/your-username/your-repo.git'
               }
           }
           
           stage('Build Docker Image') {
               steps {
                   sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                   sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
               }
           }
           
           stage('Test Container') {
               steps {
                   sh """
                       docker run -d --name test-container-${BUILD_NUMBER} -p 808${BUILD_NUMBER}:80 ${DOCKER_IMAGE}:${DOCKER_TAG}
                       sleep 5
                       curl -f http://localhost:808${BUILD_NUMBER} || exit 1
                       docker stop test-container-${BUILD_NUMBER}
                       docker rm test-container-${BUILD_NUMBER}
                   """
               }
           }
           
           stage('Push to DockerHub') {
               steps {
                   withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                       sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                       sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                       sh "docker push ${DOCKER_IMAGE}:latest"
                   }
               }
           }
       }
       
       post {
           always {
               sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG} || true"
               sh "docker rmi ${DOCKER_IMAGE}:latest || true"
           }
       }
   }
   ```

### Method 2: Pipeline from SCM (Advanced)
- Use the included `Jenkinsfile`
- Configure pipeline to use "Pipeline script from SCM"
- Point to this Git repository

## 🔧 DockerHub Credentials Setup

1. Go to Jenkins → Manage Jenkins → Credentials
2. Add new Username/Password credential:
   - **ID:** `dockerhub-credentials`
   - **Username:** Your DockerHub username
   - **Password:** Your DockerHub password/token

## 🎯 What Students Learn

- ✅ Docker basics (build, run, push)
- ✅ Jenkins pipeline creation
- ✅ CI/CD concepts with visual results
- ✅ Container registry integration
- ✅ Automated testing in pipelines

## 🐛 Troubleshooting

**Docker build fails:**
```bash
docker build -t test-demo .
docker logs <container-id>
```

**Jenkins can't access Docker:**
- Ensure Jenkins user is in docker group
- Restart Jenkins service

**Port conflicts:**
- Use different ports: `-p 8081:80`, `-p 8082:80`, etc.
- Check running containers: `docker ps`

**Pipeline fails at push stage:**
- Verify DockerHub credentials in Jenkins
- Test manual login: `docker login`

## Good for simle demo

## 📚 Perfect for Teaching Because:

- **Visual Results** - Students see a beautiful webpage, not just logs
- **Simple Technology** - Just HTML + nginx, easy to understand  
- **Fast Builds** - No compilation, quick feedback
- **Real-world Relevant** - Static site deployment is common
- **Minimal Setup** - Only 4 files needed!

## 🏗️ Jenkins Setup Instructions

### Prerequisites
- Jenkins with Docker plugin installed
- DockerHub credentials configured in Jenkins
- Git repository access

### Jenkins Configuration

1. **Install required plugins:**
   - Docker Pipeline Plugin
   - Git Plugin
   - Credentials Plugin

2. **Configure DockerHub credentials:**
   - Go to Jenkins → Manage Jenkins → Credentials
   - Add new Username/Password credential
   - ID: `dockerhub-credentials`
   - Username: Your DockerHub username
   - Password: Your DockerHub password/token

### 🎯 Hands-On Workshop: Build Pipeline in Jenkins UI

**Step 1: Create a new Pipeline Job**
- New Item → Pipeline
- Name: `cloudspace-docker-demo`
- Choose "Pipeline script" (not from SCM)

**Step 2: Build the Pipeline Script in Jenkins UI**

Start with this basic structure and build it stage by stage:

```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/jenkins-demo'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '📥 Checking out code...'
                git 'https://github.com/your-username/your-repo.git'
            }
        }
        
        // Students add more stages here during workshop
    }
}
```

**Step 3: Add Stages Progressively**

Have students add these stages one by one, testing after each:

1. **Install Dependencies:**
```groovy
stage('Install Dependencies') {
    steps {
        echo '📦 Installing dependencies...'
        sh 'npm ci'
    }
}
```

2. **Run Tests:**
```groovy
stage('Run Tests') {
    steps {
        echo '🧪 Running tests...'
        sh 'npm test'
    }
}
```

3. **Build Docker Image:**
```groovy
stage('Build Docker Image') {
    steps {
        echo '🐳 Building Docker image...'
        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
    }
}
```

4. **Test Docker Image:**
```groovy
stage('Test Docker Image') {
    steps {
        echo '🔍 Testing Docker image...'
        sh """
            docker run -d --name test-container -p 3001:3000 ${DOCKER_IMAGE}:${DOCKER_TAG}
            sleep 10
            curl -f http://localhost:3001/health || exit 1
            docker stop test-container
            docker rm test-container
        """
    }
}
```

5. **Push to DockerHub:**
```groovy
stage('Push to DockerHub') {
    steps {
        echo '🚀 Pushing to DockerHub...'
        withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
            sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            sh "docker push ${DOCKER_IMAGE}:latest"
        }
    }
}
```

### 🎁 Bonus: Pipeline as Code

After students master the UI approach, introduce the Jenkinsfile in this repo as the "professional way" to manage pipelines.

## 📋 Pipeline Stages

1. **Checkout** - Get code from repository
2. **Install Dependencies** - Run `npm ci`
3. **Run Tests** - Execute test suite
4. **Build Docker Image** - Create container image
5. **Test Docker Image** - Verify container works
6. **Push to DockerHub** - Upload to registry
7. **Cleanup** - Remove local images

## 🧪 API Endpoints

- `GET /` - Welcome message with app info
- `GET /health` - Health check endpoint
- `GET /api/students` - Sample students data

## 🎓 Teaching Notes

### Workshop Flow:
1. **Start Simple** - Basic pipeline with just checkout and echo
2. **Add Dependencies** - Show npm ci stage
3. **Include Testing** - Demonstrate test automation
4. **Docker Build** - Introduce containerization
5. **Docker Test** - Show container validation
6. **Registry Push** - Complete the CI/CD cycle
7. **Bonus Round** - Introduce Jenkinsfile approach

### Key Concepts to Cover:
- Pipeline stages and steps
- Environment variables in Jenkins
- Credentials management
- Docker integration
- Error handling and cleanup
- Pipeline as Code (Jenkinsfile) as advanced topic

### Hands-On Learning Benefits:
- Students see immediate results after each stage
- Easy to debug when something breaks
- Understanding before automation
- Natural progression from simple to complex

### Common Issues Students Might Face:
- Docker daemon not running on Jenkins agent
- Missing Jenkins credentials for DockerHub
- Port conflicts during container testing
- Permission issues with Docker socket
- Git repository access problems

### Extension Ideas After Basic Pipeline:
- Add parallel stages for different tests
- Implement approval gates
- Add notifications (Slack/email)
- Include security scanning
- Set up multi-environment deployments

## 🔧 Troubleshooting

**Container won't start:**
```bash
docker logs <container-id>
```

**Tests failing:**
```bash
npm test -- --verbose
```

**Jenkins pipeline fails:**
- Check Jenkins logs
- Verify credentials
- Ensure Docker is available to Jenkins

## 📚 Additional Resources

- [Jenkins Pipeline Documentation](https://www.jenkins.io/doc/book/pipeline/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Node.js in Docker](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)

