pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\Bharat Choudhary\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        IMAGE = 'devops-real-project'
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
                bat '"%DOCKER%" run -d --name test-container -p 5001:5000 %IMAGE%:%BUILD_NUMBER%'
                bat 'timeout /t 5 /nobreak'
                bat 'curl -f http://localhost:5001/health'
            }
        }

        stage('Cleanup') {
            steps {
                bat '"%DOCKER%" stop test-container'
                bat '"%DOCKER%" rm test-container'
            }
        }
    }
}
