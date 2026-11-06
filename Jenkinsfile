
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
                sh 'docker build -t aws-cicd-app:latest .'
            }
        }

        stage('Deploy Locally') {
            steps {
                sh 'docker rm -f aws-cicd-app || true'
                sh 'docker run -d --name aws-cicd-app -p 8082:80 aws-cicd-app:latest'
            }
        }

        stage('Prepare Docker Image') {
            steps {
                sh 'docker save -o aws-cicd-app.tar aws-cicd-app:latest'
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh '''
                        set -e

                        scp -o StrictHostKeyChecking=no \
                            aws-cicd-app.tar \
                            ubuntu@13.233.192.179:/tmp/

                        ssh -o StrictHostKeyChecking=no \
                            ubuntu@13.233.192.179 '
                                set -e
                                sudo docker load -i /tmp/aws-cicd-app.tar
                                sudo docker rm -f aws-cicd-app || true
                                sudo docker run -d \
                                    --name aws-cicd-app \
                                    -p 80:80 \
                                    aws-cicd-app:latest
                                rm -f /tmp/aws-cicd-app.tar
                            '
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD deployment completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
