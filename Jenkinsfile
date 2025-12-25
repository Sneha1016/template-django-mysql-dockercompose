pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ACCOUNT_ID = '716244586157'
        ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.ap-south-1.amazonaws.com/django-app"
    }

    stages {

        stage('Checkout Source') {
            steps {
                git branch: 'master', url: 'https://github.com/Sneha1016/template-django-mysql-dockercompose.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker image..."
                    docker build -t django-app .
                '''
            }
        }

        stage('Login to ECR & Push Image') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh '''
                        echo "Logging into ECR..."
                        aws ecr get-login-password --region $AWS_REGION \
                        | docker login --username AWS --password-stdin $ECR_REPO

                        echo "Tagging Docker image..."
                        docker tag django-app:latest $ECR_REPO:latest

                        echo "Setting timeouts and retries..."
                        export DOCKER_CLIENT_TIMEOUT=300
                        export COMPOSE_HTTP_TIMEOUT=300
                        export AWS_MAX_ATTEMPTS=5

                        echo "Pushing Docker image with reliability..."
                        docker push $ECR_REPO:latest --max-concurrent-uploads=1
                    '''
                }
            }
        }

        stage('Deploy using CloudFormation') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh '''
                        echo "Deploying EC2 using CloudFormation..."
                        aws cloudformation deploy \
                        --stack-name django-ec2-stack \
                        --template-file infra/ec2.yaml \
                        --parameter-overrides KeyName=django-key \
                        --capabilities CAPABILITY_NAMED_IAM
                    '''
                }
            }
        }
    }
}

