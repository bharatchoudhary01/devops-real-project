pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\Bharat Choudhary\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        IMAGE = 'devops-real-project'
        CONTAINER = 'test-container'
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
    }

    post {
        always {
            bat '"%DOCKER%" rm -f %CONTAINER% 2>nul || exit /b 0'
        }
    }
}
