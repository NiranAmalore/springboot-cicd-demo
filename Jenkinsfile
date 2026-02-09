pipeline {
    agent any

    tools {
        jdk 'Java17'
        maven 'Maven'
    }

    environment {
        APP_NAME        = "demo-app"
        IMAGE_TAG       = "latest"
        DOCKERHUB_USER  = "<dockerhub-username>"
        IMAGE_FULL_NAME = "docker.io/${DOCKERHUB_USER}/${APP_NAME}:${IMAGE_TAG}"
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_FULL_NAME .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push $IMAGE_FULL_NAME
                      docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                  kubectl apply -f k8s/deployment.yaml
                  kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build, Push & Deploy successful"
            echo "🚀 Image: ${IMAGE_FULL_NAME}"
        }
        failure {
            echo "❌ Pipeline failed"
        }
        always {
            cleanWs()
        }
    }
}
