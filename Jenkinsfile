pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'rayenneji'
        DOCKERHUB_REGISTRY = 'docker.io'
    }


    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            "$DOCKERHUB_REGISTRY" \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    docker build -t ${DOCKERHUB_REGISTRY}/${DOCKERHUB_USERNAME}/devops-backend:latest ./backend
                    docker build -t ${DOCKERHUB_REGISTRY}/${DOCKERHUB_USERNAME}/devops-frontend:latest ./frontend
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh '''
                    docker push ${DOCKERHUB_REGISTRY}/${DOCKERHUB_USERNAME}/devops-backend:latest
                    docker push ${DOCKERHUB_REGISTRY}/${DOCKERHUB_USERNAME}/devops-frontend:latest
                '''
            }
        }

        stage('Deploy Docker Compose') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Verify Containers') {
            steps {
                sh 'docker compose ps'
            }
        }
    }

    post {
        always {
            sh 'docker logout "$DOCKERHUB_REGISTRY" || true'
        }
    }
}