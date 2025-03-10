pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'sarosh17/depl-project'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
            }
        }
        
        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-credentials', variable: 'DOCKER_HUB_PASSWORD')]) {
                    sh "docker login -u sarosh17 -p ${DOCKER_HUB_PASSWORD}"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh "docker stop depl-project-container || true"
                sh "docker rm depl-project-container || true"
                sh "docker run -d -p 80:80 --name depl-project-container ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
}