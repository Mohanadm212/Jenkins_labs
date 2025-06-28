pipeline {
    agent any

    environment {
        IMAGE_NAME = "mohanadmahmoud/nti-day1:${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Security Scan - Trivy') {
            steps {
                script {
                    def workDir = sh(script: 'pwd', returnStdout: true).trim()
                    echo "Running Trivy vulnerability scan on ${IMAGE_NAME}"
                    sh """
                        docker run --rm \
                            -v /var/run/docker.sock:/var/run/docker.sock \
                            aquasec/trivy:latest image \
                            --exit-code 0 \
                            --severity HIGH,CRITICAL \
                            --format table \
                            ${IMAGE_NAME}
                    """
                }
            }
        }

        
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                    echo $PASSWORD | docker login -u $USERNAME --password-stdin
                    docker push ${IMAGE_NAME}
                    """
                }
            }
        }
    }
}
