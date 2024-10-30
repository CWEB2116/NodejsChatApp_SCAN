pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ameliamae/nodejschatapp'
        GIT_REPO = 'https://github.com/CWEB2116/NodejsChatApp.git'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub Repository...'
                git branch: 'main', url: "${env.GIT_REPO}"
            }
        }

        stage('Build and Tag Docker Image with Compose') {
            steps {
                script {
                    echo 'Building and Tagging Docker Image with Docker Compose...'
                    sh "ls"
                    sh "docker-compose -f docker-compose.yml build app" 
                    sh "docker tag nodejschatapp ${DOCKER_IMAGE}:latest"
                    sh "docker tag nodejschatapp ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker Image to Docker Hub...'
                    sh "docker push ${DOCKER_IMAGE}:latest"
                    sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Run Application with Docker Compose') {
            steps {
                script {
                    echo 'Pulling and Running Docker Image with Docker Compose...'
                    // Pull the latest image
                    sh "docker pull ${DOCKER_IMAGE}:latest"
                    
                    // Start application container using docker-compose
                    sh "docker-compose -f docker-compose.yml up -d"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up Docker resources...'
            sh "docker-compose -f docker-compose.yml down"
            sh "docker system prune -f"
        }
    }
}
