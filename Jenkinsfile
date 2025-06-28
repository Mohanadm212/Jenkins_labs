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
                sh """
                docker run --rm \
                    -v /var/run/docker.sock:/var/run/docker.sock \
                    -v $(pwd):/root/reports \
                    aquasec/trivy:latest image \
                    --exit-code 1 \
                    --severity HIGH,CRITICAL \
                    --format table \
                    --output /root/reports/trivy-report.txt \
                    ${IMAGE_NAME}
                """
            }
        }

        stage('Archive Scan Report') {
            steps {
                archiveArtifacts artifacts: 'trivy-report.txt', fingerprint: true
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
