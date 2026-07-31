pipeline {
    agent any

    tools {
        nodejs 'Node18'
    }

    environment {
    	KUBECONFIG = "/home/saidev/.kube/config"
    IMAGE_TAG = "${BUILD_NUMBER}"

    FRONTEND_IMAGE = "chakkadocker/hayroo-frontend:${IMAGE_TAG}"
    BACKEND_IMAGE = "chakkadocker/hayroo-backend:${IMAGE_TAG}"
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
            }git add .

        }

        stage('Build Frontend Docker Image') {
            steps {
                dir('client') {
                    sh 'docker build -t $FRONTEND_IMAGE .'
                }
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                dir('server') {
                    sh 'docker build -t $BACKEND_IMAGE .'
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                        docker push $FRONTEND_IMAGE
                        docker push $BACKEND_IMAGE

                        docker logout
                    '''
                }
            }
        }
        
stage('Deploy Frontend') {
    steps {
        sh '''
            kubectl --kubeconfig=/home/saidev/.kube/config \
                set image deployment/frontend \
                frontend=$FRONTEND_IMAGE

            kubectl --kubeconfig=/home/saidev/.kube/config \
                rollout status deployment/frontend
        '''
    }
}

stage('Deploy Backend') {
    steps {
        sh '''
            kubectl --kubeconfig=/home/saidev/.kube/config \
                set image deployment/backend \
                backend=$BACKEND_IMAGE

            kubectl --kubeconfig=/home/saidev/.kube/config \
                rollout status deployment/backend
        '''
    }
}
    }

    post {
        always {
            echo 'Pipeline finished.'
        }

        success {
            archiveArtifacts artifacts: 'client/build/**', fingerprint: true
            echo 'Frontend and Backend images pushed successfully!'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
