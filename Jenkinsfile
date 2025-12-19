pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = "djangoapp"
    }

    stages {

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
            echo '✅ Jenkins deployment successful'
        }
        failure {
            echo '❌ Jenkins deployment failed'
        }
    }
}
