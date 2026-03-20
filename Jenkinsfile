pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sonalihub/javaapp1"
        DOCKER_TAG = "${BUILD_NUMBER}"
        REGION = "ap-south-1"
        CLUSTER_NAME = "java-eks-cluster"
        SONARQUBE_URL = "http://3.110.210.157:9000"
        SONAR_PROJECT_KEY = "java-app"
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    docker.image('sonarsource/sonar-scanner-cli:latest').inside('--entrypoint=""') {
                      sh '''
sonar-scanner \
-Dsonar.projectKey=java-app \
-Dsonar.sources=. \
-Dsonar.host.url=http://3.110.210.157:9000 \
-Dsonar.login=$SONAR_TOKEN \
-Dsonar.userHome=$WORKSPACE/.sonar
'''
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker push $DOCKER_IMAGE:$DOCKER_TAG
                docker tag $DOCKER_IMAGE:$DOCKER_TAG $DOCKER_IMAGE:latest
                docker push $DOCKER_IMAGE:latest
                '''
            }
        }

    }   // ✅ IMPORTANT: closing stages block

    post {
        success {
            echo "Deployment Successful 🚀"
        }
        failure {
            echo "Pipeline Failed ❌"
        }
    }
}
