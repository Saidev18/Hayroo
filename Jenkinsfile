pipeline {
    agent any

    tools {
        nodejs 'Node18'
    }

    environment {
        IMAGE_NAME = 'chakkadocker/hayroo-frontend:latest'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Node Version') {
            steps {
                sh 'node --version'
                sh 'npm --version'
            }
        }

        stage('Frontend Install') {
            steps {
                dir('client') {
                    sh 'npm install --legacy-peer-deps'
                }
            }
        }

        stage('Frontend Build') {
            steps {
                dir('client') {
                    sh '''
                        export NODE_OPTIONS=--openssl-legacy-provider
                        npm run build
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('client') {
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME
                        docker logout
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }

        success {
            archiveArtifacts artifacts: 'client/build/**', fingerprint: true
            echo 'Docker image pushed successfully!'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
