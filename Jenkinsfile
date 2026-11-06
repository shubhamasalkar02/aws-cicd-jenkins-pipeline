
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
                sh 'docker build -t aws-cicd-app:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy Locally') {
            steps {
                sh '''
                    docker rm -f aws-cicd-app || true
                    docker run -d --name aws-cicd-app \
                        -p 8082:80 aws-cicd-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Prepare Image') {
            steps {
                sh '''
                    docker save -o aws-cicd-app-${BUILD_NUMBER}.tar \
                    aws-cicd-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy with Rollback to EC2') {
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh '''
                        set -e

                        scp -o StrictHostKeyChecking=no \
                        aws-cicd-app-${BUILD_NUMBER}.tar \
                        ubuntu@13.233.192.179:/tmp/

                        ssh -o StrictHostKeyChecking=no \
                        ubuntu@13.233.192.179 \
                        "NEW_IMAGE=aws-cicd-app:${BUILD_NUMBER} TAR_FILE=/tmp/aws-cicd-app-${BUILD_NUMBER}.tar bash -s" <<'REMOTE'

                        set -e

                        PREVIOUS_IMAGE=$(sudo docker inspect \
                        --format='{{.Config.Image}}' aws-cicd-app 2>/dev/null || true)

                        sudo docker load -i "$TAR_FILE"
                        sudo docker rm -f aws-cicd-app || true

                        if sudo docker run -d --name aws-cicd-app \
                        -p 80:80 "$NEW_IMAGE"; then
                            sleep 5

                            if false; then
                                echo "New deployment successful!"
                            else
                                echo "Deployment failed. Starting rollback..."
                                sudo docker rm -f aws-cicd-app || true

                                if [ -n "$PREVIOUS_IMAGE" ]; then
                                    sudo docker run -d --name aws-cicd-app \
                                    -p 80:80 "$PREVIOUS_IMAGE"
                                    echo "Rollback completed!"
                                fi

                                exit 1
                            fi
                        else
                            echo "New deployment failed!"
                            if [ -n "$PREVIOUS_IMAGE" ]; then
                                sudo docker run -d --name aws-cicd-app \
                                -p 80:80 "$PREVIOUS_IMAGE"
                                echo "Rollback completed!"
                            fi
                            exit 1
                        fi

                        rm -f "$TAR_FILE"
REMOTE
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Check rollback status in Console Output.'
        }
    }
}
