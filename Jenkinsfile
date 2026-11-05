pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t aws-cicd-app:latest .'
            }
        }

        stage('Deploy Application') {
            steps {
                sh 'docker rm -f aws-cicd-app || true'
                sh 'docker run -d --name aws-cicd-app -p 8080:80 aws-cicd-app:latest'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }
        failure {
            echo 'CI/CD Pipeline failed. Check Console Output.'
        }
    }
}
