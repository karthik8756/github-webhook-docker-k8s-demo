pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'YOUR_DOCKERHUB_USERNAME'
        IMAGE_NAME = 'myweb'
        IMAGE_TAG = 'v1'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Code downloaded successfully'
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh 'docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
