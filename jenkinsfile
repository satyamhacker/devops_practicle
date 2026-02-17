pipeline {
    agent any // Kisi bhi available agent pe chalne do

    stages {
        stage('Checkout') {
            steps {
                echo 'Building branch: ' + env.BRANCH_NAME
                checkout scm // Ye automatically sahi branch download karega
            }
        }
        stage('Install Dependencies') {
            steps {
                echo 'Installing...'
                // sh 'npm install' // Agar Node.js hai toh
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'echo "Tests passed!"'
            }
        }
        stage('Deploy to Production') {
            when {
                branch 'main' // Sirf main branch deploy hogi
            }
            steps {
                echo 'Deploying only from main branch...'
            }
        }
    }
}