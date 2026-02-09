pipeline {
    agent any

    tools {
        jdk 'Java17'
        maven 'Maven'
    }

    environment {
        APP_NAME   = "demo-app"
        IMAGE_TAG  = "1.0"
        IMAGE_NAME = "${APP_NAME}:${IMAGE_TAG}"
    }

    stages {

        stage('Checkout Source Code') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh '''
                    mvn clean package
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh '''
                    docker images | grep $APP_NAME
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully"
            echo "🐳 Docker Image: ${IMAGE_NAME}"
        }
        failure {
            echo "❌ Pipeline failed"
        }
        always {
            cleanWs()
        }
    }
}
