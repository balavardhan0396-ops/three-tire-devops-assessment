pipeline {

    agent any

    environment {
        COMPOSE_DOCKER_CLI_BUILD = "1"
        DOCKER_BUILDKIT = "1"
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Verify Repository') {
            steps {
                sh '''
                pwd
                ls -la
                '''
            }
        }

        stage('Frontend Dependencies') {
            steps {
                dir('frontend') {
                    sh '''
                    npm install
                    '''
                }
            }
        }

        stage('Backend Dependencies') {
            steps {
                dir('backend') {
                    sh '''
                    npm install
                    '''
                }
            }
        }

        stage('Build React Application') {
            steps {
                dir('frontend') {
                    sh '''
                    npm run build
                    '''
                }
            }
        }

        stage('Security Scan') {
            steps {
                dir('frontend') {
                    sh '''
                    npm audit || true
                    '''
                }

                dir('backend') {
                    sh '''
                    npm audit || true
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker compose build
                '''
            }
        }

        stage('Deploy Containers') {
            steps {
                sh '''
                docker compose up -d
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh '''
                docker ps
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 20

                curl http://localhost:3001 || true

                docker compose ps
                '''
            }
        }

    }

    post {

        always {

            echo "Pipeline Finished"

        }

        success {

            echo "Application deployed successfully"

        }

        failure {

            echo "Deployment Failed"

        }
    }

}
