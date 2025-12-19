pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = "djangoapp"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/sin-tanaka/template-django-mysql-dockercompose.git',
                    branch: 'main'
            }
        }

        stage('Stop Old Containers') {
            steps {
                sh 'docker compose down || true'
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Start Containers') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Wait for DB') {
            steps {
                sh 'sleep 15'
            }
        }

        stage('Run Migrations') {
            steps {
                sh 'docker compose exec -T web python manage.py migrate'
            }
        }

        stage('Collect Static Files') {
            steps {
                sh 'docker compose exec -T web python manage.py collectstatic --noinput'
            }
        }
    }

    post {
        success {
            echo '✅ Django deployed successfully via Jenkins'
        }
        failure {
            echo '❌ Jenkins deployment failed'
        }
    }
}
