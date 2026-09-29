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
                sh 'docker build -t devops-real-project:${BUILD_NUMBER} .'
            }
        }

        stage('Test Application') {
            steps {
                sh 'docker run -d --name test-container -p 5001:5000 devops-real-project:${BUILD_NUMBER}'
                sh 'sleep 5'
                sh 'curl -f http://localhost:5001/health'
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker stop test-container || true'
                sh 'docker rm test-container || true'
            }
        }
    }
}
