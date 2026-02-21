pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'edwinaabah'
        IMAGE_NAME = 'jenkins-demo'
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_IMAGE = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/TechByEdwina/jenkins-container-pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }
    }

    post {
        success {
            echo 'Image built and pushed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
