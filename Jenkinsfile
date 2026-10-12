pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\Bharat Choudhary\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        IMAGE = 'devops-real-project'
        CONTAINER = 'test-container'
        DOCKERHUB_REPO = 'bharat181481/devops-real-project'
        EC2_HOST = '13.60.208.81'
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

        stage('Deploy to AWS EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'EC2_USER'
                )]) {
                    bat 'icacls "%SSH_KEY%" /inheritance:r /remove:g "*S-1-5-32-545" /grant:r "*S-1-5-18:F"'

                    bat 'ssh -i "%SSH_KEY%" -o StrictHostKeyChecking=accept-new %EC2_USER%@%EC2_HOST% "sudo docker pull %DOCKERHUB_REPO%:latest && (sudo docker rm -f devops-app || true) && sudo docker run -d --name devops-app -p 80:5000 --restart unless-stopped %DOCKERHUB_REPO%:latest"'
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
