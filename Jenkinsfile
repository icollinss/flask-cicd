pipeline {
    agent any

    stages {
        stage('Clone from GitHub') {
            steps {
                git 'https://github.com/icollinss/flask-cicd.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-cicd-app .'
            }
        }
        stage('Stop and Remove Old container') {
            steps {
                sh '''
                docker stop flask_app || true
                docker rm flask_app || true

                # Kill any process using port 5000 (rare fallback)
                fuser -k 5000/tcp || true
                '''
            }
        }
        stage('Run new Docker Container') {
            steps {
                sh 'docker run -d -p 5000:5000 --name flask_app flask-cicd-app'
            }
        }
    }
}