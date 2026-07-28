pipeline {
    agent any

    tools {
        nodejs 'Node18'
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
	 stage('Build Docker Image') {
    		steps {
        		dir('client') {
            			sh 'docker build -t hayroo-frontend:latest .'
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
        echo 'Frontend build archived successfully!'
    }

    failure {
        echo 'Frontend build failed.'
    }
}
}
