
pipeline {
    agent any

    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checked out successfully'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'node --version'
                bat 'npm ci'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -f dockerfile -t nodejs-demo-app-2:%BUILD_NUMBER% .'
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                    docker rm -f nodejs-demo-app-2 2>NUL
                    docker run -d --name nodejs-demo-app-2 -p 3001:3000 nodejs-demo-app-2:%BUILD_NUMBER%
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                bat 'powershell -NoProfile -Command "Start-Sleep -Seconds 5; (Invoke-WebRequest -UseBasicParsing http://localhost:3001).Content"'
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
