pipeline {

    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        COMPOSE_PROJECT_NAME = "three-tier-app"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Source Code') {
            steps {
                echo "Checking out source code from GitHub..."
                checkout scm
            }
        }

        stage('Verify Repository Structure') {
            steps {
                bat '''
                echo ==========================================
                echo Verifying Repository Structure
                echo ==========================================

                cd
                dir
                '''
            }
        }

        stage('Trivy Filesystem Scan') {
            steps {
                bat '''
                echo ==========================================
                echo Running Trivy Filesystem Scan
                echo ==========================================

                trivy fs --severity HIGH,CRITICAL --format table . > trivy-fs-report.txt

                type trivy-fs-report.txt
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                bat '''
                echo ==========================================
                echo Building Docker Images
                echo ==========================================

                docker compose build
                '''
            }
        }

        stage('Trivy Docker Image Scan') {
            steps {
                bat '''
                echo ==========================================
                echo Scanning Docker Images
                echo ==========================================

                trivy image frontend:latest > frontend-image-report.txt
                trivy image backend:latest > backend-image-report.txt

                type frontend-image-report.txt
                type backend-image-report.txt
                '''
            }
        }

        stage('Deploy Docker Containers') {
            steps {
                bat '''
                echo ==========================================
                echo Deploying Containers
                echo ==========================================

                docker compose up -d
                '''
            }
        }

        stage('Verify Running Containers') {
            steps {
                bat '''
                echo ==========================================
                echo Running Containers
                echo ==========================================

                docker ps

                echo.

                docker compose ps
                '''
            }
        }

        stage('Application Health Check') {
            steps {
                bat '''
                echo ==========================================
                echo Performing Health Check
                echo ==========================================

                timeout /t 20

                curl http://localhost:3001

                '''
            }
        }

    }

    post {

        success {

            echo "==========================================="
            echo "Application deployed successfully."
            echo "==========================================="

        }

        failure {

            echo "==========================================="
            echo "Pipeline failed."
            echo "Check Console Output."
            echo "==========================================="

        }

        always {

            archiveArtifacts artifacts: '*.txt', fingerprint: true

            echo "Pipeline execution completed."

        }

    }

}
