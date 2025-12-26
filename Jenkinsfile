pipeline {
    agent any

    stages {
        stage('Checkout Source') {
            steps { ... }
        }

        stage('Build Docker Image') {
            steps { ... }
        }

        stage('Login to ECR & Push Image') {
            steps { ... }
        }

        stage('Deploy using CloudFormation') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh """#!/bin/bash
echo Deploying EC2 using CloudFormation...

aws cloudformation deploy \
--stack-name django-ec2-stack-v3 \
--template-file infra/ec2.yaml \
--parameter-overrides KeyName=django-key \
--capabilities CAPABILITY_NAMED_IAM \
--no-fail-on-empty-changeset

echo "Deployment command executed."
"""
                }
            }
        }
    }   // <--- CLOSE stages block
}       // <--- CLOSE pipeline block
