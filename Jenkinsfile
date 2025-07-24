pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-login') // we’ll set this up in Jenkins
        DOCKER_IMAGE = "icollinss/flask-cicd-app"
    }

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
                sh 'docker run -d -p 5050:5000 --name flask_app flask-cicd-app'
            }
        }
        stage('Push to Dockerhub') {
            steps {
                sh '''
                    echo "$DOCKERHUB_CREDENTIALS_PSW" | docker login -u "$DOCKERHUB_CREDENTIALS_USR" --password-stdin
                    docker tag flask-cicd-app $DOCKER_IMAGE:latest
                    docker push $DOCKER_IMAGE:latest
                    '''
            }
        }
    }
}