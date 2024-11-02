pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ameliamae/nodejschatapp'
        GIT_REPO = 'https://github.com/CWEB2116/NodejsChatApp_SCAN.git'
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
                snykSecurity(
                    snykInstallation: 'snyk', // Name from Global Tool Configuration
                    snykTokenId: 'snyk-api', // Credentials ID for the Snyk API token
                    failOnError: true, // Set to false to avoid build failure on vulnerabilities
                    monitorProjectOnBuild: true,
                    severity: 'high' // Adjust as needed: low, medium, high, critical
                )
            }
        }

        // ... rest of your stages remain the same ...
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
