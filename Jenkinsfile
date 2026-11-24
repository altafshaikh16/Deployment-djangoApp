
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        APP_DIR = '/home/ubuntu/Deployment-djangoApp-preTested'
    }

    stages {
        stage('Code Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    set -e

                    rsync -av \
                      --exclude='.git/' \
                      --exclude='.env' \
                      --exclude='data/' \
                      --exclude='db.sqlite3' \
                      --exclude='__pycache__/' \
                      --exclude='*.pyc' \
                      "$WORKSPACE/" "$APP_DIR/"

                    docker compose -f "$APP_DIR/docker-compose.yml" build
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker compose \
                      -f "$APP_DIR/docker-compose.yml" \
                      up -d
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    sleep 15
                    curl -f http://localhost:80
                '''
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully!'
        }

        failure {
            echo 'Deployment failed. Check console output.'
        }
    }
}
