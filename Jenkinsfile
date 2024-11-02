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

        stage('Snyk Security Analysis') {
            steps {
                echo 'Running Snyk Security Analysis...'
                // Optional: List the contents of the workspace and app directory
                sh 'echo "Workspace contents:" && ls -la'
                sh 'echo "App directory contents:" && ls -la app'

                dir('app') {
                    snykSecurity(
                        snykInstallation: 'snyk',     // Name from Global Tool Configuration
                        snykTokenId: 'snyk_api',      // Credentials ID for the Snyk API token
                        failOnError: false,            // Set to false to avoid build failure on vulnerabilities
                        failOnIssues: false,
                        monitorProjectOnBuild: true,
                        severity: 'high'              // Adjust as needed: low, medium, high, critical
                    )
                }
            }
        }

        stage('Build and Tag Docker Image with Compose') {
            steps {
                script {
                    echo 'Building and Tagging Docker Image with Docker Compose...'
                    // Build the Docker image using docker-compose
                    sh "docker compose -f docker-compose.yml build app"
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
                    sh "docker compose -f docker-compose.yml up -d"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up Docker resources...'
            // Uncomment the following lines if you want to clean up Docker resources after the build
            // sh "docker compose -f docker-compose.yml down"
            // sh "docker system prune -f"
        }
    }
}
