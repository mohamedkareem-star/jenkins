pipeline {
    agent any

    environment {
        IMAGE_NAME = "node-ci-cd-app"
        CONTAINER_NAME = "node-app-container"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                // Jenkins does this automatically, but this makes it explicit
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Stop Existing Container') {
            steps {
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                """
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker run -d -p 3000:3000 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Health Check') {
            steps {
                sh 'sleep 5'
                sh 'curl -f http://localhost:3000/health'
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD Pipeline Successful"
        }
        failure {
            echo "❌ Pipeline Failed"
        }
    }
}
