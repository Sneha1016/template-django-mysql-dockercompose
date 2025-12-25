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
                sh 'docker build -t django-app .'
            }
        }

        stage('Login to ECR & Push Image') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION \
                        | docker login --username AWS --password-stdin $ECR_REPO

                        docker tag django-app:latest $ECR_REPO:latest

                        docker push $ECR_REPO:latest
                    '''
                }
            }
        }

        stage('Deploy using CloudFormation') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh '''
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
