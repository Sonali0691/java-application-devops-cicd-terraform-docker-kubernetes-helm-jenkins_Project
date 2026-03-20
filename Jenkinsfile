pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sonalihub/javaapp1"
        DOCKER_TAG = "${BUILD_NUMBER}"
        REGION = "ap-south-1"
        CLUSTER_NAME = "java-eks-cluster"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        docker.image('sonarsource/sonar-scanner-cli:latest').inside('--entrypoint=""') {
                            sh '''
                            sonar-scanner \
                            -Dsonar.projectKey=java-app \
                            -Dsonar.sources=.
                            '''
                        }
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE:$DOCKER_TAG
                    docker tag $DOCKER_IMAGE:$DOCKER_TAG $DOCKER_IMAGE:latest
                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

    }

    post {
        success {
            echo "Deployment Successful 🚀"
        }
        failure {
            echo "Pipeline Failed ❌"
        }
    }
}
