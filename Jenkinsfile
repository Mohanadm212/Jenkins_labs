pipeline {
    agent any

    stages {
        stage('build') {
            steps {
                sh "docker build -t mohanadmahmoud/nti-day1:${env.BUILD_NUMBER} ."
            }

        }
        stage('login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh "docker push mohanadmahmoud/nti-day1:${env.BUILD_NUMBER}"
                }
            }

        }
    }
}