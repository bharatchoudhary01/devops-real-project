
pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\Bharat Choudhary\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        IMAGE = 'devops-real-project'
        CONTAINER = 'test-container'
        DOCKERHUB_REPO = 'bharat181481/devops-real-project'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Check') {
            steps {
                bat '"%DOCKER%" --version'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"%DOCKER%" build -t %IMAGE%:%BUILD_NUMBER% .'
            }
        }

        stage('Test Application') {
            steps {
                bat '"%DOCKER%" rm -f %CONTAINER% 2>nul || exit /b 0'
                bat '"%DOCKER%" run -d --name %CONTAINER% -p 5001:5000 %IMAGE%:%BUILD_NUMBER%'
                bat 'ping -n 6 127.0.0.1 > nul'
                bat 'curl.exe -f http://localhost:5001/health'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    bat 'powershell.exe -NoProfile -NonInteractive -Command "$env:DOCKERHUB_TOKEN | & $env:DOCKER login -u $env:DOCKERHUB_USER --password-stdin"'
                    bat '"%DOCKER%" tag %IMAGE%:%BUILD_NUMBER% %DOCKERHUB_REPO%:%BUILD_NUMBER%'
                    bat '"%DOCKER%" push %DOCKERHUB_REPO%:%BUILD_NUMBER%'
                    bat '"%DOCKER%" tag %IMAGE%:%BUILD_NUMBER% %DOCKERHUB_REPO%:latest'
                    bat '"%DOCKER%" push %DOCKERHUB_REPO%:latest'
                    bat '"%DOCKER%" logout'
                }
            }
        }
    }

    post {
        always {
            bat '"%DOCKER%" rm -f %CONTAINER% 2>nul || exit /b 0'
        }
    }
}
