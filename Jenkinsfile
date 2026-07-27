pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
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
                    sh 'npm run build'
                }
            }
        }

        stage('Backend Install') {
            steps {
                dir('server') {
                    sh 'npm install'
                }
            }
        }

        stage('Backend Test') {
            steps {
                dir('server') {
                    sh 'echo "Backend Ready"'
                }
            }
        }

        stage('Docker Build Frontend') {
            steps {
                sh 'docker build -t hayroo-frontend:latest ./client'
            }
        }

        stage('Docker Build Backend') {
            steps {
                sh 'docker build -t hayroo-backend:latest ./server'
            }
        }

    }

    post {
        always {
            echo 'Pipeline Finished'
        }

        success {
            echo 'Build Successful'
        }

        failure {
            echo 'Build Failed'
        }
    }
}
