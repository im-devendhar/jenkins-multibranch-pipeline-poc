pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo "Building HTML app from ${env.BRANCH_NAME} branch"
            }
        }
        stage('Test') {
            steps {
                echo "Simulating tests for ${env.BRANCH_NAME} branch"
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying HTML app from ${env.BRANCH_NAME} branch"
            }
        }
    }
}
