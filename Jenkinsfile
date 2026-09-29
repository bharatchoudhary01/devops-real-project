pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t devops-real-project:%BUILD_NUMBER% .'
            }
        }

        stage('Test Application') {
            steps {
                bat 'docker run -d --name test-container -p 5001:5000 devops-real-project:%BUILD_NUMBER%'
                bat 'timeout /t 5 /nobreak'
                bat 'curl -f http://localhost:5001/health'
            }
        }

        stage('Cleanup') {
            steps {
                bat 'docker stop test-container || exit /b 0'
                bat 'docker rm test-container || exit /b 0'
            }
        }
    }
}
